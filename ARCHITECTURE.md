# Architecture

A tour of how the portfolio is built. This documents the private project; it is
not a build guide, and the full source is not included.

## Rendering model

Next.js 16 App Router. The public marketing pages are **static / SSG** — they
read their content from Firestore at build (and first render) and prerender to
HTML, so the site is fast and cacheable. The private `/admin` routes are
**dynamic**, server-rendered on demand behind auth.

Route groups keep the two worlds apart: a `(site)` group carries all the public
chrome (smooth scroll, custom cursor, the page-transition, the persistent
navbar), while `/admin` is a sibling that inherits none of it — a plain, fast
tool UI with native scrolling and cursor.

## One animation loop

The single most important decision: **Lenis smooth scroll and every GSAP
ScrollTrigger share one `requestAnimationFrame` loop**, driven off GSAP's own
ticker. Lenis is created with `autoRaf: false`; each Lenis frame pushes into
`ScrollTrigger.update()`. That means scroll-linked animations are computed
against the exact smoothed scroll position on the same frame it changes — no
double RAF, no jitter between the scroll and the effects riding on it.

A recurring hazard this creates: GSAP pins insert a "pin-spacer" into the layout
*after* Lenis has measured the page, so triggers can end up positioned against a
stale layout on a hard refresh. The fix used throughout is a small, staggered
remeasure (`ScrollTrigger.refresh()` + `lenis.resize()`) as fonts, images, and
the 3D canvas settle — plus reading the Lenis instance through a ref so the
setup runs once and isn't re-triggered when the provider mounts.

## The 3D centerpiece

React Three Fiber renders a robot model (`robot.glb`) loaded client-only (WebGL
can't render on the server). Rather than a full wireframe, only **crease edges**
(faces meeting past an angle threshold) are extracted, so curved surfaces read as
clean facets instead of clutter. A halftone dot-matrix shader passes over the
solid mesh; a top-anchored clip plane sweeps through to "scan" the model into and
out of existence on entry and page-leave; a damped pointer tracker tilts it
toward the cursor; and a GLSL shoulder-pivot displacement gives the arms a subtle
idle sway. A device-quality tier (probed once on the client) scales MSAA, pixel
ratio, and particle counts, and a runtime FPS guard steps quality down if a
mis-detected device stutters. The whole loop pauses via `IntersectionObserver`
when off-screen.

## Motion system

Every animation is a small, composable helper in one module — one per effect —
each returning a GSAP tween/timeline/ScrollTrigger so callers can pause, reverse,
or kill it on cleanup. Timings and easings live in a single tokens file, so the
"house style" is defined once. Highlights:

- **Text scramble** — a per-character decode that locks left-to-right; the real
  text stays the accessible name while an aria-hidden layer shows the effect.
- **Project marquee** — a pinned column with a scroll-scrubbed focus that
  brightens the row nearest the viewport center and dims the rest.
- **Career timeline** — a serpentine SVG connector between alternating cards,
  drawn via `stroke-dashoffset` scrubbed to scroll, with a bright tip that rides
  the draw front and nodes that light as it passes.
- **Pixel transitions** — grids of cells that dissolve/assemble for route
  changes and for a scroll-scrubbed hero reveal.
- **Liquid button** — a measured, reversible fill timeline (see the sample).

Every helper respects `prefers-reduced-motion`, resolving to the end state
instead of animating.

## Content & CMS

All user-facing copy and data (hero, about, career, projects, achievements,
navbar, footer) live in Firestore `content/*` documents. Server components read
them through cached, tagged getters (`unstable_cache` + `revalidateTag`), so:

- pages prerender statically, and
- an admin write invalidates exactly the one document that changed and pushes it
  live instantly — no redeploy.

The `/admin` app is a form-driven editor over those documents, with image uploads
to Cloudinary. `firebase-admin` is externalized from the bundler and loaded as a
real Node module at runtime (bundling it broke a transitive ESM dependency in the
serverless runtime).

## Large-display scaling

The design was drawn against a ~1440px-wide reference. Rather than let it read as
sparse on bigger monitors, the root font size becomes fluid *above* that width,
so `1rem` tracks the screen and the entire rem-based layout scales as one — with
a clamp floor so phones, tablets, and ordinary laptops are byte-identical to
before. A handful of canvas/SVG visuals that size themselves in raw pixels read
the same scale back so they grow in step.

## Reliability notes

A few problems that shaped the code:

- **WebGL context lifetime** — vanilla three.js backdrops release their GPU
  context eagerly on unmount (not just `dispose()`, which leaks it), so
  navigating between WebGL pages doesn't pile up contexts toward the browser's
  limit.
- **GSAP context teardown** — a guard makes GSAP's context graph traversal
  cycle-proof, so a malformed context can't overflow the stack and take down the
  tab on navigation.
- **Hard-refresh measurement races** — see "One animation loop" above.

---

© 2026 Arif Hasan. All rights reserved. See [LICENSE](./LICENSE).

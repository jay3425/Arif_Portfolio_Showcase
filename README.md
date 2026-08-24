# Arif Hasan — Portfolio

A motion-led personal portfolio for **Arif Hasan**, full-stack developer.
Dark, HUD/technical aesthetic with a real-time WebGL centerpiece, scroll-scrubbed
storytelling, and a Firebase-backed CMS so every word and image is editable
without a redeploy.

### ▶ Live site: **https://arif-hasan.vercel.app**

> **This is a showcase, not the full source.** The complete project is private.
> What's here is a curated set of components and documentation that demonstrate
> how it's built. See [LICENSE](./LICENSE) — the code and design are all rights
> reserved and not licensed for reuse.

---

## What it does

- **3D robot centerpiece** — a 50k-triangle model rendered live in the browser
  (React Three Fiber), drawn as bright crease-edge lines with a halftone pass, a
  "scanned into existence" materialize effect, mouse-tracked tilt, and
  drag-to-rotate. Tap it to route into the About page.
- **Motion-first, everywhere** — a single shared animation loop drives smooth
  scrolling and every scroll-linked effect together, so nothing jitters: a
  scrubbed project marquee, a serpentine "living circuit" career timeline that
  draws itself as you scroll, a pixel-dissolve page-transition, per-section
  entrance choreography, and a bespoke text-scramble decode used site-wide.
- **Editable without code** — a private `/admin` app writes to Firestore; the
  public pages read it at build/first-render and revalidate instantly on save.
  Nothing user-facing is hardcoded.
- **Responsive + accessible** — one fluid scale keeps the design proportional
  from a laptop to a 4K display; a full `prefers-reduced-motion` path and a
  device-quality tier gracefully drop the heavy effects on weaker hardware.
- **A generated social share card and favicon**, a large-display UI scale, an
  installable PWA, and Vercel Web Analytics.

## Tech

| Area | Stack |
|------|-------|
| Framework | Next.js 16 (App Router, React 19, Turbopack), TypeScript |
| Styling | Tailwind CSS v4 |
| Motion | GSAP + ScrollTrigger, `@gsap/react`, Lenis smooth scroll (one shared ticker) |
| 3D | React Three Fiber + drei + three.js |
| Backend / CMS | Firebase (Firestore + Admin SDK), a custom `/admin` editor |
| Media | Cloudinary + `next/image` |
| Hosting | Vercel |

See **[ARCHITECTURE.md](./ARCHITECTURE.md)** for how the pieces fit together.

## Sample code

The [`samples/`](./samples) folder holds a few self-contained components,
included to show code quality and documentation style. They are excerpts from a
larger private codebase and are not meant to compile on their own.

- [`ScrambleText.tsx`](./samples/ScrambleText.tsx) — the site-wide text “decode”
  effect: each character cycles through random glyphs and locks left-to-right.
  GSAP-driven, accessible (the real text stays the element's accessible name),
  and reduced-motion aware.
- [`LiquidButton.tsx`](./samples/LiquidButton.tsx) — a cream button whose badge
  floods to fill it on hover via a measured, reversible GSAP timeline.
- [`Hud.tsx`](./samples/Hud.tsx) — the ambient HUD: a live clock in the owner's
  timezone with a slow crossfading status line.
- [`CursorDot.tsx`](./samples/CursorDot.tsx) — the custom cursor, an eased dot
  that tracks the pointer on the shared ticker and hides the native cursor.

---

© 2026 Arif Hasan. All rights reserved. This project is source-available for
viewing only — see [LICENSE](./LICENSE).

# Sample components

These are self-contained excerpts from the private portfolio codebase, included
to show code quality, structure, and documentation style. They import shared
helpers (`@/lib/...`) that are not part of this repository, so they are meant to
be **read**, not compiled.

| File | What it shows |
|------|---------------|
| `ScrambleText.tsx` | The site-wide text "decode" effect. GSAP-driven, accessible (the real text remains the element's accessible name; an aria-hidden layer shows the scramble), and reduced-motion aware. |
| `LiquidButton.tsx` | A cream button whose black badge floods to fill it on hover, via a measured, reversible GSAP timeline re-built from DOM geometry on resize. |
| `Hud.tsx` | The ambient HUD: a live clock in the owner's timezone plus a slow crossfading status line, on existing motion tokens. |
| `CursorDot.tsx` | The custom cursor — an eased dot that tracks the pointer on the shared GSAP ticker and hides the native cursor on fine-pointer devices. |

© 2026 Arif Hasan. All rights reserved. See [../LICENSE](../LICENSE).

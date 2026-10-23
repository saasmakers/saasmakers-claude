---
paths:
  - "**/*.vue"
  - "**/*.css"
---

### Motion tokens

- Use `duration-fast|base|slow` (150/200/300ms, default `base`) and the global ease-out. No raw `duration-<ms>` or `ease-*` (lint enforces it).
- Keep `motion-reduce:duration-0` next to `motion-reduce:transition-none` on `<Transition>`: Vue times enter/leave from it.
- Plain CSS: `var(--duration-*)` / `var(--easing)`. motion-v: `fadeIn`… or `motionDurations`. Values live in `ui/app/assets/styles/motion.css` and `ui/app/tokens/motion.ts`: keep both in sync.

### Stacking tokens

- Use `z-raised|dock|overlay|popover|toast|splash` (10/50/60/70/80/9999). No raw `z-<n>` or `z-[…]` (lint enforces it; `z-0` allowed). In TS use `zIndices` from `ui/app/tokens/stacking.ts`.

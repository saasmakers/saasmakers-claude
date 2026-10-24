---
paths:
  - "**/*.vue"
  - "**/*.css"
---

### Motion tokens

- Use `duration-fast|base|slow` (150/250/500ms, default `base`) and the global ease-out. No raw `duration-<ms>` or `ease-*` (lint enforces it).
- Keep `motion-reduce:duration-0` next to `motion-reduce:transition-none` on `<Transition>`: Vue times enter/leave from it.
- Plain CSS: `var(--duration-*)` / `var(--easing)`. motion-v: `fadeIn`… or `motionDurations`. Values live in `ui/app/assets/styles/motion.css` and `ui/app/tokens/motion.ts`: keep both in sync.

### Stacking tokens

- Use `z-raised|dock|overlay|popover|toast|splash` (10/50/60/70/80/9999). No raw `z-<n>` or `z-[…]` (lint enforces it; `z-0` allowed). In TS use `zIndices` from `ui/app/tokens/stacking.ts`.

### Radius tokens

- Use `rounded-tag|inner|control|card|dialog` (4/6/8/12/16px): tags and kbd, targets inside a control, buttons and fields, cards and bordered blocks, modal and sheet. Sides work (`rounded-t-dialog`). `rounded-full` / `rounded-none` stay. No raw `rounded`, `rounded-<size>` or `rounded-[…]` (lint enforces it).
- Nested radii stay concentric: an element inside a `control` with little padding uses `inner`.
- Plain CSS: `var(--radius-*)`. Values live in `ui/app/assets/styles/radius.css` and `ui/app/tokens/radius.ts`: keep both in sync.

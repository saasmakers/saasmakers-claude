---
paths:
  - "**/*.vue"
  - "**/*.css"
  - "**/uno.config.ts"
---

### Motion tokens

- Durations come from `@saasmakers/ui`: `duration-fast` (150ms), `duration-base` (200ms, default), `duration-slow` (300ms). Never raw `duration-<ms>` (enforced by `saasmakers/vue-format-template`).
- Easing is global (`theme.easing.DEFAULT`, ease-out). Don't add `ease-*` classes.
- Bare `transition` / `transition-colors` already use `duration-base`; add a token only to change it.
- Keep `motion-reduce:duration-0` next to `motion-reduce:transition-none` on Vue `<Transition>` classes: Vue reads `transition-duration` to time leave/enter.
- Plain CSS: `var(--duration-fast|base|slow)` and `var(--easing)` from `packages/ui/app/assets/styles/motion.css`.
- motion-v: reuse `fadeIn`, `fadeInUp`… or `motionDurations` / `motionEasing` from `packages/ui/app/utils/animations.ts`.
- Changing a value: update both `animations.ts` and `motion.css`.

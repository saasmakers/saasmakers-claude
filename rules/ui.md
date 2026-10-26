---
paths:
  - "**/*.vue"
  - "**/*.css"
---

### Fields

- Uppercase by default (an `uppercase` prop defaulting to `true`, like `FieldSteps`), for labels, chips and toggles.
- Groups of toggles: native `<button>`s, each reachable with Tab. No roving tabindex or arrow-only navigation.
- Don't pass a display or position class (`hidden`, `flex`, `absolute`…) to a component whose root sets its own: which one wins depends on CSS order. Use `v-show` or a wrapper.

### Tokens

Each token lives in `ui/app/tokens/<name>.ts` and `ui/app/assets/styles/<name>.css` (change both); plain CSS uses `var(--<name>-*)`. Raw values are lint errors, except spacing.

- Motion: `duration-fast|base|slow` (150/250/500ms) and the global ease-out. On `<Transition>`, keep `motion-reduce:duration-0` next to `motion-reduce:transition-none` (Vue times enter/leave from it). motion-v: `fadeIn`… or `motionDurations`.
- Stacking: `z-raised|dock|overlay|popover|toast|splash` (`z-0` allowed). In TS: `zIndices`.
- Radius: `rounded-tag|inner|control|card|dialog` (4–16px) for tags, targets inside a control, buttons and fields, cards, modals and sheets. Sides work (`rounded-t-dialog`); `rounded-full|none` stay. Nested radii stay concentric (`inner` inside a `control`).
- Spacing: vertical stacks of fields use `gap-stack` (16px); a row's horizontal `gap-2` stays. `LayoutBottomSheet` already spaces its slot: pass fields as direct children (`<template v-if>`, no `flex flex-col gap-…` wrapper).

---
paths:
  - "**/package.json"
  - "**/pnpm-workspace.yaml"
  - ".changeset/**"
---

### Dependencies

- In any `package.json`, use `"catalog:"` as the version. Versions live in the `pnpm-workspace.yaml` catalog.

### Publishing `@saasmakers/*`

- One changeset per release (batch related changes). Never `npm publish` from `packages/*` — use changesets from `saasmakers-turborepo` root; otherwise `catalog:` stays in the published tarball.
- Default bump in `.changeset/*.md`: `patch` (e.g. `"@saasmakers/ui": patch`). Use `minor` or `major` only when the user explicitly asks.
- Flow: `.changeset/*.md` → `pnpm exec changeset version` → `CI=true pnpm exec changeset publish`.
- Before bumping a consumer catalog, verify the release is installable: `curl -sfL "$(npm view @saasmakers/<pkg>@<version> dist.tarball)" -o /dev/null` must exit 0 (`npm view … version` can list versions not yet downloadable). `npm view … dependencies` must not contain `catalog:`.
- On a 404, retry every ~30s for up to 5 minutes (npm propagation). Never bump or publish a new version because of a temporary 404. If it stays broken (e.g. npm 409 "previously staged"), stop and ask the user.
- Then update the consumer `pnpm-workspace.yaml` catalog and `pnpm install`. On install failure, diagnose first — never manual `npm publish` from `packages/*`.

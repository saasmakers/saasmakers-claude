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
- Publishing is possible: don't tell the user you can't. The devcontainer has no npm login, so copy the host one in for the publish only: `docker cp ~/.npmrc projects-devcontainer-1:/root/.npmrc`, check `npm whoami`, publish, then always `docker exec projects-devcontainer-1 rm -f /root/.npmrc`, even if publishing fails. Never print the file.
- The devcontainer has no git identity and `changeset version` commits by itself: pass the host identity (`git config user.name` / `user.email`) as `-e GIT_AUTHOR_NAME -e GIT_AUTHOR_EMAIL -e GIT_COMMITTER_NAME -e GIT_COMMITTER_EMAIL` to `docker exec`. `.changeset` is a submodule, so its "Pathspec is in submodule" error during `version` is expected.
- Publishing packs the working tree, not the commit. Right before publishing, `git status --short packages/<pkg>` must be empty; otherwise stop and ask the user. Never publish someone else's uncommitted changes.
- `changeset publish` releases every package whose local version is not on npm yet. If another package is versioned but must not be released, publish only the target from the root: `pnpm publish --filter @saasmakers/<pkg> --no-git-checks` (pnpm still resolves `catalog:`).
- Before bumping a consumer catalog, verify the release is installable: `curl -sfL "$(npm view @saasmakers/<pkg>@<version> dist.tarball)" -o /dev/null` must exit 0 (`npm view … version` can list versions not yet downloadable). `npm view … dependencies` must not contain `catalog:`.
- On a 404, retry every ~30s for up to 5 minutes (npm propagation). Never bump or publish a new version because of a temporary 404. If it stays broken (e.g. npm 409 "previously staged"), stop and ask the user.
- Then update the consumer `pnpm-workspace.yaml` catalog and `pnpm install`. On install failure, diagnose first — never manual `npm publish` from `packages/*`.

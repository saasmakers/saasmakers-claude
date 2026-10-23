### Good practices

- Always write comments in English.
- No block comments above functions that restate the name or describe obvious behavior. Comment only non-obvious invariants, edge cases, or intent.
- Avoid using single-letter variable names.
- Use descriptive names that clearly express the intent.
- Use `undefined` internally. Use `null` only when reflecting external sources (DB, JSON, API schemas).
- Prefer `field?: T` over `field: T | null | undefined`. Use `| null` only to mirror external schemas.
- At boundaries, normalize external `null` with `value ?? undefined`.
- Use `camelCase` for all variables, including constants and exported arrays/objects (write `parkings`, not `PARKINGS`). No `SCREAMING_SNAKE_CASE`.
- Exceptions: Zod schemas (PascalCase + `Schema`), types/interfaces (PascalCase).
- Name model instances after the entity (`apexDomain`), not ORM-flavored (`apexDomainRow`).

### Modules & Types

- Prefer ESM `import`/`export` (root `"type": "module"` in [package.json](package.json)).
- Declare shared vendor types in `types/global.d.ts` as `type X = import('…').X`. Reference globally, don't re-import.
- App-local `type` / `interface` live in ambient `*.d.ts` under `types/`:
  - Nuxt web: `apps/web/app/types/` (e.g. `tasks.d.ts`)
  - Adonis API: `apps/api/types/` (e.g. `trophies.d.ts`, `performance.d.ts`)
- Define them once in `declare global { … }` — do not export them from implementation `.ts` / `.vue` files; use them without importing.
- Prefer domain files (`tasks.d.ts`, `habits.d.ts`, `trophies.d.ts`) over dumping everything in `global.d.ts`. Keep `global.d.ts` for cross-cutting types and vendor aliases.
- Exceptions that may stay next to implementation: `typeof`-derived aliases (e.g. `keyof typeof icons`), and Vine `ContractIsInSync` asserts colocated with validators.

### Tooling

- Use `pnpm` at repo root; run tasks with `turbo run <task>` (not npm/yarn).
- Workspace layout: [pnpm-workspace.yaml](pnpm-workspace.yaml) — `apps/*`, `packages/*`, `packages/helpers/*`.
- Package scope: `@saasmakers/*` imports over deep relative paths when a package exists.
- Target one package: `pnpm --filter @saasmakers/<name> <script>`.
- Agent shells may use an older Node than the devcontainer; if `node -v` ≠ root `engines.node`, prepend `export PATH="/usr/local/share/nvm/versions/node/v24.14.1/bin:$PATH"` before `pnpm lint`, `pnpm typecheck`, or `pnpm test`.
- **Cross-repo ops: sequential only.** Run `pnpm install`, `git push`, etc. one turborepo at a time — never in parallel (port conflicts, `ENOMEM`, flaky lockfiles).
- Do not open a browser to verify changes.

### Commits

- **Never commit or push** unless the user explicitly asks.
- Allowed scopes: `scope-enum` rule in `@saasmakers/config` (`commitlint.js` export).

### Publishing `@saasmakers/*`

- **Never publish** unless the user explicitly asks. One changeset per release (batch related changes). Never `npm publish` from `packages/*` — use changesets from `saasmakers-turborepo` root; otherwise `catalog:` stays in the published tarball.
- Default bump in `.changeset/*.md`: `patch` (e.g. `"@saasmakers/ui": patch`). Use `minor` or `major` only when the user explicitly asks.
- Flow: `.changeset/*.md` → `pnpm exec changeset version` → `CI=true pnpm exec changeset publish`.
- Before bumping a consumer catalog: verify the release is installable — `curl -sfL "$(npm view @saasmakers/<pkg>@<version> dist.tarball)" -o /dev/null` must exit 0 (`npm view … version` alone can list versions that are not yet downloadable). Retry 404s every ~30s for up to 5 minutes (npm propagation). Do **not** bump or publish a new version just because of a temporary 404 — wait until that same version’s tarball succeeds. If it stays broken (e.g. npm 409 “previously staged”), stop and ask the user; never blind version bumps. `npm view … dependencies` must not contain `catalog:`.
- Then update consumer `pnpm-workspace.yaml` catalog and `pnpm install`. On install failure, diagnose first — never manual `npm publish` from `packages/*`.

### Zod schemas

- PascalCase + `Schema` suffix: `DomainSchema`, `UserColorModeSchema`
- Types via `z.infer<typeof XSchema>`, never manual duplicates

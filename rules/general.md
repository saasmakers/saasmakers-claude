### Good practices

- Always write comments in English.
- No block comments above functions that restate the name or describe obvious behavior. Comment only non-obvious invariants, edge cases, or intent.
- No single-letter variable names.
- Use `undefined` internally. Use `null` only when reflecting external sources (DB, JSON, API schemas).
- Prefer `field?: T` over `field: T | null | undefined`. Use `| null` only to mirror external schemas.
- At boundaries, normalize external `null` with `value ?? undefined`.
- Use `camelCase` for all variables, including constants and exported arrays/objects (write `parkings`, not `PARKINGS`). No `SCREAMING_SNAKE_CASE`, except Zod schemas (PascalCase + `Schema`) and types/interfaces (PascalCase).
- Name model instances after the entity (`apexDomain`), not ORM-flavored (`apexDomainRow`).

### Types

- Declare shared vendor types in the app's `types/global.d.ts` (e.g. `apps/api/types/global.d.ts`) as `type X = import('…').X`. Reference globally, don't re-import.
- App-local `type` / `interface` live in ambient `*.d.ts` under `types/`:
  - Nuxt web: `apps/web/app/types/` (e.g. `tasks.d.ts`)
  - Adonis API: `apps/api/types/` (e.g. `trophies.d.ts`, `performance.d.ts`)
- Define them once in `declare global { … }` — do not export them from implementation `.ts` / `.vue` files; use them without importing.
- Prefer domain files (`tasks.d.ts`, `habits.d.ts`, `trophies.d.ts`) over dumping everything in `global.d.ts`. Keep `global.d.ts` for cross-cutting types and vendor aliases.
- Exceptions that may stay next to implementation: `typeof`-derived aliases (e.g. `keyof typeof icons`), and Vine `ContractIsInSync` asserts colocated with validators.

### Zod schemas

- Name: PascalCase + `Schema` suffix (`DomainSchema`, `UserColorModeSchema`).
- Types via `z.infer<typeof XSchema>`, never manual duplicates.

### Tooling

- Use `pnpm` (not npm/yarn). Root scripts (`pnpm lint|typecheck|test`) run turbo.
- Workspace layout: `apps/*`, `packages/*`, `packages/helpers/*` (see `pnpm-workspace.yaml`).
- Package scope is the repo's: `@saasmakers/*`, `@companies/*` or `@resilienceclub/*`. Import from the package over deep relative paths when one exists.
- Target one package: `pnpm --filter @<scope>/<name> <script>`.
- The macOS host has no Node: run `pnpm` in the devcontainer with `docker exec -w /workspaces/projects/<repo> projects-devcontainer-1 pnpm <script>`.
- In `saasmakers-turborepo`, `@saasmakers/eslint` is an injected copy (`node_modules/.pnpm/@saasmakers+eslint@file+packages+eslint*`), not a symlink: after changing it, run `pnpm --filter @saasmakers/eslint build` then `pnpm install --frozen-lockfile` before linting, or lint (pre-push included) runs the stale rules.
- **Cross-repo ops: sequential only.** Run `pnpm install`, `git push`, etc. one turborepo at a time — never in parallel (port conflicts, `ENOMEM`, flaky lockfiles).
- Do not open a browser to verify changes unless asked.

### Mobile builds

- "Build iOS" (or Android) means: run `pnpm build:ios` (`build:android`) in the app folder inside the devcontainer (`docker exec -w /workspaces/projects/<repo>/apps/web …`). It generates the static site with `.env.production` (published `@saasmakers/*`, not the local kit) and runs `cap sync`.
- The devcontainer has no CocoaPods, so `cap sync ios` skips `pod install`. On the host, run `pod install` in `apps/web/ios/App` only when `Podfile.lock` and `Pods/Manifest.lock` differ.
- Check that iOS compiles on the host: `xcodebuild -workspace App.xcworkspace -scheme App -configuration Debug -sdk iphonesimulator -destination 'generic/platform=iOS Simulator' -derivedDataPath <scratchpad> build` in `apps/web/ios/App`. Device builds and App Store archives go through Xcode with the user's signing: don't attempt them.

### Git

- **Never commit, push or publish** unless the user explicitly asks.
  - The user reads the diff before every commit. A request to make changes is not a request to commit, even when the previous task ended with one.
  - Do not put commit, push or publish steps in a task plan the user did not ask for. Stop after the checks and ask.
  - Each go-ahead covers only the commit or push it was given for. Commit and push are separate go-aheads.
- Commit inside the devcontainer: the `commit-msg` hook runs commitlint with `pnpm`. It has no git identity, so pass the host one (`git config user.name` / `user.email`): `git -c user.name=… -c user.email=… commit`, or `-e GIT_AUTHOR_NAME -e GIT_AUTHOR_EMAIL -e GIT_COMMITTER_NAME -e GIT_COMMITTER_EMAIL` to `docker exec` for tools that commit by themselves.
- Push from the host: the devcontainer has no SSH access to GitHub. Run the `pre-push` checks (`lefthook.yml`) in the devcontainer first, then `LEFTHOOK=0 git push`.
- Allowed scopes: `scope-enum` rule in `@saasmakers/config` (`commitlint.js` export).
- To publish a package, read `rules/publishing.md` first.

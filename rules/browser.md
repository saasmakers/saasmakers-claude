### Browser testing

- Every project uses the same local test account, `test@claude.ai`, created by a development-only API seeder (password in the seeder). Sign in with it on the local web app only, never on staging or production:
  - The seeder is always `apps/api/database/seeders/00_UserSeeder.ts`.
  - Web apps: `resilienceclub-turborepo` at `http://localhost:6101`, `saasmakers-turborepo` at `http://localhost:3101`.
  - `companies-turborepo`: no login route yet. When one lands, add the same seeder with the same email and password.
- If the account is missing, run `node ace db:seed --files database/seeders/00_UserSeeder.ts` in `apps/api` inside the devcontainer.
- Local `.env` and `.env.example` files use Cloudflare's Turnstile test keys, so the captcha passes on its own. `.env.production` keeps the real keys, and `generate` only loads that file.
- Never solve a captcha. If a Turnstile challenge still blocks the login, stop and ask the user to sign in in the browser pane.

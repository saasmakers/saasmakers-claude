---
paths:
  - "**/i18n/locales/**"
  - "**/resources/locales/**"
  - "**/schemas/locales.ts"
  - "**/seeds/locales.ts"
---

### Adding a locale

The list lives in `@saasmakers/shared` (`LocaleSchema`). `LocaleStringsSchema` and the lint rule `saasmakers/vue-format-i18n` derive from it, so once the code is in the enum, lint and typecheck list every place still missing it. Work through the repos in order: kit, publish, then each app.

#### 1. Kit (`saasmakers-turborepo`)

- `packages/shared/schemas/locales.ts` (enum) and `seeds/locales.ts` (title in its own language, `flagpack:` icon). Keep the seed in the existing order: `id` and `vi` sit at the end, not alphabetical. `CountryNameSchema` derives from the enum.
- Seeds, from the root, after `pnpm --filter @saasmakers/shared build`:
  - `node packages/shared/scripts/sync-country-names.mjs` fills the countries of every locale still missing (i18n-iso-countries).
  - `node packages/shared/scripts/add-locale-emoji-keywords.mjs <code>` fills the 3,701 emojis. Add the locale to its `localeConfig` first. Locales absent from `emojibase-data` fall back to CLDR and need a `regionalIndicator` entry.
- `packages/helpers/apps-helpers/index.ts`: App Store / Play storefront country, plus its test.
- `packages/ui`: the `<i18n>` block of every component (lint lists them), `app/i18n/relativeDate.ts`, and the badges `public/images/layout/LayoutApps/{appstore,googleplay}-<code>.svg`.
  - App Store: the official SVG from `https://toolbox.marketingtools.apple.com/api/v2/badges/download-on-the-app-store/black/<lang>-<region>`.
  - Google Play: only a PNG exists (`https://play.google.com/intl/en_us/badges/static/images/badges/<code>_badge_web_generic.png`, 646×250 with transparent margins). Crop it to rows 29–220 and wrap it as a base64 `<image>` in an SVG (`viewBox="0 0 646 192"`, `height="40"`).
- Demo apps: `apps/web/i18n/locales/<code>.ts`, `apps/api/resources/locales/<code>.json`, the `<i18n>` blocks, and a migration that rewrites the `Users_locale_check` constraint.
- Before checking, rebuild the copies lint and typecheck read: `pnpm --filter @saasmakers/eslint build`, `pnpm install --frozen-lockfile` (eslint is injected), `pnpm --filter @saasmakers/shared-tmp build`. Otherwise they keep rejecting the new code.
- Run the migration on the test database too (`NODE_ENV=test node ace migration:run`): the mail tests cover every locale and fail on the old constraint.
- Publish `shared`, `ui`, `apps-helpers` and `eslint` together (see `publishing.md`).

#### 2. Each app (`resilienceclub-turborepo`, `companies-turborepo`)

- Bump the catalog to the published kit first. Until then, the published `@saasmakers/shared` rejects the new code.
- `apps/web/i18n/locales/<code>.ts` and `apps/api/resources/locales/<code>.json`, translated from `en`.
- The `<i18n>` blocks: run lint and it lists every file.
- Migration rewriting `Users_locale_check`, then `pnpm build:packages` (the app's own `@<scope>/shared` ships a built `dist`).
- Seeds typed with `LocaleStrings` (Resilience: `trophies.ts`, `quotes.ts`).
- Dates: the `dayjs/locale/<code>` import in the dayjs plugin, and `dayMonthOrderLocales` (`date-helpers`) when the locale writes the day first.

#### Translations

- Match the tone the app already uses in its other languages (informal in Resilience).
- Long legal texts (terms, privacy) and quotes: say they were machine-translated and suggest a native review.

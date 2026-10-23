---
paths:
  - "**/package.json"
---

### Dependency version conventions

- When adding dependencies to any `package.json` in this monorepo, always use `\"catalog:\"` as the version instead of a specific version number.
  
- The versions are managed centrally in the `pnpm-workspace.yaml` catalog.

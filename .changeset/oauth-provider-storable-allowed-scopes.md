---
"@better-auth/oauth-provider": minor
---

`allowedScopes` now has one storable contract across adapters:

- Omit it for typed config (or pass `null` at runtime) to allow every requested scope.
- A non-empty array is an allowlist; scopes outside it are removed and an empty intersection is rejected with `invalid_scope`.
- An empty array is rejected on input. Use `disabled: true` to stop issuing tokens for the resource, or `null` through the admin API to remove the restriction.
- Configured resources are validated when the plugin is constructed, and the admin create/update body rejects `[]`.
- Writers now store `[]` instead of `null`, so Prisma on PostgreSQL can persist a resource without scope restrictions.

**Migration:** resources whose stored `allowedScopes` is empty currently refuse every token. This includes Prisma rows where SQL `NULL` is read back as `[]`. After upgrading, those resources allow every requested scope. If you relied on an empty array to block a resource, set `disabled: true` for it before upgrading. Existing rows are not rewritten and no database migration is required. A `resources` entry with `allowedScopes: []` now fails validation, and the `mcp()` resource no longer needs the explicit workaround.

Verified on Prisma/PostgreSQL. CockroachDB and MongoDB were not part of the acceptance run.

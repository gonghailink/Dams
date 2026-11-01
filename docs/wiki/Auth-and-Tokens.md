# Auth and Tokens

The whole app has **one credential type**: opaque `dams_…` bearer tokens.
They are hashed (SHA-256) in D1's `api_tokens` table and never stored raw.

## Scopes

Ranked `read_only < read_write < admin`.

| Scope | Can do |
|---|---|
| `read_only` | Read memory, docs, search, tasks |
| `read_write` | Plus append memory, edit docs, propose plans |
| `admin` | Plus manage tokens, approve/reject plans, reindex |

REST defaults are method-based (GET/HEAD → `read_only`, otherwise
`read_write`). `/api/tokens` requires `admin`. `/mcp` requires `read_only`
because every MCP request is a POST. The mutating MCP tools are only
*registered* for `read_write`/`admin` tokens, so a read-only token's
`tools/list` never shows them.

## Per-token project allow-list

A token can be limited to specific project slugs (`projects` is `string[] | null`,
and `null` means all projects).

- Not allowed for `admin` tokens, which always stay global.
- Tokens are immutable. To change the list, revoke and recreate.
- **A denial is always a 404** (REST) or the same `Unknown project: {slug}`
  error (MCP), never a 403. A leaked, restricted token can't learn which other
  projects exist.
- `canAccessProject(auth, slug)` is the one function every enforcement point
  calls.

## Browser login

`POST /api/auth/login` sets an httpOnly cookie whose value is the raw token. So
revoking a token logs out its browser sessions and its bearer use at the same
time.

## `DAMS_ADMIN_TOKEN`

A Worker secret and the break-glass bootstrap credential. It is compared in
constant time and always resolves to `admin`. It isn't a database row, so
there's nothing to revoke. Create real tokens on the **Tokens** page and then
stop using it.

## What's reachable without a credential

Only `/api/auth/login`, `/api/auth/logout`, and the public `/api/share/*`
routes. See [[Sharing]].

## Share hostname guard

If `SHARE_HOSTNAME` is set, requests to that host may only reach `/mcp`,
`/.well-known/*`, `/share/*`, `/api/share/*` and `/assets/*`. Everything else
404s. This is a safety net in code. Don't rely on dashboard-side access control
alone.

See also: [[MCP-Tools]], [[REST-API]].

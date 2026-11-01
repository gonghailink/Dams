# Local Development

An npm workspaces monorepo. One `npm install` at the root installs both
workspaces.

```bash
cp server/wrangler.toml.example server/wrangler.toml   # git-ignored
# Local dev can't run Vectorize / Workers AI: delete the block between
# "# >>> semantic-search" and "# <<< semantic-search" in server/wrangler.toml
cp server/.dev.vars.example server/.dev.vars           # ANTHROPIC_API_KEY, DAMS_ADMIN_TOKEN, ENVIRONMENT=development
npm install
npm run db:migrate:local
npm run dev            # Worker on :8787
npm run dev:client     # frontend on :5173, proxies /api to :8787
```

Open http://localhost:5173 and log in with the `DAMS_ADMIN_TOKEN` from
`.dev.vars`.

Locally, search is **keyword-only** (FTS5). Everything else works, including
plans and the assistant, which needs a real `ANTHROPIC_API_KEY`. To exercise
the assistant loop without spending tokens, run `wrangler dev` with
`--var ANTHROPIC_BASE_URL:http://localhost:<port> --var ANTHROPIC_API_KEY:fake`
against a scripted stand-in for `/v1/messages`.

## Commands (from the repo root)

| Command | Does |
|---|---|
| `npm run dev` | Worker dev server |
| `npm run dev:client` | Frontend dev server |
| `npm run db:migrate:local` / `:remote` | Apply D1 migrations |
| `npm run typecheck` | Type-check |
| `npm run build` | Build the client into `client/dist` |
| `npm run deploy` | Build the client, then `wrangler deploy` |

`wrangler` commands (`wrangler d1 …`, `wrangler secret put …`) must run from
`server/`.

## Before a PR

Run `npm run typecheck` and `npm run build`. There is no automated test suite
yet.

See [[Contributing]] and [[Deployment]].

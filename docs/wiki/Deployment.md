# Deployment

The full guide is `docs/DEPLOY.md` in the repo. This is the short version.

## First deploy

```bash
cd server
npx wrangler d1 create dams_db && npx wrangler r2 bucket create dmas
npx wrangler secret put ANTHROPIC_API_KEY
npx wrangler secret put DAMS_ADMIN_TOKEN
npx wrangler vectorize create dams-memory --dimensions=1024 --metric=cosine
npx wrangler vectorize create-metadata-index dams-memory --property-name=projectSlug --type=string
cd ..
npm run db:migrate:remote
npm run deploy
```

Then optionally backfill the search index. The nightly cron does it too, or use
the **Reindex now** button (`POST /api/search/reindex` with an admin token).

## `wrangler.toml` is git-ignored

Only `wrangler.toml.example` is tracked, so a fork can't accidentally deploy
onto someone else's D1 database or domain. `server/scripts/render-wrangler-toml.sh`
rebuilds a real one from the template in CI. Keep the
`# >>> semantic-search` / `# <<< semantic-search` markers when editing the
template, because CI strips that block when
`WRANGLER_DISABLE_SEMANTIC_SEARCH=1`.

## Continuous deployment

Cloudflare Workers Builds is supported. The build renders `wrangler.toml` from
the template, and the deploy command applies pending D1 migrations before
shipping the code that needs them. Set `WRANGLER_DISABLE_SEMANTIC_SEARCH=1` to
deploy without a Vectorize index (keyword-only search).

## Configuration

| | Name | Purpose |
|---|---|---|
| Secret | `ANTHROPIC_API_KEY` | Chat, `ask_memory`, the assistant |
| Secret | `DAMS_ADMIN_TOKEN` | Break-glass bootstrap admin credential |
| Binding | `DAMS_DB` (D1), `DAMS_BUCKET` (R2), `ASSETS` | Required |
| Binding | `AI`, `VECTORIZE` | Optional: semantic search |
| Binding | `SEARCH_WORKFLOW`, `ACTIONS_WORKFLOW` | Optional: durable backfill / plan execution |
| Cron | `*/15 * * * *`, `0 3 * * *` | Retry sweep, plan expiry, task sync; nightly search reconcile |
| Var | `LLM_DAILY_TOKEN_CAP` (500k), `LLM_TASK_TOKEN_CAP` (100k) | Anthropic budgets |
| Var | `WORKERS_AI_DAILY_TOKEN_CAP` (5M) | Embedding budget |
| Var | `SHARE_HOSTNAME`, `ENVIRONMENT` | Share-link host; cookie `Secure` flag |

If you change `RECONCILE_CRON` in `index.ts`, keep the cron in `wrangler.toml`
in sync.

See also: [[Local-Development]], [[Architecture]].

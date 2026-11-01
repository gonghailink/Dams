# Architecture

```
   MCP clients ──┐                       ┌── D1  registry · tokens · shares · search index
   (Claude,      │   ┌───────────────┐   │       plans · tasks · audit log
    ChatGPT,     ├──▶│ Cloudflare    │───┼── R2  {slug}/memory.jsonl · {slug}/docs/*.md   ← source of truth
    Claude Code) │   │ Worker (Hono) │   ├── Vectorize + Workers AI   (derived; rebuildable)
   Web UI ───────┘   │  /api  /mcp   │   ├── Workflows  search backfill · plan execution
   (React, served    └───────────────┘   └── Anthropic API  chat · assistant
    as static assets)
```

## Repo layout

An npm workspaces monorepo with two workspaces:

- `server/`: the Cloudflare Worker (Hono, D1, R2, MCP, token auth).
- `client/`: the Vite/React frontend, built into `client/dist` and served by
  the Worker as static assets.

```
server/
  wrangler.toml.example      config template (wrangler.toml is git-ignored)
  scripts/                   render-wrangler-toml.sh builds wrangler.toml for CI
  migrations/                hand-written D1 migrations, applied in order
  src/
    index.ts                 routes, auth mounts, cron, Workflow exports
    lib/                     bindings, budget, llm, untrusted
    db/schema.ts             Drizzle table definitions
    modules/                 projects · docs · chat · tokens · shares · mcp
                             · search · actions · assistant
client/                      React + Vite + Tailwind v4 + shadcn/ui
docs/                        deploy guide, design notes, client wiring
```

## Backend module pattern

Every backend module has exactly four files:

| File | Role |
|---|---|
| `schema.ts` | Zod schemas and types |
| `service.ts` | Business logic, D1/R2 access |
| `routes.ts` | Hono handlers |
| `index.ts` | Barrel export |

The shared `Bindings`/`Env` type lives in `server/src/lib/bindings.ts`.
Modules import it and never declare their own. Every route boundary is
validated with Zod.

## Why it's shaped this way

- **One Worker.** REST, MCP and the static frontend ship as a single
  deployment on one domain.
- **R2 is the source of truth.** If local disk were canonical, "take memory
  anywhere" would bottleneck on one machine.
- **D1 is an index, not a data store.** Anything in D1 must be reconstructable
  from R2. The search tables and Vectorize are *derived* and rebuilt by
  `POST /api/search/reindex`.
- **MCP is a thin wrapper.** REST is the real implementation, and MCP tools
  call the same services. `/mcp` is stateless (no session handshake), so no
  Durable Object.
- **Retrieval, not prompt-stuffing.** Chat gives the model search tools it runs
  itself, and everything retrieved is treated as untrusted data.

## Hard preferences

npm only (no yarn/pnpm), no Express or Next.js, no `any` in TypeScript, Zod at
every route boundary, and the 4-file module pattern.

## Free-plan design

10 ms CPU per request or Workflow step, Vectorize's 5M stored dimensions
(about 4,900 entries at 1024-d), 10k Workers AI neurons/day, and 3-day Workflow
state (so task state lives in D1). Optional bindings (`AI`, `VECTORIZE`,
Workflows) degrade gracefully when absent and can never make a memory write
fail.

See also: [[Data-Model]], [[Auth-and-Tokens]], [[Deployment]].

# Contributing

See `CONTRIBUTING.md` and `CLAUDE.md` in the repo for the authoritative rules.
The essentials:

- **npm only.** No yarn or pnpm.
- **No `any`.** Use `unknown` and narrow it.
- **Zod at every route boundary.** No unvalidated `c.req.json()`.
- **4-file backend modules:** `schema.ts`, `service.ts`, `routes.ts`,
  `index.ts`.
- **Append-only memory.** Never rewrite `memory.jsonl`.
- **R2 is the truth, D1 is an index.**
- **Migrations are hand-written** in `server/migrations/`, and
  `drizzle-kit generate` output should be discarded.
- **Adding an assistant action** means extending the discriminated union in
  `actions/schema.ts`, `validate.ts`, `execute.ts` and the preview in
  `PlansPage.tsx` together.
- **Adding or changing an MCP tool** means updating `GuidePage.tsx` by hand.

## Before opening a PR

```bash
npm run typecheck
npm run build
```

There's no automated test suite yet, and adding one is on the roadmap.

See [[Local-Development]] and [[Architecture]].

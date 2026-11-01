# Frontend

`client/` is React + Vite on **Tailwind v4** with shadcn/ui (`radix-nova`
preset), built into `client/dist` and served by the Worker as static assets.

## Rules

- No `tailwind.config.js`. Theming lives in `client/src/index.css` via
  `@theme inline` and CSS custom properties. Styling uses `@tailwindcss/vite`,
  not postcss.
- Add primitives with `npx shadcn@latest add <component>` from inside
  `client/`. Don't hand-write look-alikes.
- Import with the `@/*` alias (→ `client/src/*`).

## Routing (react-router)

The URL is the source of truth, so a refresh or pasted link lands on the same
page.

| Route | Page |
|---|---|
| `/` | Overview |
| `/new` | Create project |
| `/projects/:slug` | Project (tab in `?view=list&panel=docs`; graph and chat are defaults) |
| `/guide`, `/assistant`, `/plans`, `/tokens` | Guide, Assistant, Plans, Tokens |
| `/share/:token` | Public read-only view (outside the auth gate) |

Route paths must not collide with the Worker's prefixes (`/api`, `/mcp`,
`/.well-known`). Project slugs live under `/projects/`, so one can never shadow
a top-level page. Add a breadcrumb in `App` whenever you add a route.

## Key components

- `ProjectView`: Graph / List tabs, Chat / Docs / Prompts, Share and Export.
- `MemoryGraph`: assumes entities have a `name` and relations point at names.
  See [[Data-Model]].
- `ChatPanel`: source-agnostic through its `ask` prop, so the authenticated
  view and the share view reuse it.
- `ShareView`: fetches share metadata first so disabled tabs are never shown.
- `PlansPage`: previews each action, and mirrors the backend's confirmation
  rules.
- `GuidePage` and `lib/prompts.ts`: in-app guide and prompt templates, kept in
  sync by hand with the MCP tool list.

Visual system notes are in the repo's `docs/DESIGN.md`. See also [[Sharing]]
and [[Contributing]].

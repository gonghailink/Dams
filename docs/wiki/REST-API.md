# REST API

All `/api/*` routes need a bearer token (or the session cookie the web UI
sets), except `/api/auth/login|logout` and the public `/api/share/*`. GET
defaults to `read_only` and other methods to `read_write`.

| Area | Routes |
|---|---|
| Projects | `GET/POST /api/projects`, `PATCH /api/projects/:slug`, `GET …/:slug/memory`, `GET …/:slug/memory/raw`, `POST …/:slug/memory` |
| Docs | `GET/POST /api/projects/:slug/docs`, `GET/PUT/DELETE …/docs/:filename` |
| Chat | `POST /api/projects/:slug/chat` (SSE) |
| Share links | `GET/POST /api/projects/:slug/share`, `PATCH/DELETE …/share/:token`; public: `GET /api/share/:token[/memory\|/docs…]`, `POST /api/share/:token/chat` |
| Search | `GET /api/search?q=&projects=&topK=`, `GET /api/search/status`, `POST /api/search/reindex` (admin; optional `{"project":"<slug>"}`) |
| Plans | `GET/POST /api/plans`, `GET /api/plans/:id`, `POST …/:id/approve\|reject\|resume` (admin) |
| Assistant | `POST /api/assistant/chat` (SSE), `GET …/conversations[/:id]`, `GET …/tasks[/:id]`, `POST …/tasks/:id/cancel\|resume` (admin) |
| Tokens | `GET/POST /api/tokens`, `DELETE /api/tokens/:id` (admin) |
| Auth | `POST /api/auth/login`, `POST /api/auth/logout`, `GET /api/auth/me` |

## Conventions

- Every request body is validated with Zod at the route boundary.
- A project the token can't access returns **404**, never 403.
- `GET /api/projects` filters to allowed slugs. `POST /api/projects` returns
  403 for a project-restricted token.
- Plan list/get are hidden from project-restricted tokens.
- Unknown, expired and gated-off share tokens all return the same generic 404.

## Example

```bash
curl -H "Authorization: Bearer dams_your_token" \
  https://memory.example.com/api/projects
```

See [[Auth-and-Tokens]] and [[MCP-Tools]].

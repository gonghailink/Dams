# MCP Tools

The server exposes a stateless MCP endpoint at `/mcp` (no session handshake).
Tools are thin wrappers over the same services the REST API uses.

| Tool | Min scope | Purpose |
|---|---|---|
| `list_projects` | read_only | Projects you can access |
| `read_memory` | read_only | A project's full log (all revisions) |
| `search_memory` | read_only | Hybrid search with citations across opted-in projects |
| `ask_memory` | read_only | Ask about one project; returns an answer and sources |
| `list_tasks`, `get_task` | read_only | What the assistant is doing |
| `append_memory` | read_write | Append an entity, relation or observation |
| `update_entity` | read_write | Append a new entity revision (e.g. set its category) |
| `append_doc`, `update_doc`, `delete_doc` | read_write | Project markdown docs |
| `propose_actions` | read_write | File a plan. It runs nothing until an admin approves it |

## Notes

- Any tool that takes a `slug` first checks the token's project allow-list. A
  disallowed slug returns the same `Unknown project` error as a missing one.
- `propose_actions` can't approve its own plan, because approval needs an
  `admin` token.
- `update_entity` fails if the entity doesn't exist yet, and `append_memory`
  creates it.
- The in-app **Guide** page lists the same tools. It's kept in sync with
  `server/src/modules/mcp/service.ts` by hand.

See [[Connecting-AI-Clients]] for setup and [[Auth-and-Tokens]] for scopes.

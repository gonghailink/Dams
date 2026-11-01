# Data Model

## Where things live

| Store | Holds | Source of truth? |
|---|---|---|
| **R2** `{slug}/memory.jsonl` | A project's memory log | **Yes** |
| **R2** `{slug}/docs/*.md` | Project markdown docs | **Yes** |
| **D1** `projects` | slug, title, summary, tags, r2_key, counts, flags | Index only |
| **D1** tokens, shares | Credentials (hashed) and share links | Operational state |
| **D1** `entry_fts`, Vectorize | Search indexes | Derived, rebuildable |
| **D1** plans, tasks, audit log | Assistant operational state | Operational state |

D1 should never hold memory content that isn't reconstructable from R2.

## `memory.jsonl` is append-only

One JSON entry per line. **Nothing ever rewrites existing lines.** Two devices
that append can always be merged as a set union sorted by id (ulid per entry),
which is what the planned local sync CLI depends on.

"Editing" means appending a new entry:

- Entities: append the entity again under the same `name` (last-write-wins).
- Moves: copy-and-mark.
- Tags: annotation entries.
- Archive: a flag on the project, not a delete.

## Entry types

| `type` | Content shape |
|---|---|
| `entity` | `{ name, category? }`. `category` is one of ten values (`entityCategorySchema`). Readers default to `"other"` when absent. |
| `observation` | `{ text, entity? }`. `text` is the note. `entity` optionally names which entity it's about, and absent means a general project note. |
| `relation` | `{ source, target, label? }`. `source` and `target` match entity names. |

These shapes are documented conventions rather than Zod-enforced, so older
entries written before a convention existed are still valid. The graph in
`client/src/components/MemoryGraph.tsx` relies on entities having a `name` and
relations pointing at those names. If you change entry shapes, update it too.

## Entities are last-write-wins

The raw file and `read_memory` return **every** revision. Any reader building a
"current" view (graph, entries table, `update_entity`) dedupes by
`content.name` and keeps the last occurrence. The canonical implementation is
`currentEntities()`, duplicated on purpose in
`server/src/modules/projects/service.ts` and `client/src/lib/memory.ts`. Keep
both in sync.

Relations and observations are never deduped.

`update_entity` (MCP) appends a new revision that merges the given fields onto
the entity's current content. It fails clearly if the entity doesn't exist, and
creating one is `append_memory`'s job.

## Migrations

D1 migrations are hand-written in `server/migrations/` in the
`CREATE TABLE IF NOT EXISTS` style and applied in order. `drizzle-kit generate`
doesn't track prior migrations here, so discard what it generates and write the
incremental migration by hand.

See also: [[Architecture]], [[Search]].

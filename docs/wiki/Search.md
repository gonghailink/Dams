# Search

Hybrid search lives in `server/src/modules/search`.

- **Semantic:** Workers AI `bge-m3` (1024-d, multilingual including Arabic) →
  Vectorize.
- **Keyword:** D1 FTS5 (`entry_fts`).
- **Fusion:** reciprocal rank fusion in code.

Exposed as the `search_memory` MCP tool and `GET /api/search`. Per-project chat
and `ask_memory` use it for retrieval.

## Derived, not authoritative

Both indexes are rebuildable from R2 via `POST /api/search/reindex` (or the
**Reindex now** button for admins). This is the one place D1 holds copied entry
text, and it's allowed because it's reconstructable.

Index keys are stable hashes. Entities key on project + name, so a revision
overwrites, and everything else keys on project + entry id. Re-indexing is
idempotent.

## Opt-in per project

`include_in_global_search` (default **off**) is the privacy flag, and the index
**follows the flag**:

- Turn it on and the project is indexed (via the backfill Workflow, with an
  inline fallback).
- Turn it off, or archive the project, and it's removed from the index.
- A project named explicitly in a query bypasses the flag but never the token's
  allow-list.
- A nightly cron (`0 3 * * *`) reconciles any drift.

## Never blocks a write

Indexing happens after the R2 write and never throws. Every binding (`AI`,
`VECTORIZE`, `SEARCH_WORKFLOW`) is optional. A missing one degrades to
keyword-only, not to an error. Locally, search is keyword-only.

## Limits

Vectorize's free tier holds about 4,900 entries at 1024-d. Beyond that, new
entries stay keyword-searchable. See the limits table in the repo's
`docs/DEPLOY.md`.

See also: [[Data-Model]], [[Assistant-and-Actions]].

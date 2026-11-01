# DAMS — Distributed AI Memory System

A personal, cross-provider memory server. One Cloudflare Worker that lets any
MCP-capable AI client (Claude, ChatGPT, Gemini CLI, Claude Code) read and write
structured memory for your projects, plus a web UI, hybrid semantic search
across all your projects, and a **global assistant** that answers with
citations and proposes organising changes that only happen after **you**
approve them.

It runs entirely on Cloudflare's **Free plan**: D1, R2, Vectorize, Workers AI,
Workflows, and the Anthropic API for chat.

## Start here

| I want to… | Read |
|---|---|
| Understand how it fits together | [[Architecture]] |
| Know how memory is stored and why it's append-only | [[Data-Model]] |
| Run it on my machine | [[Local-Development]] |
| Deploy it to Cloudflare | [[Deployment]] |
| Connect Claude / ChatGPT / Gemini CLI | [[Connecting-AI-Clients]] |
| See every MCP tool | [[MCP-Tools]] |
| Call the REST API | [[REST-API]] |
| Understand tokens, scopes and project access | [[Auth-and-Tokens]] |
| Share a project read-only | [[Sharing]] |
| Understand search | [[Search]] |
| Understand the assistant and approvals | [[Assistant-and-Actions]] |
| Work on the web UI | [[Frontend]] |
| Contribute | [[Contributing]] |

## Features at a glance

- **Structured project memory:** entities, relations and observations,
  append-only, readable and writable over MCP or REST.
- **Web UI:** overview, memory graph, entries list, docs, per-project chat,
  prompt templates, Guide, Tokens, Plans and Assistant pages.
- **Hybrid search:** Workers AI `bge-m3` + Vectorize and D1 FTS5, fused with
  reciprocal rank fusion. Opt-in per project.
- **Global assistant:** cross-project chat with validated citations and
  persistent, cancellable, resumable tasks.
- **Actions with approval:** typed plans that run only after an admin approves.
- **Token auth:** `read_only` / `read_write` / `admin`, optionally limited to
  specific projects.
- **Share links:** per-project, unguessable, read-only, optional expiry.
- **Budget guards:** daily and per-task token caps.

## Status

Built: memory + MCP + token auth + sharing, hybrid search, approved actions,
the global assistant with tasks.

Still ahead: the local sync CLI, an undo executor, docs in search, per-action
approval, automated tests.

The canonical, always-current conventions live in the repo's
[`CLAUDE.md`](https://github.com/mahmoud-bebars/distributed-ai-memory-system/blob/main/CLAUDE.md);
this wiki is the friendlier tour.

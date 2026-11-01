# Sharing

A project can have any number of read-only **share links**. Open **Share** from
a project's actions menu to create, edit and revoke them.

Each link has:

- a `label`
- `allowChat` and `allowDocs` toggles
- an optional expiry (`expiresAt`, null means never)

## What a link exposes

- The **Graph and Entries** (project memory) are always included. Sharing at
  all means sharing at least that.
- **Docs** and **Chat** are opt-in per link, and enforced server-side on the
  public routes, not just hidden in the UI.
- Share chat has no mutating tools, and a docs-off link also keeps doc content
  out of chat answers.
- There's no create-project form, no Export and no doc editing, whatever the
  link settings.

## Public routes

`GET /api/share/:token` (metadata), `/memory`, `/docs…`, and
`POST /api/share/:token/chat`. The token *is* the auth.

An unknown, expired or gated-off token always returns the same generic 404, so
a guess can't learn anything from the response.

## Custom share hostname

Set `SHARE_HOSTNAME` to build links against a separate domain. This is useful
if your main domain sits behind an access gate that recipients can't pass. Any
edge-level access control must bypass auth for `/mcp`, `/.well-known/*`,
`/share/*` and `/api/share/*` on that hostname. An in-code guard also limits
what that hostname can reach. See [[Auth-and-Tokens]].

The `/share/:token` page is a client-side route. It works on a cold load
because `wrangler.toml` sets `not_found_handling = "single-page-application"`.
Don't remove that.

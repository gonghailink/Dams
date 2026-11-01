# Connecting AI Clients

## 1. Create a token

Log in to the web UI with an `admin` session, open **Tokens**, and create a
token. Pick the narrowest scope that works (`read_only` for browsing,
`read_write` for an agent that records memory). Optionally restrict it to
specific projects. The raw `dams_…` value is shown once.

## 2. Register the server

Claude Code:

```bash
claude mcp add --transport http dams https://memory.example.com/mcp \
  --header "Authorization: Bearer dams_your_token" --scope user
```

Any other MCP-capable client (ChatGPT, Gemini CLI, …) needs the same two
things: the `/mcp` URL and an `Authorization: Bearer dams_…` header.

## 3. Use it

Ask the client to list your projects, read or search memory, or append what it
learned. The seed and sync prompt templates in each project's **Prompts** tab
are a good starting point.

## If something goes wrong

- 401: token missing, revoked, or typed wrong.
- "Unknown project": the slug is wrong, **or** the token's allow-list excludes
  it. These look identical on purpose.
- Missing write tools: the token is `read_only`.

See [[MCP-Tools]] and [[Auth-and-Tokens]]. The repo's `docs/WIRE_CLAUDE_CODE.md`
has the longer walkthrough.

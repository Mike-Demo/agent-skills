---
name: indeed
description: "Indeed MCP skill — BLOCKED as of 2026-10-01: Indeed's MCP server is allowlisted to Claude's own connector clients. Re-check the status before use; do not use until the block is lifted."
---

# Indeed MCP skill

## Status: BLOCKED (last checked 2026-10-01 — re-verify before relying on this)

Indeed's MCP server (`https://mcp.indeed.com/claude/mcp`) is **allowlisted to
Claude's own connector clients**. We completed a full valid OAuth 2.0 +
PKCE flow (dynamic client registration at `secure.indeed.com`, user
authorization, token exchange, RFC 8707 resource-bound refresh so the token
audience includes the MCP endpoint) and the server still answers:

```json
{"error": "invalid_client", "error_description": "Client not allowed"}
```

This matches the docs: "available only for Claude Connector". There is no
generic `/mcp` endpoint (404) and no public allowlist process.

## What exists here

- `bin/indeed-mcp` — CLI (list-tools, search, details, resume, company).
  Works end-to-end *except* the server rejects our client_id.
- Valid Indeed OAuth tokens for the registered client (`0ef01059…`), all four
  job_seeker scopes, held in local credential storage (mode 600) — never
  committed to the repo. The CLI auto-refreshes.

## How to retry

If Indeed opens the beta beyond Claude, just run
`bin/indeed-mcp list-tools` — the CLI refreshes the token itself. If the
refresh token has expired by then, redo the Safari PKCE loop: open the
authorization URL in Safari, approve, copy the `?code=` from the redirect,
and exchange it for tokens (same dynamic-client + PKCE flow as the first
setup — the CLI holds the client registration).

## For the user

As of the last check, the only working path is inside Claude: claude.ai →
Search & Tools → Add connectors → Indeed → sign in. Re-check before
recommending it.

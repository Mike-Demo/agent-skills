# Indeed MCP skill

## Status: BLOCKED (2026-10-01)

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
- `.indeed-tokens.json` (mode 600) — valid Indeed OAuth tokens for our
  registered client (`0ef01059…`), all four job_seeker scopes, auto-refresh
  in the CLI.

## How to retry

If Indeed opens the beta beyond Claude, just run
`bin/indeed-mcp list-tools` — the CLI refreshes the token itself. If the
refresh token has expired by then, redo the Safari PKCE loop (see memory
2026-09-30 for the steps).

## For the user

The only working path today is inside Claude: claude.ai → Search & Tools →
Add connectors → Indeed → sign in.

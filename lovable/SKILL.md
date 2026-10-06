---
name: "lovable"
description: "Use Lovable when the user asks about their Lovable projects, deployments, or publishing."
---

# Lovable

## Purpose
Use Lovable with the user-connected `custom.lovable` credential (OAuth to the
user's Lovable account). This skill lets you:
- list workspaces and projects
- inspect a project (preview URLs, publish status, diffs, file tree)
- send prompts to Lovable's agent to iterate on a project
- deploy/publish a project to its live production URL

## CLI
`bin/lovable-mcp` — `lovable-mcp tools/list`, or
`lovable-mcp tools/call <tool> '<json-args>'`.
Examples:
- `lovable-mcp tools/list` (discover available tools)
- `lovable-mcp tools/call list_projects`
- `lovable-mcp tools/call deploy_project '{"project_id": "..."}'`

## Operating Rules
1. Deploying/publishing changes a live site — confirm with the user before
   any deploy, and read back the result to verify.
2. Read-only checks (project list, status, diffs) need no confirmation.
3. Never invent project state: report only what the tools return.
4. Verify the project ID with `list_projects` before acting — never reuse an
   ID from memory.

## Tooling
Add service-specific CLIs under `~/workspace/skills/lovable/bin/`.

CLIs must use authd or shared connector helpers for authenticated requests. They must not read OAuth client credentials, browser callback payloads, refresh tokens, access tokens, or Muse auth files directly.

## Auth
The credential is stored as `custom.lovable` via OAuth. Never ask the user to
paste a raw token in chat, set a secret environment variable, pass a secret
flag, or write an auth file.

A 401 or 403 is a question about the request before it is a question about
the credential. Check that the credential was attached at all: a request
built without the helpers named under Tooling carries nothing, and that
looks exactly like a wrong or under-scoped token. Only once a request that
did carry the credential is still rejected, reconnect the `custom.lovable`
connector. Restrict authenticated requests to: mcp.lovable.dev.
Do not print, log, or persist raw credentials.

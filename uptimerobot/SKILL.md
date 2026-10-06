---
name: "uptimerobot"
description: "Use UptimeRobot when the user asks about site uptime, monitors, incidents, or status pages."
---

# UptimeRobot

## Purpose
Use UptimeRobot with the user-connected `custom.uptimerobot` credential (Main API Key).

Demo has a paid UptimeRobot plan. This skill lets you:
- list monitors and filter by status (up / down / paused)
- get monitor details, uptime stats, and response times
- create, pause, resume, and update monitors
- manage monitor groups, maintenance windows, incidents, and status pages
  (paid-plan features)

## CLI
`bin/uptimerobot-mcp` — `uptimerobot-mcp tools/list`, or
`uptimerobot-mcp tools/call <tool> '<json-args>'`.
Examples:
- `uptimerobot-mcp tools/list` (discover available tools)
- `uptimerobot-mcp tools/call getMonitors`
- `uptimerobot-mcp tools/call getMonitors '{"statuses": [9]}'` (down monitors)

## Operating Rules
1. Creating, pausing, deleting, or changing a monitor affects real
   monitoring — confirm with the user before any write action, and read
   back the result to verify.
2. Read-only checks (status, uptime, response times) need no confirmation.
3. Never invent monitor state: report only what the tools return.

## Tooling
Add service-specific CLIs under `~/workspace/skills/uptimerobot/bin/`.

CLIs must use authd or shared connector helpers for authenticated requests. They must not read OAuth client credentials, browser callback payloads, refresh tokens, access tokens, or Muse auth files directly.

## Auth
The credential is already stored; nothing here collects one. Never ask the user to paste a raw key in chat, set a secret environment variable, pass a secret flag, or write an auth file.

A 401 or 403 is a question about the request before it is a question about the key. Check that the credential was attached at all: a request built without the helpers named under Tooling carries nothing, and that looks exactly like a wrong or under-scoped token. Only once a request that did carry the credential is still rejected, call `credentials.request_api_access` with `reconnect` to replace it. The connector is stored as `custom.uptimerobot`.

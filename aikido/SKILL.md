---
name: "aikido"
description: "Use Aikido security scanning when the user asks to scan code for vulnerabilities or secrets, or to triage Aikido security issues."
---

# Aikido

## Purpose
Aikido's MCP server (`@aikidosec/mcp`, v1.0.25) exposes:
- local SAST / secrets / IaC scans of files on disk (`aikido_scan_paths`)
- scans of inline file content (`aikido_full_scan`)
- open-source dependency scans of manifests/lockfiles (`aikido_oss_dependency_scan`)
- the Aikido issue feed, with triage actions (`aikido_issues_list`,
  `aikido_ignore_issue`, `aikido_unignore_issue`, `aikido_snooze_issue`,
  `aikido_unsnooze_issue`)
- workspace listing (`aikido_workspaces_list`)

This is the same Aikido whose automated dependency PRs appear on the user's
GitHub repos.

## CLI
`bin/aikido-mcp` — `aikido-mcp tools/list`, or
`aikido-mcp tools/call <tool> '<json-args>'`.
The CLI speaks MCP over stdio to `npx -y @aikidosec/mcp` and prints the
JSON-RPC result object. Verified working 2026-09-26 (tools/list returns all
10 tools).

## Auth
The server requires `AIKIDO_API_KEY` in its environment. There is no OS
keychain on this VM (keytar ships no usable prebuild for it), and the Secure
Vault cannot inject secrets into local subprocess environments — its
credential surrogates only attach to HTTPS requests. There is therefore no
persistent, vault-backed way to authenticate this server.

The only supported path is transient, and only when the user explicitly
chooses it: they generate a Personal Access Token at Aikido → Settings →
Integrations → AI Code Generators → Aikido MCP Server
(`https://app.aikido.dev/settings/integrations/ide/mcp`; region hosts
`app.us.aikido.dev`, `app.me.aikido.dev`, `app.au.aikido.dev`), then supply it
for one session. Pass it via the `env` of the exec call that runs the CLI.
Never write it to a file, never log it, never repeat it in chat, never save
it to memory. It must be re-supplied every session.

## Operating Rules
1. Triage actions on the Aikido feed (ignore / unignore / snooze /
   unsnooze) change the user's real Aikido workspace — confirm with the user
   before any of them, and read back the result.
2. Read-only scans need no confirmation.
3. Never invent findings: report only what the tools return.
4. Never ask the user to paste the key as a first resort; explain the
   transient-only limitation before offering it.

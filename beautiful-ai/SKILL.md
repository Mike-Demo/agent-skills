---
name: "beautiful-ai"
description: "Build Beautiful.ai presentations: list themes/folders/decks, create decks from an outline or template, export to PDF/PPTX. Use when Demo asks to build or export a Beautiful.ai presentation."
---

# Beautiful.ai

## Purpose
Demo's Beautiful.ai account, via the `openai-mcp` MCP server. Use when the
user asks for a slide deck, presentation, or to work with their Beautiful.ai
presentations, themes, or templates.

## CLI
`bin/beautiful-mcp` — `beautiful-mcp tools/list`, or
`beautiful-mcp tools/call <tool> '<json-args>'`.
Examples:
- `beautiful-mcp tools/call list_presentations`
- `beautiful-mcp tools/call get_themes`
- `beautiful-mcp tools/call review_presentation_outline '{"title": "...", "slides": [...]}'`

Key tools: `get_themes`, `get_folders`, `list_presentations`,
`list_presentation_templates`, `get_presentation`,
`get_presentation_template_variables`, `review_presentation_outline`,
`create_presentation`, `create_presentation_from_template`,
`export_presentation` (PDF/PPTX, returns a temporary signed download URL).

## Deck workflow
1. Draft the outline (title + slides, each with title, summary, slide type).
2. `review_presentation_outline` with the outline — it returns a preview and
   a review approval token tied to that exact outline.
3. `create_presentation` with the same outline plus the approval token.
4. Optionally `export_presentation` for a PDF/PPTX download link.

## Auth
OAuth via Beautiful.ai (dynamic client, `openid bai` scopes), connected
2026-09-25 with the user's sign-in. Tokens live in
`~/workspace/.openai-mcp/oauth.json`; the CLI refreshes the access token
automatically. Never print or paste the tokens. If calls start failing with
auth errors, the connection needs re-doing — ask the user before starting
any re-auth flow.

## Operating Rules
1. Creating a presentation writes to the user's Beautiful.ai account —
   confirm the outline with the user before creating.
2. `export_presentation` URLs are temporary signed links; download promptly
   if the user wants a file kept.
3. Restrict requests to `openai-mcp-696419881849.us-central1.run.app`.

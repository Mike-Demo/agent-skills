---
name: "adobe_firefly"
description: "Generate or edit images through Adobe Firefly's web app (firefly.adobe.com) via the live browser, using the user's paid Adobe account. Default for explicit image-creation or image-editing requests: text-to-image, app icons, splash screens, social cards, style concepts, and Generative Fill/Expand edits. Not for image search, image analysis, video, or audio."
---

# Adobe Firefly Image Generation

## Purpose
Create or edit images through Adobe Firefly's web app, driven via the live browser on the user's paid Adobe account. This is the default path when the user explicitly asks for a new or edited image.

## Trigger
Use this skill when the request is to **produce or materially alter an image**, e.g.:
- "generate an image of…", "create a logo/icon/splash screen/social card", "make a hero image", "mock up a style concept"
- "edit this image…", "remove the background", "expand the canvas", "fill in…"

Do NOT trigger for:
- Finding, searching, or describing/analyzing an existing image
- Writing or improving a prompt for another tool
- Charts, diagrams, or images embedded in documents/spreadsheets/decks
- Video, audio, voiceover, or music requests
- Questions *about* Firefly (pricing, how-to) with no generation ask

## Scope
Two jobs, each with its own trigger:
1. **Text-to-image** — new image from a prompt (the default job).
2. **Edit existing image** — Generative Fill / Generative Expand on a user-supplied source image, with an explicit edit instruction. Requires the source image file and (for Fill) the area/mask intent; never invent a source image.

Out of scope: Generate Video, audio/music, Text Effects, Boards/moodboards. Say so and stop if asked.

## Auth / Preflight
- Requires an **interactive browser** and a **user-completed Adobe sign-in**. Never type, paste, or request passwords, OTP codes, or session cookies in chat.
- Before generating, verify login state in the live browser: signed in = account avatar + generative-credits counter visible (top-right). The settings panel rendering alone does NOT prove sign-in.
- If logged out or the session expired mid-task (bounced to a sign-in page): **pause and hand control to the user** to sign in manually, then re-verify. Do not drive login, MFA, consent screens, or terms acceptance.
- Never click "Buy more"/"Buy now", purchase credits, or dismiss content-policy notices on the user's behalf. If a credits/paywall/policy notice appears, report it verbatim and stop.
- Never switch Adobe profiles/orgs without explicit direction.

## Workflow
1. Confirm the brief: final prompt text, job (new vs. edit + source file), model family if the user cares (default: Adobe Firefly model), aspect ratio, how many results to keep, and the destination folder.
2. Refine the prompt (offer, don't lecture): concrete subject + setting + lighting + mood + style. Adobe guidance: at least 3 words; descriptive prompts outperform single-word ones.
3. Open the live browser at `https://firefly.adobe.com/generate/image` (the image view; labels vary by UI version/locale — "Text to Image" or "Image → Generate image"; match controls by role/position, not exact strings).
4. Verify sign-in per Auth / Preflight. Pick the model; **re-read the settings panel after any model change** — available controls depend on the model (see references).
5. Enter the prompt, set aspect ratio and model-appropriate options, then read the live credit readout ("Uses N credits" vs. "Unlimited access") before clicking Generate. Never assume cost.
6. Wait for results. **Result count is model-dependent** — Adobe Firefly models typically return a grid of variations; partner models typically return one. Observe the live result; never wait for or promise a fixed count.
7. Select per the Selection rule below. Download via Firefly's own Download controls (per-tile icon or header Download / Download all); click once. Save to the run's working directory.
8. Verify the file: exists, nonzero size, decodes as an image (MIME/dimensions sane). On transient failure: **retry once**, then report. Never hand back session-bound asset URLs or unverified files.
9. Report only values actually observed: prompt used, model, aspect ratio, options set, which result(s) kept, file path(s).

See `references/firefly-workflow.md` for the detailed browser brief, model/controls map, and troubleshooting.

## Output Contract
- Saved image file(s) in the working directory, verified as real image bytes
- The exact prompt, model, aspect ratio, and options observed in the session
- Which result(s) were kept and why (when selection was delegated)
- Any notices encountered (credits, policy, sign-in) reported verbatim

## Selection rule
- If the user delegated the pick: keep the strongest result that matches the brief and complies with policy.
- Otherwise: download all candidates (or the user's shortlist) and let the user choose — don't silently discard variations.

## Operating Rules
1. This skill is the default for explicit image-creation/editing requests; it does not trigger for search, analysis, documents, video, or audio.
2. One transient retry max per operation. Stop conditions (report verbatim, no workarounds): credit-exhausted, paywall, content-policy block, auth failure, service error.
3. Report only observed values — never invent model names, settings, costs, or output counts.
4. Adobe Firefly models are trained on licensed Adobe Stock + public-domain content and Adobe offers IP indemnification (check the plan's terms); partner models are NOT covered — flag the tradeoff when a partner model is chosen.
5. Firefly typically attaches Content Credentials (AI-generated provenance) to downloads; treat as typical, not guaranteed.
6. Runtime note: this skill targets an agent workspace with a live browser and a local working directory (here `~/workspace/`). If ported, replace the browser mechanism and download directory accordingly.
7. If the web app is unreachable or sign-in can't be completed, say so plainly and offer the built-in generator as a fallback — never switch silently.

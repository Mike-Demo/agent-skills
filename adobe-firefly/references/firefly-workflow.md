# Adobe Firefly Web App — Detailed Workflow

Reference for the `adobe-firefly` skill. Model lists and UI labels drift — always read the live page; treat snapshots below as orientation, not truth.

## Browser brief template

Give the live-browser task a self-contained brief (it cannot see the chat):
- Goal: generate or edit N image(s) from the prompt below (verbatim)
- URL: `https://firefly.adobe.com/generate/image`
- Job: text-to-image, OR edit (attach source image + exact edit instruction)
- Settings: model (default Adobe Firefly model unless told otherwise), aspect ratio, content type / style options
- Auth: verify sign-in via avatar + credits counter (top-right) before generating; if logged out or bounced to sign-in, pause and ask the user to sign in manually — never type credentials
- Cost: read the live "Uses N credits" / "Unlimited access" readout before generating; never assume
- Success: file(s) downloaded via Firefly's Download controls, saved to the working directory, verified as real image bytes
- Stop and report verbatim on: credits/paywall/policy notices, auth failure, service errors

## Model families (read live — this drifts)

Two families, very different behavior:

| | Adobe Firefly models (Image 5 / 4 Ultra / 4 / 3) | Partner models (GPT Image, Gemini/Nano Banana, FLUX, Runway…) |
|---|---|---|
| Cost | Typically "Unlimited access" (no credit charge) on observed plans — verify live | Credits per generation (varies by model and quality) — read the live readout |
| Output | Grid of variations (commonly four) | Typically a single image |
| Settings panel | Rich: content type, visual intensity, composition + style references, effects | Minimal: aspect + resolution *or* quality |
| Commercial terms | Trained on licensed Adobe Stock + public domain; Adobe IP indemnification (check plan terms) | NOT covered by Adobe's guarantee |

Present the live model list grouped this way when the user cares; otherwise default to an Adobe Firefly model and note the choice.

## Controls (model-dependent)

After any model change, re-read the panel. Typical controls:
- **General**: model picker, aspect ratio (square / portrait / landscape / widescreen / automatic)
- **Adobe models**: content type (Photo / Art / Auto), visual-intensity slider, Composition reference (layout), Style reference (aesthetic) each with intensity, effects browser (color, tone, lighting, camera angle)
- **Partner models**: resolution *or* quality selector instead of the above; single reference-image slot on some
- Offer only options the live panel actually lists; never set a value the model doesn't show.

## Prompt guidance

- Minimum per Adobe guidance: at least 3 words; descriptive beats terse. Shape: subject + setting + lighting + mood + style/medium.
- Avoid command verbs ("generate", "make") — describe the image, don't instruct the tool.
- Keep it focused; extremely long prompts dilute. One tight descriptive sentence usually beats a paragraph.

## Generate (text-to-image)

1. Prompt field is at the bottom ("Describe the image you want to generate" or similar). Type the final prompt.
2. Generate button (bottom-right) enables once the prompt is non-empty. Read the credit readout, then click.
3. Wait for results (roughly 10–35s depending on model). Count is model-dependent — observe, don't assume.
4. Refine by adjusting prompt/options and regenerating, or use "Show Similar" on a close variation.

## Edit existing image (Generative Fill / Expand)

- Requires the user's source image — never invent one.
- **Generative Fill**: select/brush the area, describe the addition/removal/replacement.
- **Generative Expand**: extend the canvas / change framing; describe what fills the new space.
- This is a separate trigger from text-to-image; confirm the edit instruction explicitly before running.

## Download (verified pattern)

- Hover a result tile to reveal controls, or use the header buttons.
- Per-tile download icon = that variation only; header "Download" = single result, "Download all" = every tile in a grid.
- Click **once** — each click saves another copy (filenames derive from the prompt, so extras just get `(1)`, `(2)`).
- Files land in the browser's download folder as PNG (typical name `Firefly_<prompt>_<id>.png`). Move to the working directory and verify: exists, nonzero, decodes as an image with sane dimensions.
- If a download opens an OS "Save as" dialog the automation can't reach, ask the user to confirm it manually.

## Failure handling

- **Transient glitch** (stalled generation, failed download): retry **once**, then report.
- **Stop conditions** — report verbatim and stop, no workarounds: credit-exhausted / paywall / "Buy more", content-policy block, auth failure / session expiry, service errors.
- **Session expiry mid-task** (bounced to sign-in): pause, ask the user to sign in manually, re-verify avatar + credits, then resume.
- **Zero-byte / HTML / corrupt file**: treat as failed download — retry once, then report. Never present an unverified file as done.
- Never return direct session-bound asset URLs as a "download".

## Notes & qualified claims

- **Credits**: "Unlimited" applies to Adobe Firefly models on the plans observed (including this user's paid account, which has generated without hitting a cap as of Oct 2026). Partner models always cost credits. Read the live readout every time.
- **Commercial use**: Firefly's training-data and indemnification story is Adobe's differentiator, but terms vary by plan — check Adobe's current terms before client work. Partner-model outputs carry no Adobe IP guarantee.
- **Content Credentials**: Firefly typically attaches AI-provenance credentials to downloads; treat as typical, not guaranteed.
- **Locales**: UI labels follow the browser locale — match controls by role/position/icon and confirm visually rather than trusting exact strings.

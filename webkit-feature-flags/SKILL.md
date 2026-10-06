---
name: webkit-feature-flags
description: "Reference for Safari WebKit Feature Flags (iOS/iPadOS 27): which flag gates which web feature, when to enable it, and which flags are internal engine switches to leave alone. Use when a developer asks which feature flag to toggle for testing a web feature, what a flag does, whether a flag is safe to change, or whether the flags apply on iPadOS, watchOS, or tvOS."
---

# WebKit Feature Flags

A developer reference for Safari's WebKit Feature Flags
(Settings → Apps → Safari → Advanced → Feature Flags), captured from a real
iOS 27 device (Safari 27.0, build 20625.1.29). All 220 flags documented with
their recorded on/off state, what each gates, and whether it is a developer
feature or an internal engine switch. Flag states are a snapshot — reverify
against the installed build before acting on any recorded on/off value,
especially after OS updates.

## How to use this skill

1. **Task → flag:** scan the quick reference below.
2. **Flag → details:** look the flag up in
   `references/webkit-feature-flags-guide.md` (alphabetical catalog in
   9 categories, each entry with recorded state, usage taxonomy, ship
   status, and a verified documentation link).
3. **Respect the usage taxonomy** — not every toggle is a developer feature.

## Usage taxonomy

- **Developer use** — off by default, gates a real web API/CSS feature.
  Enable it to test that feature.
- **Testing use** — on by default and/or already shipped. Leave at the
  recorded state; change only to test fallback behavior.
- **Internal only** — engine internals (GPU process, rendering, media
  pipelines). Do not change unless debugging WebKit itself.
- **Do not change** — changing it is misleading or reduces security
  (e.g. passkey site-specific hacks).
- **No confirmed developer use** — purpose is undocumented; don't guess.

**Flag roles in the table below:** **Bold** identifies the primary flag
associated with the feature — if it is already on, leave it on. *Italic*
identifies an optional related flag. ⚠️ INTERNAL identifies an engine or
debug switch that should not be changed for normal website testing.

## Quick reference

| # | Developer task | Relevant flag(s) and recommended state |
|---|---|---|
| 1 | Building/testing a PWA or offline-capable site | **Service Workers** is the primary capability. *Service Worker Navigation Preload* can reduce navigation startup delay. *Storage API* is optional when the app needs storage estimates or durable-storage requests. |
| 2 | Testing WebGPU compute/rendering | **WebGPU** is the primary flag and is on in the recorded build. Leave it on. Add *WebGPU support for HDR* only when testing HDR output. ⚠️ **Allow WebGL in Web Workers** controls WebGL, not WebGPU — not needed here. |
| 3 | Anchor-positioned UI (tooltips, popovers tied to an anchor) | **CSS Anchor Positioning** |
| 4 | Styling native scrollbars | **CSS scrollbar-color property**, **CSS scrollbar-width property**, **CSS scrollbar-gutter property** |
| 5 | HDR photos, video, or canvas | **Canvas Color Spaces** is the author-facing capability. ⚠️ **Support HDR Display** is an internal pipeline switch and **HDR Accelerated Apply GainMap** an internal implementation path — do not change for normal testing. Add *WebGPU support for HDR* only when specifically testing WebGPU HDR output. |
| 6 | WebRTC calls with AV1 or end-to-end encryption | **WebRTC AV1 codec**, **WebRTC SFrame Transform API** (both recorded off; preference type not confirmed — enable only in a controlled test environment and feature-detect codec support) |
| 7 | Custom-styled `<select>` dropdowns | **Enhanced HTML select element** (required for styling/rich content). **`<select> showPicker()`** is a separate capability — only needed to open the picker programmatically. |
| 8 | Web Push notifications | **Declarative Web Push** (note: **Notifications** is off in iPhone Safari tabs — push works in Home Screen web apps) |
| 9 | Local file editing (File System Access API) | **File System WritableStream**, **File System Handle Serialization** (recorded off) |
| 10 | Debugging ITP / cookie behavior | **ITP Debug Mode** (recorded off) — enable temporarily when diagnosing ITP classification; disable after testing because diagnostic logs may contain visited domain names |
| 11 | Text-fragment deep links (`#:~:text=`) | **Scroll To Text Fragment** (+ Feature Detection / Generation), **::target-text pseudo-element** |
| 12 | Scroll-driven animations | No internal switch should normally be changed. Test the public syntax (`animation-timeline: scroll()` / `view()`) with the recorded states. ⚠️ **Threaded Scroll-driven Animations** changes the implementation path, not the author-facing CSS feature. |
| 13 | Passkey / WebAuthn testing | No flag needed — use the standard WebAuthn API. ⚠️ **Passkeys site-specific hacks** is Apple's internal per-site compatibility list; enabling it may activate site-specific behavior rather than the standard path. Do not change for general testing. |
| 14 | Gamepad haptics in browser games | **Gamepad.vibrationActuator support**, **Gamepad trigger vibration support** (both recorded off) |
| 15 | Low-latency networking (WebTransport / QUIC) | **WebTransport** |
| 16 | Digital credentials / identity verification | **Digital Credentials API** (recorded off) |
| 17 | Canvas 2D filter effects | **Canvas Filters** (recorded off) |
| 18 | CSS custom functions | **CSS @function** (recorded off) |
| 19 | Viewport behavior with on-screen keyboards | **Meta Viewport Interactive Widget** (recorded off) |
| 20 | Diagnosing video playback issues | **Show Media Stats** (recorded off — on-screen media diagnostics) |

## Rules

- Never tell a user to toggle flags as a workaround for a production site.
  Flags are for local testing only; re-verify after every iOS update.
- A visible toggle does not necessarily map one-to-one to a public web
  API. When the flag-to-feature relationship is unconfirmed, say so.
- Many behaviors also affect `WKWebView`, but Safari toggle behavior is
  not guaranteed to map one-to-one to every embedded app. Verify in the
  target app.
- iPadOS 27 ships Safari 27, so most of the catalog is expected to apply —
  but verify the visible flag list on the target iPad.
- watchOS has no Safari and WebKit is not available to watchOS apps;
  tvOS has no Safari browser. The flag catalog does not apply to them.
- When a flag's purpose cannot be established, label it undocumented
  rather than guessing.

## Full catalog

See `references/webkit-feature-flags-guide.md`: all 220 flags with recorded
states, usage taxonomy, ship statuses, documentation links under a 7-tier
source hierarchy (Apple / WebKit article / WebKit impl / Spec / MDN /
Community / Undocumented), a Safari release/version reference, platform
expansion for iPadOS/watchOS/tvOS, and a source-coverage appendix.

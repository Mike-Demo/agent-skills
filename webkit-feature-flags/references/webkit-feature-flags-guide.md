# Safari WebKit Feature Flags — Developer Reference

*Captured from Settings → Apps → Safari → Advanced → Feature Flags on iOS 27 / Safari 27 (build 20625.1.29), October 2026 (device model not recorded). 220 flags documented.*

> **Independent reference disclaimer:** This is an independent reference based on one recorded iOS 27 device and public Apple, WebKit, standards, and compatibility sources — not an official Apple compatibility matrix. A visible toggle does not necessarily map one-to-one to a public web API. Test public features through feature detection and verify behavior on each target OS, device, and application.

## 1. Introduction

**WebKit Feature Flags** are per-feature toggles for experimental, in-development, or conditionally-shipped web platform features in Safari's WebKit engine. Apple exposes them at **Settings → Apps → Safari → Advanced → Feature Flags** (on iOS 18+; on older iOS, Settings → Safari → Advanced → Feature Flags). They exist so developers and QA can preview upcoming web features, test interop behavior, and debug sites against engine changes before they ship to everyone.

**What on/off means in this guide.** Each entry records the toggle state as seen on the recording device ("Recorded state: on/off"). A flag that is **on** generally means the feature is enabled in this Safari build — either because it has shipped or because Apple has it on by default while it finishes baking. A flag that is **off** means the feature is present in the engine but disabled; turning it on lets you test it early. Defaults vary by iOS version and sometimes by device.

**Warning: these are experimental.** Flags can change meaning, disappear, or break page rendering between iOS releases. Never ask users to toggle flags as a workaround for a production site — use them for local testing only, and re-verify after every iOS update. Some flags are internal test harnesses (e.g. the `[ITP …]` timeout flags) with no developer-facing effect worth chasing.

**How this document is organized.** Section 2 is a task-oriented quick reference ("I want to do X → toggle Y"). Section 3 is the complete catalog of all 220 flags grouped by category, each with a plain-English explanation, usage guidance, ship status, and the most authoritative documentation link(s) the research verified. Section 4 maps recent Safari releases to iOS/iPadOS versions. Section 5 covers iPadOS, watchOS, and tvOS.

---

## 2. How to use this guide — quick reference

Find your task, flip the flag, test in Safari. Many behaviors also affect `WKWebView` because it uses WebKit, but Safari feature-toggle behavior is not guaranteed to map one-to-one to every embedded app — app entitlements, process configuration, and OS policy can all affect behavior. Verify in the target app.

**Flag roles in this table:** **Bold** identifies the primary flag associated with the feature. If it is already on, leave it on. *Italic* identifies an optional related flag. ⚠️ INTERNAL identifies an engine or debug switch that should not be changed for normal website testing.

| # | Developer task | Relevant flag(s) and recommended state |
|---|---|---|
| 1 | Building/testing a PWA or offline-capable site | **Service Workers** is the primary capability. *Service Worker Navigation Preload* can reduce navigation startup delay. *Storage API* is optional when the app needs storage estimates or durable-storage requests. |
| 2 | Testing WebGPU compute/rendering | **WebGPU** is the primary flag and is on in the recorded build. Leave it on. Add *WebGPU support for HDR* only when testing HDR output. ⚠️ **Allow WebGL in Web Workers** controls WebGL, not WebGPU — not needed here. |
| 3 | Anchor-positioned UI (tooltips, popovers tied to an anchor) | **CSS Anchor Positioning** |
| 4 | Styling native scrollbars | **CSS scrollbar-color property**, **CSS scrollbar-width property**, **CSS scrollbar-gutter property** |
| 5 | HDR photos, video, or canvas | **Canvas Color Spaces** is the author-facing capability. ⚠️ **Support HDR Display** is an internal pipeline switch and **HDR Accelerated Apply GainMap** an internal implementation path — do not change for normal testing. Add *WebGPU support for HDR* only when specifically testing WebGPU HDR output. |
| 6 | WebRTC calls with AV1 or end-to-end encryption | **WebRTC AV1 codec**, **WebRTC SFrame Transform API** (both recorded off) |
| 7 | Custom-styled `<select>` dropdowns | **Enhanced HTML select element** (required for styling/rich content). **`<select> showPicker()`** is a separate capability — only needed to open the picker programmatically. |
| 8 | Web Push notifications | **Declarative Web Push** (note: **Notifications** is off in iPhone Safari tabs — push works in Home Screen web apps) |
| 9 | Local file editing (File System Access API) | **File System WritableStream**, **File System Handle Serialization** (recorded off) |
| 10 | Debugging ITP / cookie behavior | **ITP Debug Mode** (off), **Disable Full 3rd-Party Cookie Blocking (ITP)** (off) to compare |
| 11 | Text-fragment deep links (`#:~:text=`) | **Scroll To Text Fragment** (+ Feature Detection / Generation), **::target-text pseudo-element** |
| 12 | Scroll-driven animations | No internal switch should normally be changed. Test the public syntax (`animation-timeline: scroll()` / `view()`) with the recorded states. ⚠️ **Threaded Scroll-driven Animations** changes the implementation path, not the author-facing CSS feature. |
| 13 | Passkey / WebAuthn testing | No flag needed — use the standard WebAuthn API. ⚠️ **Passkeys site-specific hacks** is Apple's internal per-site compatibility list; enabling it may activate site-specific behavior rather than the standard path. Do not change for general testing. |
| 14 | Gamepad haptics in browser games | **Gamepad.vibrationActuator support**, **Gamepad trigger vibration support** (both off) |
| 15 | Low-latency networking (WebTransport / QUIC) | **WebTransport** |
| 16 | Digital credentials / identity verification | **Digital Credentials API** (recorded off) |
| 17 | Canvas 2D filter effects | **Canvas Filters** (recorded off) |
| 18 | CSS custom functions | **CSS @function** (recorded off) |
| 19 | Viewport behavior with on-screen keyboards | **Meta Viewport Interactive Widget** (recorded off) |
| 20 | Diagnosing video playback issues | **Show Media Stats** (recorded off — on-screen media diagnostics) |

---

## 3. Flag catalog

Every flag from the iOS 27 recording, grouped by category. "Recorded state" is the toggle state on the recorded device. Each entry was researched individually and links were checked for relevance, but some links document the underlying API or implementation work rather than the exact visible Safari toggle — confidence notes identify those cases.

**Source hierarchy** (strongest first), used for the **Docs** labels:

1. **(Apple)** — Apple Release Notes and Apple Developer documentation
2. **(WebKit article)** — official WebKit blog release articles
3. **(WebKit impl)** — WebKit source, merged pull requests, and bug tracker entries (upstream project source, but an implementation PR is *not* equivalent to release documentation)
4. **(Spec)** — standards specifications (W3C, WHATWG, WICG)
5. **(MDN)** — MDN and browser compatibility data (e.g. caniuse)
6. **(Community)** — secondary sources: established technical publications, vendor articles, forums, Wikipedia, mirrors, anecdotal reports. Ship-status claims resting only on these are marked **Unverified**.
7. **Undocumented** — no public source found; the entry says so instead of guessing.

Where no documentation exists, the entry says so instead of guessing.
## 3.1 CSS: selectors, pseudo-classes & layout

### ::target-text pseudo-element
- **Recorded state:** on
- **What it gates:** The `::target-text` highlight pseudo-element, which lets pages style the specific text a user was scrolled to when arriving via a text-fragment URL (`#:~:text=…`). Baseline 2024, supported in all major engines.
- **Usage:** Testing use — leave at default; turn off only to test fallback behavior where text-fragment highlight styling is unsupported.
- **Ship status:** Shipped in Safari (Baseline 2024).
- **Docs:** [::target-text CSS pseudo-element](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Selectors/::target-text) (MDN)

### :open pseudo-class
- **Recorded state:** on
- **What it gates:** The `:open` CSS pseudo-class, which matches elements in an open state — `<details>`, `<dialog>`, `<select>`, or `<input>` with `showPicker()` open. Baseline May 2026, with Safari 26.5 support.
- **Usage:** Testing use — leave at default; turn off only to test how pages render where the :open pseudo-class is unsupported.
- **Ship status:** Shipped in Safari 26.5.
- **Docs:** [:open CSS pseudo-class](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Selectors/:open) (MDN)

### CSS 3D Transform Interoperability for backface-visibility
- **Recorded state:** off
- **Likely behavior:** Undocumented experimental flag — purpose not publicly documented. From the name alone (inference): likely gates WebKit's alignment of `backface-visibility` behavior with the CSS 3D-transforms interoperability effort, where browsers have historically diverged on whether the back face of a rotated element is hidden. Do not treat this description as confirmed.
- **Evidence limit:** The visible flag-to-feature relationship has not been publicly documented.
- **Usage:** No confirmed developer use — purpose not publicly documented; leave it off unless Apple documents it.
- **Ship status:** Unknown.
- **Docs:** No official documentation found.

### CSS @counter-style `<image>` symbols
- **Recorded state:** off
- **What it gates:** Image-based symbols in `@counter-style` (the `<symbol>` type, which accepts `image` alongside `string` and `custom-ident`), letting list counters render pictures instead of text. The image-symbol capability has been carried in the spec as "at risk".
- **Usage:** Developer use — prototyping list counters with custom image bullets via `@counter-style` in experimental builds.
- **Ship status:** Experimental.
- **Docs:** [\<symbol\> - CSS: Cascading Style Sheets](https://developer.mozilla.org/docs/Web/CSS/@counter-style/symbols) (MDN)

### CSS @function
- **Recorded state:** off
- **What it gates:** The `@function` rule for author-defined CSS functions (with a `result` descriptor), letting stylesheets declare reusable typed functions usable inside declarations, as an alternative to `var()`-based workarounds.
- **Usage:** Developer use — testing reusable custom functions in CSS in Safari Technology Preview 249 or newer.
- **Ship status:** Experimental (preview builds only; Chrome 139+ also previews it).
- **Docs:** [@function CSS at-rule](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/At-rules/@function) (MDN)

### CSS Accent Color
- **Recorded state:** on
- **What it gates:** The `accent-color` CSS property, which tints native form controls (checkboxes, radios, range sliders, progress bars) with a chosen accent color instead of the default blue.
- **Usage:** Testing use — leave at default; turn off only to test fallback styling where accent-color is unsupported.
- **Ship status:** Shipped in Safari.
- **Docs:** [accent-color CSS property](https://developer.Mozilla.org/en-US/docs/Web/CSS/Reference/Properties/accent-color) (MDN)

### CSS appearance: base
- **Recorded state:** off
- **What it gates:** The `base` keyword for the `appearance` property (css-ui-4), which gives a form control the UA's baseline styled look — a step between `auto` (native/platform look) and `none` (fully unstyled) — so authors can restyle controls while keeping them recognizable.
- **Usage:** Developer use — experimenting with restyling form controls against the spec's baseline appearance in preview builds.
- **Ship status:** **Unverified** — Experimental (spec'd, but currently supported by no browser).
- **Docs:** [appearance CSS property](https://github.com/josephjsteen1/contents/blob/HEAD/files/en-us/web/css/appearance/index.md) (Community)

### CSS attr() substitution function
- **Recorded state:** off
- **What it gates:** The modern, typed `attr()` substitution function, which can read any HTML attribute into any CSS property with types, units, and fallbacks (e.g. `attr(data-size type(<length>))`); note `<url>` values are barred for security (attr-tainting).
- **Usage:** Developer use — testing typed attribute-to-CSS value substitution in Safari Technology Preview 249 or newer.
- **Ship status:** Experimental (Chrome 133+, Firefox 155; Safari preview only).
- **Docs:** [attr() CSS function](https://developer.mozilla.org/en-US/docs/Web/CSS/attr) (MDN)

### CSS axis-relative position keywords
- **Recorded state:** on
- **What it gates:** The axis-relative keywords `x-start`, `x-end`, `y-start`, `y-end` inside `<position>` / `<bg-position>` values, letting positioned backgrounds be declared relative to the x/y axis rather than physical left/right/top/bottom. WebKit implements them behind the `CSSAxisRelativePositionKeywordsEnabled` preference.
- **Usage:** Testing use — leave at default; turn off only to test fallback rendering without axis-relative position keywords.
- **Ship status:** Experimental in WebKit (behind engine pref; flag is on in this build).
- **Docs:** [[css-backgrounds-4] Add support for axis-relative position keywords](https://github.com/WebKit/WebKit/pull/44525) (WebKit impl)

### CSS calc-mix()
- **Recorded state:** off
- **What it gates:** The `calc-mix()` function from CSS Values 5, which computes a weighted average of its arguments — a generalized sibling of `calc()` for blending multiple values.
- **Usage:** Developer use — testing weighted-average math functions in Safari Technology Preview 249 or newer.
- **Ship status:** **Unverified** — Experimental (Values 5 editor's draft; Safari preview only).
- **Docs:** [Safari Technology Preview 249 release notes](https://artofstyleframe.com/blog/safari-technology-preview-249-css-features/) (Community)

### CSS color-layers()
- **Recorded state:** off
- **What it gates:** The `color-layers()` function from CSS Color 6, which composites multiple colors together (with optional blend modes, defaulting to Source Over) into a single computed color.
- **Usage:** Developer use — prototyping layered/blended color values in experimental builds.
- **Ship status:** Experimental (CSS Color 6 editor's draft).
- **Docs:** [CSS Color Module Level 6](https://drafts.csswg.org/css-color-6/) (Community)

### CSS corner-shape property
- **Recorded state:** off
- **What it gates:** The `corner-shape` CSS property, which changes the shape of box corners (round, squircle, square, bevel, scoop, notch, or a `superellipse()` curve) when a non-zero `border-radius` is set, from CSS Borders 4.
- **Usage:** Developer use — designing non-rectangular corners (e.g. squircles) on cards or buttons in Safari previews.
- **Ship status:** **Unverified** — Experimental in Safari (Chrome 139+ ships it).
- **Docs:** [corner-shape - CSS-Tricks Almanac](https://css-tricks.com/almanac/properties/c/corner-shape/) (Community)

### CSS d property
- **Recorded state:** off
- **What it gates:** The CSS `d` property for SVG `<path>` elements, accepting `none` or `path("<string>")`, which overrides the path's `d` attribute — including making the path animatable via CSS animations/transitions.
- **Usage:** Developer use — animating or styling SVG path geometry through CSS instead of SMIL or attributes in preview builds.
- **Ship status:** Experimental in Safari.
- **Docs:** [d CSS property](https://developer.mozilla.org/en-US/docs/Web/CSS/d) (MDN)

### CSS display: grid-lanes
- **Recorded state:** on
- **What it gates:** Native masonry layout via `display: grid-lanes` (the new CSS Grid Lanes spec), replacing the old `-webkit-` prefixed masonry prototypes; includes `flow-tolerance` (renamed from `item-tolerance` in January 2026) for controlling how strictly items pack into lanes.
- **Usage:** Testing use — leave at default; turn off only to test fallback layouts where native masonry is unsupported.
- **Ship status:** Shipped in Safari 26.4.
- **Docs:** [Introducing CSS Grid Lanes](https://webkit.org/blog/17660/introducing-css-grid-lanes/) (WebKit article)

### CSS dynamic-range-limit-mix()
- **Recorded state:** off
- **What it gates:** The `dynamic-range-limit-mix()` function, which blends custom percentages of the HDR luminance-limit keywords (e.g. mixing `standard` and `no-limit`) to fine-tune how HDR content is clamped.
- **Usage:** Developer use — fine-tuning HDR luminance limits on pages with HDR imagery in experimental builds.
- **Ship status:** Experimental.
- **Docs:** [dynamic-range-limit-mix() CSS function](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Values/dynamic-range-limit-mix) (MDN)

### CSS dynamic-range-limit: constrained
- **Recorded state:** off
- **What it gates:** The `constrained` keyword for the `dynamic-range-limit` property (alongside `standard` and `no-limit`), an HDR luminance cap stricter than `standard`; WebKit removed its parsing behind the `CSSConstrainedDynamicRangeLimitEnabled` preference while the implementation continues.
- **Usage:** Developer use — testing constrained HDR luminance capping in experimental builds.
- **Ship status:** Experimental (parsing gated off pending full implementation).
- **Docs:** [dynamic-range-limit CSS property](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/dynamic-range-limit) (MDN)

### CSS field-sizing property
- **Recorded state:** on
- **What it gates:** The `field-sizing` CSS property; `field-sizing: content` makes form controls like `<input>`, `<select>`, and `<textarea>` shrinkwrap or grow with their content instead of using fixed sizes. Baseline June 2026, shipped in Safari 26.2.
- **Usage:** Testing use — leave at default; turn off only to test fallback sizing where field-sizing is unsupported.
- **Ship status:** Shipped in Safari 26.2.
- **Docs:** [field-sizing CSS property](https://developer.mozilla.org/docs/Web/CSS/field-sizing) (MDN)

### CSS font-synthesis-style: oblique-only
- **Recorded state:** off
- **What it gates:** The `oblique-only` keyword of the `font-synthesis-style` property: like `auto` (browser may synthesize a missing oblique face), but no synthesis occurs when `font-style: italic` is set — added by the CSSWG (issue #9390) so italic stays unsynthesized while oblique may be.
- **Usage:** Developer use — controlling font-synthesis behavior separately for italic vs. oblique text in experimental builds.
- **Ship status:** Experimental (property itself is widely available; the `oblique-only` value is new).
- **Docs:** [font-synthesis-style CSS property](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/font-synthesis-style) (MDN)

### CSS font-variant-emoji property
- **Recorded state:** off
- **What it gates:** The `font-variant-emoji` CSS property (CSS Fonts 4), which sets the default emoji presentation style — `normal`, `text` (monochrome, as with U+FE0E), `emoji` (color, as with U+FE0F), or `unicode` (follow the character's Emoji presentation property). MDN lists it as limited availability.
- **Usage:** Developer use — forcing text vs. color emoji presentation (e.g. ☎ as glyph vs. graphic) in experimental builds.
- **Ship status:** Experimental in Safari.
- **Docs:** [font-variant-emoji CSS property](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/font-variant-emoji) (MDN)

### CSS ident() function
- **Recorded state:** off
- **What it gates:** The `ident()` arbitrary substitution function from CSS Values 5, which constructs `<custom-ident>` / `<dashed-ident>` values from strings, integers, and idents (e.g. `view-timeline-name: ident("--tl-" sibling-index())`); WebKit implements it behind the `CSSIdentFunctionEnabled` preference.
- **Usage:** Developer use — building identifier values dynamically from attribute data or counters in experimental builds.
- **Ship status:** Experimental (implemented in WebKit behind pref).
- **Docs:** [[css-values-5] Implement ident() arbitrary substitution function](https://github.com/WebKit/WebKit/pull/71301) (WebKit impl)

### CSS Input Security
- **Recorded state:** off
- **What it gates:** A standardized form of text obfuscation in form fields (the `input-security` concept from css-ui-4, resolved by the CSSWG in 2018) — visually masking typed characters in text inputs with shapes (circle/disc/square), like password fields but for non-password inputs. It descends from the non-standard `-webkit-text-security`, which WebKit removed in 2021 (bug 235557); the flag's reappearance suggests renewed standardization work.
- **Usage:** Developer use — prototyping masked-input styling in experimental builds.
- **Ship status:** Experimental.
- **Docs:** [-webkit-text-security CSS property (predecessor)](https://developer.mozilla.org/en-us/docs/web/css/-webkit-text-security) (MDN)

### CSS line-clamp
- **Recorded state:** off
- **What it gates:** The standardized, unprefixed `line-clamp` property (CSS Overflow 4): `none | <integer> | <'block-ellipsis'>`, which clamps block content to a number of lines with optional ellipsis control — no longer requiring the `display: -webkit-box` + `-webkit-box-orient: vertical` incantation of the legacy prefixed version.
- **Usage:** Developer use — testing standard multi-line truncation in Safari previews.
- **Ship status:** Experimental in Safari (legacy `-webkit-line-clamp` long shipped; Firefox 155 ships unprefixed).
- **Docs:** [line-clamp CSS property](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/line-clamp) (MDN)

### CSS math-depth
- **Recorded state:** on
- **What it gates:** The `math-depth` CSS property (MathML Core), which records each element's nesting depth in a math formula and drives font-size scaling of subformulas via `font-size: math` (the default for `<math>`); values `auto-add`, `add(<integer>)`, and absolute `<integer>`.
- **Usage:** Testing use — leave at default; turn off only to test rendering of MathML formulas without math-depth scaling.
- **Ship status:** Shipped in Safari (Baseline 2026).
- **Docs:** [math-depth CSS property](https://developer.Mozilla.Org/en-US/docs/Web/CSS/Reference/Properties/math-depth) (MDN)

### CSS object-view-box property
- **Recorded state:** off
- **What it gates:** The `object-view-box` CSS property, which crops/zooms replaced elements (images, video) by defining a rectangular view box with `inset()`, `xywh()`, or `rect()` — enabling pan/zoom effects (even animated) without changing the element's layout box. Shipped in Chrome 104+.
- **Usage:** Developer use — implementing zoom/pan crops of images or video in Safari previews.
- **Ship status:** Experimental in Safari.
- **Docs:** [object-view-box CSS property](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/object-view-box) (MDN)

### CSS overflow-clip-margin
- **Recorded state:** off
- **What it gates:** The `overflow-clip-margin` CSS property (CSS Overflow 3), which expands the clip boundary of `overflow: clip` outward from the padding box by a `<length>` (optionally from a different `<visual-box>`), so slightly-overflowing content like box-shadows isn't cut off.
- **Usage:** Developer use — keeping `overflow: clip` while letting shadows or decorations bleed past the padding box in experimental builds.
- **Ship status:** Experimental in Safari.
- **Docs:** [overflow-clip-margin CSS property](https://Developer.Mozilla.org/en-US/docs/Web/CSS/Reference/Properties/overflow-clip-margin) (MDN)

### CSS Devolvable Widgets
- **Recorded state:** on
- **What it gates:** WebKit engine behavior (not a web API): "devolvable" form controls (input, button, select, textarea, meter, progress) drop their native/platform appearance for a simpler primitive appearance when authors set CSS properties from the css-ui-4 appearance-disabling list (e.g. `background`), matching Chrome and Firefox.
- **Usage:** Internal only — engine-internal behavior, not a web API; do not change unless debugging WebKit itself.
- **Ship status:** Shipped in Safari (engine behavior since the iOS 18.4 era).
- **Docs:** [Support devolvable widgets](https://github.com/WebKit/WebKit/pull/37511) (WebKit impl)

### CSS Anchor Positioning
- **Recorded state:** on
- **What it gates:** CSS Anchor Positioning — tethering an absolutely positioned element to another element via `anchor-name`, `position-anchor`, `position-area`, the `anchor()` function, and `position-try` fallback positions; pairs well with the `popover` attribute for menus and tooltips. Baseline 2026.
- **Usage:** Testing use — leave at default; turn off only to test fallback layouts where anchor positioning is unsupported.
- **Ship status:** Shipped in Safari 26.0.
- **Docs:** [WebKit Features in Safari 26.0](https://webkit.org/blog/17333/webkit-features-in-safari-26-0/) (WebKit article)

## 3.2 CSS: text, scrolling & color

### CSS Overscroll Behavior
- **Recorded state:** on
- **What it gates:** The `overscroll-behavior` CSS property, which controls what happens when a scroll reaches the edge of a scroll container — whether the scroll "chains" up to a parent scroller and whether the rubber-band bounce / pull-to-refresh gestures are allowed. Values include `auto`, `contain`, and `none`.
- **Usage:** Testing use — leave at default; disable only to test how pages behave without overscroll containment.
- **Ship status:** Shipped in Safari 16.0
- **Docs:** [Release Notes for Safari Technology Preview 141](https://webkit.org/blog/12434/release-notes-for-safari-technology-preview-141/) — (WebKit article)

### CSS Painting API
- **Recorded state:** off
- **What it gates:** The CSS Paint API (part of CSS Houdini): `CSS.paintWorklet` and the `paint()` image function, which let JavaScript draw custom backgrounds, borders, and masks programmatically at paint time. The flag guards the worklet global scope, input properties/arguments passing, and repaint-on-property-change behavior.
- **Usage:** Developer use — enable when testing Houdini paint worklets that draw dynamic CSS images without extra DOM or canvas elements.
- **Ship status:** Experimental (WebKit has implemented it behind this flag since 2018; not shipped in stable Safari)
- **Docs:** [Release Notes for Safari Technology Preview 72](https://webkit.org/blog/8547/release-notes-for-safari-technology-preview-72/) — (WebKit article)

### CSS random()
- **Recorded state:** on
- **What it gates:** The CSS `random()` function (CSS Values and Units 5), which produces a random value within a range — e.g. `width: random(100px, 200px)`. Named values (`random(--s, 100px, 200px)`) reuse one cached random number; since Safari 26.5 names are global by default, with the `element-scoped` keyword opting back into per-element caching. Safari was the first browser to ship it.
- **Usage:** Testing use — leave at default; disable only to test fallback styling without the random() function.
- **Ship status:** Shipped in Safari 26.2 (named-value scoping refined in Safari 26.5)
- **Docs:** [WebKit Features for Safari 26.2](https://webkit.org/blog/17640/webkit-features-for-safari-26-2/) — (WebKit article)

### CSS random-item()
- **Recorded state:** off
- **What it gates:** The CSS `random-item()` function (CSS Values and Units 5): picks one item from a comma-separated list of values using the same random-caching options as `random()`, e.g. `random-item(--c, red, blue, green)`. It is an arbitrary substitution function resolved at computed-value time, like `var()` and `attr()`. Note: WebKit trunk turned the flag on by default in August 2026 (commit 319180@main), but the iOS 27 capture still records it as off — treat as experimental.
- **Usage:** Developer use — enable to experiment with picking a random value from a discrete list (e.g. a random accent color) in pure CSS; treat as experimental.
- **Ship status:** Experimental (parse-time and computed-value support landed on trunk; no stable Safari release confirmed)
- **Docs:** [[css-values-5] Enable random-item() by default — WebKit PR #71629](https://github.com/WebKit/WebKit/pull/71629) — (WebKit impl)

### CSS Rhythmic Sizing
- **Recorded state:** off
- **What it gates:** The CSS Rhythmic Sizing module (css-rhythm-1) properties for maintaining vertical rhythm — primarily `line-height-step`, which rounds each line box's height up to a multiple of a step length so baselines stay on a grid. WebKit so far has only registered the spec category in its CSS properties data; there is no shipping implementation.
- **Usage:** Developer use — enable only when experimenting with baseline-grid typography; no browser ships it yet.
- **Ship status:** Experimental (no browser ships it yet)
- **Docs:** [Add CSS Rhythmic Sizing as valid category in CSSProperties.json — WebKit PR #10106](https://github.com/WebKit/WebKit/pull/10106) — (WebKit impl)

### CSS Scroll Anchoring
- **Recorded state:** on
- **What it gates:** CSS scroll anchoring (the `overflow-anchor` property), which automatically compensates the scroll position when content above the viewport changes size — so a page doesn't visually jump when images load, ads insert, or DOM above the fold mutates. This was the last major engine gap versus Chrome/Firefox.
- **Usage:** Testing use — leave at default; disable only to debug scroll jumps against pre-anchoring behavior.
- **Ship status:** Shipped in Safari 27
- **Docs:** [WebKit Features for Safari 27.0](https://webkit.org/blog/18325/webkit-features-for-safari-27-0/) (WebKit article)

### CSS Scroll State Container Queries
- **Recorded state:** off
- **What it gates:** `@container scroll-state(...)` container queries, which style descendants based on a container's scroll state: `stuck` (sticky element pinned to an edge), `snapped` (scroll-snap position engaged), `scrollable` (can scroll in a direction), and `scrolled`, used with `container-type: scroll-state`. WebKit has landed parsing/serialization behind the flag; evaluation is still a placeholder returning false.
- **Usage:** Developer use — enable to style sticky headers or snapped carousels via `@container scroll-state(...)` without JS scroll listeners; evaluation is still a placeholder returning false.
- **Ship status:** Experimental (Chrome 133+ ships it; WebKit still implementing)
- **Docs:** [Parse and serialize scroll-state container queries — WebKit PR #66675](https://github.com/WebKit/WebKit/pull/66675) — (WebKit impl)

### CSS scrollbar-color property
- **Recorded state:** on
- **What it gates:** The standard `scrollbar-color` CSS property, which sets the colors of the scrollbar thumb and track (e.g. `scrollbar-color: gray transparent`). It is the standardized replacement for the non-standard `::-webkit-scrollbar` pseudo-elements.
- **Usage:** Testing use — leave at default; disable only to test un-themed scrollbar rendering.
- **Ship status:** Shipped in Safari 26.2 (completing the scrollbar property set alongside `scrollbar-width` and `scrollbar-gutter`)
- **Docs:** [WebKit Features for Safari 26.2](https://webkit.org/blog/17640/webkit-features-for-safari-26-2/) — (WebKit article)

### CSS scrollbar-gutter property
- **Recorded state:** on
- **What it gates:** The `scrollbar-gutter` CSS property, which reserves space for the scrollbar in the layout (e.g. `scrollbar-gutter: stable`) so content doesn't shift when scrollbars appear or disappear. Useful on containers with asynchronously loaded content.
- **Usage:** Testing use — leave at default; disable only to test layout behavior without reserved scrollbar space.
- **Ship status:** Shipped in Safari 18.2
- **Docs:** [WebKit Features for Safari 26.2](https://webkit.org/blog/17640/webkit-features-for-safari-26-2/) — (WebKit article)

### CSS scrollbar-width property
- **Recorded state:** on
- **What it gates:** The `scrollbar-width` CSS property (`auto`, `thin`, `none`), which controls how thick the scrollbar is or hides it entirely while keeping the element scrollable.
- **Usage:** Testing use — leave at default; disable only to test fallback scrollbar rendering without the standard property.
- **Ship status:** Shipped in Safari 18.2
- **Docs:** [WebKit Features for Safari 26.2](https://webkit.org/blog/17640/webkit-features-for-safari-26-2/) — (WebKit article)

### CSS text-decoration-line: spelling-error/grammar-error
- **Recorded state:** on
- **What it gates:** The `spelling-error` and `grammar-error` values of the `text-decoration-line` property, which apply the browser's native error-marker styling (typically the red/green wavy underline from the OS spell checker) to arbitrary text. Pairs with the `::spelling-error` / `::grammar-error` pseudo-elements (shipped in Safari 17.4) for overriding the marker styling.
- **Usage:** Testing use — leave at default; disable only to test rendering without native error-marker styling.
- **Ship status:** Shipped in Safari 26.2
- **Docs:** [WebKit Features for Safari 26.2](https://webkit.org/blog/17640/webkit-features-for-safari-26-2/) — (WebKit article)

### CSS text-group-align property
- **Recorded state:** off
- **What it gates:** The `text-group-align` property from CSS Text Module Level 4: after lines are wrapped, it re-shrink-wraps the block to its longest line and aligns that whole group of lines within the container (e.g. left-aligned text centered as a block — a common TTML/CJK typesetting request). Values: `none | start | end | left | right | center`.
- **Usage:** Developer use — enable when typesetting multi-line text blocks aligned as a unit (e.g. centered block with left-aligned lines); spec still a draft.
- **Ship status:** Experimental (no browser ships it; spec still a draft)
- **Docs:** [CSS Text Module Level 4 (W3C Working Draft)](https://www.w3.org/TR/2019/WD-css-text-4-20191113/) — (Spec)

### CSS text-justify property
- **Recorded state:** off
- **What it gates:** The `text-justify` CSS property, which selects the justification algorithm used with `text-align: justify` — `auto`, `inter-word`, `inter-character`, or `none` — controlling whether extra space goes between words, between characters, or nowhere. Safari has never implemented it (longstanding WebKit bug 9945).
- **Usage:** Developer use — enable to test justified-text rendering algorithms or CJK inter-character justification; Safari has never implemented it.
- **Ship status:** **Unverified** — Experimental (Chrome/Edge/Firefox ship it; Safari does not)
- **Docs:** [text-justify — web-platform-dx developer signals](https://github.com/web-platform-dx/developer-signals/issues/597) — (Community)

### CSS text-spacing-trim property
- **Recorded state:** off
- **What it gates:** The `text-spacing-trim` longhand of the CSS `text-spacing` shorthand, controlling the spacing of full-width CJK punctuation at line starts/ends and between adjacent characters (values like `normal`, `space-all`, `space-first`, `trim-start`). WebKit repurposed its old `text-spacing` flag to guard just this longhand while `text-autospace` got its own flag.
- **Usage:** Developer use — enable to fine-tune Japanese/Chinese punctuation spacing at line boundaries per JLREQ typography rules.
- **Ship status:** Experimental (partial WebKit implementation behind the flag; Chromium ships it)
- **Docs:** [Repurpose CSSTextSpacing feature flag to CSSTextSpacingTrim — WebKit PR #34851](https://github.com/WebKit/WebKit/pull/34851) — (WebKit impl)

### CSS text-transform: math-auto
- **Recorded state:** off
- **What it gates:** The `math-auto` value of `text-transform`, which automatically renders single-character Latin/Greek letters and a few math symbols as math italic (e.g. "x" becomes "𝑥", while "exp" stays as-is). It replaces the legacy MathML `mathvariant` system and is the default styling mechanism for `<mi>` elements in MathML Core.
- **Usage:** Developer use — enable when rendering MathML formulas where single-letter identifiers should appear in math italic.
- **Ship status:** **Unverified** — Experimental (implemented on WebKit trunk, behind this flag)
- **Docs:** [WebKit Igalia Periodical #44](https://blogs.igalia.com/webkit/blog/2025/wip-44/) — (Community)

### CSS text-wrap: pretty
- **Recorded state:** on
- **What it gates:** The `pretty` value of the `text-wrap` property: the line-breaking algorithm looks ahead to even out the ragged edge, improve hyphenation choices, and avoid short orphan words on the last line. In WebKit, all lines of the element are improved, not just the final ones.
- **Usage:** Testing use — leave at default; disable only to compare paragraph rags without the pretty line-breaking algorithm.
- **Ship status:** Shipped in Safari 26.0
- **Docs:** [WebKit Features in Safari 26.0](https://webkit.org/blog/17333/webkit-features-in-safari-26-0/) — (WebKit article)

### CSS Tree Counting Functions
- **Recorded state:** on
- **What it gates:** The CSS Values 5 tree-counting functions `sibling-count()` (number of siblings including the element itself) and `sibling-index()` (the element's 1-based index among siblings). They produce integers usable anywhere an integer is allowed, including inside `calc()` — e.g. `width: calc(100% / sibling-count())` to split space evenly among siblings.
- **Usage:** Testing use — leave at default; disable only to test styling fallbacks without sibling-count()/sibling-index().
- **Ship status:** Shipped in Safari 26.2
- **Docs:** [WebKit Features for Safari 26.2](https://webkit.org/blog/17640/webkit-features-for-safari-26-2/) — (WebKit article)

### CSS Typed OM: Color Support
- **Recorded state:** off
- **What it gates:** The color portion of the CSS Typed OM API: the typed color interfaces (`CSSColorValue`, `CSSRGB`, `CSSHSL`, `CSSHWB`, `CSSLCH`, `CSSLab`, `CSSOKLCH`, `CSSOKLab`, `CSSColor`) used by `CSSStyleValue.parse()` and friends. WebKit keeps this part flagged off separately because the color API spec was still changing and other engines hadn't shipped it, so Typed OM can be enabled without the unstable color surface.
- **Usage:** Developer use — enable to manipulate colors programmatically through the CSS Typed OM instead of string parsing.
- **Ship status:** Experimental
- **Docs:** [Add experimental feature flag for CSS Typed OM Color support — WebKit PR #6886](https://github.com/WebKit/WebKit/pull/6886) — (WebKit impl)

### CSS Unprefixed Backdrop Filter
- **Recorded state:** on
- **What it gates:** Support for the unprefixed `backdrop-filter` CSS property (it previously required `-webkit-backdrop-filter` in Safari). It applies graphical effects — `blur()`, `brightness()`, `saturate()`, `contrast()`, etc. — to the content behind an element, enabling frosted-glass UIs.
- **Usage:** Testing use — leave at default; disable only to test pages against the prefixed-only backdrop-filter behavior.
- **Ship status:** Shipped in Safari 18.0 (prefixed form dates back to Safari 9.0)
- **Docs:** [WebKit Features in Safari 18.0](https://webkit.org/blog/15865/webkit-features-in-safari-18-0/) — (WebKit article)

### CSS URL Modifiers
- **Recorded state:** on
- **What it gates:** The CSS Values 5 request URL modifiers inside `url()`: `cross-origin()` and `referrer-policy()` let a stylesheet control the CORS mode and referrer policy of a resource fetch directly from CSS — e.g. `background-image: url("image.png" cross-origin(anonymous))` — without HTML attributes or JS. (The third modifier, `integrity()`, is gated by its own separate flag.)
- **Usage:** Testing use — leave at default; disable only to test CSS resource loads without declared cross-origin/referrer modifiers.
- **Ship status:** Shipped in Safari 26.2
- **Docs:** [WebKit Features for Safari 26.2](https://webkit.org/blog/17640/webkit-features-for-safari-26-2/) — (WebKit article)

### CSS URL Modifiers: integrity()
- **Recorded state:** off
- **What it gates:** The `integrity()` request URL modifier, which would apply subresource-integrity checking to resources loaded from CSS `url()` — the CSS analog of the HTML `integrity` attribute. WebKit has not implemented it yet (it was unclear where the integrity check belongs in the image-loading pipeline), while `cross-origin()` and `referrer-policy()` already ship.
- **Usage:** Developer use — enable to test integrity-checked CSS resource loads once WebKit implements it.
- **Ship status:** Experimental (not yet shipped in WebKit)
- **Docs:** [[css-values-5] integrity() CSS URL request modifier — webkit/standards-positions#656](https://github.com/webkit/standards-positions/issues/656) — (WebKit impl)

### Next-generation flex layout integration (FFC)
- **Recorded state:** on
- **Likely behavior:** Undocumented experimental flag — purpose not publicly documented. Inference only (not verified against any public source): the name and WebKit's public codebase structure indicate this gates the integration of WebKit's rewritten flexbox layout engine (the FlexFormattingContext, "FFC") with the legacy render tree — the flexbox analog of the verified GFC grid integration — so that flex containers are laid out by the new engine instead of the legacy `RenderFlexibleBox`.
- **Evidence limit:** The visible flag-to-feature relationship has not been publicly documented.
- **Usage:** Internal only — do not change; it is an internal engine rollout switch with no documented author-facing effect.
- **Ship status:** Unknown (no public release note or announcement; enabled by default on this build)
- **Docs:** No official documentation found.

### Next-generation grid layout integration (GFC)
- **Recorded state:** on
- **What it gates:** The integration of WebKit's rewritten grid layout engine (the GridFormattingContext, "GFC") with the legacy render tree. Coverage gates in the layout-integration layer progressively move grid cases onto the modern `GridFormattingContext`, falling back to the legacy `RenderGrid` only for not-yet-supported cases (e.g. grid items with percentage/calc padding were moved onto GFC in mid-2026).
- **Usage:** Internal only — do not change; it gates the rewritten grid layout engine integration and has no author-facing syntax.
- **Ship status:** Unknown (no public release note; internal rollout in progress, on by default in Safari 27 builds)
- **Docs:** [[GFC] Remove GridItemHasPercentOrCalcPadding avoidance reason — WebKit PR #69614](https://github.com/WebKit/WebKit/pull/69614) — (WebKit impl)

### Subpixels in inline layout (in block direction)
- **Recorded state:** on
- **What it gates:** An internal rendering-precision switch: whether WebKit's layout-integration inline layout keeps fractional (subpixel) positions in the block direction instead of snapping line/block positions to whole CSS pixels. Per WebKit trunk cleanup (date not verified), subpixel inline layout is now always enabled and the underlying setting was removed, but the flag remains visible in the UI.
- **Usage:** Internal only — do not change; it keeps inline layout at subpixel precision (the underlying setting is already always-on).
- **Ship status:** Enabled by default (internal; the dedicated setting was removed as always-on in mid-2026 per WebKit PR #69531)
- **Docs:** [Dead if in fieldset baseline computation after subpixel-inline-layout cleanup — WebKit PR #69531](https://github.com/WebKit/WebKit/pull/69531) — (WebKit impl)

### Text Shaping Across Inline Boxes
- **Recorded state:** on
- **What it gates:** Shaping of complex-script text across inline element boundaries: in scripts like Arabic or N'Ko, where letters join and change shape, WebKit now shapes the word as a whole even when individual letters are wrapped in separate `<span>`s or other inline elements. Useful for teaching materials that color individual letters of a joined word.
- **Usage:** Testing use — leave at default; disable only to test joining-script text without cross-inline-element shaping.
- **Ship status:** Shipped in Safari 26.2
- **Docs:** [WebKit Features for Safari 26.2](https://webkit.org/blog/17640/webkit-features-for-safari-26-2/) — (WebKit article)

### word-break: auto-phrase enabled
- **Recorded state:** off
- **What it gates:** The `auto-phrase` value of the `word-break` property, which asks the engine to break lines at phrase boundaries (using linguistic segmentation) rather than at character or word boundaries — primarily useful for Japanese text where it produces more natural line breaks.
- **Usage:** Developer use — enable to test Japanese line-breaking quality with phrase-aware breaks (enabled in Safari Technology Preview 246, not stable Safari).
- **Ship status:** Experimental (enabled in Safari Technology Preview 246; not in stable Safari)
- **Docs:** [Release Notes for Safari Technology Preview 246](https://webkit.org/blog/18128/release-notes-for-safari-technology-preview-246/) — (WebKit article)

### scrollend event
- **Recorded state:** on
- **What it gates:** The `scrollend` event, which fires once when scrolling has definitively completed — whether triggered by user gestures, keyboard, smooth scrolling, or `scrollTo()` — replacing imprecise timer-based scroll debouncing. It completed cross-browser Baseline coverage when Safari added it.
- **Usage:** Testing use — leave at default; disable only to test timer-based scroll-debounce fallbacks.
- **Ship status:** Shipped in Safari 26.2
- **Docs:** [WebKit Features for Safari 26.2](https://webkit.org/blog/17640/webkit-features-for-safari-26-2/) — (WebKit article)

## 3.3 JavaScript & DOM APIs

### <select> showPicker() method
- **Recorded state:** off
- **What it gates:** The `HTMLSelectElement.showPicker()` method, which programmatically opens a `<select>` element's options picker UI — the same picker shown on user tap — from a button press or other gesture. Requires transient user activation and a same-origin context.
- **Usage:** Developer use — open a native select dropdown from a custom button or gesture.
- **Ship status:** Experimental (not shipped in Safari; WebKit standards position is "support", implementation work tracked in WebKit).
- **Docs:** [HTMLSelectElement: showPicker() method — MDN](https://developer.mozilla.org/en-US/docs/Web/API/HTMLSelectElement/showPicker) (MDN)

### BroadcastChannel API
- **Recorded state:** on
- **What it gates:** The `BroadcastChannel` interface, a one-to-many message bus letting same-origin browsing contexts (tabs, windows, iframes, workers) exchange messages over a named channel — e.g., syncing login state across tabs.
- **Usage:** Testing use — leave at default; disable only to test fallback behavior for same-origin messaging.
- **Ship status:** Shipped in Safari 15.4.
- **Docs:** [New WebKit Features in Safari 15.4](https://webkit.org/blog/12445/new-webkit-features-in-safari-15-4/) (WebKit article)

### Close Watcher API
- **Recorded state:** off
- **What it gates:** The `CloseWatcher` interface, which lets custom UI components (sidebars, modals, pickers) respond to device-specific close actions (Esc key on desktop, back button/gesture on Android) the way built-in popovers and dialogs do, via `cancel`/`close` events.
- **Usage:** Developer use — prototype custom closable components that honor the platform's native close gesture.
- **Ship status:** Experimental (not supported in Safari; shipped in Chrome 126 and Firefox 149).
- **Docs:** [CloseWatcher — MDN](https://developer.mozilla.org/en-US/docs/Web/API/CloseWatcher) (MDN)

### Compression Stream API
- **Recorded state:** on
- **What it gates:** The `CompressionStream` and `DecompressionStream` interfaces, which compress/decompress streaming data (gzip, deflate, deflate-raw, brotli, zstd) as `TransformStream`-shaped objects usable with `pipeThrough()`.
- **Usage:** Testing use — leave at default.
- **Ship status:** Shipped in Safari 18.4
- **Docs:** [CompressionStream — MDN](https://Developer.Mozilla.org/en-US/docs/Web/API/CompressionStream) (MDN)

### Cookie Store API CookieStoreManager
- **Recorded state:** off
- **What it gates:** The `CookieStoreManager` portion of the Cookie Store API — `subscribe()`, `unsubscribe()`, and `getSubscriptions()` — which lets a service worker registration subscribe to cookie-change events. WebKit implemented the manager but does not yet fire `cookiechange` events to service workers.
- **Usage:** Developer use — experiment with service-worker cookie-change subscriptions.
- **Ship status:** Experimental (manager implemented in WebKit; change-event delivery to service workers not yet implemented).
- **Docs:** [Implement CookieStoreManager for the CookieStore API — WebKit PR](https://github.com/WebKit/WebKit/pull/31979) (WebKit impl)

### Cookie Store API
- **Recorded state:** on
- **What it gates:** The async, promise-based `cookieStore` API (`get`, `set`, `getAll`, `delete`, plus `change` events) — a modern replacement for the synchronous `document.cookie` string that also works in service workers.
- **Usage:** Testing use — leave at default.
- **Ship status:** Shipped in Safari 18.4 (partial implementation; window-context subset)
- **Docs:** [Cookie Store API — MDN](https://DEVELOPER.mozilla.org/en-US/docs/Web/API/Cookie_Store_API) (MDN)

### Declarative Web Push
- **Recorded state:** on
- **What it gates:** WebKit's declarative push model, where a web push subscription is created via `window.pushManager` and incoming push payloads carry a JSON `notification` object (title, body, navigate URL) that the browser displays directly — no service worker required, saving battery/CPU and reducing misuse surface.
- **Usage:** Testing use — leave at default.
- **Ship status:** Shipped in Safari 18.4 (iOS/iPadOS: Home Screen web apps only)
- **Docs:** [WebKit Features in Safari 18.4 — WebKit](https://webkit.org/blog/16574/webkit-features-in-safari-18-4/) (WebKit article)

### DOM mutation events
- **Recorded state:** on
- **What it gates:** The legacy synchronous DOM mutation events (`DOMNodeInserted`, `DOMNodeRemoved`, `DOMSubtreeModified`, `DOMAttrModified`, etc.) — deprecated since they fire synchronously on every DOM change and severely degrade performance.
- **Usage:** Testing use — leave at default; new code should use `MutationObserver` instead.
- **Ship status:** **Unverified** — Shipped in Safari (legacy; deprecated — do not use for new development), per a community blog; not confirmed by Apple or WebKit sources.
- **Docs:** [Mutation Events Are Deprecated: Here's How to Replace Them with Mutation Observers](https://medium.com/@dhunganaprashant/mutation-events-are-deprecated-heres-how-to-replace-them-with-mutation-observers-0199416dfec5) (Community)

### document.caretPositionFromPoint() API
- **Recorded state:** on
- **What it gates:** The standard `document.caretPositionFromPoint(x, y)` method, which returns a `CaretPosition` (node + character offset) for viewport coordinates — the standards-based replacement for WebKit's proprietary `document.caretRangeFromPoint()`.
- **Usage:** Testing use — leave at default.
- **Ship status:** Shipped in Safari 26.2
- **Docs:** [Document: caretPositionFromPoint() method — MDN](https://developer.mozilla.org/en-US/docs/Web/API/Document/caretPositionFromPoint) (MDN)

### Enable Element.currentCSSZoom
- **Recorded state:** on
- **What it gates:** The `Element.currentCSSZoom` read-only property, which reports the effective CSS `zoom` on an element (the product of `zoom` values on the element and all ancestors), so scripts can correct measurements that don't include zoom (e.g., `clientHeight`, `offset*`, `scroll*`).
- **Usage:** Testing use — leave at default.
- **Ship status:** Shipped in Safari 26.4 (Baseline 2026)
- **Docs:** [Element: currentCSSZoom property — MDN](https://developer.mozilla.org/en-US/docs/Web/API/Element/currentCSSZoom) (MDN)

### Enhanced HTML select element
- **Recorded state:** on
- **What it gates:** The customizable `<select>` element: opting in with `appearance: base-select` unlocks full CSS styling of the dropdown (new `::picker()`, `::picker-icon`, `::checkmark` pseudo-elements, `<selectedcontent>` element, rich markup inside `<option>`) while keeping native keyboard, screen-reader, validation, and form-submission behavior.
- **Usage:** Testing use — leave at default.
- **Ship status:** Shipped in Safari 27.0
- **Docs:** [WebKit Features for Safari 27.0 — WebKit](https://webkit.org/blog/18325/webkit-features-for-safari-27-0/) (WebKit article)

### Enhanced HTML select element parsing
- **Recorded state:** on
- **Likely behavior:** The parser half of the customizable-select work: modern HTML parsing that preserves arbitrary elements written inside `<select>` (wrappers, `<button>`, `<selectedcontent>`, images) in the DOM instead of dropping non-`<option>`/`<optgroup>`/`<hr>` content as older parsers did. (Inference: no standalone public documentation found; this is the parser counterpart to the "Enhanced HTML select element" rendering flag, shipped with it in Safari 27.0.)
- **Evidence limit:** The visible flag-to-feature relationship has not been publicly documented.
- **Usage:** Testing use — leave at default.
- **Public feature status:** Shipped in Safari 27.0 (customizable select).
- **Visible preference relationship:** Inferred — this flag likely ships with the customizable-select feature; not confirmed.
- **Docs:** [<select>: The HTML Select element — MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/select) (MDN)

### Enumerated ARIA Attribute Reflection
- **Recorded state:** off
- **What it gates:** The ARIA-spec change converting attributes like `aria-checked`, `aria-expanded`, `aria-hidden`, `aria-live`, `aria-selected` (and ~16 more) to enumerated attributes with defined keyword values and invalid/missing-value defaults, reflected through IDL getters/setters. WebKit carries this behind a "status: developer" flag.
- **Usage:** Developer use — experiment with the upcoming enumerated-ARIA reflection behavior for accessibility work.
- **Ship status:** Experimental (developer flag; not shipped)
- **Docs:** [Add feature flag for enumerated ARIA attributes and new ARIA IDL — WebKit PR](https://github.com/WebKit/WebKit/pull/48937) (WebKit impl)

### Event Timing API
- **Recorded state:** on
- **What it gates:** `PerformanceEventTiming` entries observed via `PerformanceObserver` (`{type: "event"}`), reporting input delay (`processingStart - startTime`), handler duration, and `interactionId` — the data needed to measure Interaction to Next Paint (INP).
- **Usage:** Testing use — leave at default.
- **Ship status:** Shipped in Safari 26.2 (per reports quoting Apple's documentation)
- **Docs:** [PerformanceEventTiming — MDN](https://developer.mozilla.org/en-US/docs/Web/API/PerformanceEventTiming) (MDN)

### File System Handle Serialization
- **Recorded state:** off
- **Likely behavior:** Structured serialization of `FileSystemHandle` objects so they can be transferred via `postMessage()` to service workers and shared workers. WebKit reworked this around global (UUID) identifiers registered with the network process to avoid cross-process races; Safari Technology Preview notes track serialization fixes as active work. (Inference: no public-facing feature documentation found; purpose inferred from WebKit change history.)
- **Evidence limit:** The visible flag-to-feature relationship has not been publicly documented.
- **Usage:** Developer use — experiment with passing file-system handles between a page and its workers.
- **Ship status:** Experimental
- **Docs:** [FileSystemHandle: add global identifiers for cross-process serialization — WebKit PR](https://github.com/WebKit/WebKit/pull/64371) (WebKit impl)

### File System WritableStream
- **Recorded state:** on
- **What it gates:** `FileSystemFileHandle.createWritable()`, which returns a `FileSystemWritableFileStream` for writing to a file (write/seek/truncate), with changes committed to disk on `close()` — the write path of the File System API for OPFS files and files from pickers.
- **Usage:** Testing use — leave at default.
- **Ship status:** Shipped in Safari 26 (per browser-compat-data/community reports; previously missing in WebKit)
- **Docs:** [FileSystemFileHandle: createWritable() method — MDN](https://developer.mozilla.org/en-US/docs/Web/API/FileSystemFileHandle/createWritable) (MDN)

### Get(Bounding)ClientRect API zoomed
- **Recorded state:** on
- **What it gates:** Whether `getBoundingClientRect()`/`getClientRects()` return zoom-scaled rects in viewport pixels, per the CSS Viewport spec — replacing WebKit's historical behavior of reporting unzoomed (layout-space) rects under CSS `zoom`, which desynced them from pointer `clientX/Y` and `elementFromPoint()`.
- **Usage:** Testing use — leave at default.
- **Ship status:** Shipped in Safari 26.4 (per community reports; flag retained in 27 — mark as uncertain)
- **Docs:** [fix: support the CSS `zoom` property — floating-ui PR](https://github.com/floating-ui/floating-ui/pull/3492) (Community)

### HTML alpha and colorspace attribute support for color inputs
- **Recorded state:** on
- **What it gates:** The `alpha` (boolean) and `colorspace` (`limited-srgb` / `display-p3`) attributes on `<input type="color">`, letting users adjust opacity and pick colors in the Display P3 wide gamut, with the value serialized as a CSS color — standardized with WHATWG.
- **Usage:** Testing use — leave at default.
- **Ship status:** Shipped in Safari 18.4
- **Docs:** [WebKit Features in Safari 18.4 — WebKit](https://webkit.org/blog/16574/webkit-features-in-safari-18-4/) (WebKit article)

### HTML auto-expanding <details>
- **Recorded state:** on
- **What it gates:** Automatic expansion of closed `<details>` elements when their hidden content matches: a find-in-page ("Find") search, or a text-fragment (`#:~:text=`) navigation targeting text inside them — alongside `hidden=until-found` and the `beforematch` event.
- **Usage:** Testing use — leave at default.
- **Ship status:** Shipped in Safari 26.2
- **Docs:** [WebKit Features for Safari 26.2 — WebKit](https://webkit.org/blog/17640/webkit-features-for-safari-26-2/) (WebKit article)

### HTML command & commandfor attributes
- **Recorded state:** on
- **What it gates:** The Invoker Commands API: `commandfor` (target element id) and `command` (`show-modal`, `close`, `request-close`, `show-popover`, `hide-popover`, `toggle-popover`, plus custom `--*` commands) attributes on `<button>`, declaratively wiring buttons to dialogs and popovers with no JavaScript.
- **Usage:** Testing use — leave at default.
- **Ship status:** Shipped in Safari 26.2
- **Docs:** [WebKit Features for Safari 26.2 — WebKit](https://webkit.org/blog/17640/webkit-features-for-safari-26-2/) (WebKit article)

### HTML popover attribute
- **Recorded state:** on
- **What it gates:** The `popover` global attribute (`auto`/`manual`) plus `popovertarget`/`popovertargetaction` trigger attributes and the `showPopover()`/`hidePopover()`/`togglePopover()` JS API — native top-layer overlays with light-dismiss and focus handling built in.
- **Usage:** Testing use — leave at default.
- **Ship status:** Shipped in Safari 17.0
- **Docs:** [WebKit Features in Safari 17.0 — WebKit](https://webkit.org/blog/14445/webkit-features-in-safari-17-0/) (WebKit article)

### HTML switch control
- **Recorded state:** on
- **What it gates:** The native switch control: `<input type="checkbox" switch>` renders as an OS-styled on/off toggle (ARIA `switch` role, announced "On"/"Off"), degrading gracefully to a plain checkbox where unsupported.
- **Usage:** Testing use — leave at default.
- **Ship status:** Shipped in Safari 17.4
- **Docs:** [An HTML Switch Control — WebKit](https://webkit.org/blog/15054/an-html-switch-control/) (WebKit article)

### Largest Contentful Paint
- **Recorded state:** on
- **What it gates:** `LargestContentfulPaint` performance entries (observed via `PerformanceObserver` with `{type: "largest-contentful-paint"}`), reporting render time, size, element, and URL of the largest visible image/text block — a Core Web Vital.
- **Usage:** Testing use — leave at default.
- **Ship status:** Shipped in Safari 26.2 (per reports quoting Apple's documentation)
- **Docs:** [Performance APIs — MDN](https://developer.mozilla.org/en-US/docs/Web/API/Performance_API) (MDN)

### Navigation API
- **Recorded state:** on
- **What it gates:** The `window.navigation` API for single-page apps: listen for all navigations via the `navigate` event, intercept them into same-document navigations with `event.intercept()` (including precommit handlers), and inspect history entries — replacing History-API-plus-click-listener hacks.
- **Usage:** Testing use — leave at default.
- **Ship status:** Shipped in Safari 26.2
- **Docs:** [WebKit Features for Safari 26.2 — WebKit](https://webkit.org/blog/17640/webkit-features-for-safari-26-2/) (WebKit article)

### Notifications
- **Recorded state:** off
- **What it gates:** The Web Notifications API (`Notification`, `Notification.requestPermission()`) surface in Safari. On iPhone/iPad, web notifications are not available in regular Safari tabs — they work only inside web apps the user added to the Home Screen (iOS/iPadOS 16.4+), where permission is requested after a user gesture. The off default matches iOS Safari tabs not exposing the API.
- **Usage:** Developer use — test notification flows in an iOS Home Screen web app; the API stays unavailable in regular Safari tabs regardless of this flag.
- **Ship status:** Shipped for Home Screen web apps in iOS and iPadOS 16.4. This source does not establish availability in ordinary Safari tabs.
- **Docs:** [WebKit Features in Safari 16.4](https://webkit.org/blog/13966/webkit-features-in-safari-16-4/) (WebKit article)

### Observable API
- **Recorded state:** off
- **What it gates:** The Observable proposal for the web platform: a lazy push-based primitive (`new Observable(subscriber => {...})`, `.subscribe()`, `.map()`/`.filter()` operators) plus `EventTarget.when()`, standardizing reactive event streams without libraries like RxJS.
- **Usage:** Developer use — experiment with the Observable proposal or prototype reactive code against the draft spec.
- **Ship status:** Experimental (shipped only in Chromium 135+; WebKit has a positive standards position and implementation underway, not yet shipped in Safari)
- **Docs:** [Observable API proposal — WICG](https://github.com/WICG/observable) (Community)

### Origin API
- **Recorded state:** on
- **What it gates:** A structured `Origin` object (`Origin.from(value)`) exposing origin information without string parsing, including `isSameSite()` comparisons that work without the Public Suffix List — even for opaque origins from built-in objects like `MessageEvent`, which previously serialized to `"null"`.
- **Usage:** Testing use — leave at default.
- **Ship status:** Shipped in Safari 26.5
- **Docs:** [WebKit Features for Safari 26.5 — WebKit](https://webkit.org/blog/17938/webkit-features-for-safari-26-5/) (WebKit article)

### Permissions API
- **Recorded state:** on
- **What it gates:** `navigator.permissions.query()` for reading the current state (`granted`/`denied`/`prompt`) of powerful-feature permissions without triggering a prompt. Safari's implementation covers camera, microphone, and geolocation permission names; other names throw a `TypeError`.
- **Usage:** Testing use — leave at default.
- **Ship status:** Shipped in Safari 16 (camera, microphone, and geolocation only)
- **Docs:** [Permissions API — MDN](https://developer.mozilla.org/en-US/docs/Web/API/Permissions_API) (MDN)

### Scoped custom element registry
- **Recorded state:** on
- **What it gates:** Constructable `CustomElementRegistry` instances (`new CustomElementRegistry()`) that can be attached per shadow root, so different parts of an app (microfrontends, component libraries) can define the same tag name without colliding on the global `customElements` registry.
- **Usage:** Testing use — leave at default.
- **Ship status:** Shipped in Safari 26.0 (first browser to ship it)
- **Docs:** [WebKit Features in Safari 26.0 — WebKit](https://webkit.org/blog/17333/webkit-features-in-safari-26-0/) (WebKit article)

## 3.4 Workers, service workers, streams & storage

### Allow WebGL in Web Workers
- **Recorded state:** on
- **What it gates:** Allows WebGL/WebGL2 rendering contexts to be created inside Web Workers (e.g. `OffscreenCanvas.getContext('webgl')` in a worker), instead of the main thread only.
- **Usage:** Testing use — leave at default; disable only to test degraded rendering or main-thread-only fallback behavior.
- **Ship status:** **Unverified** — reported shipped in Safari 17 per WebKit bugzilla discussion on bug 183720 (also listed in AllTrails' Safari troubleshooting steps); not confirmed by Apple or WebKit sources.
- **Docs:** [Why am I seeing a MapboxGL error? — AllTrails Help](https://support.alltrails.com/hc/en-us/articles/360051234912-Why-am-I-seeing-a-MapboxGL-error) (Community)

### Enable background-fetch API
- **Recorded state:** off
- **What it gates:** The Background Fetch API: a service worker calls `registration.backgroundFetch.fetch()` to have the browser download large files in a user-visible, cancellable way that survives tab closure and offline periods.
- **Usage:** Developer use — enable to experiment with Background Fetch downloads that survive tab closure and offline periods.
- **Ship status:** Experimental — Safari has not shipped it (WebKit standards position: no signal; Chrome has announced deprecation of its own implementation due to near-zero usage).
- **Docs:** [Background Fetch API — MDN](https://developer.mozilla.org/en-US/docs/Web/API/Background_Fetch_API) (MDN)

### IndexedDB SQLite Memory Backing Store
- **Recorded state:** on
- **What it gates:** An internal engine change: IndexedDB in private/ephemeral browsing sessions uses SQLite's in-memory database (`:memory:`) instead of WebKit's custom in-memory backing store, giving ephemeral IndexedDB the same ACID guarantees and SQL query engine as persistent storage. Controlled by the `IndexedDBSQLiteMemoryBackingStoreEnabled` preference.
- **Usage:** Internal only — do not change unless debugging WebKit itself.
- **Ship status:** Experimental — landed in WebKit main in February 2026 (PR 58216); not announced in any Safari release notes.
- **Docs:** [WebKit PR #58216: Port IndexedDB Memory Backing Store to SQLite In-Memory Database](https://github.com/WebKit/WebKit/pull/58216) (WebKit impl)

### IndexedDB getAllRecords() API and IDBGetAllOptions
- **Recorded state:** off
- **What it gates:** The new `getAllRecords()` method on `IDBObjectStore`/`IDBIndex`, which returns record snapshots containing key, primary key, and value in one pass, plus an `IDBGetAllOptions` dictionary that adds a direction option (ascending/descending) to `getAll()` and `getAllKeys()`.
- **Usage:** Developer use — enable to experiment with getAllRecords() bulk reads and IDBGetAllOptions direction on getAll()/getAllKeys().
- **Ship status:** **Unverified** — reported shipped in Safari Technology Preview 242 (Chrome 141 and Firefox 153 shipped it); the Safari 27 flag is off and Apple has not announced it; not confirmed by Apple or WebKit sources.
- **Docs:** [Intent to ship: IndexedDB getAllRecords() and IDBGetAllOptions — Mozilla dev-platform](https://groups.google.com/a/mozilla.org/g/dev-platform/c/yJLlEvNHws0/m/J5uyxljBCwAJ) (Community)

### Link rel=preconnect via HTTP early hints
- **Recorded state:** on
- **What it gates:** Acting on `Link: <origin>; rel=preconnect` headers sent in an HTTP 103 Early Hints response, so Safari opens connections to critical origins (fonts, APIs, CDNs) before the final page response arrives.
- **Usage:** Testing use — leave at default; disable only to test page load without 103 Early Hints preconnect behavior.
- **Ship status:** Shipped in Safari 17 (listed as a new [on] flag in Safari 17-era release coverage).
- **Docs:** [103 Early Hints — MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status/103) (MDN)

### LinkPrefetch
- **Recorded state:** off
- **Likely behavior:** Classic link prefetching (`<link rel="prefetch">` and `Link` header hints) — downloading resources the user is likely to need next, ahead of navigation.
- **Evidence limit:** The visible flag-to-feature relationship has not been publicly documented.
- **Usage:** Developer use — enable to experiment with speculative prefetching of likely-next documents or subresources.
- **Ship status:** **Unverified** — reported experimental: Safari does not support standard link prefetching (support tables list Safari: No; the WebKit implementation request, bug 194539, remains open); not confirmed by Apple or WebKit sources.
- **Docs:** [Link prefetching — Wikipedia](https://en.wikipedia.org/wiki/Link_prefetching) (Community)

### Load Web Archive with ephemeral storage
- **Recorded state:** on
- **Likely behavior:** Undocumented experimental flag — purpose not publicly documented. Likely an engine-internal behavior for opening saved web archives (.webarchive) inside an ephemeral website-data store so archived pages render without persisting cookies, storage, or cache — but this is inference, not confirmed.
- **Evidence limit:** The visible flag-to-feature relationship has not been publicly documented.
- **Usage:** No confirmed developer use — purpose undocumented; no official documentation found.
- **Ship status:** Unknown.
- **Docs:** No official documentation found.

### MediaSource in a Worker
- **Recorded state:** on
- **What it gates:** Constructing and driving `MediaSource` (via `MediaSourceHandle`) inside a dedicated worker, so adaptive-bitrate players can demux and append media segments off the main thread and hand the handle to a `<video>` element.
- **Usage:** Testing use — leave at default; disable only to test MSE behavior without the worker path.
- **Ship status:** **Unverified** — reported shipped in Safari 18 (Safari 18 release notes quoted in a w3c/media-source PR discussion: "Added support for MSE in workers"); not confirmed by Apple or WebKit sources.
- **Docs:** [w3c/media-source PR #329 discussion, quoting the Safari 18 release notes](https://github.com/w3c/media-source/pull/329) (Community)

### OffscreenCanvas in Workers
- **Recorded state:** on
- **What it gates:** 3D rendering contexts (WebGL/WebGL2) inside worker-side `OffscreenCanvas`. The base `OffscreenCanvas` API (2D) shipped in Safari 16.4; this flag covers the worker 3D-context path, which is a separate, younger code path.
- **Usage:** Testing use — leave at default; disable only to test worker-3D fallback or degraded rendering behavior.
- **Ship status:** Shipped in Safari 17 (uncertain — WebKit bug 183720 discussion indicates Safari 17.0+ supports WebGL in worker OffscreenCanvas, with OS-version caveats; no Apple announcement located).
- **Docs:** [WebKit Features in Safari 16.4](https://webkit.org/blog/13966/webkit-features-in-safari-16-4/) (WebKit article)

### OffscreenCanvas
- **Recorded state:** on
- **What it gates:** The base `OffscreenCanvas` API: a DOM-detached canvas usable on the main thread and transferable to workers via `transferControlToOffscreen()`, with 2D contexts (3D arrived later per the flag above).
- **Usage:** Testing use — leave at default; disable only to test fallback behavior without OffscreenCanvas.
- **Ship status:** Shipped in Safari 16.4 (2D operations).
- **Docs:** [WebKit Features in Safari 16.4](https://webkit.org/blog/13966/webkit-features-in-safari-16-4/) (WebKit article)

### ReadableByteStream for fetch API
- **Recorded state:** on
- **What it gates:** Fetch request and response bodies being byte streams under the hood (`response.body` is a `ReadableByteStream`), enabling BYOB ("bring your own buffer") readers for efficient zero-copy handling of binary responses.
- **Usage:** Testing use — leave at default; disable only to test non-byte-stream fetch body handling.
- **Ship status:** Shipped in Safari 26.4 (WebKit: "adds support for using byte streams as fetch request and response bodies, and for reading Blob.stream() with a BYOB reader").
- **Docs:** [WebKit Features for Safari 26.4](https://webkit.org/blog/17862/webkit-features-for-safari-26-4/) (WebKit article)

### ReadableByteStream
- **Recorded state:** on
- **What it gates:** The `ReadableByteStream` interface itself — byte-oriented streams in the Streams API, designed for binary data (files, network responses, media) with BYOB readers that let you supply your own `ArrayBuffer`.
- **Usage:** Testing use — leave at default; disable only to test fallback for binary streams without BYOB support.
- **Ship status:** Shipped in Safari 26.4 ("completing the implementation of byte-oriented streams in the Streams API").
- **Docs:** [WebKit Features for Safari 26.4](https://webkit.org/blog/17862/webkit-features-for-safari-26-4/) (WebKit article)

### ReadableStream async iterable
- **Recorded state:** on
- **What it gates:** `ReadableStream` implementing `Symbol.asyncIterator`, so streams can be consumed with `for await (const chunk of stream)` instead of a manual `getReader()` pump loop.
- **Usage:** Testing use — leave at default; disable only to test code paths that avoid `for await...of` stream consumption.
- **Ship status:** Shipped in Safari 27.
- **Docs:** [WebKit Features for Safari 27.0](https://webkit.org/blog/18325/webkit-features-for-safari-27-0/) (WebKit article)

### ReadableStream.from
- **Recorded state:** on
- **What it gates:** The `ReadableStream.from()` static method, which creates a `ReadableStream` from any sync or async iterable (arrays, sets, async generators, other streams).
- **Usage:** Testing use — leave at default; disable only to test fallback construction of streams from iterables.
- **Ship status:** Shipped in Safari 27.
- **Docs:** [WebKit Features for Safari 27.0](https://webkit.org/blog/18325/webkit-features-for-safari-27-0/) (WebKit article)

### ReadableStream/WritableStream/TransformStream transfer
- **Recorded state:** on
- **What it gates:** Streams as transferable objects — a live `ReadableStream`, `WritableStream`, or `TransformStream` can be moved between contexts (window ↔ worker) via `postMessage(stream, [stream])`.
- **Usage:** Testing use — leave at default; disable only to test behavior without cross-context stream transfer.
- **Ship status:** Shipped in Safari 27.
- **Docs:** [WebKit Features for Safari 27.0](https://webkit.org/blog/18325/webkit-features-for-safari-27-0/) (WebKit article)

### Service Worker Install Event
- **Recorded state:** on
- **Likely behavior:** The `InstallEvent` interface for the service worker `install` event, including the `addRoutes()` method used by the Service Worker static routing API to declare routes the browser may bypass the worker for entirely.
- **Evidence limit:** The visible flag-to-feature relationship has not been publicly documented.
- **Usage:** Testing use — leave at default; disable only to test fallback without the static routing API.
- **Public feature status:** Shipped in Safari 27 (static routing API).
- **Visible preference relationship:** Inferred — the static routing API was announced for Safari 27; the exact flag-to-`InstallEvent` mapping is not confirmed.
- **Docs:** [WebKit Features for Safari 27.0](https://webkit.org/blog/18325/webkit-features-for-safari-27-0/) (WebKit article)

### Service Worker Navigation Preload
- **Recorded state:** on
- **What it gates:** `NavigationPreloadManager`: when enabled via `registration.navigationPreload.enable()`, the browser fires the navigation request in parallel with service worker startup (sending the `Service-Worker-Navigation-Preload` header), so the worker isn't a cold-start bottleneck.
- **Usage:** Testing use — leave at default; disable only to test cold-start behavior without navigation preload.
- **Ship status:** Shipped in Safari 15.4.
- **Docs:** [New WebKit Features in Safari 15.4](https://webkit.org/blog/12445/new-webkit-features-in-safari-15-4/) (WebKit article)

### Service Workers
- **Recorded state:** on
- **What it gates:** The master switch for the Service Worker API — registration, install/activate/fetch lifecycle, and the Cache API used by workers.
- **Usage:** Testing use — leave at default; disabling removes the entire Service Worker API from the test environment.
- **Ship status:** Shipped in Safari 11.1.
- **Docs:** [New WebKit Features in Safari 11.1](https://webkit.org/blog/8216/new-webkit-features-in-safari-11-1/) (WebKit article)

### SharedWorker
- **Recorded state:** on
- **What it gates:** The `SharedWorker` constructor: one background script shared across all same-origin tabs, iframes, and workers, each connecting through a `MessagePort`.
- **Usage:** Testing use — leave at default; disable only to test fallback without shared workers.
- **Ship status:** Shipped in Safari 16.0.
- **Docs:** [WebKit Features in Safari 16.0](https://webkit.org/blog/13152/webkit-features-in-safari-16-0/) (WebKit article)

### SpeculationRules prefetch
- **Recorded state:** off
- **What it gates:** Speculation Rules prefetch (`<script type="speculationrules">` with `prefetch` rules). WebKit's initial implementation (PR #48322, Oct 2025) covers same-origin prefetch only, with `immediate` and `conservative` eagerness — no prerender, no subresource preloading.
- **Usage:** Developer use — enable to test speculative same-origin document prefetching (immediate/conservative eagerness) for likely-next navigations.
- **Ship status:** Experimental — implemented in WebKit but off by default; absent from Safari release notes (WebKit standards position still "needs position").
- **Docs:** [WebKit PR #48322: Implement speculation rules — same origin conservative prefetch](https://github.com/WebKit/WebKit/pull/48322) (WebKit impl)

### Storage API Estimate
- **Recorded state:** on
- **What it gates:** `navigator.storage.estimate()` — returns the origin's `usage`, `quota` (and `usageDetails` breakdown) so pages can measure their storage budget.
- **Usage:** Testing use — leave at default; disable only to test fallback for apps that read storage usage/quota.
- **Ship status:** Shipped in Safari 17 (uncertain — the flag appeared as newly-on in Safari 17-era coverage; MDN marks `estimate()` Baseline widely available).
- **Docs:** [StorageManager: estimate() method — MDN](https://developer.mozilla.org/en-US/docs/Web/API/StorageManager/estimate) (MDN)

### Storage API
- **Recorded state:** on
- **What it gates:** The Storage API surface on `navigator.storage` — principally `persist()` / `persisted()` for requesting protection from eviction under storage pressure, alongside `estimate()`.
- **Usage:** Testing use — leave at default; disable only to test behavior without persist()/persisted() durability APIs.
- **Ship status:** Shipped in Safari 17 (uncertain — persist support documented in Safari 17-era sources; flag is on in Safari 27).
- **Docs:** [StorageManager: estimate() method — MDN](https://developer.mozilla.org/en-US/docs/Web/API/StorageManager/estimate) (MDN)

### Web Locks API
- **Recorded state:** on
- **What it gates:** `navigator.locks.request()` / `query()` — asynchronous mutual-exclusion locks shared across tabs, windows, iframes, and workers of an origin.
- **Usage:** Testing use — leave at default; disable only to test fallback coordination without navigator.locks.
- **Ship status:** Shipped in Safari 15.4.
- **Docs:** [New WebKit Features in Safari 15.4](https://webkit.org/blog/12445/new-webkit-features-in-safari-15-4/) (WebKit article)

### Worker in SharedWorker
- **Recorded state:** off
- **What it gates:** Creating dedicated workers from inside a shared worker (nested workers), per the HTML Standard.
- **Usage:** Developer use — enable to experiment with nested dedicated workers created inside a shared worker.
- **Ship status:** Experimental — added in Safari Technology Preview 244; still off by default in Safari 27.
- **Docs:** [Release Notes for Safari Technology Preview 244](https://webkit.org/blog/17962/release-notes-for-safari-technology-preview-244/) (WebKit article)

### Worker parse error reporting
- **Recorded state:** off
- **What it gates:** Reporting of worker script parse errors back to the parent page (so a syntax error in a worker script surfaces as an error on the constructing context rather than failing silently). Safari Technology Preview 244 fixed "parse errors in workers were not reported to the parent"; this flag gates that behavior.
- **Usage:** Developer use — enable to surface worker script syntax errors on the parent page while debugging.
- **Ship status:** Experimental — off by default in Safari 27 (the reporting fix landed in STP 244).
- **Docs:** [Release Notes for Safari Technology Preview 244](https://webkit.org/blog/17962/release-notes-for-safari-technology-preview-244/) (WebKit article)

## 3.5 Media: audio, video & capture

### Audio descriptions for video - Extended
- **Recorded state:** on
- **What it gates:** Enables synthesized audio descriptions for `<video>` elements using the text-to-speech engine to speak WebVTT `kind="descriptions"` cues. When Extended is on, playback is automatically paused if speaking a cue's text takes longer than the cue's duration, and resumes when the utterance finishes. Also requires enabling Audio Descriptions in system Accessibility settings.
- **Usage:** Testing use — leave at default; turn off only to test video playback without extended description pauses.
- **Ship status:** Experimental.
- **Docs:** [AX: Feature request: Support WebVTT-based synthesized audio description in video](https://webkit.org/b/266724) (WebKit impl); [AD Support in HTML Video](https://adrianroselli.com/2023/12/ad-support-in-html-video.html) (Community).

### Audio descriptions for video - Standard
- **Recorded state:** on
- **What it gates:** Same synthesized text-to-speech audio description support for `<video>` (WebVTT `kind="descriptions"` cues), but playback is NOT auto-paused if the speech engine hasn't finished a cue by its end time. Requires the system Accessibility "Play audio descriptions when available" setting.
- **Usage:** Testing use — leave at default; turn off only to test playback with standard (non-pausing) description behavior.
- **Ship status:** Experimental.
- **Docs:** [AX: Feature request: Support WebVTT-based synthesized audio description in video](https://webkit.org/b/266724) (WebKit impl).

### AudioSession full API
- **Recorded state:** off
- **What it gates:** Gates the full Audio Session web API beyond the partial `navigator.audioSession.type` that shipped in Safari 16.4 (e.g. the still-missing `state` member, tracked in WebKit's bug tracker). Underlying support was built out during 2024–2025 but remains behind this flag.
- **Usage:** Developer use — experimenting with web-exposed audio-session state and type beyond the shipped subset.
- **Ship status:** Experimental.
- **Docs:** No official documentation found.

### Detachable Media Source
- **Recorded state:** off
- **What it gates:** Allows constructing a `MediaSource` (or `ManagedMediaSource`) that is *detachable*: detaching it from a media element no longer destroys its source buffers and content, so it can be re-attached later without re-fetching everything. The feature shipped in Safari 26.0.
- **Usage:** Developer use — building seamless player switching (e.g. ad insertion) with MSE where the source should survive being detached from a `<video>` element.
- **Ship status:** Shipped in Safari 26 (flag recorded off on this build). Apple documents the public API as shipped; the recorded toggle is off, so this preference likely controls a narrower code path, a subfeature, or platform-specific behavior — the exact flag-to-API relationship was not confirmed.
- **Docs:** [Provide option to "detach" a MediaSource element in a non-destructive fashion](https://github.com/WebKit/WebKit/pull/33773) (WebKit impl); [WebKit Features in Safari 26.0](https://webkit.org/blog/17333/webkit-features-in-safari-26-0/) (WebKit article).

### Find in Video
- **Recorded state:** off
- **What it gates:** Includes WebVTT caption/subtitle cues in Find-in-Page on macOS: Cmd+F matches against dialogue text, and navigating to a cue match seeks the video to the cue's start time and scrolls it into view.
- **Usage:** Developer use — testing caption search in media-heavy pages.
- **Ship status:** Experimental (macOS-only behavior).
- **Docs:** [Include video caption cues in Find-in-Page navigation on macOS](https://github.com/WebKit/WebKit/pull/67687) (WebKit impl).

### HDR Accelerated Apply GainMap
- **Recorded state:** on
- **What it gates:** Internal rendering flag (stable status): applies ISO 21496 gain maps (SDR base image + gain map, reconstructed to HDR) on the GPU-accelerated path when rendering HDR images. Related to the HDR image support that shipped in Safari 26.0.
- **Usage:** Internal only — do not change unless debugging WebKit's HDR rendering pipeline.
- **Ship status:** Shipped in Safari 26 (uncertain — internal stable flag; HDR image support shipped in Safari 26).
- **Docs:** [WebKit Features in Safari 26.0](https://webkit.org/blog/17333/webkit-features-in-safari-26-0/) (WebKit article).

### Image Capture API
- **Recorded state:** on
- **What it gates:** Exposes the MediaStream Image Capture API (`ImageCapture`, `takePhoto()`, `grabFrame()`, `getPhotoCapabilities()`, `getPhotoSettings()`) for full-resolution still capture from a camera track.
- **Usage:** Testing use — leave at default; turn off only to test pages without Image Capture support.
- **Ship status:** Shipped in Safari 18.4.
- **Docs:** [WebKit Features in Safari 18.4](https://webkit.org/blog/16574/webkit-features-in-safari-18-4/) (WebKit article).

### Limited Matroska Support
- **Recorded state:** off
- **What it gates:** Partial Matroska/WebM support for web compatibility: allows H.264 video and PCM audio inside WebM containers in both the WebM player and `MediaRecorder` (e.g. `video/webm;codecs=h264,pcm`).
- **Usage:** Developer use — testing H.264/PCM-in-WebM recording or playback.
- **Ship status:** Experimental.
- **Docs:** [Support video/webm;codecs=h264,pcm in MediaRecorder](https://github.com/WebKit/WebKit/pull/39443) (WebKit impl).

### Mac Inline Media Controls Specs
- **Recorded state:** on
- **What it gates:** Adopts new size specifications for the inline (non-fullscreen) media controls UI on macOS (status preview). Internal UI styling flag with no web-exposed API.
- **Usage:** Internal only — do not change unless debugging WebKit's media-controls styling.
- **Ship status:** Experimental (macOS-only internal UI flag).
- **Docs:** No official documentation found.

### MediaRecorder WebM
- **Recorded state:** on
- **What it gates:** Adds WebM output to `MediaRecorder` using the Opus audio codec with VP8 or VP9 video (e.g. `new MediaRecorder(stream, { mimeType: 'video/webm' })`).
- **Usage:** Testing use — leave at default; turn off only to test MediaRecorder without WebM output.
- **Ship status:** Shipped in Safari 18.4.
- **Docs:** [WebKit Features in Safari 18.4](https://webkit.org/blog/16574/webkit-features-in-safari-18-4/) (WebKit article).

### MediaSession capture related API
- **Recorded state:** on
- **What it gates:** The MediaSession capture mute API (shipped in Safari 18.4): pages can detect camera/microphone/screenshare mute state via specific MediaSession actions and request mute/unmute via dedicated methods (`setCameraActive`, `setMicrophoneActive`, `setScreenshareActive`); unmuting requires user activation.
- **Usage:** Testing use — leave at default; turn off only to test conferencing UIs without capture-mute detection.
- **Ship status:** Shipped in Safari 18.4.
- **Docs:** [WebKit Features in Safari 18.4](https://webkit.org/blog/16574/webkit-features-in-safari-18-4/) (WebKit article); [[Cocoa] Enable MediaSession capture mute API by default](https://github.com/WebKit/WebKit/pull/34779) (WebKit impl).

### MediaSession extended actions
- **Recorded state:** off
- **What it gates:** Exposes additional MediaSession action handlers beyond the standard set (play, pause, seek, track): `hangup`, `previousslide`, `nextslide`, and `enterpictureinpicture`. Used by communication and presentation apps to receive hardware/remote-control events.
- **Usage:** Developer use — testing non-standard media-session actions (e.g. call hangup) beyond play/pause/seek/track.
- **Ship status:** Experimental.
- **Docs:** No official documentation found.

### MediaSource prefers DecompressionSession
- **Recorded state:** on
- **What it gates:** Internal MSE playback preference: routes decoding through WebKit's `WebCoreDecompressionSession` path (rather than handing compressed samples straight to `AVSampleBufferDisplayLayer`), improving power and frame-pacing behavior. Enabled by default since Safari Technology Preview 213.
- **Usage:** Internal only — do not change unless debugging WebKit's MSE decode path.
- **Ship status:** Shipped in Safari 26 (enabled by default in STP 213).
- **Docs:** [Release Notes for Safari Technology Preview 213](https://webkit.org/blog/16461/release-notes-for-safari-technology-preview-213/) (WebKit article); [WebKit Features in Safari 26.0](https://webkit.org/blog/17333/webkit-features-in-safari-26-0/) (WebKit article).

### MediaStreamTrackHandle
- **Recorded state:** off
- **What it gates:** Exposes the transferable `MediaStreamTrackHandle` proxy (status preview) from the MediaStreamTrack Insertable Media Processing spec: lets a worker-side `MediaStreamTrackProcessor` act as a sink for a `MediaStreamTrack` living in another context.
- **Usage:** Developer use — experimenting with cross-context track processing in workers (insertable streams).
- **Ship status:** Experimental.
- **Docs:** [MediaStreamTrack Insertable Media Processing using Streams](https://www.w3.org/TR/mediacapture-transform) (Spec).

### Prefers most visible video in element fullscreen.
- **Recorded state:** on
- **What it gates:** Internal UI heuristic for Safari's fullscreen presentation UI: when multiple videos are visible during element fullscreen, the UI (docking, immersive/spatial buttons) selects the most-visible video (filling over 25% of the web view area) rather than the NowPlaying-eligible one, decoupling it from the old audible-track requirement.
- **Usage:** Internal only — do not change unless debugging WebKit's fullscreen presentation UI.
- **Ship status:** Experimental.
- **Docs:** [Spatial Video does not show Immersive or View Spatial button when muted or lacks audio track](https://github.com/WebKit/WebKit/pull/41509) (WebKit impl).

### Shape Detection API
- **Recorded state:** off
- **What it gates:** Exposes the Shape Detection API (`BarcodeDetector`, `TextDetector`, `FaceDetector`) for hardware-accelerated detection of barcodes, text, and faces in images/video frames. Flagged in Safari since Safari 17 per browser-compat-data.
- **Usage:** Developer use — prototyping barcode/text/face detection without a JS/WASM fallback.
- **Ship status:** Experimental.
- **Docs:** [Verify and consolidate feature flags across BCD](https://github.com/mdn/browser-compat-data/issues/30010) (Community).

### Show Media Stats
- **Recorded state:** off
- **What it gates:** With the Develop menu enabled, adds a "Show Media Stats" item to the `<video>` context menu, overlaying technical details (source type, size, performance metrics, resolution, codec string, color configuration) useful for crafting `MediaCapabilities` queries. Also depends on the Track Configuration pref.
- **Usage:** Developer use — debugging which codec/configuration a video actually plays with.
- **Ship status:** Unknown (believed shipped around Safari 17; not verified).
- **Docs:** [[Modern Media Controls] add a way to show stats about the `<video>`](https://github.com/WebKit/WebKit/pull/3463) (WebKit impl).

### Support HDR Compositor tonemapping
- **Recorded state:** off
- **What it gates:** Internal rendering flag (status testable): enables HDR tone mapping during compositing — how HDR layers are tone-mapped into the composited page, e.g. when `dynamic-range-limit: standard` is applied. No web-exposed API.
- **Usage:** Internal only — do not change unless debugging WebKit's HDR compositing path.
- **Ship status:** Experimental.
- **Docs:** [[HDR] Tonemap the HDR image when "dynamic-range-limit: standard;" is applied](https://github.com/WebKit/WebKit/pull/44874) (WebKit impl).

### Support HDR Display
- **Recorded state:** on
- **What it gates:** Enables HDR display output support in the rendering pipeline (extended-dynamic-range presentation of HDR content), related to the HDR image support shipped in Safari 26.0. Internal pipeline flag.
- **Usage:** Internal only — do not change unless debugging WebKit's HDR rendering pipeline.
- **Ship status:** Unknown (HDR image display shipped in Safari 26; flag-specific status not publicly documented).
- **Docs:** [WebKit Features in Safari 26.0](https://webkit.org/blog/17333/webkit-features-in-safari-26-0/) (WebKit article).

### Text Tracks in MSE
- **Recorded state:** off
- **What it gates:** Experimental support for in-band text tracks in Media Source Extensions (e.g. WebVTT carried inside MP4/WebM segments, surfaced as text tracks). Safari 26 announced in-band tracks in MSE, but this flag was recorded off on this build, so its exact coverage is uncertain.
- **Usage:** Developer use — testing caption tracks delivered inside MSE segments.
- **Ship status:** Unknown — Safari 26 announced in-band tracks in MSE, but this flag was recorded off on the device, so its exact coverage is uncertain.
- **Docs:** [[GStreamer][MSE] Support text tracks in MSE](https://github.com/WebKit/WebKit/pull/41012) (WebKit impl); [WebKit Features in Safari 26.0](https://webkit.org/blog/17333/webkit-features-in-safari-26-0/) (WebKit article).

### Track Configuration API
- **Recorded state:** off
- **What it gates:** WebKit explainer-stage API exposing per-track configuration details (e.g. codec, dimensions) of media tracks to web content, beyond what `MediaCapabilities` provides. Still pre-standardization.
- **Usage:** Developer use — experimenting with track-level media introspection per the WebKit explainer.
- **Ship status:** Experimental.
- **Docs:** [WebKit/explainers — TrackConfiguration API](https://github.com/webkit/explainers) (WebKit impl).

### Use Microphone Mute Status API
- **Recorded state:** off
- **What it gates:** Internal capture-behavior flag: while capturing the microphone, listens for system microphone mute-status notifications (e.g. pressing AirPods stems) and toggles the page's microphone capture mute state accordingly. No web-exposed API.
- **Usage:** Internal only — do not change unless debugging WebKit's capture behavior.
- **Ship status:** Experimental.
- **Docs:** [Add support for AirPods mute API](https://github.com/WebKit/WebKit/pull/33469) (WebKit impl).

### VideoMediaSampleRenderer fallback to AVSBDL for protected content disabled
- **Recorded state:** on
- **What it gates:** Internal pipeline flag: disables the fallback that enqueues compressed samples directly to `AVSampleBufferDisplayLayer` for protected (encrypted) content, keeping WebKit's `WebCoreDecompressionSession` decode path instead. (Added so the original AVSBDL fallback could be restored if needed.)
- **Usage:** Internal only — do not change unless debugging WebKit's protected-content decode path.
- **Ship status:** Unknown (stable-status internal flag).
- **Docs:** [VideoMediaSampleRenderer doesn't handle going from clear to protected content](https://github.com/WebKit/WebKit/pull/45056) (WebKit impl).

### VideoMediaSampleRenderer use DecompressionSession for protected content
- **Recorded state:** off
- **What it gates:** Internal pipeline flag (status preview): routes protected-content (encrypted) video decoding through `WebCoreDecompressionSession` instead of handing compressed samples to `AVSampleBufferDisplayLayer`. Related to the protected-content fallback preference added alongside it.
- **Usage:** Internal only — do not change unless debugging WebKit's protected-content decode path.
- **Ship status:** Experimental.
- **Docs:** [VideoMediaSampleRenderer doesn't handle going from clear to protected content](https://github.com/WebKit/WebKit/pull/45056) (WebKit impl).

### WebCodecs Audio API
- **Recorded state:** on
- **What it gates:** Exposes WebCodecs `AudioEncoder` and `AudioDecoder` for low-level encode/decode of `AudioData`/`EncodedAudioChunk`. Enabled by default since Safari Technology Preview 213; announced for Safari 26.0.
- **Usage:** Testing use — leave at default; turn off only to test custom audio pipelines without WebCodecs.
- **Ship status:** Shipped in Safari 26.
- **Docs:** [Release Notes for Safari Technology Preview 213](https://webkit.org/blog/16461/release-notes-for-safari-technology-preview-213/) (WebKit article); [WebKit Features in Safari 26.0](https://webkit.org/blog/17333/webkit-features-in-safari-26-0/) (WebKit article).

### WebCodecs AV1 codec
- **Recorded state:** off
- **What it gates:** Enables the AV1 codec in WebCodecs encode/decode (status preview; actual availability depends on hardware decode support). Without it, WebCodecs `AudioEncoder`/`VideoEncoder` configs specifying AV1 fail as unsupported.
- **Usage:** Developer use — testing AV1 through WebCodecs where hardware decode exists.
- **Ship status:** Experimental.
- **Docs:** No official documentation found.

## 3.6 Graphics: canvas, WebGL, WebGPU & animation

### Canvas Color Spaces
- **Recorded state:** on
- **What it gates:** Lets a page request a non-sRGB color space when creating a canvas 2D context (e.g. `canvas.getContext('2d', { colorSpace: 'display-p3' })`), so shapes, text, gradients and shadows can render in wide gamut Display P3 instead of sRGB.
- **Usage:** Testing use — on by default and shipped in Safari 15.2; leave at default, toggling off only to test sRGB fallback rendering.
- **Ship status:** Shipped in Safari 15.2 (wide gamut); Safari 27 added linear-light spaces `srgb-linear` and `display-p3-linear` to Canvas, WebGL and other APIs.
- **Docs:** [WebKit Features for Safari 27.0](https://webkit.org/blog/18325/webkit-features-for-safari-27-0/) (WebKit article)

### Canvas Color Types and ImageData Pixel Formats
- **Recorded state:** off
- **Likely behavior:** Experimental support for alternate ImageData pixel formats/color types beyond the standard 8-bit RGBA — i.e. the `colorType` member of the `ImageDataSettings` dictionary (`rgba-unorm8` vs `rgba-float16` half-float pixel data). This is an inference from the spec surface and WebKit's flag naming; WebKit has not publicly documented which formats this enables.
- **Evidence limit:** The visible flag-to-feature relationship has not been publicly documented.
- **Usage:** No confirmed developer use — purpose undocumented; leave at default.
- **Ship status:** Experimental (uncertain which Safari version, if any, will expose it).
- **Docs:** No official documentation found.

### Canvas Filters
- **Recorded state:** off
- **What it gates:** The `CanvasRenderingContext2D.filter` property, which applies a CSS or SVG filter (e.g. `ctx.filter = 'blur(5px)'`) to everything subsequently drawn on a 2D canvas. WebKit historically does not implement this property (`'filter' in ctx` is false in Safari).
- **Usage:** Developer use — enable to test per-draw `ctx.filter` effects ahead of any shipped Safari support.
- **Ship status:** Experimental — not shipped as of Safari 27.
- **Docs:** [CanvasRenderingContext2D - Web APIs](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D) (MDN)

### Canvas Layers
- **Recorded state:** off
- **What it gates:** The `beginLayer()` / `endLayer()` canvas 2D API, which lets drawing be isolated into a compositing layer (with its own filter, alpha, composite operation and shadow state) that is composited back onto the canvas as a unit — per the WHATWG HTML canvas-layers proposal.
- **Usage:** Developer use — enable to test grouped canvas drawing with per-layer filters and compositing (experimental draft API).
- **Ship status:** Experimental — draft API, not shipped.
- **Docs:** [WebKit PR #72022 — Support CanvasRenderingContext2D layers](https://github.com/WebKit/WebKit/pull/72022) (WebKit impl)

### Enable Canvas fingerprinting-related quirk
- **Recorded state:** on
- **What it gates:** A page-targeted compatibility quirk for Safari's canvas anti-fingerprinting noise injection. Sites whose login flows depend on canvas fingerprinting (e.g. fedex.com, walgreens.com) broke under noise injection, so on affected pages WebKit returns a fixed substitute image data URL instead of the noise-modified rendering.
- **Usage:** Internal only — page-targeted compatibility quirk applied automatically per site; do not change unless debugging WebKit itself.
- **Ship status:** Shipped (added 2023, still on by default).
- **Docs:** [WebKit PR #16067 — Add page-targeted quirk for Canvas2D noise injection](https://github.com/WebKit/WebKit/pull/16067) (WebKit impl)

### GPU Process: Canvas Rendering
- **Recorded state:** on
- **What it gates:** Routes canvas rendering (2D and WebGL) through WebKit's isolated GPU process instead of the web content process, improving security and keeping graphics-intensive work off the content process.
- **Usage:** Internal only — internal GPU process pipeline switch; do not change unless debugging WebKit itself.
- **Ship status:** Shipped (required together with the WebGPU flag during Safari Technology Preview 185 testing; now default-on).
- **Docs:** [WebGPU now available for testing in Safari Technology Preview](https://webkit.org/blog/14879/webgpu-now-available-for-testing-in-safari-technology-preview/) (WebKit article)

### GPU Process: DOM Rendering
- **Recorded state:** on
- **What it gates:** Routes general DOM page rendering through WebKit's isolated GPU process instead of the web content process, separating graphics handling from web content for security and performance.
- **Usage:** Internal only — internal GPU process pipeline switch; do not change unless debugging WebKit itself.
- **Ship status:** Shipped (default-on; originally an experimental flag alongside GPU Process: Canvas Rendering).
- **Docs:** [WebGPU now available for testing in Safari Technology Preview](https://webkit.org/blog/14879/webgpu-now-available-for-testing-in-safari-technology-preview/) (WebKit article)

### GraphicsContext Filter Rendering
- **Recorded state:** on
- **What it gates:** Undocumented experimental flag — purpose not publicly documented. (Name suggests an internal switch for the rendering pipeline path used to apply CSS/SVG filter effects in WebKit's GraphicsContext, but no public source confirms this.)
- **Usage:** No confirmed developer use — purpose undocumented; leave at default.
- **Ship status:** Unknown.
- **Docs:** No official documentation found.

### HTML `<model>` element
- **Recorded state:** on
- **What it gates:** The `<model>` HTML element, which embeds interactive 3D content (USDZ/GLB, e.g. `<model src="teapot.usdz">`) directly in a page — like `img`/`video` but for 3D models, with `environmentmap`/`stagemode` attributes and a JavaScript playback API.
- **Usage:** Testing use — on by default and shipped in Safari 27.0; leave at default, toggling off only to test fallback behavior.
- **Ship status:** Shipped in Safari 27.0 (previously visionOS-only in Safari 26).
- **Docs:** [WebKit Features for Safari 27.0](https://webkit.org/blog/18325/webkit-features-for-safari-27-0/) (WebKit article)

### HTML `<model>` elements in stand-alone documents
- **Recorded state:** off
- **What it gates:** Undocumented experimental flag — purpose not publicly documented. (Name suggests support for rendering `<model>` elements inside browser-generated stand-alone documents, e.g. when navigating directly to a 3D model file, mirroring how images/video get stand-alone documents — but this could not be confirmed.)
- **Usage:** No confirmed developer use — purpose undocumented; leave at default.
- **Ship status:** Unknown.
- **Docs:** No official documentation found.

### Gamepad trigger vibration support
- **Recorded state:** off
- **What it gates:** The `"trigger-rumble"` effect type for `GamepadHapticActuator.playEffect()` — vibrating the gamepad's triggers (as Xbox controllers support) in addition to the standard `"dual-rumble"` handle vibration. Added behind an experimental flag, off by default.
- **Usage:** Developer use — enable to test trigger-rumble haptics on controllers whose triggers support it.
- **Ship status:** Experimental — not shipped as a default; behind this flag.
- **Docs:** [WebKit PR #9075 — Add support for "trigger-rumble" effect type](https://github.com/WebKit/WebKit/pull/9075) (WebKit impl)

### Gamepad.vibrationActuator support
- **Recorded state:** off
- **What it gates:** Exposure of the `Gamepad.vibrationActuator` property, which returns a `GamepadHapticActuator` for playing rumble effects (e.g. `playEffect('dual-rumble', {...})`) on controllers with haptic hardware.
- **Usage:** Developer use — enable to test gamepad rumble from web content on iOS, where the API is otherwise not exposed.
- **Ship status:** **Unverified** — Shipped in Safari 16.4 (desktop/macOS); still gated/off by default on this iOS build. Apple documents the public API as shipped; the recorded toggle is off, so this preference likely controls a narrower code path, a subfeature, or platform-specific behavior — the exact flag-to-API relationship was not confirmed.
- **Docs:** [Gamepad.vibrationActuator - Web APIs](https://docs.w3cub.com/dom/gamepad/vibrationactuator) (Community)

### Threaded Scroll-driven Animations
- **Recorded state:** on
- **What it gates:** Resolution of scroll-driven animations (`animation-timeline: scroll()` / `view()`) on a background thread in WebKit's remote layer tree, so they stay smooth and off the main thread — the threaded-animation-resolution work that previously sat behind the single `ThreadedAnimationResolutionEnabled` flag.
- **Usage:** Internal only — internal animation threading pipeline; do not change unless debugging WebKit itself.
- **Ship status:** Shipped — enabled by default (scroll-driven animations themselves shipped in Safari 26.0).
- **Docs:** [WebKit PR #53283 — individual run-time flags for scroll-driven and time-based animations](https://github.com/WebKit/WebKit/pull/53283) (WebKit impl)

### Threaded Time-based Animations
- **Recorded state:** off
- **What it gates:** Resolution of ordinary time-based (clock-driven) animations on a background thread in WebKit's remote layer tree, separate from the scroll-driven variant; keeps animation ticking off the main thread.
- **Usage:** Internal only — internal animation threading pipeline; do not change unless debugging WebKit itself.
- **Ship status:** Experimental — off by default.
- **Docs:** [WebKit PR #53283 — individual run-time flags for scroll-driven and time-based animations](https://github.com/WebKit/WebKit/pull/53283) (WebKit impl)

### Web Animations custom effects
- **Recorded state:** off
- **What it gates:** Undocumented experimental flag — purpose not publicly documented. (Name suggests an in-progress WebKit implementation of custom animation effects for the Web Animations API, a feature deferred to a later level of the Web Animations spec — but no public source confirms which surface this gates.)
- **Usage:** No confirmed developer use — purpose undocumented; leave at default.
- **Ship status:** Unknown.
- **Docs:** No official documentation found.

### Web Animations custom frame rate
- **Recorded state:** off
- **What it gates:** Undocumented experimental flag — purpose not publicly documented. (Name suggests experimental support for author-specified frame rates on Web Animations, but no public source confirms this.)
- **Usage:** No confirmed developer use — purpose undocumented; leave at default.
- **Ship status:** Unknown.
- **Docs:** No official documentation found.

### WebGPU
- **Recorded state:** on
- **What it gates:** The `navigator.gpu` WebGPU API for high-performance 3D graphics and general-purpose GPU compute in JavaScript (WGSL shaders, GPUDevice/GPUCanvasContext), originally enabled via this flag plus the two GPU Process flags in Safari Technology Preview 185.
- **Usage:** Testing use — on by default and shipped in Safari 26.0; leave at default, toggling off only to test fallback behavior.
- **Ship status:** Shipped in Safari 26.0.
- **Docs:** [WebKit Features in Safari 26.0](https://webkit.org/blog/17333/webkit-features-in-safari-26-0/) (WebKit article)

### WebGPU support for HDR
- **Recorded state:** on
- **What it gates:** High-dynamic-range rendering on WebGPU canvases — the setting that made HDR canvas output testable through Safari's Feature Flags UI.
- **Usage:** Testing use — on by default; leave at default, toggling off only to test non-HDR WebGPU fallback behavior.
- **Ship status:** Experimental — landed as a testable feature-flag entry; default-on in this build but not announced as a shipped API.
- **Docs:** [WebKit PR #34814 — Make HDR canvas testable via Safari](https://github.com/WebKit/WebKit/pull/34814) (WebKit impl)

### WebGL Draft Extensions
- **Recorded state:** off
- **What it gates:** Exposes WebGL extensions that are still Khronos drafts (not yet ratified/community-approved), e.g. `EXT_clip_control`, `EXT_conservative_depth`, `EXT_depth_clamp`, `WEBGL_polygon_mode`, `WEBGL_render_shared_exponent`, `WEBGL_stencil_texturing` — hidden from `getSupportedExtensions()` unless the flag is on.
- **Usage:** Developer use — enable when you need a draft (unratified) WebGL extension for development or testing.
- **Ship status:** Experimental — draft extensions stay gated by design.
- **Docs:** [webkit-changes — Implement new WebGL extension drafts](https://www.mail-archive.com/webkit-changes@lists.webkit.org/msg202030.html) (Community)

### WebGL Timer Queries
- **Recorded state:** off
- **What it gates:** The `EXT_disjoint_timer_query` / `EXT_disjoint_timer_query_webgl2` WebGL extensions, which let developers measure GPU-side timing (TIME_ELAPSED_EXT, TIMESTAMP_EXT) for performance profiling. Disabled by default partly due to GPU timing side-channel (GLitch) concerns.
- **Usage:** Developer use — enable when you need GPU timing data for WebGL performance profiling (disabled by default over timing side-channel concerns).
- **Ship status:** **Unverified** — Experimental — off by default; Web3D surveys show near-zero support in Safari with the flag off.
- **Docs:** [stats-gl README — enable Timer Queries under WebKit Feature Flags](https://github.com/raju-2313/abyss-deepseaexploration/blob/HEAD/node_modules%20copy/stats-gl/README.md) (Community)

### Evaluation time zoom enabled
- **Recorded state:** on
- **What it gates:** Undocumented experimental flag — purpose not publicly documented. (Name suggests an internal zoom-related evaluation path, but no public source confirms what it controls.)
- **Usage:** No confirmed developer use — purpose undocumented; leave at default.
- **Ship status:** Unknown.
- **Docs:** No official documentation found.

## 3.7 Privacy & tracking prevention

### Consistent Query Parameter Filtering
- **Recorded state:** on
- **What it gates:** A WebKit quirk that applies Safari's tracking query-parameter filtering and hiding rules consistently — i.e. without the site-specific exceptions/quirks that normally carve out web-compatibility cases. It sits behind `needsConsistentQueryParameterFilteringQuirk` in `Quirks.cpp` and is consulted on the navigation path by link-decoration filtering.
- **Usage:** Internal only — do not change unless debugging WebKit itself; this quirk bypasses site-specific compat exceptions in tracking-parameter filtering.
- **Ship status:** Experimental. No Safari release announcement was found.
- **Docs:** [WebKit PR: Add quirk for consistently applying filtering rules](https://github.com/WebKit/WebKit/pull/57411) (WebKit impl)

### Disable Full 3rd-Party Cookie Blocking (ITP)
- **Recorded state:** off
- **What it gates:** A kill-switch for Safari's full third-party cookie blocking: cookies for cross-site resources are blocked by default (Safari 13.1+, the first mainstream browser to do so), and enabling this flag restores the pre-13.1 behavior where some cross-site cookies were permitted.
- **Usage:** Do not change — this kill-switch weakens third-party cookie blocking; compare behavior via the Storage Access API or OAuth-token flows instead.
- **Ship status:** Shipped in Safari 13.1 / iOS 13.4. Apple documents the public API as shipped; the recorded toggle is off, so this preference likely controls a narrower code path, a subfeature, or platform-specific behavior — the exact flag-to-API relationship was not confirmed.
- **Docs:** [Full Third-Party Cookie Blocking and More](https://webkit.org/blog/10218/full-third-party-cookie-blocking-and-more/) (WebKit article)

### Disable Removal of Non-Cookie Data After 7 Days of No User Interaction (ITP)
- **Recorded state:** off
- **What it gates:** A kill-switch for ITP's 7-day cap on script-writable storage: Safari deletes all of a website's script-writable storage (IndexedDB, LocalStorage, SessionStorage, media keys, Service Worker registrations and cache) after seven days of Safari use without user interaction on the site. Enabling the flag disables that purge.
- **Usage:** Do not change — disabling the 7-day purge weakens ITP's storage protections; test app persistence without relying on script-writable storage.
- **Ship status:** Shipped in Safari 13.1 / iOS 13.4. Apple documents the public API as shipped; the recorded toggle is off, so this preference likely controls a narrower code path, a subfeature, or platform-specific behavior — the exact flag-to-API relationship was not confirmed.
- **Docs:** [Full Third-Party Cookie Blocking and More](https://webkit.org/blog/10218/full-third-party-cookie-blocking-and-more/) (WebKit article)

### [ITP Live-On] 1 Hour Timeout For Non-Cookie Data Removal
- **Recorded state:** off
- **What it gates:** An internal ITP test-harness preference. When active, it shortens the non-cookie website-data removal timeout from 7 days to 1 hour so engineers can observe ITP's data-purging behavior without waiting a week.
- **Usage:** Internal only — do not change unless debugging WebKit itself; a test-harness timing shortcut, not a developer setting.
- **Ship status:** Internal only — test harness, not a shipping feature.
- **Docs:** No official documentation found.

### [ITP Repro] 30 Second Timeout For Non-Cookie Data Removal
- **Recorded state:** off
- **What it gates:** An internal ITP test-harness preference. When active, it shortens the non-cookie website-data removal timeout from 7 days to 30 seconds, allowing engineers to reproduce and verify data-removal behavior in automated tests.
- **Usage:** Internal only — do not change unless debugging WebKit itself; a test-harness timing shortcut, not a developer setting.
- **Ship status:** Internal only — test harness, not a shipping feature.
- **Docs:** No official documentation found.

### Filter Link Decoration
- **Recorded state:** off
- **What it gates:** Stripping tracking query parameters ("link decoration") from URLs at navigation time, tied to tracking prevention. The underlying `FilterLinkDecorationByDefaultEnabled` preference was moved to stable and enabled by default (date not verified).
- **Usage:** Testing use — leave at default; enable only to test how your analytics/ad-click/affiliate flows behave with link-decoration filtering applied.
- **Ship status:** Shipped — preference made stable date not verified (exact Safari version unconfirmed).
- **Docs:** [WebKit PR: Set FilterLinkDecorationByDefaultEnabled to stable](https://github.com/WebKit/WebKit/pull/34562) (WebKit impl)

### ITP Debug Mode
- **Recorded state:** off
- **What it gates:** Verbose ITP diagnostics: ITP classification and storage decisions are logged at INFO level (Console / `log stream`), the test domain 3rdpartytestwebkit.org is always treated as a tracker, and you can manually mark your own domain as prevalent for repeatable testing.
- **Usage:** Testing use — enable temporarily when diagnosing ITP classification or storage behavior. Disable it after testing because diagnostic logs may contain visited domain names.
- **Ship status:** Shipped as an experimental feature since Safari Technology Preview 62.
- **Docs:** [ITP Debug Mode in Safari Technology Preview 62](https://webkit.org/blog/8387/itp-debug-mode-in-safari-technology-preview-62/) (WebKit article)

### Login Status API
- **Recorded state:** off
- **What it gates:** WebKit's implementation of the Login Status API (w3c-fedid): a site tells the browser its login state via `navigator.login.setStatus("logged-in" | "logged-out")` or a `Set-Login` HTTP response header, so identity flows can skip network calls and fail fast when the user is logged out.
- **Usage:** Developer use — enable to experiment with login-state-aware federated identity flows (`navigator.login.setStatus()` or the `Set-Login` response header).
- **Ship status:** Experimental / Unknown — no WebKit shipping announcement found.
- **Docs:** [Login Status API spec (w3c-fedid)](https://github.com/w3c-fedid/login-status) (Community)

### Opt-in partitioned cookies (CHIPS)
- **Recorded state:** on
- **What it gates:** CHIPS (Cookies Having Independent Partitioned State): the opt-in `Partitioned` cookie attribute (requires `SameSite=None; Secure`) that keys a third-party cookie to the top-level site it was set on, so embedded content (chat widgets, payment iframes, SaaS embeds) can use cookies without cross-site tracking. Safari partitions by site, matching other browsers.
- **Usage:** Testing use — leave at default; disable only to test fallback behavior when partitioned cookies are unavailable.
- **Ship status:** Shipped in Safari 26.2 (originally shipped in Safari 18.4, briefly removed in 18.5 for refinement, restored in 26.2).
- **Docs:** [WebKit Features for Safari 26.2](https://webkit.org/blog/17640/webkit-features-for-safari-26-2/) (WebKit article)

### Private Click Measurement Debug Mode
- **Recorded state:** off
- **What it gates:** A testing mode for Private Click Measurement (PCM) attribution: attribution reports are sent with a ~10-second delay instead of the production 24–48 hour delay, and IP-address protection is skipped, so ad-measurement flows can be tested locally.
- **Usage:** Testing use — enable temporarily when debugging Private Click Measurement attribution on a staging site. Disable it after testing because it skips IP protection and production delays.
- **Ship status:** Shipped alongside PCM (Safari 14.5+).
- **Docs:** [PCM: Click Fraud Prevention and Attribution Sent to Advertiser](https://webkit.org/blog/11940/pcm-click-fraud-prevention-and-attribution-sent-to-advertiser/) (WebKit article)

### Private Click Measurement Fraud Prevention
- **Recorded state:** on
- **What it gates:** PCM's optional unlinkable-token mechanism (RSA blind signatures) that lets the click-source site sign a token proving a click was trustworthy, giving advertisers privacy-preserving proof that attribution reports were triggered by genuine click events.
- **Usage:** Testing use — leave at default; disable only to test fallback behavior without unlinkable-token fraud prevention.
- **Ship status:** Shipped in Safari 15 (click-side); conversion-side fraud prevention added in Safari 15.4.
- **Docs:** [PCM: Click Fraud Prevention and Attribution Sent to Advertiser](https://webkit.org/blog/11940/pcm-click-fraud-prevention-and-attribution-sent-to-advertiser/) (WebKit article)

### Private Token usage by third party
- **Recorded state:** on
- **What it gates:** WebKit's PrivateToken proposal: a Permissions Policy-controlled `private-token` feature that allows third-party contexts (e.g. cross-origin iframes) to engage in the Privacy Pass token issuance/redemption flow (HTTP `PrivateToken` auth scheme), with the tokens usable as unlinkable fraud/trust signals similar to how third-party cookies would otherwise be used.
- **Usage:** Testing use — leave at default; disable only to test fallback behavior without third-party Privacy Pass token flows.
- **Ship status:** Experimental — WebKit proposal/explainer; no Safari release notes found.
- **Docs:** [ThirdPartyPrivateTokens explainer (WebKit)](https://github.com/webkit/explainers/blob/HEAD/ThirdPartyPrivateTokens/README.md) (WebKit impl)

## 3.8 Security headers, networking & loading

### Allow universal access from file: URLs
- **Recorded state:** off
- **What it gates:** Whether JavaScript running in the context of a `file://` URL may access content from any origin — e.g. `fetch()`/`XMLHttpRequest` to local files or remote sites, and local storage — instead of being confined by the same-origin policy.
- **Usage:** Developer use — enable when testing a local HTML app (opened as a file, not served over HTTP) that needs to load cross-origin resources or local files via JS.
- **Ship status:** Experimental (off by default; long-standing WebKit preference).
- **Docs:** [WebKit2.Settings:allow-universal-access-from-file-urls](https://webkitgtk.org/reference/webkit2gtk/2.40.0/property.Settings.allow-universal-access-from-file-urls.html) (Community)

### Clear-Site-Data HTTP Header
- **Recorded state:** on
- **What it gates:** Handling of the `Clear-Site-Data` response header, which lets a server tell the browser to delete data for its origin — cookies (`"cookies"`), storage (`"storage"`), and caches (`"cache"`).
- **Usage:** Testing use — leave at default; change only to test fallback/degraded behavior when `Clear-Site-Data` handling is disabled.
- **Ship status:** Shipped in Safari 17.0 (first-party cookies; partitioned-cookie clearing added in Safari 18.4).
- **Docs:** [WebKit Features in Safari 18.4](https://webkit.org/blog/16574/webkit-features-in-safari-18-4/) (WebKit article)

### Clear-Site-Data: 'executionContexts' support
- **Recorded state:** off
- **What it gates:** Support for the experimental `"executionContexts"` directive, which asked the browser to reload all browsing contexts (frames) of the response's origin — Safari was the only engine to implement it.
- **Usage:** No confirmed developer use — support was removed in Safari 18.3; do not rely on this value.
- **Ship status:** Experimental — support was removed in Safari 18.3 after the wildcard `"*"` (which includes this directive) caused infinite frame-reload loops in the wild (e.g. on MDN).
- **Docs:** [WebKit Features in Safari 18.3](https://webkit.org/blog/16439/webkit-features-in-safari-18-3/) (WebKit article)

### Cross-Origin-Embedder-Policy (COEP) header
- **Recorded state:** on
- **What it gates:** Enforcement of the `Cross-Origin-Embedder-Policy` response header: with `require-corp`, cross-origin subresources requested in `no-cors` mode must opt in via CORP or CORS. Together with COOP it enables cross-origin isolation (`crossOriginIsolated`, `SharedArrayBuffer`).
- **Usage:** Testing use — leave at default; change only to test fallback/degraded behavior when COEP enforcement is off.
- **Ship status:** Shipped in Safari 15.2.
- **Docs:** [Cross-Origin-Embedder-Policy (COEP) header](https://developer.Mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Cross-Origin-Embedder-Policy) (MDN)

### Cross-Origin-Opener-Policy (COOP) header
- **Recorded state:** on
- **What it gates:** Enforcement of the `Cross-Origin-Opener-Policy` response header (`same-origin`, `same-origin-allow-popups`), which isolates the document's browsing-context group from cross-origin windows. Half of the cross-origin-isolation pair with COEP.
- **Usage:** Testing use — leave at default; change only to test fallback/degraded behavior when COOP enforcement is off.
- **Ship status:** Shipped in Safari 15.2.
- **Docs:** [Cross-Origin-Opener-Policy compatibility table](https://caniuse.com/mdn-http_headers_cross-origin-opener-policy) (MDN)

### Defer async scripts until DOMContentLoaded or first-paint
- **Recorded state:** on
- **What it gates:** A page-load optimization where execution of `async` scripts is held back until `DOMContentLoaded` or first paint (whichever comes first), so async JS cannot delay the first paint.
- **Usage:** Internal only — do not change unless debugging WebKit itself; leave on for best first-paint performance.
- **Ship status:** Shipped (on by default; performance behavior from WebKit bug 208896, date not verified).
- **Docs:** [Bug 208896 – Defer async scripts until DOMContentLoaded or first paint, whichever comes first](https://bugs.webkit.org/show_bug.cgi?id=208896) (WebKit impl)

### Deprecation Reporting
- **Recorded state:** off
- **What it gates:** Reporting of the `"deprecation"` report type via the Reporting API (`ReportingObserver` / `Reporting-Endpoints`) when a page uses deprecated web-platform features.
- **Usage:** No confirmed developer use — off by default with no public evidence WebKit implements deprecation reporting at all.
- **Ship status:** Unknown — off by default and there is no public evidence WebKit implements deprecation reporting at all, even behind the flag.
- **Docs:** [Reconcile Safari Reporting-Endpoints default data (MDN browser-compat-data)](https://github.com/mdn/browser-compat-data/pull/29720) (Community)

### Enable EnhancedSecurity Heuristics
- **Recorded state:** on
- **What it gates:** The heuristic that decides when a page is loaded in a hardened `WebContent.EnhancedSecurity` process — on iOS 27 this triggers for pages loaded over plain HTTP (loopback is exempted; private LAN addresses are not).
- **Usage:** Do not change — internal hardened-process heuristics; disabling it trades the hardened process for performance on plain-HTTP pages.
- **Ship status:** **Unverified** — reported shipped in Safari 27 on iOS per an unrelated community repository issue; not confirmed by Apple or WebKit sources.
- **Docs:** [Improve webview performance on iOS 27 (home-assistant/iOS #5883)](https://github.com/home-assistant/iOS/pull/5883) (Community)

### Enhanced Security for links
- **Recorded state:** on
- **What it gates:** Applying the Enhanced Security policy to URLs "donated" by external sources (links opened from Messages, Mail, the App Store share surface, etc.): such URLs are checked and loaded in a hardened Enhanced Security web content process.
- **Usage:** Do not change — lockdown-adjacent internal flag; disabling it removes hardened-process protection for externally donated links.
- **Ship status:** Shipped in Safari 27.
- **Docs:** [Enhanced Security: Move HAVE_ENHANCED_SECURITY_LINKS to WebKit](https://github.com/WebKit/WebKit/pull/69374) (WebKit impl)

### FTP support enabled
- **Recorded state:** off
- **What it gates:** The `FTPEnabled` gate in the Network process that blocks or allows `ftp://` URL loads. Safari has never offered in-browser FTP rendering; FTP directory-listing support was removed entirely from WebKit in 2026.
- **Usage:** Internal only — legacy/engineering gate for unsupported FTP; do not change unless debugging WebKit itself.
- **Ship status:** Experimental (never shipped to users; off by default).
- **Docs:** [Remove dead FTP directory listing support (ENABLE_FTPDIR)](https://github.com/WebKit/WebKit/pull/68089) (WebKit impl)

### HTTPS-by-default (HTTPS-First)
- **Recorded state:** off
- **What it gates:** The `HTTPSByDefault` preference controlling whether `https:` is used by default for navigations and resource loads (automatic HTTP→HTTPS upgrades with fallback), replacing the older `HTTPSFirst`/`HTTPSOnly` modes.
- **Usage:** Developer use — flip it on to test automatic HTTPS upgrades on your site and verify HTTP fallback still works for non-HTTPS hosts.
- **Ship status:** Experimental (off by default).
- **Docs:** [Introduce HTTPSByDefault preference](https://github.com/WebKit/WebKit/pull/31171) (WebKit impl)

### Multi-Process Back/Forward Cache
- **Recorded state:** off
- **What it gates:** Back/forward cache (bfcache) support under Site Isolation, where a cached page's main frame and its cross-site iframe processes are suspended across multiple web processes and restored on back/forward navigation.
- **Usage:** Developer use — flip it on to test back/forward-cache behavior for site-isolated pages with cross-site iframes (e.g. verifying pages restore instead of reload).
- **Ship status:** Experimental (off by default; in newer builds it auto-enables whenever Site Isolation is active).
- **Docs:** [[BFCache] Add MultiProcessBackForwardCacheEnabled feature flag](https://github.com/WebKit/WebKit/pull/61776) (WebKit impl)

### Origin-Agent-Cluster header
- **Recorded state:** off
- **What it gates:** Honoring the `Origin-Agent-Cluster: ?1` HTTP response header, which requests that the page's origin get its own agent cluster — isolating it from same-site cross-origin pages (breaks `document.domain` relaxation) and limiting Spectre-style cross-page data access.
- **Usage:** Developer use — send `Origin-Agent-Cluster: ?1` from the server to harden a security-sensitive page (harmless to unsupported browsers).
- **Ship status:** Experimental — added in Safari Technology Preview 246; still off by default in Safari 27.
- **Docs:** [Release Notes for Safari Technology Preview 246](https://webkit.org/blog/18128/release-notes-for-safari-technology-preview-246/) (WebKit article)

### Remote Snapshotting
- **Recorded state:** off
- **What it gates:** Capturing page snapshots through the GPU process's remote rendering backend (`drawRemoteToPDF`, `takeSnapshotWithConfiguration:`) instead of the web content process — infrastructure for site isolation and WKWebView snapshot APIs.
- **Usage:** Internal only — GPU-process rendering infrastructure; do not change unless debugging WebKit itself.
- **Ship status:** Experimental (off by default; was briefly on by default, then reverted after a PDF-export regression).
- **Docs:** [Add a feature flag for remote snapshotting](https://github.com/WebKit/WebKit/pull/38228) (WebKit impl)

### Swap Processes on Cross-Site Navigation
- **Recorded state:** on
- **What it gates:** Whether Safari moves the page to a fresh web content process when navigating to a different site — a process-level isolation barrier that limits what speculative-execution (Spectre-class) attacks can read across origins.
- **Usage:** Do not change — internal Spectre-class isolation behavior; disabling it reduces cross-origin security protections.
- **Ship status:** **Unverified** — reported on by default on iOS (present since date not verified) per a community forum discussion; not confirmed by Apple or WebKit sources.
- **Docs:** [iLeakage attack resurrects Spectre (AppleInsider forums)](https://forums.appleinsider.com/discussion/234090) (Community)

### Trusted Types
- **Recorded state:** on
- **What it gates:** The Trusted Types API (`trustedTypes.createPolicy`, `TrustedHTML`/`TrustedScript`/`TrustedScriptURL`) plus enforcement via the `require-trusted-types-for` CSP directive — requiring dangerous DOM sinks (`innerHTML`, `document.write`, etc.) to receive sanitized, policy-produced values instead of raw strings.
- **Usage:** Testing use — leave at default; change only to test fallback/degraded behavior with Trusted Types enforcement disabled.
- **Ship status:** Shipped in Safari 18.4 (support announced during the 18.4 cycle in Safari Technology Preview 215; on by default in iOS 27).
- **Docs:** [Release Notes for Safari Technology Preview 215](https://webkit.org/blog/16523/release-notes-for-safari-technology-preview-215/) (WebKit article)

### Upgrade IP addresses and localhost in mixed content
- **Recorded state:** off
- **What it gates:** Whether WebKit's Mixed Content Level 2 upgrades (auto-upgrading `http:` subresources to `https:` instead of blocking them) also apply to subresources served from IP addresses and localhost; when off, localhost connections are not upgraded but other mixed-content rules still apply.
- **Usage:** Developer use — flip it on to test mixed-content handling for `http://localhost`/IP subresources from local development servers.
- **Ship status:** Experimental (off by default; testing preference).
- **Docs:** [Allow some access to localhost as mixed content](https://github.com/WebKit/WebKit/pull/27270) (WebKit impl)

### Verify window.open user gesture
- **Recorded state:** off
- **What it gates:** Extra verification (checked in the UI process) that a genuine user gesture — transient activation — occurred before `window.open()` is allowed to create or focus a window, blocking popunder-style bypasses where activation is spoofed across top-level windows.
- **Usage:** Developer use — flip it on to verify legitimate `window.open()` calls still work with the stricter gesture check when hardening against popup/popunder abuse.
- **Ship status:** Experimental (off by default).
- **Docs:** [Popunder bypass via overlapping transient activations](https://github.com/WebKit/WebKit/pull/71490) (WebKit impl)

### Web Content Restrictions Transitive Trust
- **Recorded state:** on
- **What it gates:** Undocumented experimental flag — purpose not publicly documented.
- **Usage:** No confirmed developer use — purpose is undocumented; no official documentation found.
- **Ship status:** Unknown.
- **Docs:** No official documentation found.

### Scroll To Text Fragment
- **Recorded state:** on
- **What it gates:** Parsing and scrolling to text fragments in URLs (`#:~:text=...`) — the page auto-scrolls to and highlights the matched passage on load.
- **Usage:** Testing use — leave at default; change only to test fallback/degraded behavior when text-fragment scrolling is disabled.
- **Ship status:** Shipped in Safari 17 (enabled by default in Safari Technology Preview 154).
- **Docs:** [Release Notes for Safari Technology Preview 154](https://webkit.org/blog/13207/release-notes-for-safari-technology-preview-154/) (WebKit article)

### Scroll To Text Fragment Feature Detection
- **Recorded state:** on
- **What it gates:** The `document.fragmentDirective` API, which lets pages feature-detect text-fragment support (`'fragmentDirective' in document`).
- **Usage:** Testing use — leave at default; change only to test fallback behavior when text-fragment feature detection is unavailable.
- **Ship status:** Shipped in Safari 18.4 (added in Safari Technology Preview 209).
- **Docs:** [Release Notes for Safari Technology Preview 209](https://webkit.org/blog/16296/release-notes-for-safari-technology-preview-209/) (WebKit article)

### Scroll To Text Fragment Generation
- **Recorded state:** on
- **What it gates:** Generating text-fragment URLs from a text selection — this powers Safari's "Copy Link with Highlight" context-menu item, which produces a `#:~:text=` link that scrolls and highlights the selected passage when opened.
- **Usage:** Testing use — leave at default; change only to test fallback behavior when text-fragment generation is unavailable.
- **Ship status:** Shipped in Safari 18.2 (when "Copy Link with Highlight" launched in Safari).
- **Docs:** [::target-text: An easy way to style text fragments](https://webkit.org/blog/17628/target-text-an-easy-way-to-style-text-fragments/) (WebKit article)

### Enable experimental network loader
- **Recorded state:** off
- **What it gates:** A rewritten networking stack (NSURLSession-based network loader in the Network process) replacing WebKit's default resource-loading path.
- **Usage:** Internal only — experimental networking stack; do not change unless debugging WebKit itself (a known cause of page-loading breakage).
- **Ship status:** Experimental (off by default).
- **Docs:** [WebKit timeline — "Add log when experimental network loader is used"](https://trac.webkit.org/timeline?authors=&daysback=4&from=2021-08-02) (WebKit impl)

## 3.9 Input, interaction, accessibility & misc

### altitudeAngle PointerEvent Property
- **Recorded state:** on
- **What it gates:** A read-only `PointerEvent` property giving the angle (in radians, 0–π/2) between a pointer or stylus transducer axis and the screen's X-Y plane, defaulting to π/2. It is used with `azimuthAngle` as an alternative to `tiltX`/`tiltY`.
- **Usage:** Testing use — leave at default; change only to test fallback behavior when the property is unavailable.
- **Ship status:** Shipped in Safari 18 (Baseline 2024).
- **Docs:** [PointerEvent: altitudeAngle property](https://developer.mozilla.org/en-US/docs/Web/API/PointerEvent/altitudeAngle) (MDN)

### azimuthAngle PointerEvent Property
- **Recorded state:** on
- **What it gates:** A read-only `PointerEvent` property giving the angle (in radians, 0–2π) between the Y-Z plane and the plane containing the transducer axis and the Y axis; 0 when the pen is perpendicular to the surface.
- **Usage:** Testing use — leave at default; change only to test fallback behavior when the property is unavailable.
- **Ship status:** Shipped in Safari 18 (Baseline 2024).
- **Docs:** [PointerEvent: azimuthAngle property](https://developer.mozilla.org/en-US/docs/Web/API/PointerEvent/azimuthAngle) (MDN)

### aria-actions
- **Recorded state:** off
- **What it gates:** A W3C ARIA draft attribute (spec PR w3c/aria#1805) letting an element reference interactive elements (idrefs) that perform actions on it, so assistive technology can announce and activate those actions (e.g., action menus in tab/listbox APG patterns).
- **Usage:** Developer use — enable in experimental builds when building accessible custom widgets that expose element-level actions to screen readers.
- **Ship status:** Experimental (shipped in WebKit and Firefox; Chromium still pending, per axe-core PR).
- **Docs:** [axe-core PR #5200: fix "aria-allowed-attr" rule for aria-actions](https://github.com/dequelabs/axe-core/pull/5200) (Community)

### referenceTarget support for aria-owns
- **Recorded state:** off
- **What it gates:** Whether `aria-owns` relationships resolve through a shadow root's reference target (a separate, more specific preference from the general `referenceTarget` feature, WebKit bug 290744).
- **Usage:** Developer use — enable in experimental builds when you need ARIA ownership semantics across shadow-DOM reference targets.
- **Ship status:** Experimental.
- **Docs:** [WebKit PR #43301: "Expose reference target… (referenceTarget support for aria-owns)"](https://github.com/WebKit/WebKit/pull/43301) (WebKit impl)

### referenceTarget
- **Recorded state:** off
- **What it gates:** A Shadow DOM proposal letting a shadow root declare a reference target (via `attachShadow({referenceTarget})` or `shadowrootreferencetarget`) so IDREF attributes (`for`, `aria-labelledby`, etc.) from outside the shadow tree can target an element inside it while preserving encapsulation.
- **Usage:** Developer use — enable in experimental builds when prototyping cross-shadow-boundary IDREF linking.
- **Ship status:** Experimental.
- **Docs:** [WebKit PR #43301: "Expose reference target as part of referenceTarget API"](https://github.com/WebKit/WebKit/pull/43301) (WebKit impl)

### hidden=until-found
- **Recorded state:** on
- **What it gates:** `<element hidden="until-found">` keeps the element unrendered until the user finds its content via find-in-page, at which point a `beforematch` event fires just before it is revealed.
- **Usage:** Testing use — leave at default; change only to test fallback behavior when the feature is unavailable.
- **Ship status:** Shipped in Safari 26.2.
- **Docs:** [WebKit Features for Safari 26.2](https://webkit.org/blog/17640/webkit-features-for-safari-26-2/) (WebKit article)

### Screen Orientation API
- **Recorded state:** on
- **What it gates:** `screen.orientation` with `type`, `angle`, and the `change` event (the read-only subset Apple shipped; locking/unlocking is gated separately). Note: MDN does not list Safari for the base ScreenOrientation interface, while Apple's Safari 16.4 notes list the API as supported — exact supported members should be feature-detected.
- **Usage:** Testing use — leave at default; feature-detect `screen.orientation` members before use.
- **Ship status:** Shipped in Safari 16.4 (read-only subset).
- **Docs:** [ScreenOrientation — MDN](https://developer.mozilla.org/en-US/docs/Web/API/ScreenOrientation) (MDN)

### Screen Orientation API (Locking / Unlocking)
- **Recorded state:** off
- **What it gates:** `screen.orientation.lock()` and `unlock()` for programmatic screen-orientation locking. Apple listed only `type`/`angle`/`onchange` for the Safari 16.4 subset, and `"lock" in window.screen.orientation` returns false on desktop and iOS Safari.
- **Usage:** Developer use — enable in experimental builds when prototyping orientation locking; feature-detect before calling.
- **Ship status:** Experimental (not shipped in Safari).
- **Docs:** [mdn/browser-compat-data#19355: screen.orientation.lock() Safari data](https://github.com/mdn/browser-compat-data/issues/19355) (Community)

### Screen Wake Lock API
- **Recorded state:** on
- **What it gates:** `navigator.wakeLock.request("screen")`, which keeps the screen awake while the lock is held; the lock auto-releases when the page hides, so re-acquire on `visibilitychange`.
- **Usage:** Testing use — leave at default; change only to test fallback behavior when wake lock is unavailable.
- **Ship status:** Shipped in Safari 16.4.
- **Docs:** [WebKit Features in Safari 16.4](https://webkit.org/blog/13966/webkit-features-in-safari-16-4/) (WebKit article)

### Contact Picker API
- **Recorded state:** off
- **What it gates:** The WICG draft Contact Picker API, letting a site invoke the device's contact picker so the user selects specific contacts to share (privacy-scoped access). Chrome-only in production; WebKit implemented it behind this flag since the Safari 14.1 betas.
- **Usage:** Developer use — enable in experimental builds when testing contact-picker flows; production Safari still has it behind the flag.
- **Ship status:** Experimental (behind flag in Safari).
- **Docs:** [fyrd/caniuse#5264: Contact Picker API support request](https://github.com/fyrd/caniuse/issues/5264) (Community)

### Digital Credentials API
- **Recorded state:** off
- **What it gates:** The W3C Digital Credentials API, surfaced via `navigator.credentials.get({ digital: { requests } })` (protocol `org-iso-mdoc`, ISO/IEC 18013-7 Annex C), letting sites request identity documents (e.g., a driver's license) from Apple Wallet or registered Identity Document Providers with user consent.
- **Usage:** Developer use — enable in experimental builds when testing identity-document request flows with a registered provider (e.g., Apple Wallet).
- **Ship status:** Shipped in Safari 26.0 (flag recorded off on this build). Apple documents the public API as shipped; the recorded toggle is off, so this preference likely controls a narrower code path, a subfeature, or platform-specific behavior — the exact flag-to-API relationship was not confirmed.
- **Docs:** [Online Identity Verification with the Digital Credentials API](https://webkit.org/blog/17431/online-identity-verification-with-the-digital-credentials-api/) (WebKit article)

### Web Share API Level 2
- **Recorded state:** on
- **What it gates:** The Level 2 additions to `navigator.share()`: sharing `files` (an array of `File`) and `navigator.canShare()` to pre-check whether a given data payload is shareable. Level 1 (title/text/url) was the original API.
- **Usage:** Testing use — leave at default; change only to test fallback behavior when file sharing is unavailable.
- **Ship status:** Shipped (file sharing on iOS Safari since ~Safari 15).
- **Docs:** [Navigator: canShare() method — MDN](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/canShare) (MDN)

### User Activation API
- **Recorded state:** on
- **What it gates:** `navigator.userActivation`, exposing transient (`isActive`) and sticky (`hasBeenActive`) user-activation state of the current window context.
- **Usage:** Testing use — leave at default; change only to test behavior without user-activation state.
- **Ship status:** Shipped in Safari 16.4 (Baseline: widely available).
- **Docs:** [UserActivation — MDN](https://developer.mozilla.org/en-US/docs/Web/API/UserActivation) (MDN)

### Lazy image loading
- **Recorded state:** on
- **What it gates:** Native `loading="lazy"` on `<img>` and `<iframe>`, deferring offscreen resource fetches until near the viewport. Long-lived behind this flag; enabled by default since Safari 15.4 (images; iframes followed).
- **Usage:** Testing use — leave at default; change only to test behavior with lazy loading disabled.
- **Ship status:** Shipped in Safari 15.4.
- **Docs:** [New WebKit Features in Safari 15.4](https://webkit.org/blog/12445/new-webkit-features-in-safari-15-4/) (WebKit article)

### Meta Viewport Interactive Widget
- **Recorded state:** off
- **What it gates:** The `interactive-widget` viewport-meta key, which controls viewport resize behavior when an on-screen keyboard appears: `resizes-visual` (default), `resizes-content`, or `overlays-content`. Implemented in WebKit source (~Aug 2026) but not yet shipped in any public Safari/STP; visible as a feature flag.
- **Usage:** Developer use — enable in experimental builds when testing keyboard-appearance viewport resize behavior.
- **Ship status:** **Unverified** — Experimental.
- **Docs:** [WebKit supports interactive-widget, and hopefully Safari will too — bram.us](https://www.bram.us/2026/09/11/webkit-supports-interactive-widget-and-hopefully-safari-will-too/) (Community)

### Lockdown Mode Safe Fonts
- **Recorded state:** on
- **What it gates:** In Lockdown Mode, most web fonts were historically blocked; the new Safe Font Parser evaluates web fonts and allows those that pass, so pages render with their specified fonts instead of falling back.
- **Usage:** Do not change — leave enabled; disabling re-blocks web fonts under Lockdown Mode.
- **Ship status:** Shipped in Safari 26.0; applies only under Lockdown Mode.
- **Docs:** [WebKit Features in Safari 26.0](https://webkit.org/blog/17333/webkit-features-in-safari-26-0/) (WebKit article)

### Vertical form control support
- **Recorded state:** on
- **What it gates:** `writing-mode: vertical-rl` / `vertical-lr` support on form controls (`button`, `textarea`, `progress`, `meter`, `input`, `select`), an Interop 2023 effort for CJK/Mongolian vertical text.
- **Usage:** Testing use — leave at default; change only to test fallback behavior without vertical form controls.
- **Ship status:** Shipped in Safari 17.4.
- **Docs:** [Implementing Vertical Form Controls](https://webkit.org/blog/15190/implementing-vertical-form-controls/) (WebKit article)

### Deprecate legacy mathvariant
- **Recorded state:** off
- **What it gates:** The MathML Core change removing the legacy `mathvariant` attribute except `mathvariant="normal"` (per Math WG resolution; WebKit's `acceptsLegacyMathVariantAttribute()`), gating deprecation of legacy rendering.
- **Usage:** Developer use — enable in experimental builds when testing MathML rendering without legacy mathvariant behavior.
- **Ship status:** Experimental (deprecation in progress).
- **Docs:** [w3c/mathml-core#182: Remove mathvariant](https://github.com/w3c/mathml-core/issues/182) (Community)

### Disable href attribute on MathML elements
- **Recorded state:** off
- **Likely behavior:** WebKit historically supported `href` on *any* MathML element; MathML Core is moving toward a dedicated `<a>` element for links, and this flag gates turning off the legacy all-elements `href` (inference — exact scope not publicly documented).
- **Evidence limit:** The visible flag-to-feature relationship has not been publicly documented.
- **Usage:** Developer use — enable in experimental builds when testing MathML links against the MathML Core `<a>`-element model.
- **Ship status:** Experimental.
- **Docs:** [Improvements in MathML Rendering](https://webkit.org/blog/6803/improvements-in-mathml-rendering/) (WebKit article)

### Legacy FontFaceSet Constructor API
- **Recorded state:** off
- **What it gates:** The `new FontFaceSet()` constructor from the CSS Font Loading API, removed in Safari 26.4 per CSSWG resolution ("rarely used"); the flag preserves the legacy behavior.
- **Usage:** Developer use — enable when testing compatibility with code that still calls `new FontFaceSet()`.
- **Ship status:** Removed in Safari 26.4 (flag = legacy toggle).
- **Docs:** [WebKit Features for Safari 26.4](https://webkit.org/blog/17862/webkit-features-for-safari-26-4/) (WebKit article)

### Legacy showModalDialog() API
- **Recorded state:** off
- **What it gates:** The deprecated synchronous `window.showModalDialog()` dialog API; WebKit made it runtime-enabled and effectively off by default (it requires the client to implement modal UI); desktop-Safari-only, never on iOS.
- **Usage:** Developer use — enable in experimental builds only for legacy web content that still calls `showModalDialog()`.
- **Ship status:** Legacy / off-by-default; desktop Safari only, never on iOS.
- **Docs:** [WebKit bug 151885: Implement window.showModalDialog](https://bugs.webkit.org/show_bug.cgi?format=multiple&id=151885) (WebKit impl)

### Fullscreen API
- **Recorded state:** off
- **What it gates:** The standard Fullscreen API (`requestFullscreen`, etc.) for arbitrary elements. On iPhone, `Element.requestFullscreen` does not exist — WebKit ships element fullscreen on iPadOS only (since iPadOS 16.4); iPhone video goes through the WebKit-specific `webkitEnterFullscreen()`, which explains the flag being off on iOS.
- **Usage:** Developer use — enable in experimental builds when testing element fullscreen; on iPhone use `webkitEnterFullscreen()` for video.
- **Ship status:** Shipped in Safari 16.4 on macOS and iPadOS per the cited WebKit article. The flag is recorded off on this iOS 27 device; iPhone availability is not confirmed by the cited source.
- **Docs:** [WebKit Features in Safari 16.4](https://webkit.org/blog/13966/webkit-features-in-safari-16-4/) (WebKit article)

### Passkeys site-specific hacks
- **Recorded state:** on
- **What it gates:** An Apple-internal per-site compatibility list (of the Quirks.cpp site-specific-hacks mechanism) applying passkey/WebAuthn workarounds for specific sites. It is surfaced in device settings; there is no developer API surface.
- **Usage:** Do not change — Apple-managed per-site compatibility list with no developer API surface.
- **Ship status:** Internal (shipped behavior).
- **Docs:** [Device Settings — WebKit](https://webkit.org/web-inspector/device-settings/) (WebKit impl)

### WebAssembly ES module integration support
- **Recorded state:** off
- **What it gates:** The Wasm proposal letting ES modules and WebAssembly modules import each other directly (`.wasm` in the module graph, including source-phase imports), tracked in WebKit bug 274908.
- **Usage:** Developer use — enable in experimental builds when prototyping direct Wasm↔ES-module imports.
- **Ship status:** Experimental.
- **Docs:** [WebKit bug 274908: [Wasm] ES Module Integration](https://bugs.webkit.org/show_bug.cgi?id=274908) (WebKit impl)

### WebTransport
- **Recorded state:** on
- **What it gates:** Low-latency bidirectional client↔server communication over HTTP/3+QUIC — streams plus datagrams — a modern WebSocket alternative for games, live collaboration, and video conferencing.
- **Usage:** Testing use — leave at default; change only to test fallback behavior without WebTransport.
- **Ship status:** Shipped in Safari 26.4.
- **Docs:** [WebKit Features for Safari 26.4](https://webkit.org/blog/17862/webkit-features-for-safari-26-4/) (WebKit article)

### WebRTC AV1 codec
- **Recorded state:** off
- **What it gates:** AV1 as a WebRTC codec. In Safari, AV1 support is hardware-gated (M3+ Macs, iPhone 15 Pro/16 and later), and WebRTC AV1 remains experimental behind this flag.
- **Usage:** Developer use — preference type not confirmed by the cited source. Enable only in a controlled test environment and feature-detect codec support.
- **Ship status:** Experimental; hardware-gated (AV1 decode requires M3+ Macs or iPhone 15 Pro/16 and later).
- **Docs:** [Web video codec guide — MDN](https://developer.mozilla.org/en-US/docs/Web/Media/Guides/Formats/Video_codecs) (MDN)

### WebRTC L4S support
- **Recorded state:** off
- **Likely behavior:** L4S (Low queuing Latency, Low Loss, Scalable throughput — the IETF architecture using ECN/DualPI2) applied to WebRTC's congestion control, for more consistent low-latency real-time media (inference from flag name and IETF context).
- **Evidence limit:** The visible flag-to-feature relationship has not been publicly documented.
- **Usage:** Internal only — do not change unless debugging WebKit itself.
- **Ship status:** Experimental.
- **Docs:** [L4S Architecture — IETF](https://datatracker.ietf.org/doc/html/draft-ietf-tsvwg-l4s-arch) (Community)

### WebRTC SFrame Transform API
- **Recorded state:** off
- **Likely behavior:** `SFrameTransform` (encrypt/decrypt roles, `setEncryptionKey`) from WebRTC Encoded Transform, enabling end-to-end encrypted media frames (SFrame, RFC 9605) independent of DTLS-SRTP; the specific SFrameTransform API surface is flagged in WebKit (inference).
- **Evidence limit:** The visible flag-to-feature relationship has not been publicly documented.
- **Usage:** Developer use — enable in experimental builds when testing end-to-end-encrypted media frames with SFrame.
- **Ship status:** Experimental.
- **Docs:** [WebRTC Encoded Transform — W3C](https://www.w3.org/TR/2023/WD-webrtc-encoded-transform-20230727/) (Spec)

### requestIdleCallback
- **Recorded state:** off
- **What it gates:** The `window.requestIdleCallback()` / `cancelIdleCallback()` API for scheduling low-priority callbacks during browser idle periods. WebKit implements it only behind this feature flag; it is not enabled by default in any stable Safari (caveat: some builds exposed it without `cancelIdleCallback`).
- **Usage:** Developer use — enable in experimental builds when testing idle-time scheduling; use a `setTimeout` fallback for production code.
- **Ship status:** **Unverified** — Experimental (behind flag in Safari).
- **Docs:** [requestIdleCallback: Browser Support, Use Cases, Polyfill — TestMu](https://www.testmuai.com/learning-hub/requestidlecallback-browser-support/) (Community)

### Prefer Page Rendering Updates near 60fps
- **Recorded state:** on
- **What it gates:** The WebKit preference `PreferPageRenderingUpdatesNear60FPSEnabled` (default true everywhere except visionOS): it caps page rendering and `requestAnimationFrame` near 60Hz on 120Hz ProMotion displays to save power and avoid pages that break at non-60Hz.
- **Usage:** Internal only — do not change unless debugging WebKit itself.
- **Ship status:** **Unverified** — Internal (shipped preference, on by default).
- **Docs:** [WebKit FPS research notes — lambdawaves](https://github.com/magic-commons/lambdawaves/blob/HEAD/research/optimization-2026-09-24/WEBKIT-FPS-RESEARCH.md) (Community)



---
## 4. Release & version reference

Recent Safari releases mapped to their OS releases. Safari on iOS/iPadOS is version-locked to the OS — you cannot update Safari independently of the system.

| Safari version | Ships with | Notes |
|---|---|---|
| **Safari 27** | iOS 27, iPadOS 27.0.1 | Stable capture used for this guide: Safari 27.0, build 20625.1.29, recorded in October 2026. Source of the flag list in this guide. [Safari 27 release notes](https://developer.apple.com/documentation/safari-release-notes/safari-27-release-notes) (Apple) |
| Safari 26.x | iOS 26, iPadOS 26 | Added Declarative Web Push, Digital Credentials API, `FileSystemWritableFileStream` groundwork. |
| Safari 18.x | iOS 18, iPadOS 18 | Added Distraction Control; engine work included `::target-text` highlight styling groundwork and Writing Tools integrations. |
| Safari 17.x | iOS 17, iPadOS 17 | Baseline for many currently-on flags: OffscreenCanvas, WebGL in Workers groundwork, JPEG XL images. |
| Safari 16.4 | iOS 16.4, iPadOS 16.4 | Web Push for Home Screen web apps, Badging API. |

Key index pages (Apple):
- [Safari release notes index](https://developer.apple.com/documentation/safari-release-notes) (Apple) — every Safari version's notes.
- [WebKit Feature Status](https://webkit.org/status/) (WebKit project) — WebKit's official tracker of feature implementation status across Safari releases.

---

## 5. Platform expansion: iPadOS, watchOS, tvOS

### iPadOS 27 — expected to match, verify on device

iPadOS 27 also ships **Safari 27**, so much of this catalog is expected to apply, and the Feature Flags screen lives at the same Settings location (Settings → Apps → Safari → Advanced → Feature Flags). This research did not independently capture and compare every iPadOS toggle, so the exact on/off states were not verified per flag. Platform-specific behavior can differ because of hardware, Stage Manager windowing, input methods (trackpad/mouse hover, Apple Pencil `altitudeAngle`/`azimuthAngle` pointer events), and Safari policy. Verify the visible flag list and behavior on the target iPad.

### watchOS 27 — no Safari, no WebKit for apps

watchOS 27 (latest stable 27.0.1) **does not ship Safari** as a browser and Apple does **not** make WebKit available to watchOS apps (confirmed via Apple developer forums). There is no Feature Flags screen on Apple Watch. Limited web content can appear only in narrow system-controlled contexts (e.g. rich notification content, Now Playing metadata); there is no general web rendering surface for developers to target, so the flag catalog does not apply. Track watchOS 27 as a platform target, not a Safari target.

### tvOS 27 — no Safari browser; JS/TVML inside apps only

tvOS 27 (latest stable 27.0) **does not ship Safari** as a user-facing browser, and WebKit is not a listed tvOS framework. tvOS apps can run JavaScript and TVML content (TVMLKit) inside the app sandbox, and that content **can be remotely inspected from desktop Safari's Web Inspector** ([Inspecting tvOS](https://docs.developer.apple.com/documentation/safari-developer-tools/inspecting-tvos) — Apple). There is no on-device Feature Flags UI and no published Safari-version mapping for the tvOS web runtime, so the Section 3 catalog should not be assumed to apply per-flag; verify behavior on-device via remote inspection.

---

## 6. Sources

Selected primary Apple and WebKit sources cited in the catalog, deduplicated:

- [::target-text: An easy way to style text fragments](https://webkit.org/blog/17628/target-text-an-easy-way-to-style-text-fragments/)
- [Add a feature flag for remote snapshotting](https://github.com/WebKit/WebKit/pull/38228)
- [Add feature flag for enumerated ARIA attributes and new ARIA IDL — WebKit PR](https://github.com/WebKit/WebKit/pull/48937)
- [Add support for AirPods mute API](https://github.com/WebKit/WebKit/pull/33469)
- [Allow some access to localhost as mixed content](https://github.com/WebKit/WebKit/pull/27270)
- [An HTML Switch Control — WebKit](https://webkit.org/blog/15054/an-html-switch-control/)
- [AX: Feature request: Support WebVTT-based synthesized audio description in video](https://webkit.org/b/266724)
- [Bug 208896 – Defer async scripts until DOMContentLoaded or first paint, whichever comes first](https://bugs.webkit.org/show_bug.cgi?id=208896)
- [Device Settings — WebKit](https://webkit.org/web-inspector/device-settings/)
- [Enhanced Security: Move HAVE_ENHANCED_SECURITY_LINKS to WebKit](https://github.com/WebKit/WebKit/pull/69374)
- [FileSystemHandle: add global identifiers for cross-process serialization — WebKit PR](https://github.com/WebKit/WebKit/pull/64371)
- [Full Third-Party Cookie Blocking and More](https://webkit.org/blog/10218/full-third-party-cookie-blocking-and-more/)
- [Implement CookieStoreManager for the CookieStore API — WebKit PR](https://github.com/WebKit/WebKit/pull/31979)
- [Implementing Vertical Form Controls](https://webkit.org/blog/15190/implementing-vertical-form-controls/)
- [Improvements in MathML Rendering](https://webkit.org/blog/6803/improvements-in-mathml-rendering/)
- [Include video caption cues in Find-in-Page navigation on macOS](https://github.com/WebKit/WebKit/pull/67687)
- [Introduce HTTPSByDefault preference](https://github.com/WebKit/WebKit/pull/31171)
- [Introducing CSS Grid Lanes](https://webkit.org/blog/17660/introducing-css-grid-lanes/)
- [ITP Debug Mode in Safari Technology Preview 62](https://webkit.org/blog/8387/itp-debug-mode-in-safari-technology-preview-62/)
- [New WebKit Features in Safari 11.1](https://webkit.org/blog/8216/new-webkit-features-in-safari-11-1/)
- [New WebKit Features in Safari 15.4](https://webkit.org/blog/12445/new-webkit-features-in-safari-15-4/)
- [Online Identity Verification with the Digital Credentials API](https://webkit.org/blog/17431/online-identity-verification-with-the-digital-credentials-api/)
- [PCM: Click Fraud Prevention and Attribution Sent to Advertiser](https://webkit.org/blog/11940/pcm-click-fraud-prevention-and-attribution-sent-to-advertiser/)
- [Popunder bypass via overlapping transient activations](https://github.com/WebKit/WebKit/pull/71490)
- [Provide option to "detach" a MediaSource element in a non-destructive fashion](https://github.com/WebKit/WebKit/pull/33773)
- [Release Notes for Safari Technology Preview 154](https://webkit.org/blog/13207/release-notes-for-safari-technology-preview-154/)
- [Release Notes for Safari Technology Preview 209](https://webkit.org/blog/16296/release-notes-for-safari-technology-preview-209/)
- [Release Notes for Safari Technology Preview 213](https://webkit.org/blog/16461/release-notes-for-safari-technology-preview-213/)
- [Release Notes for Safari Technology Preview 215](https://webkit.org/blog/16523/release-notes-for-safari-technology-preview-215/)
- [Release Notes for Safari Technology Preview 244](https://webkit.org/blog/17962/release-notes-for-safari-technology-preview-244/)
- [Release Notes for Safari Technology Preview 246](https://webkit.org/blog/18128/release-notes-for-safari-technology-preview-246/)
- [Remove dead FTP directory listing support (ENABLE_FTPDIR)](https://github.com/WebKit/WebKit/pull/68089)
- [Safari 27 release notes](https://developer.apple.com/documentation/safari-release-notes/safari-27-release-notes)
- [Safari release notes index](https://developer.apple.com/documentation/safari-release-notes)
- [Spatial Video does not show Immersive or View Spatial button when muted or lacks audio track](https://github.com/WebKit/WebKit/pull/41509)
- [Support devolvable widgets](https://github.com/WebKit/WebKit/pull/37511)
- [Support video/webm;codecs=h264,pcm in MediaRecorder](https://github.com/WebKit/WebKit/pull/39443)
- [ThirdPartyPrivateTokens explainer (WebKit)](https://github.com/webkit/explainers/blob/HEAD/ThirdPartyPrivateTokens/README.md)
- [VideoMediaSampleRenderer doesn't handle going from clear to protected content](https://github.com/WebKit/WebKit/pull/45056)
- [WebGPU now available for testing in Safari Technology Preview](https://webkit.org/blog/14879/webgpu-now-available-for-testing-in-safari-technology-preview/)
- [WebKit bug 151885: Implement window.showModalDialog](https://bugs.webkit.org/show_bug.cgi?format=multiple&id=151885)
- [WebKit Features for Safari 26.2 — WebKit](https://webkit.org/blog/17640/webkit-features-for-safari-26-2/)
- [WebKit Features for Safari 26.4](https://webkit.org/blog/17862/webkit-features-for-safari-26-4/)
- [WebKit Features for Safari 26.5 — WebKit](https://webkit.org/blog/17938/webkit-features-for-safari-26-5/)
- [WebKit Features for Safari 27.0](https://webkit.org/blog/18325/webkit-features-for-safari-27-0/)
- [WebKit Features in Safari 16.0](https://webkit.org/blog/13152/webkit-features-in-safari-16-0/)
- [WebKit Features in Safari 16.4](https://webkit.org/blog/13966/webkit-features-in-safari-16-4/)
- [WebKit Features in Safari 17.0 — WebKit](https://webkit.org/blog/14445/webkit-features-in-safari-17-0/)
- [WebKit Features in Safari 18.3](https://webkit.org/blog/16439/webkit-features-in-safari-18-3/)
- [WebKit Features in Safari 18.4 — WebKit](https://webkit.org/blog/16574/webkit-features-in-safari-18-4/)
- [WebKit Features in Safari 26.0](https://webkit.org/blog/17333/webkit-features-in-safari-26-0/)
- [WebKit PR #16067 — Add page-targeted quirk for Canvas2D noise injection](https://github.com/WebKit/WebKit/pull/16067)
- [WebKit PR #34814 — Make HDR canvas testable via Safari](https://github.com/WebKit/WebKit/pull/34814)
- [WebKit PR #43301: "Expose reference target… (referenceTarget support for aria-owns)"](https://github.com/WebKit/WebKit/pull/43301)
- [WebKit PR #48322: Implement speculation rules — same origin conservative prefetch](https://github.com/WebKit/WebKit/pull/48322)
- [WebKit PR #53283 — individual run-time flags for scroll-driven and time-based animations](https://github.com/WebKit/WebKit/pull/53283)
- [WebKit PR #58216: Port IndexedDB Memory Backing Store to SQLite In-Memory Database](https://github.com/WebKit/WebKit/pull/58216)
- [WebKit PR #72022 — Support CanvasRenderingContext2D layers](https://github.com/WebKit/WebKit/pull/72022)
- [WebKit PR #9075 — Add support for "trigger-rumble" effect type](https://github.com/WebKit/WebKit/pull/9075)
- [WebKit PR: Add quirk for consistently applying filtering rules](https://github.com/WebKit/WebKit/pull/57411)
- [WebKit PR: Set FilterLinkDecorationByDefaultEnabled to stable](https://github.com/WebKit/WebKit/pull/34562)
- [WebKit timeline — "Add log when experimental network loader is used"](https://trac.webkit.org/timeline?authors=&daysback=4&from=2021-08-02)
- [WebKit/explainers — TrackConfiguration API](https://github.com/webkit/explainers)

## 7. Appendix: source coverage

Documentation-link coverage across the catalog and reference sections, using the 7-level source hierarchy from Section 3:

| Tier | Links cited |
|---|---:|
| Apple (release notes, developer documentation) | 2 |
| WebKit article (official blog release articles) | 68 |
| WebKit impl (source, merged PRs, bug tracker) | 45 |
| Spec (W3C, WHATWG, WICG) | 2 |
| MDN / compatibility data | 41 |
| Community (secondary sources) | 29 |
| Undocumented (no public source found) | 16 entries |
| WebKit project (feature status tracker) | 1 |

Entries whose only sources are Community-tier are marked **Unverified** in their Ship status lines.

*Counting method: counts represent every Docs link occurrence in the catalog, not unique URLs. "Undocumented" counts entries without any public source.*

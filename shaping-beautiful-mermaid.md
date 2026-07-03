---
shaping: true
---

# Beautiful-Mermaid Integration — Shaping

## Source

> Check this repo: https://github.com/lukilabs/beautiful-mermaid. Would it be possible to enhance my project using it but still be able to deploy on GitHub Pages? I want themed diagrams, and dual output; explore alternatives.
>
> My questions for the future: Can we extend the number of supported diagram types? Can we leverage the available foundation to support animated diagrams?

---

## Problem

`md-viewer` currently renders Mermaid diagrams with the official `mermaid@11.6.0` CDN library. It supports many diagram types but theming is limited to Mermaid's built-in `dark`/`default` themes, and there is no native ASCII/Unicode text output. The user wants better-looking themed diagrams and a dual SVG+ASCII output, while keeping GitHub Pages deployment.

---

## Outcome

A markdown preview that renders Mermaid diagrams with consistent app theming, offers SVG for the preview and ASCII for copying, and still deploys as a static site to GitHub Pages without a heavy build pipeline.

---

## Requirements (R)

| ID | Requirement | Status |
|----|-------------|--------|
| R0 | Diagrams render with theming that matches the active app theme | Must-have |
| R1 | Provide both SVG output (preview) and ASCII/Unicode output (copyable text) | Must-have |
| R2 | Continue deploying on GitHub Pages as a static site | Must-have |
| R3 | Existing markdown documents with Mermaid diagrams still render correctly | Must-have |
| R4 | Preserve the current plain HTML/JS workflow (no mandatory build step) | Leaning yes |
| R5 | Architecture allows extending supported diagram types in the future | Nice-to-have |
| R6 | Foundation can support animated/interactive diagrams in the future | Nice-to-have |

---

## Shapes

### CURRENT: Mermaid.js only

Keep `mermaid@11.6.0` via CDN. Theming limited to `theme: 'dark' | 'default'`. No ASCII output.

### A: Replace Mermaid.js with beautiful-mermaid

Use `beautiful-mermaid` via ESM CDN for all Mermaid code blocks. Render SVG synchronously with `renderMermaidSVG()` and ASCII with `renderMermaidASCII()`. Drop Mermaid.js.

### B: Hybrid — beautiful-mermaid primary, Mermaid.js fallback

Try `renderMermaidSVG()` first. On parse/render failure, fall back to the existing `<div class="mermaid">` + `mermaid.init()` flow. Keep both libraries loaded.

### C: Keep Mermaid.js, add server-side ASCII export

No change to diagram rendering. Generate ASCII via a backend service or external API when exporting. Requires a server, so not GitHub Pages friendly.

### D: Use beautiful-mermaid only for new/explicit blocks

Leave current ` ```mermaid ` blocks rendered by Mermaid.js. Add a new fenced language (e.g. ` ```beautiful-mermaid `) that uses the new renderer. Users opt-in per block.

### E: Build-free ESM CDN

Drop Mermaid.js. Load `beautiful-mermaid` via an ESM CDN (`esm.sh`, `esm.run`, or jsDelivr ESM) using `<script type="module">` or an import map. No local build step; GitHub Pages serves the same HTML/JS files.

### F: textusm only

`textusm` is an Elm/JavaScript library that turns indented text into diagrams such as User Story Maps, Mind Maps, Gantt charts, ER diagrams, and Business Model Canvases. It is **not a Mermaid renderer**: you write diagrams in its own indentation-based syntax, not in ` ```mermaid ` blocks.

| Aspect | Finding |
|--------|---------|
| Theming | Configurable per diagram via constructor options |
| Output | SVG only; no ASCII/Unicode text rendering |
| Mermaid compatibility | None — existing ` ```mermaid ` blocks would not render |
| GitHub Pages fit | Can be loaded via CDN, so static hosting works |
| Achieves the same goal? | **No.** It cannot take standard Mermaid syntax and produce themed SVG + ASCII. |
| Easier than Shape E? | **No, unless you rewrite every diagram in `textusm` syntax and drop ASCII output.** That is more work and loses compatibility with existing documents. |

### G: mermaid.mq as a preprocessor

`harehare/mermaid.mq` is a Mermaid parser implemented as an `mq` module. It reads standard Mermaid text and produces a structured AST, plus a stringify function to turn the AST back into Mermaid text.

| Aspect | Finding |
|--------|---------|
| Theming | None built-in — theming would have to be invented in the custom renderer |
| Output | Parse data + text; no SVG or ASCII renderer included |
| Mermaid compatibility | Parses standard syntax, but only exposes the parsed structure |
| GitHub Pages fit | Could be loaded as a module, but you still need a renderer |
| Achieves the same goal? | **No by itself.** It only solves the parsing half of the problem. |
| Easier than Shape E? | **No.** Using `mermaid.mq` means building your own SVG renderer, ASCII renderer, theming, and error handling from scratch. That is strictly harder than using `beautiful-mermaid`. |

---

## Fit Check

| Req | Requirement | Status | CURRENT | A | B | C | D | E | F | G |
|-----|-------------|--------|:-------:|:-:|:-:|:-:|:-:|:-:|:-:|
| R0 | Themed diagrams matching app theme | Must-have | ❌ | ✅ | ✅ | ❌ | ✅ | ✅ | ✅ | ❌ |
| R1 | Dual SVG + ASCII output | Must-have | ❌ | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ |
| R2 | GitHub Pages static deployment | Must-have | ✅ | ⚠️ | ⚠️ | ❌ | ⚠️ | ✅ | ⚠️ | ✅ |
| R3 | Existing Mermaid docs render correctly | Must-have | ✅ | ❌ | ✅ | ✅ | ✅ | ❌ | ❌ | ✅ |
| R4 | Preserve plain HTML/JS workflow | Leaning yes | ✅ | ⚠️ | ⚠️ | ❌ | ⚠️ | ✅ | ⚠️ | ✅ |
| R5 | Future extensibility for more diagram types | Nice-to-have | ✅ | ❌ | ⚠️ | ✅ | ⚠️ | ❌ | ❌ | ⚠️ |
| R6 | Foundation for animated/interactive diagrams | Nice-to-have | ⚠️ | ⚠️ | ⚠️ | ⚠️ | ⚠️ | ⚠️ | ❌ | ⚠️ |

**Notes:**
- CURRENT fails R0/R1: built-in Mermaid themes are limited and there is no ASCII path.
- A fails R3: beautiful-mermaid supports only 6 diagram types (flowchart, state, sequence, class, ER, xy chart).
- A/B/D are ⚠️ on R2/R4 because they rely on an ESM CDN or a self-hosted bundled file; not as drop-in as a classic `<script>` tag.
- C fails R2/R4 because it requires a backend.
- **E is the recommended approach**: it is build-free (R4 ✅), GitHub Pages friendly (R2 ✅), and gives themed SVG + ASCII (R0/R1 ✅). The only loss is broad diagram-type support (R3/R5 ❌), which the user has accepted for now.
- R6 remains ⚠️ for all shapes because advanced animation is a future consideration, not a current requirement.
- **Feedback response — `textusm` and `mermaid.mq`:** Neither tool achieves the same goal as `beautiful-mermaid` for this project. `textusm` uses a different input language (no Mermaid compatibility, no ASCII output) and would require rewriting existing diagrams. `mermaid.mq` only parses Mermaid into an AST; you would still have to build SVG/ASCII rendering and theming from scratch. Both are therefore **not easier or better fits** than Shape E.

---

## Forward-looking assessment

### Extending supported diagram types

`beautiful-mermaid` is open-source TypeScript. Adding a new diagram type means adding a parser + SVG renderer + ASCII renderer for that type. There is no published plugin API, so extension today means forking the repo or contributing upstream. This is feasible but not trivial; each diagram type is a meaningful engineering effort.

**Complexity estimate:** **7/10** for a typical new diagram type.

The `beautiful-mermaid` pipeline has three layers: parsing, layout, and rendering. To add a diagram type you generally need to:

1. Create a type-specific parser that converts Mermaid syntax into a typed AST.
2. Create a layout step (Dagre-based for graph-like diagrams, custom for timeline/non-graph diagrams).
3. Create an SVG renderer that produces the final SVG.
4. Create an ASCII renderer for the same diagram.
5. Wire the new type into `detectDiagramType` in both `src/index.ts` and `src/ascii/index.ts`.
6. Add parser and integration tests.

**Easiest to add:** diagram types structurally similar to flowcharts or state diagrams (directed graphs with nodes and edges), because they can reuse much of the existing `parseMermaid`, Dagre layout, and generic `renderSvg` logic.

**Hardest to add:** diagram types that need a completely different layout algorithm (like sequence diagrams with their custom timeline layout), diagrams with complex nested syntax, or diagram types with specialized visual notation (like ER diagrams with crow's-foot cardinality markers).

Because we are dropping the fallback for now, the chosen shape accepts the current 6 supported diagram types as the supported set. If the user later needs Gantt, Pie, Git graph, Timeline, etc., the options are:
1. Contribute those types upstream to `beautiful-mermaid`.
2. Temporarily or permanently re-add `mermaid.js` as a fallback renderer.
3. Switch to a different rendering strategy.

### CDN fallback

The primary load path is an ESM CDN such as `esm.sh` or `esm.run` using an import map. A secondary fallback is desirable so diagrams still render if the primary CDN is unreachable.

- **Primary:** ESM CDN (`esm.sh` / `esm.run`) for clean import-map integration.
- **Fallback (to verify):** A second ESM CDN URL for the same pinned version, or a self-hosted copy. When the specific `beautiful-mermaid.browser.global.js` path was probed on unpkg and jsDelivr for the pinned version, both returned 404. So a browser-global bundle fallback is not confirmed yet and must be re-checked before implementation.

If a working browser-global bundle is found, it exposes `window.beautifulMermaid` with the older API (`renderMermaid`, `renderMermaidAscii`, `THEMES`, `DEFAULTS`), which may differ from newer ESM names like `renderMermaidSVG`. Keeping both sources on the same pinned version lets us align the API we actually call.

A simple loader pattern once the fallback URL is confirmed:

```html
<script type="importmap">
{
  "imports": {
    "beautiful-mermaid": "https://esm.sh/beautiful-mermaid@1.1.3"
  }
}
</script>
<!-- Verify this URL exists for the chosen version -->
<script src="https://unpkg.com/beautiful-mermaid@1.1.3/dist/beautiful-mermaid.browser.global.js"></script>
```

Then in the module code:

```javascript
import { renderMermaidSVG, renderMermaidASCII, THEMES } from "beautiful-mermaid";
```

If the primary ESM CDN fails but the global bundle loaded, the global can be mapped manually:

```javascript
if (typeof window.beautifulMermaid !== "undefined") {
  // Expose global as a module-like object if the ESM CDN failed
  window.__beautifulMermaidFallback = window.beautifulMermaid;
}
```

For now, the practical options are:
- Use a second ESM CDN URL as the fallback (simplest, still build-free).
- Download the library file into the repo and serve it locally (removes the CDN dependency entirely; adds one static asset to maintain).
- Accept that if all CDN paths are unavailable, diagrams will not render (rare scenario for a personal project).

This keeps the workflow build-free while reducing the single-CDN risk.

### Animated / interactive diagrams

The library renders SVGs and uses CSS custom properties for colors. That makes **CSS-driven animations and transitions** (theme changes, hover states, fades) possible out of the box. `beautiful-mermaid` also supports `interactive: true` for XY-chart tooltips, so lightweight interactivity already exists for that diagram type.

For richer animation in the future — playback-style sequence reveals, step-by-step flows, or custom transitions — the SVG output can be augmented with CSS animations or SMIL, but `beautiful-mermaid` itself does not have a built-in animation engine. Those animations would need to be added by post-processing the generated SVG or by extending the library upstream.

---

## Decisions captured

1. **Unsupported diagram fallback** — No fallback required. The user is building for themselves and accepts the 6 diagram types currently supported by `beautiful-mermaid`.

2. **Build / CDN trade-off** — Stay build-free and use an ESM CDN. This preserves simple static-site hosting and the current plain HTML/JS workflow.

   **Pros for build-free + ESM CDN:**
   - No build tool to install or configure.
   - GitHub Pages deployment stays identical: push HTML/JS/CSS, done.
   - Faster iteration — edit files directly and reload.
   - Smaller repo — the library lives on the CDN, not in your repo.

   **Cons / tradeoffs:**
   - An external dependency: if the CDN is down, diagram rendering breaks.
   - ESM-only import syntax is slightly different from traditional `<script>` tags.
   - You cannot easily pin a local copy or patch the library without switching to a bundler.

3. **Animation scope** — The user wants all three levels eventually: smooth theme/color transitions, hover/interactive tooltips, and playback-style step-by-step animations. `beautiful-mermaid` supports the first two via CSS variables / `interactive: true` for XY charts; playback-style animations would require future custom work or upstream extension.

4. **CDN fallback** — Pin a specific `beautiful-mermaid` version in the ESM import map (e.g. `esm.sh`) and prepare a fallback path to another ESM CDN or a self-hosted copy. The exact `beautiful-mermaid.browser.global.js` bundle path returned 404 on both unpkg and jsDelivr when probed for the pinned version, so the fallback URL must be verified before implementation. If all CDN paths are unavailable, diagrams will not render, which is acceptable for a personal project.

5. **Requested alternatives (`textusm`, `mermaid.mq`) are not viable** — `textusm` is a separate indented-text diagram generator with no Mermaid support and no ASCII output; `mermaid.mq` is only a parser and would require building a complete renderer. They were evaluated because of file feedback, but neither satisfies R0–R3, and neither is easier than Shape E.

---

## Recommended shape

**Shape E — Build-free ESM CDN with beautiful-mermaid only** is the best fit given the captured decisions.

| Part | Mechanism |
|------|-----------|
| E1 | Remove Mermaid.js script tag from `index.html` |
| E2 | Load `beautiful-mermaid` via `esm.sh` import map |
| E3 | Replace `renderer.code` Mermaid branch to call `renderMermaidSVG()` and inject the SVG |
| E4 | Add an ASCII export/copy action per diagram using `renderMermaidASCII()` |
| E5 | Map active md-viewer theme to `beautiful-mermaid` theme colors (use CSS variables or `THEMES` object) |
| E6 | Add error handling for unsupported diagram types — show a clear message instead of silently failing |

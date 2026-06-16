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

---

## Fit Check

| Req | Requirement | Status | CURRENT | A | B | C | D | E |
|-----|-------------|--------|:-------:|:-:|:-:|:-:|:-:|
| R0 | Themed diagrams matching app theme | Must-have | ❌ | ✅ | ✅ | ❌ | ✅ | ✅ |
| R1 | Dual SVG + ASCII output | Must-have | ❌ | ✅ | ✅ | ✅ | ✅ | ✅ |
| R2 | GitHub Pages static deployment | Must-have | ✅ | ⚠️ | ⚠️ | ❌ | ⚠️ | ✅ |
| R3 | Existing Mermaid docs render correctly | Must-have | ✅ | ❌ | ✅ | ✅ | ✅ | ❌ |
| R4 | Preserve plain HTML/JS workflow | Leaning yes | ✅ | ⚠️ | ⚠️ | ❌ | ⚠️ | ✅ |
| R5 | Future extensibility for more diagram types | Nice-to-have | ✅ | ❌ | ⚠️ | ✅ | ⚠️ | ❌ |
| R6 | Foundation for animated/interactive diagrams | Nice-to-have | ⚠️ | ⚠️ | ⚠️ | ⚠️ | ⚠️ | ⚠️ |

**Notes:**
- CURRENT fails R0/R1: built-in Mermaid themes are limited and there is no ASCII path.
- A fails R3: beautiful-mermaid supports only 6 diagram types (flowchart, state, sequence, class, ER, xy chart).
- A/B/D are ⚠️ on R2/R4 because they rely on an ESM CDN or a self-hosted bundled file; not as drop-in as a classic `<script>` tag.
- C fails R2/R4 because it requires a backend.
- **E is the recommended approach**: it is build-free (R4 ✅), GitHub Pages friendly (R2 ✅), and gives themed SVG + ASCII (R0/R1 ✅). The only loss is broad diagram-type support (R3/R5 ❌), which the user has accepted for now.
- R6 remains ⚠️ for all shapes because advanced animation is a future consideration, not a current requirement.

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

`beautiful-mermaid` can be loaded as a browser bundle (`beautiful-mermaid.browser.global.js`) from unpkg/jsdelivr in addition to the ESM path. We can use that as a fallback if the primary ESM CDN fails:

- **Primary:** ESM CDN (`esm.sh` / `esm.run`) for clean import-map integration.
- **Fallback:** Browser global bundle from unpkg or jsdelivr, loaded via a traditional `<script>` tag. It exposes `window.beautifulMermaid`, which can be mapped to the same import name in the module code.

However, the fallback bundle only provides the older API (`renderMermaid`, `renderMermaidAscii`, `THEMES`, `DEFAULTS`) and may not expose newer names like `renderMermaidSVG`. The README/early releases show `renderMermaid`, while later docs use `renderMermaidSVG`. The exact API available on the browser global depends on which version is served by the CDN. If we keep the primary and fallback aligned at the same version, we can choose whichever API that version exposes.

A simple fallback loader pattern:

```html
<script type="importmap">
{
  "imports": {
    "beautiful-mermaid": "https://esm.sh/beautiful-mermaid@1.1.3"
  }
}
</script>
<script src="https://unpkg.com/beautiful-mermaid@1.1.3/dist/beautiful-mermaid.browser.global.js"></script>
```

Then in the module code:

```javascript
import { renderMermaidSVG, renderMermaidASCII, THEMES } from "beautiful-mermaid";
```

If `esm.sh` fails to load, the browser global from unpkg/jsdelivr can be mapped manually before the import is used:

```javascript
if (typeof window.beautifulMermaid !== "undefined") {
  // Expose global as a module-like object if the ESM CDN failed
  window.__beautifulMermaidFallback = window.beautifulMermaid;
}
```

In practice, a robust fallback requires either:
- keeping both sources at the same pinned version and using only the API that the browser global exposes, or
- bundling the library locally and serving it from the repo (which removes the CDN dependency entirely but requires a small build step).

For now, the simplest reliable approach is:
- Pin a specific version (`@1.1.3`) in both the ESM import map and the global script fallback.
- Test that the chosen API works with that exact version.
- Accept that if both CDNs are unavailable, diagrams will not render (a rare scenario for a personal project).

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

4. **CDN fallback** — Pin a specific `beautiful-mermaid` version in both the ESM import map and a global bundle fallback from unpkg/jsdelivr. This keeps the workflow build-free while reducing the risk of a single CDN failure. If both CDNs are down, diagrams will not render, which is acceptable for a personal project.

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

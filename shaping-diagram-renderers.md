---
shaping: true
---

# Diagram Renderer Comparison — Shaping

## Source

> I want to explore other alternatives and ideas to see if they will solve the hard parts of requirements. Suppose we do not have to use mermaid diagrams. And suppose we have LLM available in browser. What if we can ask LLM generate same diagram but create SVG directly or design diagram in text-based format first (mermaid syntax or ascii), then use it as guidance and generate SVG diagram or maybe instead of mermaidjs, use alternative text based syntax that can be transformed relatively cheap and easy to SVG?
>
> Must-haves: theming, deploying on GitHub Pages, ideally convertible to standalone HTML, no build step.
>
> Can modern LLMs generate good-looking SVG diagrams?
>
> Include D3, D2, Mermaid, Graphviz, Satori, and similar tools.

## Problem

Existing `md-viewer` uses Mermaid.js for diagrams. The user wants themed, dual SVG+ASCII output and GitHub Pages static hosting without a build step. Mermaid.js alone does not satisfy theming/ASCII well. The user wants a broader comparison of browser-native rendering options, including LLM-assisted generation, to find the approach that best fits the constraints.

## Outcome

Select a diagram rendering strategy that:
- Runs entirely in static HTML/JS deployable on GitHub Pages
- Requires no build step
- Supports custom theming
- Can leverage LLMs for diagram creation if desired
- Optionally supports ASCII/Unicode copy output and animation

---

## Requirements (R)

| ID | Requirement | Status |
|----|-------------|--------|
| R0 | Diagrams render with theming that matches the active app theme | Must-have |
| R1 | Deployable as a standalone HTML file or static GitHub Pages site | Must-have |
| R2 | No build step or bundler required | Must-have |
| R3 | Accepts markdown-like or simple text input | Must-have |
| R4 | Produces SVG output | Must-have |
| R5 | Produces ASCII/Unicode text output | Nice-to-have |
| R6 | Supports animation or interactivity | Nice-to-have |
| R7 | Supports custom or extensible diagram types | Nice-to-have |
| R8 | Lightweight runtime payload | Nice-to-have |

---

## Shapes

### A: Mermaid.js (current)

Keep the official `mermaid` library loaded via CDN. Supports many diagram types, basic built-in themes.

| Part | Mechanism | Flag |
|------|-----------|:----:|
| A1 | Load `mermaid@11.6.0` from CDN as a global script | |
| A2 | Marked `renderer.code` wraps ` ```mermaid ` blocks in `<div class="mermaid">` | |
| A3 | Call `mermaid.init()` to render diagrams asynchronously | |
| A4 | Theme via `mermaid.initialize({ theme: 'dark' \| 'default' })` | |

**Weight:** Heavy (~1.2 MB+).  
**Animation:** None built-in.  
**ASCII:** No.

### B: beautiful-mermaid

Replace Mermaid.js with `beautiful-mermaid` loaded as an ESM module from a CDN. Renders themed SVG and ASCII for the 6 supported diagram types.

| Part | Mechanism | Flag |
|------|-----------|:----:|
| B1 | Load `beautiful-mermaid` via `esm.sh` import map | |
| B2 | Parse Mermaid text synchronously in the browser | |
| B3 | Render SVG with `renderMermaidSVG()` and CSS-variable themes | |
| B4 | Render ASCII/Unicode with `renderMermaidASCII()` | |
| B5 | Map active `md-viewer` theme to `beautiful-mermaid` `THEMES` object | |

**Weight:** Medium (~200–500 KB).  
**Animation:** CSS theme transitions possible; XY charts support `interactive: true` tooltips.  
**ASCII:** Yes.

### C: Graphviz + Viz.js

Use Graphviz DOT syntax rendered by `viz.js`, a WebAssembly port of Graphviz.

| Part | Mechanism | Flag |
|------|-----------|:----:|
| C1 | Load `viz.js` (WASM bundle) from a CDN | |
| C2 | Accept Graphviz DOT syntax input | |
| C3 | Graphviz layout engine computes node/edge positions | |
| C4 | Render generated SVG and apply theme via CSS | |

**Weight:** Heavy (~2–3 MB WASM).  
**Animation:** No.  
**ASCII:** No.

### D: D3.js custom renderer

Use D3 as a low-level SVG drawing library and build a custom text-to-SVG renderer.

| Part | Mechanism | Flag |
|------|-----------|:----:|
| D1 | Load `d3` from a CDN | |
| D2 | Parse a custom text DSL or JSON into a graph structure | |
| D3 | Compute layout with `d3-hierarchy`, `d3-force`, or custom code | |
| D4 | Draw nodes, edges, and labels as SVG elements | |
| D5 | Apply theme colors and fonts via CSS/JS | |

**Weight:** Heavy (~300 KB+) plus custom renderer code.  
**Animation:** Excellent support via transitions, enter/exit selections, and events.  
**ASCII:** No (possible but requires a separate renderer).

### E: textusm

Use the `textusm` Elm/JavaScript library. Takes indented text and renders fixed diagram types such as mind maps, ER diagrams, and sequence diagrams.

| Part | Mechanism | Flag |
|------|-----------|:----:|
| E1 | Load compiled `textusm` bundle from a CDN or npm | |
| E2 | Parse indented text input per diagram type | |
| E3 | Render SVG via built-in Elm templates | |
| E4 | Theme via constructor or configuration options | |

**Weight:** Medium.  
**Animation:** No.  
**ASCII:** No.

### F: Vercel Satori

Use Satori to render JSX-like markup into static SVG. Useful for cards, diagrams-as-layout, and social-image-style visuals.

| Part | Mechanism | Flag |
|------|-----------|:----:|
| F1 | Load `satori` and its yoga WASM layout engine from a CDN | |
| F2 | Author diagrams as JSX-like JSON objects | |
| F3 | Render to SVG with Tailwind-like class strings or inline styles | |
| F4 | Load/embed fonts for consistent typography | |

**Weight:** Heavy (WASM + font files).  
**Animation:** No — produces static SVG.  
**ASCII:** No.

### G: LLM generates SVG directly

Prompt an LLM to output raw SVG markup, then sanitize and inject it into the page.

| Part | Mechanism | Flag |
|------|-----------|:----:|
| G1 | Call LLM API from the browser or a serverless edge function | |
| G2 | Prompt for self-contained, themed SVG markup | |
| G3 | Sanitize and inject the returned SVG into the DOM | |
| G4 | Retry or fix malformed SVG with follow-up LLM calls | |

**Weight:** None for diagrams (only LLM network call).  
**Animation:** Possible if the prompt asks for CSS or SMIL animation.  
**ASCII:** No.

### H: LLM → Mermaid → beautiful-mermaid

Use an LLM to generate Mermaid syntax, then render it with `beautiful-mermaid` for SVG and ASCII output.

| Part | Mechanism | Flag |
|------|-----------|:----:|
| H1 | Prompt LLM to produce valid Mermaid syntax for a described diagram | |
| H2 | Feed the generated Mermaid text into `renderMermaidSVG()` | |
| H3 | Feed the same text into `renderMermaidASCII()` for copy/export | |
| H4 | Validate/fix invalid Mermaid with a retry prompt | |

**Weight:** Medium (only `beautiful-mermaid` loaded).  
**Animation:** As `beautiful-mermaid` — CSS transitions and XY-chart tooltips.  
**ASCII:** Yes.

### I: D2 declarative diagrams

Use the D2 language compiled to SVG via a WASM build of the D2 compiler.

| Part | Mechanism | Flag |
|------|-----------|:----:|
| I1 | Load `d2` or `d2-wasm` JS/WASM bundle from a CDN | |
| I2 | Parse D2 text syntax into an abstract diagram | |
| I3 | Layout nodes and edges with Dagre/ELK | |
| I4 | Render to SVG and apply theme variables | |

**Weight:** Heavy (~1.5 MB WASM).  
**Animation:** No.  
**ASCII:** No.

### J: Rough.js hand-drawn diagrams

Use `rough.js` to render sketch-style SVG diagrams from a simple custom graph DSL.

| Part | Mechanism | Flag |
|------|-----------|:----:|
| J1 | Load `rough.js` from a CDN | |
| J2 | Parse a minimal text DSL into nodes and edges | |
| J3 | Draw shapes and connectors with a hand-drawn aesthetic | |
| J4 | Apply theme colors to strokes and fills | |

**Weight:** Light (~10 KB).  
**Animation:** No.  
**ASCII:** No.

---

## Fit Check

| Req | Requirement | Status | A | B | C | D | E | F | G | H | I | J |
|-----|-------------|--------|---|---|---|---|---|---|---|---|---|---|---|
| R0 | Themed diagrams matching app theme | Must-have | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| R1 | Deployable as a standalone HTML file or static GitHub Pages site | Must-have | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| R2 | No build step or bundler required | Must-have | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| R3 | Accepts markdown-like or simple text input | Must-have | ✅ | ✅ | ❌ | ❌ | ✅ | ❌ | ❌ | ✅ | ✅ | ❌ |
| R4 | Produces SVG output | Must-have | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| R5 | Produces ASCII/Unicode text output | Nice-to-have | ❌ | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ | ❌ | ❌ |
| R6 | Supports animation or interactivity | Nice-to-have | ❌ | ✅ | ❌ | ✅ | ❌ | ❌ | ✅ | ✅ | ❌ | ❌ |
| R7 | Supports custom or extensible diagram types | Nice-to-have | ❌ | ❌ | ❌ | ✅ | ❌ | ✅ | ✅ | ❌ | ❌ | ✅ |
| R8 | Lightweight runtime payload | Nice-to-have | ❌ | ✅ | ❌ | ❌ | ❌ | ❌ | ✅ | ✅ | ❌ | ✅ |

**Notes:**
- A fails R5/R6/R7/R8: Mermaid.js is heavy, has no ASCII output, no animation, and locked diagram types with limited theming.
- B satisfies all must-haves and R5/R6/R8, but fails R7: `beautiful-mermaid` supports only 6 diagram types and has no plugin API.
- C fails R3/R5/R6/R7/R8: DOT is technical, not markdown-like; WASM is heavy; static output only.
- D fails R3/R5/R8: requires building a custom DSL and renderer; D3 itself is large; ASCII is possible but not free.
- E fails R5/R6/R7: no ASCII, no animation, fixed set of diagram types; Mermaid docs would not render without rewriting.
- F fails R3/R5/R6/R7/R8: input is JSX-like JSON, not plain text; heavy due to WASM/fonts; static only.
- G fails R3/R5: prompts replace stored text input; no ASCII path unless a second LLM call generates it.
- H satisfies all must-haves and R5/R6/R8, but fails R7: extensibility is limited to what `beautiful-mermaid` supports.
- I fails R5/R6/R7/R8: heavy WASM, static output, no ASCII, and D2 types are fixed by the compiler.
- J fails R3/R5/R6: you must invent a custom DSL; no animation or ASCII built-in, though the payload is tiny.

---

## Comparative summary

| Shape | Input language | Weight | Animation | ASCII | Best for |
|-------|----------------|--------|-----------|-------|----------|
| A Mermaid.js | Mermaid | Heavy | No | No | Broad Mermaid compatibility |
| B beautiful-mermaid | Mermaid | Medium | Partial | Yes | Themed SVG + ASCII, no build |
| C Graphviz/Viz.js | DOT | Heavy | No | No | Precise graph layouts |
| D D3.js | Custom DSL/JSON | Heavy | Yes | Hard | Fully custom interactive diagrams |
| E textusm | Indented text | Medium | No | No | Specific business/agile diagrams |
| F Satori | JSX-like JSON | Heavy | No | No | Styled static images/cards |
| G LLM → SVG | Natural language prompt | None | Prompt-dependent | No | One-off generated visuals |
| H LLM → Mermaid → beautiful-mermaid | Natural language → Mermaid | Medium | Partial | Yes | Natural-language diagrams with dual output |
| I D2 | D2 language | Heavy | No | No | Clean declarative diagrams |
| J Rough.js | Custom DSL | Light | No | No | Sketch-style simple diagrams |

---

## Recommended shape

**For the stated requirements (theming, GH Pages, no build, text input, SVG + optional ASCII):**

### Without LLM dependency: Shape B — beautiful-mermaid

- ✅ All must-haves
- ✅ ASCII output
- ✅ Lightest practical payload among the full-featured options
- ✅ Partial animation/interactivity
- ❌ Limited to 6 diagram types

### With LLM assistance: Shape H — LLM → Mermaid → beautiful-mermaid

- ✅ All must-haves
- ✅ ASCII output
- ✅ User describes diagrams in plain language instead of learning Mermaid
- ✅ Same runtime payload as B
- ❌ Adds LLM API dependency, cost, latency, and privacy considerations
- ❌ Same 6-diagram-type limit as B

**Shape H is the best fit if the user is willing to rely on an LLM.** It removes the need to write Mermaid by hand while keeping the proven browser renderer.  
**Shape B remains the best fit if the user wants zero LLM dependency.**

---

## Decisions captured

1. **LLMs can generate good-looking SVG**, but output is inconsistent without an intermediate structured format. The most reliable path is LLM → structured text → tested renderer, not LLM → raw SVG.
2. **Text-based intermediates beat direct SVG generation** for repeatability, editing, and ASCII export.
3. **Among all compared tools, only `beautiful-mermaid` and the LLM-assisted variant satisfy the full set of must-haves plus ASCII output while staying build-free and GH Pages friendly.**
4. **Graphviz, D2, and Satori are too heavy or lack the needed text/markdown input and ASCII output.**
5. **D3.js is the most flexible for animation and custom shapes**, but it requires building and maintaining a custom renderer, which violates the simplicity goal.

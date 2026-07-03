# Browser-Only Mermaid → Standalone HTML Export

**Context:** The `md-viewer` project is a static-site online editor where users paste markdown (with ` ```mermaid ` code blocks) and want to export as a standalone HTML file. No build step — everything runs in the browser.

## Q: Can mermaid → standalone HTML be done purely in the browser?

**Yes.** Load mermaid from CDN, render to SVG in the editor, inject into a self-contained HTML template, trigger download — all client-side, no Node.js.

## Q: What's the core API?

```js
import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs';
const { svg } = await mermaid.render('id', 'graph TD; A-->B');
```

Returns SVG string. Replace ` ```mermaid ` blocks in markdown with these SVG strings, wrap in template, download as HTML.

## Q: What are the output options?

| Option | How | Size | Network? | Editable? |
|---|---|---|---|---|
| **A: Pre-render SVG** | Embed SVG directly | Smallest | No | No |
| **B: CDN Runtime** | Include `<script src="...mermaid.js">` | Medium | Yes | Yes |
| **C: Hybrid** | SVG + source data attr + CDN | Largest | Optional | Toggle |

**Recommendation: Option A** — zero-dependency output, same approach as archify.

## Q: What template to use?

Archify's `template.html` (~1100 lines CSS + JS) handles: dark/light theme toggle, keyboard shortcuts (`T`/`E`), clipboard copy, PNG/JPEG/WebP/SVG export. Inject content via sentinel comments (`<!-- ARCHIFY:SVG_SLOT -->`).

## Q: How to trigger download?

```js
const blob = new Blob([html], { type: 'text/html' });
const url = URL.createObjectURL(blob);
const a = document.createElement('a');
a.href = url;
a.download = 'export.html';
a.click();
URL.revokeObjectURL(url);
```

## Diagrams

See `browser-only-options.html` (3 approaches visualized), `markdown-to-standalone.html` (enhancement path), `archify-pipeline.html` (archify's own rendering pipeline for reference).

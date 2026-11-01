# Mermaid & Visuals — Reference

Load this file when generating any `mermaid` block or choosing a diagram for a section.

## Mermaid Syntax Rules (mandatory)

Every `mermaid` code block MUST satisfy these rules — invalid syntax produces a red "Syntax error in text" badge in the rendered output.

**Do not include a `%%{init:%%` block.** Mermaid's built-in `default` and `dark` themes handle light/dark adaptation automatically; a custom init block would force one palette and break the theme switch.

| Rule | Wrong ✗ | Right ✓ | Why |
| ---- | ------- | ------- | --- |
| Use correct diagram keyword | `radar` | `radar-beta` | Mermaid v11+ uses `-beta` for newer diagram types |
| Axis/curve IDs must be ASCII | `axis Уверенность, Баланс` | `axis u["Уверенность"], b["Баланс"]` | Mermaid parser rejects non-ASCII identifiers; labels can be any language |
| No bare `key: value` in radar | `Уверенность: 5` | `curve team["Команда"]{5, 2, 3, 4, 4}` | Radar uses `axis` + `curve` syntax, not key-value pairs |
| Every diagram block must be valid standalone | guessing syntax | test at <https://mermaid.live> | Mermaid renders each block independently — one bad block shows an error |

**Pre-generation checklist** (run mentally before emitting each `mermaid` block):

1. Is the keyword correct for this mermaid version? (`flowchart` not `graph` for LR, `radar-beta` not `radar`, `timeline` not `gantt` when events have no durations, `mindmap` for trees)
2. Are all identifiers ASCII-only? (labels can be any script)
3. Does the block parse at [mermaid.live](https://mermaid.live)?

If unsure about any diagram type's syntax, fall back to a **Markdown table** — it always renders.

---

## Content → Visual Classification Rubric

When a Techniques section calls for a diagram, pick a visual using the table below. **Rotate formats across summaries** so patterns don't become predictable. If a chunk fits multiple categories, pick the one that hasn't been used recently.

| Content Type | Primary Visual | Alternatives (rotate) | When to Use |
| ------------ | -------------- | -------------------- | ----------- |
| **Events / timeline** | Gantt or timeline | Kanban board, flowchart | Sequence matters, dependencies exist |
| **Action items / next steps** | Gantt timeline | Kanban columns, numbered flowchart | Ownership + deadlines exist |
| **Decisions / trade-offs** | Decision tree | Comparison table, pros/cons matrix | Options were weighed |
| **Risks / uncertainties** | Risk matrix (2x2) | Heatmap, tornado chart | Probability + impact discussed |
| **Root causes / blockers** | Fishbone (Ishikawa) | 5-Whys tree, flowchart | Drilling into why something happened |
| **Metrics / data** | Bar or line chart | Table with sparklines, scatter plot | Numbers were discussed |
| **Relationships / dependencies** | Flowchart (LR orientation) | Sankey diagram, ER-style diagram | "X depends on Y" or "X feeds into Y" |
| **Themes / clusters** | Mindmap (radial) | Bubble chart, Venn diagram | Recurring topics across the conversation |
| **Sentiment / mood** | Radar chart | Pie chart, grouped bar chart | Feelings, energy, confidence expressed |

---

## Accessibility & Aesthetics Rules

- **Color contrast:** All Mermaid nodes must use dark text on light backgrounds or light text on dark backgrounds — never mid-on-mid. Mermaid's built-in `default` and `dark` themes guarantee this; do not override with inline `style` that breaks contrast.
- **If you use inline `style` on a node:** Always pair a light fill with `color:#1a1a1a` or a dark fill with `color:#ffffff`. Example: `style C fill:#D4760A,color:#ffffff`
- **Mermaid identifiers:** All node IDs, axis IDs, and curve IDs MUST be ASCII (`[a-zA-Z_][a-zA-Z0-9_]*`). Use quoted labels for display text in any script: `A["Кириллица"]` not `Кириллица`. This is a Mermaid parser constraint, not a style preference.
- **Heading hierarchy:** Exactly one `#` (title), then `##` for sections. No `###` or deeper — keeps structure flat and scannable.
- **Alt text:** Every diagram gets a `<!-- alt: ... -->` comment directly above it describing what it conveys for screen-reader users.
- **Language:** Mirror the tone and vocabulary of the source audio so the summary feels authentic (include citations where needed). Casual, direct, no corporate-speak unless the speakers use it.

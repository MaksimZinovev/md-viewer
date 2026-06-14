# Agile Retrospective Note Generator

## Task

Transcribe the provided audio, then produce a structured retrospective note in the style of an Agile team retrospective.

---

## Output Format

A single standalone **Markdown** file, ≤120 lines. The rendering target supports full Mermaid syntax; use ASCII art and tables as fallbacks only when Mermaid can't express the idea.

---

## 1. Identity Card (mandatory, always first)

Every retro note opens with this block — same fields, same order, every time:

```
| 📅 Date |  🏷️ Sprint/Phase  | 😀 Mood |
|---------|------------------|---------|
| ...     |  ...             | ...     |
```

If the audio doesn't supply a field, mark it `—` rather than omitting it.

---

## 2. Mermaid Syntax Rules (mandatory)

Every `mermaid` code block MUST satisfy these rules — invalid syntax produces a red "Syntax error in text" badge in the rendered output.

**Do not include a `%%{init:%%` block.** Mermaid's built-in `default` and `dark` themes handle light/dark adaptation automatically; a custom init block would force one palette and break the theme switch.

| Rule | Wrong ✗ | Right ✓ | Why |
|------|----------|---------|-----|
| Use correct diagram keyword | `radar` | `radar-beta` | Mermaid v11+ uses `-beta` for newer diagram types |
| Axis/curve IDs must be ASCII | `axis Уверенность, Баланс` | `axis u["Уверенность"], b["Баланс"]` | Mermaid parser rejects non-ASCII identifiers; labels can be any language |
| No bare `key: value` in radar | `Уверенность: 5` | `curve team["Команда"]{5, 2, 3, 4, 4}` | Radar uses `axis` + `curve` syntax, not key-value pairs |
| Every diagram block must be valid standalone | guessing syntax | test at https://mermaid.live | Mermaid renders each `<div class="mermaid">` independently — one bad block doesn't sink others, but it will show an error |

**Pre-generation checklist** (run mentally before emitting each `mermaid` block):

1. Is the keyword correct for this mermaid version? (`flowchart` not `graph` for LR, `radar-beta` not `radar`, `timeline` not `gantt` when events have no durations, `mindmap` for trees)
2. Are all identifiers ASCII-only? (labels can be any script)
3. Does the block parse at [mermaid.live](https://mermaid.live)?

If unsure about any diagram type's syntax, fall back to a **Markdown table** — it always renders.

---

## 3. Content Classification Rubric

Before writing, scan every semantic chunk from the transcript and classify it using the table below. The rubric ensures each chunk gets the best visual format — **rotate formats across retros** so patterns don't become predictable. If a chunk fits multiple categories, pick the one that hasn't been used recently.

| Content Type | Primary Visual | Alternatives (rotate) | When to Use |
|---|---|---|---|
| **Sentiment / mood** | Radar chart | Pie chart, grouped bar chart | Team expressed feelings, energy, confidence |
| **Events / timeline** | Gantt or timeline | Kanban board, flowchart | Sequence matters, dependencies exist |
| **What went well / didn't** | Side-by-side mindmaps | Two-column table with icons, quadrant matrix | Comparing positives vs negatives |
| **Root causes / blockers** | Fishbone (Ishikawa) | 5-Whys tree, flowchart | Drilling into why something happened |
| **Action items / next steps** | Gantt timeline | Kanban columns, numbered flowchart | Ownership + deadlines exist |
| **Decisions / trade-offs** | Decision tree | Comparison table, pros/cons matrix | Options were weighed |
| **Metrics / data** | Bar or line chart | Table with sparklines, scatter plot | Numbers were discussed |
| **Relationships / dependencies** | Flowchart (LR orientation) | Sankey diagram, ER-style diagram | "X depends on Y" or "X feeds into Y" |
| **Themes / clusters** | Mindmap (radial) | Bubble chart, Venn diagram | Recurring topics across the conversation |
| **Risks / uncertainties** | Risk matrix (2x2) | Heatmap, tornado chart | Probability + impact discussed |

**Rule:** At least 60% of the document's non-header lines must be Mermaid blocks, ASCII diagrams, or tables. Pure prose paragraphs are the minority.

---

## 4. Progressive-Disclosure Scaffold

Every content section follows this exact two-layer structure — no exceptions, no third layer, no orphaned visible detail.

**CRITICAL — Blank line rule:** A blank line MUST follow `<details>` and `<summary>` tags. Without it, Markdown renderers treat all inner content as inline HTML, collapsing line breaks and breaking bullet lists into one continuous string.

**Separator rule:** Place a `---` horizontal rule between every section (after `</details>`, before the next `##`), and one `---` between the Identity Card and the first content section. This makes the note scannable without costing many lines.

**Correct pattern (copy exactly):**

```markdown
| 📅 Date |  🏷️ Sprint/Phase  | 😀 Mood |
|---------|------------------|---------|
| ...     |  ...             | ...     |

```mermaid
```

---

## Section Name

**TL;DR:** One-line summary always visible. ≤140 characters.

<details>
<summary>Expand</summary>

[Mermaid diagram | table | ASCII visual goes here — always separated by blank lines]

- Bullet points only inside the collapsed block
- Each bullet on its own line
- No paragraphs longer than 3 lines
- Prefer examples over abstract explanations

</details>

---

## Next Section

...
```

**Wrong pattern (will break rendering):**

```markdown
<details>
- No blank line after opening tag → collapses to one line
- Same problem here
</details>
```

**Post-generation verification:** Before finalizing the document, confirm every `mermaid` code block is syntactically valid. The fastest check: paste each block into https://mermaid.live — if it renders, it's correct. If any block fails, either fix the syntax or replace with a Markdown table per the fallback rule in Section 3.

Repeat the correct pattern for every semantic chunk. The reader sees only TL;DR lines on first scan; details are one click away.

---

## 5. Accessibility & Aesthetics Rules

- **Color contrast:** All Mermaid nodes must use dark text on light backgrounds or light text on dark backgrounds — never mid-on-mid. Mermaid's built-in `default` and `dark` themes guarantee this; do not override with inline `style` that breaks contrast.
- **If you use inline `style` on a node:** Always pair a light fill with `color:#1a1a1a` or a dark fill with `color:#ffffff`. Example: `style C fill:#D4760A,color:#ffffff`
- **Mermaid identifiers:** All node IDs, axis IDs, and curve IDs MUST be ASCII (`[a-zA-Z_][a-zA-Z0-9_]*`). Use quoted labels for display text in any script: `A["Кириллица"]` not `Кириллица`. This is a Mermaid parser constraint, not a style preference.
- **Heading hierarchy:** Exactly one `#` (title), then `##` for sections. No `###` or deeper — keeps structure flat and scannable.
- **Alt text:** Every diagram gets a `<!-- alt: ... -->` comment directly above it describing what it conveys for screen-reader users.
- **Language:** Mirror the tone and vocabulary of the source audio. Casual, direct, no corporate-speak unless the speakers use it.

---

## 6. Context-Length Budget

| Zone | Max Lines |
|---|---|
| Identity card | 4 |
| TL;DR summaries (all combined) | ~10 |
| One `<details>` block per semantic chunk | ~8 each |
| Section separators (`---`) | ~5 |
| Mermaid syntax verification | ~0 (mental checklist per block) |
| Total document | **≤120 lines** |

If the transcript is rich, merge lower-priority chunks rather than exceeding the budget. If it's thin, don't pad — a 40-line retro is fine.

---

## 7. Ideas (mandatory if applicable)

Extract every brainstormed suggestion the team raised but hasn't committed to. Ideas are *possibilities*, not promises.

- Use a **mindmap (radial)** or **grouped list** inside a `<details>` block, per the scaffold in Section 4.
- One line per idea — brief, specific, no filler.
- Keep separate from Actions (Section 8). If no ideas were discussed, omit this section entirely.

---

## 8. Actions (mandatory, always present)

Every committed action item — who will do what and by when.

- Use a **numbered flowchart**, **Kanban columns**, or **Gantt timeline** inside a `<details>` block, per the scaffold in Section 4.
- Each action: **Owner** · **Task** · **Deadline**. Mark missing fields `—`.
- If no actions were agreed, write: *"No actions committed this retro."*

---

## 9. Appendix: Options Considered (mandatory, always last)

After the final `<details>` block, append:

```markdown
---

## 🧭 Options Considered

| # | Option | Decision | Why |
|---|--------|----------|-----|
| 1 | ...    | ✅ / ❌   | ... |
| 2 | ...    | ✅ / ❌   | ... |
| 3 | ...    | ✅ / ❌   | ... |
```

3–5 rows. Capture genuine design choices made while producing this specific retro — not boilerplate.
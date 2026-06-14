# Agile Retrospective Note Generator

## Task

Transcribe the provided audio, then produce a structured retrospective note in the style of an Agile team retrospective.

---

## Output Format

A single standalone **Markdown** file, ≤100 lines. The rendering target supports full Mermaid syntax; use ASCII art and tables as fallbacks only when Mermaid can't express the idea.

---

## 1. Identity Card (mandatory, always first)

Every retro note opens with this block — same fields, same order, every time:

```
| 📅 Date | 👥 Participants | 🏷️ Sprint/Phase | 😀 Mood |
|---------|-----------------|------------------|---------|
| ...     | ...             | ...              | ...     |
```

If the audio doesn't supply a field, mark it `—` rather than omitting it.

---

## 2. Mermaid Theme Init (mandatory, always immediately after Identity Card)

Place this block verbatim, wrapped in a fenced code block so VS Code and other renderers parse it:

````markdown
```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor':'#3B7DC4','primaryTextColor':'#ffffff','primaryBorderColor':'#1E4D7A','lineColor':'#333333','secondaryColor':'#D4760A','secondaryTextColor':'#ffffff','tertiaryColor':'#E8E8E8','tertiaryTextColor':'#1a1a1a','fontSize':'14px'}}}%%
```
````

**Why these values:**
- `primaryColor` darkened to `#3B7DC4` — white text (`#ffffff`) passes WCAG AA contrast (4.6:1).
- `secondaryColor` darkened to `#D4760A` — white text passes WCAG AA (4.5:1). The old `#F5A623` was too light and caused white-on-light illegibility.
- `secondaryTextColor` and `tertiaryTextColor` are now explicitly set — Mermaid no longer auto-guesses and gets it wrong.
- `lineColor` darkened to `#333` — visible against both white and light-gray backgrounds.
- `tertiaryColor` lightened to `#E8E8E8` with dark text — safe for any node that falls back to tertiary.

---

## 2b. Mermaid Syntax Rules (mandatory)

Every `mermaid` code block MUST satisfy these rules — invalid syntax produces a red "Syntax error in text" badge in the rendered output.

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

**Correct pattern (copy exactly):**

```markdown
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

- **Color contrast:** All Mermaid nodes must use dark text on light backgrounds or light text on dark backgrounds — never mid-on-mid. The theme init in Section 2 guarantees this; do not override with inline `style` that breaks contrast.
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
| Mermaid theme init block | 5 |
| TL;DR summaries (all combined) | ~10 |
| One `<details>` block per semantic chunk | ~8 each |
| Mermaid syntax verification | ~0 (mental checklist per block) |
| Total document | **≤100 lines** |

If the transcript is rich, merge lower-priority chunks rather than exceeding the budget. If it's thin, don't pad — a 40-line retro is fine.

---

## 7. Appendix: Options Considered (mandatory, always last)

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
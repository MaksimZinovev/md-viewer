# Progressive-Disclosure Scaffold — Reference

Load this file when assembling the final document structure.

## Two-Layer Section Structure

Every content section follows this exact two-layer structure — no exceptions, no third layer, no orphaned visible detail.

**CRITICAL — Blank line rule:** A blank line MUST follow `<details>` and `<summary>` tags. Without it, Markdown renderers treat all inner content as inline HTML, collapsing line breaks and breaking bullet lists into one continuous string.

**Separator rule:** Place a `---` horizontal rule between every section (after `</details>`, before the next `##`), and one `---` between the Identity Card and the first content section. This makes the summary scannable without costing many lines.

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

---
```

**Wrong pattern (will break rendering):**

```markdown
<details>
- No blank line after opening tag → collapses to one line
- Same problem here
</details>
```

**Post-generation verification:** Before finalizing the document, confirm every `mermaid` code block is syntactically valid. The fastest check: paste each block into <https://mermaid.live> — if it renders, it's correct. If any block fails, either fix the syntax or replace with a Markdown table per the fallback rule in `mermaid-and-visuals.md`.

Repeat the correct pattern for every section that warrants collapsed detail. The reader sees only TL;DR lines on first scan; details are one click away. Not every section needs a `<details>` block — short sections (Identity Card, OVERVIEW, REFERENCES) can stay flat.

---

## Context-Length Budget

| Zone | Max Lines |
| ---- | --------- |
| Identity card | 4 |
| TL;DR summaries (all combined) | ~10 |
| One `<details>` block per section | ~8 each |
| Section separators (`---`) | ~5 |
| Mermaid syntax verification | ~0 (mental checklist per block) |
| Total document | **≤120 lines** |

If the transcript is rich, merge lower-priority sections rather than exceeding the budget. If it's thin, don't pad — a 40-line summary is fine.

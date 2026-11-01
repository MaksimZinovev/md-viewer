---
name: meeting-summary-generator
description: Produce a structured, visual meeting summary from audio or text transcripts. Outputs a single Markdown file — diagrams for structured sections, tables/bullets for discrete ones.
---
<!--
version: 6
last change: split into main skill + reference files; folded diagram guidance into Techniques; softened visual quota to ≥40%; merged retro Ideas/Actions into Techniques.
-->

# Meeting Summary Generator

## Task

Use available content to produce a structured meeting summary. Input can be audio or text file(s). Immediately stop and inform the user if you cannot access files, process them, or there are major issues that may degrade output quality.

**Multiple source files:** If the source audio was split into multiple files, first identify the proper chronological sequence (use filenames, timestamps, content continuity — recurring topics, mid-sentence resumes, referenced prior statements) and reconstruct the meeting order. Treat the sequenced files as one continuous transcript during summarization. If the sequence cannot be determined confidently, stop and ask the user for the correct order.

---

## Output Format

A single standalone **Markdown** file, ≤120 lines. The rendering target supports full Mermaid syntax; use ASCII art and tables as fallbacks only when Mermaid can't express the idea.

---

## Techniques

Extract content from the transcript into the sections below. If a section has no information in the source, write "No information available" — except IDEAS, which omit entirely when none were discussed.

- **Attendees** — participants. Derive names, roles, characteristics from the transcript. Format as the creative table in Disciplines.
- **OVERVIEW** — brief meeting summary in ≤25 words: purpose + key participants.
- **KEY POINTS** — 10–20 most important discussion points (core topics, debates, significant ideas). Adapt count to meeting density. Format as bullets per Disciplines.
- **DECISIONS** — 5–10 key decisions made. **Visualise as a diagram** — options were weighed, trade-offs exist.
- **CHALLENGES** — notable challenges, risks, concerns. **Visualise as a diagram** — content has probability + impact dimensions.
- **TIMELINE** — all deadlines, important dates, milestones. **Visualise as a diagram** — content is sequential, ordered.
- **TASKS** — all action items/assignments with responsible parties and deadlines. **Visualise as a diagram** — content has ownership + deadlines (often with dependencies).
- **NEXT STEPS** — 5–10 follow-up items / next steps. **Visualise as a diagram** — content is an ordered forward sequence.
- **IDEAS** — every brainstormed suggestion raised but not committed (possibilities, not promises). **Visualise as a radial mindmap.** Omit entirely if none were discussed. Keep separate from TASKS.
- **REFERENCES** — documents, tools, projects, resources mentioned. Format as bullets or a small table per Disciplines.

For diagram-type selection and Mermaid syntax rules, see `reference/mermaid-and-visuals.md`.

---

## Disciplines

- Output only Markdown, Mermaid, ASCII diagrams, or tables.
- **Identity Card** (mandatory, always first): a 3-column table — `| 📅 Date | 🏷️ Meeting | 😀 Mood |`. If the source doesn't supply a field, mark it `—` rather than omitting it.
- **Attendees** — creative markdown table, humorous but professional:

  ```markdown
  | Name | Role | Animal | Popular movie character |
  | ---- | ---- | ------ | --------------- |
  | ...  | ...  | ...    | ...             |
  ```

  - *Role* — short description of the person's function in the meeting.
  - *Animal + ≤10 words* — best animal representing their characteristics/background, **derived from what the transcript reveals about them** (statements, tone, behavior, contributions) — not arbitrary.
  - *Movie character + ≤10 words* — best character representing their characteristics/role, **grounded in the meeting context** — pick based on how they acted or what they said in this meeting, not generic traits.
  - If no names are mentioned, write "No information available".

- **OVERVIEW** — one prose line, ≤25 words.
- **KEY POINTS** — bulleted, each ≤16 words.
- **REFERENCES** — bulleted list or a small 2-column table (Name → Note/Link).
- Use bulleted lists for text sections, not numbered lists.
- Do not repeat information across sections.
- Do not start items with the same opening words.
- If information for a section is not available in the transcript, write "No information available".
- **Visual quota:** at least 40% of non-header lines should be Mermaid blocks, ASCII diagrams, or tables. Use a diagram where the content has structure; otherwise bullets/table.

For the progressive-disclosure `<details>` scaffold, separators, and the line budget, see `reference/scaffold.md`.

---

## Section Order

1. Identity Card
2. OVERVIEW
3. Attendees
4. KEY POINTS
5. DECISIONS
6. CHALLENGES
7. TIMELINE
8. TASKS
9. NEXT STEPS
10. IDEAS (if any)
11. REFERENCES

Place a `---` horizontal rule between every section.

---

## Reference Files

- `reference/mermaid-and-visuals.md` — Mermaid syntax rules, pre-generation checklist, content→visual classification rubric, accessibility & aesthetics rules.
- `reference/scaffold.md` — progressive-disclosure `<details>` pattern, blank-line rule, section separators, context-length budget.

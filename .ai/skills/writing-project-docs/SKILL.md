---
name: writing-project-docs
description:
  Write, edit, or review Markdown under .ai/ in this repository. Use for every
  agent-resource change to keep content concise, canonical, navigational, and cleanly structured.
agents-md: Before creating or editing a file under `.ai/`, read and follow {skill}.
---

# Writing agent resources

Write for the document's audience and keep each fact in one canonical location. Agent context, prompts, workflows, and templates belong in `.ai/`.

## Prose

- Prefer concise, direct sentences and cohesive paragraphs over fragmented prose.
- Start a new paragraph only for a distinct idea. Use lists when the items are independently actionable or easier to scan as a set.
- Do not duplicate detailed content in an index or README. Link to the canonical document.
- Do not use context files for TODOs, open work, or implementation plans.

## Line width and tables

Widths are counted, not estimated, so run the formatter instead of aligning a table or measuring a line by eye. It pads every table cell so the columns line up
in the raw file, which is the only way they stay lined up after an edit.

- Wrap prose at 160 columns. Do not hand-wrap to a width you guessed at.
- Format every Markdown file you write or edit: `npx prettier@3 --prose-wrap always --print-width 160 --write <file>`. Nothing needs
  installing; npx fetches it on first use.
- Keep one concise purpose per row and avoid multiline table cells.
- Use standard Markdown table syntax.

## Structure and navigation

- Read the destination folder's `_index.md` before creating a document. If a document already serves the purpose, update it instead of creating a
  near-duplicate.
- Keep README files useful as entry points; move detailed guidance into the appropriate document and link to it.
- Use repository-relative links and link to the narrowest useful destination.

## Index files

Every folder has an `_index.md`, which is the entry point an agent reads before working in that folder. It is the only thing directing what a session opens,
so it has to be accurate before it is elegant.

The shape of an index is fixed by `.ai/templates/_index.template.md`, which is human-owned. Maintain index instances; do not change the template schema.

- Draft the **Purpose**, **Not for**, and **Conventions** prose for human review. The **Purpose** states what the folder answers and when it is worth opening,
  since that sentence is what lets a session decide whether to read further.
- Fully generate the **Index table**. Every direct file and subfolder must occur exactly once, every link must resolve, and every status must be `Current`,
  `Draft`, `Proposed`, `Superseded`, or `Reserved`.
- Do not change the section order, remove **Not for**, or invent status values.
- Update an `_index.md` in the same change that adds, removes, or renames a direct child, or changes a document's status. Regenerate only the Index table
  unless the folder's purpose, boundaries, or conventions actually changed.
- Keep indexes navigational: one concise row per entry, linking to the narrowest useful heading. Do not restate a document's contents in its index.

# 0006 - Every node is Markdown

**Status:** accepted, 2026-10-19

## Context

Decision 0005 left open how the text of a node is stored, and said Markdown only goes in and out by
conversion. Agents read and write Markdown natively, most note apps speak it, and the project lives at
knode.md. The format has to stay fast: search, the graph, permissions and sync cannot depend on
parsing text.

## Decision

- **The text of a node is Markdown:** CommonMark with GitHub Flavored Markdown and `[[links]]`,
  stored as text in `knode.db`. Agents receive and write it through MCP as it is.
- **Links are stored by id.** You write `[[Title]]`; Knode saves the target's id in the links table
  and always shows its current title, so renaming never breaks a link.
- **Metadata stays in the database.** Dates, source, agent, tags and permissions are columns and
  tables in `knode.db`, never frontmatter. YAML frontmatter is only written on export.
- **Markdown is read when a node is written, never when searching or browsing.** The plain text for
  full-text and semantic search and the links are extracted at write time.
- **A live Markdown folder, optional per space and off by default.** It mirrors every node as a `.md`
  file for Obsidian, git or any editor: one way first (v0.2), both ways later (v0.3). The `.knode`
  is always the source of truth; a change made in the folder comes in as one more edit.
- **The app edits Markdown with a live preview**, like Bear or Obsidian.
- **knode.md says it:** every node is Markdown.

## Consequences

- Search, the graph, permissions and sync cost the same as with any other text format; agents spend
  fewer tokens than with structured blocks.
- The live folder is the only real cost (extra disk writes and keeping two copies in step), paid only
  by whoever turns it on.
- The format specification fixes one Markdown dialect, so every implementation reads it the same.
- Supersedes the line of decision 0005 that kept Markdown to conversion only.

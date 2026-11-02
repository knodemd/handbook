# 0009 - Projects, and how an import becomes one

**Status:** proposed, 2026-11-03

## Context

Importing a real Obsidian vault (about 190 git repositories, 35,000 files) with the guided
onboarding made one flat list of 3,035 notes: more than half were copies from git worktrees and
submodules, one folder of 30 repositories became a single project, every note showed the time of
the import, 228 notes were all called `README`, and "projects" were generated notes with
placeholders that nothing in the app or MCP could list. A bare `[[Estado]]` resolved to whichever
of 21 `Estado` notes was oldest, inventing links between unrelated projects. Neither the person
nor their agents could find their way around it.

## Decision

- **A project is a node of kind `project`.** Other nodes belong to it with `part-of`; a project
  inside another (a repository inside an organization folder) is `part-of` it too. No new table
  or schema version: kinds and typed links already exist. The core lists projects
  (`Space::projects`, `Space::project_notes`), Knode Service serves them, and MCP has a
  `projects` tool so agents go to a project's notes without searching.
- **The onboarding finds projects in folders, deterministically.** Repositories are recognised
  by their `.git` folder; worktrees and submodules (a `.git` file) are other checkouts and stay
  out; a top-level folder with repositories in two or more subfolders is a group of projects.
  Only Markdown and the attachments notes use are read. No model decides anything; the person
  reviews the proposal before anything is written.
- **Imported notes keep their files' dates and their paths.** Dates come from the file system;
  lists, search and MCP show the file a note came from, so many `README` stay apart, without
  renaming notes and so without changing what an export writes back.
- **A link to a shared name finds the nearest note,** by `markdown.path` folders, as Obsidian
  does; ties keep the oldest, as before (format section 7.2).
- **Agent sessions join their projects.** When asked, onboarding brings the conversations agents
  held inside a project's folder, each `part-of` that project.
- **The Mac app browses by project.** The sidebar switches between Projects (drill-down) and
  Recent; a project has a page; the graph can show only projects. While an agent reads, the
  graph lights the nodes it reads, for the person only and without keeping anything.

## Consequences

- An import is useful from the first minute: a tree of projects with real dates, instead of a
  flat list. Agents can ask for one project's notes, which costs fewer tokens than searching.
- `project` joins the kinds the format defines; readers that do not know it keep and show it as
  any kind (format section 5).
- The sidebar and graph changes are a navigation design change in the Mac app and need its
  design owner's review before release.
- Several unrelated folders into one space, and syncing an imported vault with Obsidian later,
  remain open (knode#219, knode#64).

Implementation: knode #202, #204, #206, #208, #210, #213, #214, #216, #218; format #31, #32.

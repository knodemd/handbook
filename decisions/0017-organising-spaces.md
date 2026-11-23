# 0017 - How a person organises their spaces: one per audience, projects inside

**Status:** proposed, 2026-11-23

## Context

Knode can keep several spaces (v0.2), each space can hold many projects (0009), access is granted per
agent and per space (0008), and team spaces are shared per space (0015). Nothing says how a person
should combine them, and the obvious answer, one space per project, is the wrong one.

Organising a real memory showed why. One person's space held 44 projects: their own apps and
experiments, the products of a small company with three partners, a student association, and four
projects made with other people outside both. Splitting it was tempting, but with Knode as it is:

- **An agent sees one space.** `knode mcp` serves the default space or the one it is given. An agent
  working on one project could no longer read the person's preferences, the people they work with,
  or decisions made in another project, which is most of what makes a shared memory worth having.
- **Links do not cross spaces.** Notes that several projects share (a brand, a way of working, a
  person) would be copied into each space and drift apart.
- **Folder routing and MCP disagree.** Capture sends a conversation to the space of its project
  folder, but an agent's notes go to its MCP space (#398). With several spaces, a project's
  conversations and its notes end up apart.
- **What a space adds is a boundary.** Grants and team membership are per space (0015: "if someone
  must not see something, it goes in another space"). A space split by topic pays every cost above
  and buys nothing.

## Decision

- **A space is an audience.** One space per group of people, and of agents, that may read the same
  things. Topics, products and clients are projects inside a space, not spaces.
- **Start with one space.** A person's first space holds all their projects, each a project node with
  its project folder (0009). Most people never need another.
- **Split only when access requires it:**
  1. **Shared with other people:** a team space (0015), one per group of people. Several projects
     with the same people share one space; a project is not split from its team because it is
     small.
  2. **No agent should read it:** a private space where agents have `none` (finances, health,
     anything not for a model).
  3. **An agent that must not see the rest:** for example a client's agent, or an agent on an
     untrusted machine, gets a space with only what it may read.
  Size, number of projects and tidiness are not reasons to split: projects, folders and search
  already organise one space.
- **Before a project is shared, keep it as a project of the person's space**, grouped under a parent
  project named after the people or the organisation it will go with. The day the team space
  exists, that branch moves as a whole.
- **Inside a space,** one project per repository or folder of work, nested under a parent project
  when they belong together (a company and its products). Notes that apply to every project, such as
  preferences and working rules, live outside any project.
- **Onboarding suggests one space** and asks about other spaces only when the person says a project
  is shared or private (#50, #187).

## Consequences

- Until #398 lands, a second space costs context: its agents cannot read the person's other spaces,
  and notes saved through MCP go to the default space whatever the folder. Docs say so where they
  explain spaces.
- With #398, an agent reads every space it is granted and writes to the space of its working folder,
  so splitting by audience no longer loses context.
- The docs get a page, "Organise your memory", with this rule and examples (one person, a freelancer
  with clients, a team).
- Moving a project between spaces with its notes, links and history is needed when a group of
  projects becomes a team space; it is not built yet.

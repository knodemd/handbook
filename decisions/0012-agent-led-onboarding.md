# 0012 - Onboarding is done by the person's agent first

**Status:** proposed, 2026-11-17

## Context

Knode is for people who already work with agents. Its guided import (decision 0009) asks the
person to review a proposal by hand: every project, every Markdown file, every exclusion.

Moving a real vault this way, one that is also the working folder of about 80 repositories, showed
where the work is. Importing took one command. The hours went into judgment that no fixed rule or
form gets right:

- which documents are the person's notes and which are repository documentation: 1,207 of 1,584
  proposed files were `README`, `CHANGELOG`, `AGENTS.md` and similar, while some code
  repositories kept the person's status notes and session logs next to the code;
- which projects must keep their files, because a tool builds PDFs from those notes or a dedicated
  agent reads them from the folder;
- which notes are noise in Knode, such as Obsidian maps that only list every other note, and
  which folders are copies of another repository;
- changing 16 instruction files that told agents to keep memory in `Estado.md` and
  `Sesiones/`, so that agents use Knode and the two do not drift apart;
- trimming long status sections out of instruction files that load in every session.

The review screen offers this as 1,584 rows in a small list: too much detail for most people, and
still not enough to do the job well.

## Decision

- **The first choice in Get started is "Let your agent do it".** The app shows a prompt to paste
  into any agent that can run commands on the machine (Claude Code, Codex and others), with the
  chosen folder. "Do it yourself" keeps the guided review.
- **Knode ships the procedure as a guide, not as code.** `knode onboard guide` prints the steps an
  agent follows: analyze, decide what is the person's memory, ask the person once with a short
  summary, apply, check, connect the agent, move instruction files to Knode, leave a checkpoint.
  `knode onboard prompt` prints the prompt that points an agent at it. Improving onboarding is
  then mostly editing that guide.
- **The deterministic parts stay in the core.** Analysis, exclusions (generated folders, other
  checkouts, credentials), import, receipts and archive are the same commands the app uses. The
  agent only decides what needs judgment, through the editable plan.
- **One guide for every starting point.** The agent first asks where the person starts: a folder
  of notes or a vault; projects spread over several folders next to unrelated files; or nothing
  yet, where it creates the space, registers the project folders and writes a short context note
  per project from its repository. Until onboarding takes several folders natively (knode#219),
  projects in several places are gathered as a working copy of their notes, never moved.
- **Plug and play over configuration.** One prompt, no setup: the guide tells the agent which
  commands to run, what to ask and when to stop. The person approves one summary and the changes
  to their agents and instruction files.

## Consequences

- Onboarding quality follows the guide and the agent. The guide must stay short, concrete and
  safe: never touch the source, back up every file edited, ask before changing agent
  configuration.
- It costs the person's own model tokens once, for a migration they would otherwise spend hours
  on. People without an agent use the guided review.
- MCP tools for onboarding (analyze, apply, inspect) would let agents without a shell, such as
  Claude Desktop, follow the same guide; that is a later step.
- The guided review should still get simpler: a summary with good defaults first, the full list
  behind "Customize".

Implementation: knode#266 (guide, prompt, app). Real-vault migration notes: knode#193.

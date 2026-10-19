# 0004 - What Knode is

**Status:** accepted, 2026-10-19

## Context

Everything you work out with an AI agent is scattered across chats and tools, and some agents delete
their history (Claude Code removes a session after 30 days without use). Notes live in other apps.
Built-in memories, like Claude's, are closed and only work inside one product. Open tools such as
Basic Memory and Obsidian with MCP prove the need, but they lack a real app, permissions per agent,
and one memory shared by every agent.

## Decision

- Knode is a second brain for AI: one local memory that every agent reads and writes through MCP,
  with a native app to see and organise it.
- **Spaces:** each space is a `.knode` file, like the libraries of Apple Music. Agents get access per
  space, to read or to write.
- **Capture:** every conversation from every agent is kept by default, and can be turned off per
  project. Agents can also save through MCP, and notes and chat exports can be imported.
- **No AI inside the app.** The agent is the AI; Knode is its memory.
- **Free.** Sync through iCloud is free; Knode Cloud, for any platform, sharing and teams, is paid.
- **The first user is the person building it**, every day, from v0.1.

## Consequences

- The `.knode` format and the MCP server come before the app's looks.
- Every agent integration is a capture source to build and keep up to date.

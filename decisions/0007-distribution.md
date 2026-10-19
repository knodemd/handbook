# 0007 - How Knode is installed

**Status:** accepted, 2026-10-19

## Context

Some people want Knode only as a memory for their agents, from the terminal; others want the app.
Both have to share the same core and the same rule from decision 0005: only one process opens a
space.

## Decision

- **One binary, `knode`,** holds the CLI, the MCP server (`knode mcp`) and Knode Service. Installing
  the CLI is enough to give every agent a memory, without the app. When Knode Service is not
  running, the CLI starts it in the background.
- **Install the CLI:** `brew install knodemd/tap/knode` on macOS and Linux, or
  `curl -fsSL https://knode.md/install.sh | sh`.
- **The Mac app carries the same binary inside.** Install it with
  `brew install --cask knodemd/tap/knode` or from knode.md. The app offers to install the `knode`
  command; if the CLI is already installed with Homebrew, it uses that one instead.
- **Connect an agent:** `knode connect claude-code` (also `codex`, `gemini`, `claude-desktop`), or
  by hand, for example `claude mcp add knode -- knode mcp`.
- **The iPhone and iPad app** comes from the App Store; **the web app** runs at app.knode.md.
- **Libraries:** `knode-format` and `knode-md` on crates.io, and the Swift package at
  `https://github.com/knodemd/knode`.
- **Linux gets the CLI early**, because the core is Rust; the Linux and Windows apps come much
  later.
- **Everything stays in the `knode` repository.** `homebrew-tap` only holds the formula and the cask,
  and `knode.md/install` lists every command.

## Consequences

- One version for the CLI, the MCP server, the service and the apps.
- Releases build the binary for macOS (Apple silicon and Intel) and Linux, sign and notarise the Mac
  builds, and bump the formula and the cask.
- The install page on knode.md and this decision change together.

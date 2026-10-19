# 0005 - Architecture

**Status:** accepted, 2026-10-19

## Context

Knode must let a person, Claude Code and Codex write to the same memory at the same time, search it
instantly, sync it across devices and teams later, and run natively on each platform.

## Decision

- **A core in Rust**, shared by every app, the service, the MCP server, the CLI and the cloud. Each
  platform is a native app on top of it (SwiftUI on Apple), bridged with UniFFI.
- **The `.knode` format is a package** that the Finder shows as one file:
  - `manifest.json`: format version, id and space name;
  - `knode.db`: SQLite in WAL mode, with nodes, typed links, provenance, history, folders,
    permissions, full-text search (FTS5) and local semantic search (sqlite-vec);
  - `blobs/`: attachments by hash;
  - `sync/`: changes as Loro (CRDT) updates.
- **The app works only with `.knode`.** The text of each node is Markdown, and a space can also be
  mirrored as a folder of `.md` files ([decision 0006](0006-markdown.md)).
- **Knode Service** runs in the menu bar on the Mac and is the only process that opens a space. The
  app and the agents talk to it, so everyone writes at once and sees changes live; text edited by
  two at once merges with Loro, and history keeps every version.
- **Sync never copies the database.** It exchanges Loro updates: through the user's private CloudKit
  database (iCloud, free), or through Knode Cloud (paid), whose server is open source and encrypted
  end to end.
- **Installed on the Mac** with Homebrew or from knode.md; the iPhone app comes from the App Store
  ([decision 0007](0007-distribution.md)).

## Repositories

One product, so everything that ships together lives in one repository:

- `knode`: the product. `core/` (the Rust workspace: format library, Markdown converter, capture,
  service, MCP server, CLI, sync and the bindings), `mac/`, `ios/`, `web/` (app.knode.md), and later
  `android/` and `windows/`, with `brand` as a git submodule and the product docs in `docs/`.
- `format`: the open `.knode` specification and example files, for anyone implementing it.
- `cloud`: Knode Cloud, deployed and versioned on its own; it uses the crates of `knode/core`.
- `homebrew-tap`: required by Homebrew.

The library and the converter are published on their own from `knode/core` (crates.io, a Swift
package, npm). A contributor who only works on one app clones just `core/` and that app with a sparse
checkout. If platform teams ever appear, splitting is easy; joining later is not.

## Consequences

- One change to the core and the apps is one pull request.
- Other platforms and languages get their own repository on top of `core` when they are needed.

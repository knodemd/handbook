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
- **The app works only with `.knode`.** Markdown goes in and out by conversion, in both directions.
- **Knode Service** runs in the menu bar on the Mac and is the only process that opens a space. The
  app and the agents talk to it, so everyone writes at once and sees changes live; text edited by
  two at once merges with Loro, and history keeps every version.
- **Sync never copies the database.** It exchanges Loro updates: through the user's private CloudKit
  database (iCloud, free), or through Knode Cloud (paid), whose server is open source and encrypted
  end to end.
- **Installed on the Mac** with Homebrew or from knode.md; the iPhone app comes from the App Store.

## Repositories

`core` (Rust workspace: `knode-format`, `knode-md`, `knode-ingest`, `knode-service`, `knode-mcp`,
`knode-cli`, `knode-sync`, `knode-ffi`), `format` (the specification), `swift` (the core as a Swift
package), `mac`, `ios`, `cloud` and `homebrew-tap`. Each crate is published on its own, so the
library and the converter can be used without the rest.

## Consequences

- One change to the format is one pull request across the library, the converter and the server.
- Other platforms and languages get their own repository on top of `core` when they are needed.

# 6 - Roadmap

Where Knode is and what comes next. Each version is a milestone in the repositories it touches, and
each line below becomes an issue. Dates after v0.1 are set when the version before closes.

## Foundation - done

Brand kit, handbook, licences, knode.md.

## v0.1 - You use it - built

Knode working with Claude Code every day.

**Status:** built and checked end to end with Claude Code. It waits for its first release: signing,
notarisation and Homebrew ([knode#13](https://github.com/knodemd/knode/issues/13)).

- **Format:** draft specification v0: the `.knode` package, `manifest.json`, tables for nodes, links
  and provenance, time-ordered ids (ULID), an example space.
- **Core:** create and open a space; create, read, edit and delete nodes and links; full-text search.
- **Knode Service:** in the menu bar; the only process that opens a space; the app and the agents talk
  to it over a local socket.
- **MCP:** search, read, write, link and recent; installed in Claude Code with one command.
- **Capture:** every Claude Code and Codex conversation becomes a node, with its project and date.
- **Mac app:** open a space, list nodes, read and edit a note, search.
- **CLI:** `knode init`, `knode search`, `knode import`, `knode connect`; the MCP server is `knode mcp`.
- **Distribution:** the `knode` binary with Homebrew; the Mac app with Homebrew and from knode.md,
  signed and notarised, offering to install the `knode` command; every command on knode.md/install.

Done when: it is used for a week straight and Claude finds what was worked out days before.

## v0.2 - Spaces and permissions

**Status:** in progress: change history and undo, Markdown export and import, live Markdown folder,
more agents captured, and the specification for settings, folders and permissions are in.

- **Spaces:** several `.knode` files, switched like Apple Music libraries; create, rename, icon.
- **Folders:** which projects and folders feed each space; capture off per project.
- **Permissions:** per agent and per space: read, write or nothing; each agent has its own key.
- **Capture:** every agent: Gemini CLI, Cursor, Copilot CLI, OpenCode, Amp, Windsurf, Cline, Aider.
- **Markdown:** `.knode` to Markdown and back; import an Obsidian vault; an optional live folder of
  `.md` files, written out from the space.
- **History:** every change kept; undo, and see who changed what.
- **Linux:** the `knode` binary for Linux, with Homebrew and `install.sh`.

Done when: a work agent sees nothing personal, and an Obsidian vault goes in and out unchanged.

## v0.3 - Map and import

- **Map:** graph and canvas with saved positions; typed links and backlinks.
- **Search:** local semantic search (a small model and sqlite-vec) and hybrid search for agents.
- **Import:** Claude, ChatGPT, Gemini and Perplexity exports; Apple Notes, Bear, Notion, Evernote;
  PDF, web pages, Kindle and Readwise.
- **Together:** you and your agents editing one note at once, merged with Loro.
- **Markdown folder both ways:** edits made in the live `.md` folder come back into the space.
- **Attachments:** images, PDFs and files inside the space.

Done when: everything already written comes in and is found by meaning, not by words.

## v0.4 - Sync and iPhone

- **Sync:** Loro updates between devices, never the database itself.
- **iCloud:** free, through CloudKit, for Mac, iPhone and iPad.
- **Knode Cloud:** a Rust server with accounts, sync and end-to-end encryption (`cloud`).
- **iPhone and iPad:** read, search and edit; save from the share sheet.

Done when: a note written on the Mac is on the iPhone at once, and the server cannot read it.

## v0.5 - Collaboration and web

- **Sharing:** read-only links, shared spaces and team spaces with roles.
- **Web app:** app.knode.md, with the core in WebAssembly and the same encryption.
- **Payments:** Knode Cloud subscription, personal and per team.

Done when: two people edit one space, each from their own device.

## v0.9 - Public beta

- **Quality:** search 10,000 nodes in under 10 ms; no data loss under stress tests.
- **Security:** a review of the encryption and the server.
- **Onboarding:** create a space, connect your agents, import.
- **Format:** specification 1.0 frozen and published.
- **Libraries:** published on their own: crates.io, a Swift package and npm.
- **Documentation:** knode.md/developers covers the format, MCP, CLI and libraries.

Done when: ten people use it for a week without help and without losing anything.

## v1.0 - Launch

The Mac app through Homebrew and knode.md, the iPhone app on the App Store, Knode Cloud open.

Done when: anyone installs it, connects their agents and starts in five minutes.

## After v1.0

- **Plugins:** an API in the core, run in WebAssembly and sandboxed, with `plugin-template` and a
  `plugins` registry.
- **Integrations:** Raycast, Obsidian, VS Code.
- **Platforms:** Android and Windows, native on the same core.

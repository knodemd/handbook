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

## v0.2 - Spaces and permissions - built

**Status:** built: several spaces, folders, permissions with agent keys, history and undo, Markdown
out and back with Obsidian, a live Markdown folder, every agent connected and all but Windsurf's
Cascade captured, Linux builds and `install.sh`. Left: checks on real data (an Obsidian vault, real
session files of each agent), Homebrew on Linux, and the release
([knode#68](https://github.com/knodemd/knode/issues/68)).

- **Spaces:** several `.knode` files, switched like Apple Music libraries; create, rename, icon.
- **Folders:** which projects and folders feed each space; capture off per project.
- **Permissions:** per agent and per space: read, write or nothing; each agent has its own key.
- **Capture:** every agent: Gemini CLI, Cursor, Copilot CLI, OpenCode, Amp, Windsurf, Cline, Aider.
- **Markdown:** `.knode` to Markdown and back; import an Obsidian vault; an optional live folder of
  `.md` files, written out from the space.
- **History:** every change kept; undo, and see who changed what.
- **Linux:** the `knode` binary for Linux, with Homebrew and `install.sh`.

Done when: a work agent sees nothing personal, and an Obsidian vault goes in and out unchanged.

## v0.3 - Map and import - in progress

**Status:** in: graph, canvas and typed links in the Mac app; attachments; editing together with
Loro; search by meaning and hybrid search in the app, Knode Service, MCP and the CLI, on Linux arm64
too; every importer in `knode import` (chat exports, Apple Notes, Bear, Notion, Evernote, PDF, web,
Kindle, Readwise exports); the Markdown folder both ways. Left: the app asking about files deleted
from the folder, Readwise through its API, checks against real exports and on a real space (search
quality and speed), musl builds for Linux, and the release
([knode#69](https://github.com/knodemd/knode/issues/69)).

- **Map:** graph and canvas with saved positions; typed links and backlinks.
- **Search:** local semantic search (a small model and sqlite-vec) and hybrid search for agents.
- **Import:** Claude, ChatGPT, Gemini and Perplexity exports; Apple Notes, Bear, Notion, Evernote;
  PDF, web pages, Kindle and Readwise.
- **Import chats and mail:** WhatsApp ([knode#222](https://github.com/knodemd/knode/issues/222)), Telegram Desktop ([knode#223](https://github.com/knodemd/knode/issues/223)) and Slack workspace
  ([knode#224](https://github.com/knodemd/knode/issues/224)) exports; Gmail through Google Takeout and Apple Mail ([knode#225](https://github.com/knodemd/knode/issues/225)).
- **Together:** you and your agents editing one note at once, merged with Loro.
- **Markdown folder both ways:** edits made in the live `.md` folder come back into the space.
- **Attachments:** images, PDFs and files inside the space.

Done when: everything already written comes in and is found by meaning, not by words.

## v0.4 - Capture life

Sources that bring chats, mail, meetings, calendar, messages and personal media into a space by
themselves, on the device (decisions [0010](decisions/0010-sources-and-plugins.md),
[0011](decisions/0011-meetings-on-the-device.md) and [0013](decisions/0013-live-connectors.md)).

- **Sources:** one interface for importers and connectors, switched on per space in Settings >
  Sources ([knode#226](https://github.com/knodemd/knode/issues/226)), with connect, pause and
  exclude in the app and `knode sources` in the CLI ([knode#299](https://github.com/knodemd/knode/issues/299)).
- **Live chats and mail:** connect once and what is new arrives by itself, through the v0.3
  importers. On the Mac, read-only: Apple Mail with every account added to it
  ([knode#294](https://github.com/knodemd/knode/issues/294)), WhatsApp for Mac
  ([knode#295](https://github.com/knodemd/knode/issues/295)) and iMessage
  ([knode#232](https://github.com/knodemd/knode/issues/232)). Through official APIs, on Linux too:
  Telegram ([knode#296](https://github.com/knodemd/knode/issues/296)), Gmail by IMAP and Sign in
  with Google ([knode#297](https://github.com/knodemd/knode/issues/297)) and Slack as a Marketplace
  app ([knode#298](https://github.com/knodemd/knode/issues/298)).
- **Calendar:** events through EventKit, linked to the notes and meetings they belong to ([knode#227](https://github.com/knodemd/knode/issues/227)).
- **Meetings:** recorded on the device with a visible indicator and consent per meeting
  ([knode#228](https://github.com/knodemd/knode/issues/228)), transcribed on the device ([knode#229](https://github.com/knodemd/knode/issues/229)) and summarised on the device into a linked note
  with decisions and action items ([knode#230](https://github.com/knodemd/knode/issues/230)).
- **Voice Memos:** new memos brought in as they happen
  ([knode#231](https://github.com/knodemd/knode/issues/231)).
- **Personal media:** photos, videos and audio added to a space with the original kept
  ([knode#236](https://github.com/knodemd/knode/issues/236)), audio and video transcribed ([knode#237](https://github.com/knodemd/knode/issues/237)), photos read with OCR and on-device labels,
  with date and place as properties ([knode#238](https://github.com/knodemd/knode/issues/238)), and the Photos library for chosen albums
  ([knode#239](https://github.com/knodemd/knode/issues/239)).
- **Storage:** a storage view per space and limits for large files, counted in sync and Knode
  Cloud ([knode#241](https://github.com/knodemd/knode/issues/241)).

Done when: a WhatsApp message, an email and a Telegram message arrive within a minute without
exporting anything; a meeting, a voice memo and a whiteboard photo are found by what was said or written
in them, and nothing left the device.

## v0.5 - Sync and iPhone

- **Sync:** Loro updates between devices, never the database itself.
- **iCloud:** free, through CloudKit, for Mac, iPhone and iPad.
- **Knode Cloud:** a Rust server with accounts, sync and end-to-end encryption (`cloud`)
  ([decision 0014](decisions/0014-sync-cloud-and-remote-access.md)).
- **Remote access for agents:** Claude on the phone, claude.ai and other remote agents use Knode
  through a remote MCP connector: mode A asks the person's Mac through a relay that cannot read
  anything; mode B, switched on per space, answers from Knode Cloud when every device is off.
- **iPhone and iPad:** read, search and edit; save from the share sheet.
- **In-person meetings:** record on the iPhone into a space, with the same consent rules as on the
  Mac ([knode#240](https://github.com/knodemd/knode/issues/240)).

Done when: a note written on the Mac is on the iPhone at once, the server cannot read it, and
Claude on the phone finds it by asking.

## v0.6 - Collaboration and web

- **Sharing:** read-only links, shared spaces and team spaces with roles and admin
  ([decision 0015](decisions/0015-teams.md)).
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
  `plugins` registry. Plugins implement the same source interface as built-in sources.
- **Plugin sources:** a browser highlights extension
  ([knode#234](https://github.com/knodemd/knode/issues/234)), and podcast and video transcripts ([knode#235](https://github.com/knodemd/knode/issues/235)).
- **Integrations:** Raycast, Obsidian, VS Code.
- **Platforms:** Android and Windows, native on the same core.

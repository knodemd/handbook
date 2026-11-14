# 0010 - Sources and plugins

**Status:** proposed, 2026-11-14

## Context

Decision 0004 says notes and chat exports can be imported, and Knode already has importers for
Obsidian, Notion, Apple Notes, agent exports and more. Next come chats (WhatsApp, Telegram,
Slack), mail, and things that keep happening: calendar events, meetings, voice memos, messages,
and personal media such as photos, videos and recordings made on a phone or a camera. Plugins
(after v1.0) will let others add their own. Without one shape, every one of these would bring its
own settings, its own permissions and its own way of writing into a space.

## Decision

- **Two layers.** *Sources* are first-party and built into Knode. *Plugins* are third-party, run
  sandboxed in WebAssembly, and come after v1.0.
- **Two kinds of source.** *Importers* run once on a file or folder the user gives them, and live
  in the core. *Connectors* keep running and bring in what is new; they usually live in the Mac
  app because they need native APIs (audio, EventKit, Messages, PhotoKit).
- **One interface.** Every source has an id, a name, its kind, the permissions it needs and its
  settings, and writes nodes only through the space API, with provenance naming the source.
  Plugins implement the same interface, so a first-party source could later ship as a plugin, and
  third parties can add sources such as live Gmail or Slack connectors.
- **Switched on per space.** Every source is off until the user turns it on for a space in the
  app's Sources settings. It writes only into the space the user picks, under the rules of
  decision 0008. System permissions (Calendar, Microphone, Photos, Full Disk Access) are asked for
  when a source is switched on, not before.
- **The same to the user.** In Settings, a built-in source and a plugin look alike: a name, what
  it reads, and an on/off switch per space.
- **Personal media are a source too.** Photos, videos and audio added to a space keep the original
  file as an attachment of a node. On the device, Knode turns each one into searchable text: audio
  and video into a transcript (the same pipeline as meetings, decision 0011), photos into the text
  Vision finds in them plus on-device labels and, where the on-device model accepts images, a
  short description. That text is indexed for words and meaning, and the date and place in the
  file's metadata become properties of the node, so "the whiteboard photo from the Madrid meeting"
  finds it.
- **Large files are planned for.** Each space shows how much room it takes and what fills it, and
  sync and Knode Cloud quotas account for media.

## Consequences

- Importers already in the core become sources without changing what they do.
- The plugin API ([knode#61](https://github.com/knodemd/knode/issues/61)) exposes the source
  interface instead of inventing its own.
- Connectors that need a service's sign-in, review or rate limits (Gmail, Slack) are plugins, not
  part of the app, so those obligations stay outside the core.
- Spaces with media grow much larger than spaces of notes; the sync client
  ([knode#25](https://github.com/knodemd/knode/issues/25)) and Knode Cloud
  ([knode#27](https://github.com/knodemd/knode/issues/27)) must handle large attachments and count
  them in quotas.

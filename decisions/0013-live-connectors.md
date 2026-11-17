# 0013 - Live connectors for chats and mail

**Status:** accepted, 2026-11-17

This decision amends [0010](0010-sources-and-plugins.md), which planned live connectors for v0.6
and left Gmail and Slack to plugins after v1.0.

## Context

v0.3 brings chats and mail in from exports: a WhatsApp `.zip`, a Telegram Desktop JSON export, a
Slack workspace export, a Google Takeout `.mbox`, an Apple Mail mailbox. Each one asks the person
to export by hand, and whatever is said after the export is missing until they remember to do it
again. A memory that has to be refreshed by hand is not a memory agents can rely on.

The goal is to connect a source once and have Knode Service bring in what is new, the way it
already captures agent conversations. What each service allows was checked on 2026-11-17:

| Source | What is possible | What it asks of the person | Limits |
|---|---|---|---|
| Apple Mail | Its store in `~/Library/Mail` is readable on the Mac; v0.3 already reads it | Full Disk Access | Mac only |
| Gmail | Through Apple Mail when the account is there; IMAP with IDLE; the Gmail API with Google sign-in | Nothing, an app password (needs 2-Step Verification), or signing in with Google | `gmail.readonly` is a restricted scope: up to 100 users need no review; past that, Google verification (about six weeks, renewed every year) and possibly a security assessment |
| iMessage | `~/Library/Messages/chat.db` is an unencrypted SQLite database | Full Disk Access | Recent messages keep their text in a binary `attributedBody` that must be decoded |
| WhatsApp | WhatsApp for Mac keeps its chats unencrypted in `ChatStorage.sqlite`, in its group container; there is no API for personal accounts | WhatsApp for Mac linked to the phone, and Full Disk Access | Undocumented format; only the history the phone synced to the Mac; disappearing messages are removed |
| Telegram | The official MTProto API, as a client with its own `api_id` | Signing in once with the phone number and a code | Accounts on unofficial clients are watched for abuse; the client must be open source and cannot be called "Telegram"; the Mac app's own database is encrypted |
| Slack | The Web API with a user token, reading `conversations.history` and replies | Installing the app in the workspace, which many workspaces ask an admin to approve | Since 2025 apps outside the Slack Marketplace get 1 request a minute and 15 messages on history; Marketplace and internal apps keep the normal limits. Socket Mode apps cannot be listed |
| Signal | Nothing: the database is encrypted and its key is in a Keychain item only Signal can open | | No API and no export |

Sources: [Mysk on WhatsApp's local storage](https://pasqualepillitteri.it/en/news/3462/whatsapp-chats-unencrypted-macos-ios-mysk-research),
[Slack rate limits for non-Marketplace apps](https://docs.slack.dev/changelog/2025/05/29/rate-limit-changes-for-non-marketplace-apps/),
[Gmail restricted scopes](https://developer.nylas.com/docs/provider-guides/google/google-verification-security-assessment-guide/),
[Telegram API terms](https://core.telegram.org/api/terms),
[Signal Desktop key storage](https://github.com/signalapp/Signal-Desktop/pull/6849).

## Decision

- **Connectors move to v0.4.** Capture life, planned as v0.6, becomes v0.4. Sync and iPhone move
  to v0.5, and Collaboration and web to v0.6.
- **Connectors live in Knode Service, in the core.** None of these sources needs a native Apple
  framework, so they are written in Rust next to agent capture, and the ones that use a network API
  (Telegram, Gmail, Slack) also run on Linux. Gmail and Slack are built in, not plugins.
- **They reuse the v0.3 importers.** A connector reads from where it stopped, hands what is new to
  the importer of that source, and writes the same nodes an export would: one node per chat,
  thread or mail thread, one per person. A later export of the same chat finds the nodes the
  connector wrote, and the other way round.
- **Three ways in, in this order of preference:**
  1. **Local databases on the Mac:** Apple Mail (and with it every account added there: Gmail,
     iCloud, Outlook), iMessage and WhatsApp for Mac. No account, no password, nothing on the
     network.
  2. **Official APIs:** Telegram with a Knode `api_id`, Gmail by IMAP with an app password and by
     Google sign-in, Slack as a Slack Marketplace app.
  3. **Never** unofficial protocols (WhatsApp Web clones such as whatsmeow or Baileys) or breaking
     another app's encryption (Telegram's or Signal's databases). They break the services' terms
     and can get the person's account banned.
- **Undocumented databases are read, read-only.** Knode opens a copy of the database, never the
  live file, and never writes to it. Before reading, it checks the tables and columns it expects;
  if they changed, it stops, writes nothing and says the connector needs an update. Tests use
  synthetic databases with the same schema, never real ones.
- **History is asked when connecting.** Connecting a source offers **From today**, selected, or
  **Everything it has**. Older history can still come from an export.
- **Everything comes in, with exclusions.** A connector is off until the person switches it on for
  a space (decision 0010). Once on, every chat, channel or mail folder comes in, new ones included,
  and the person excludes the ones they do not want; an excluded chat is never read again. Spam and
  trash stay out unless named.
- **Gmail:** through Apple Mail when the account is there, by IMAP with an app password otherwise,
  and by **Sign in with Google** from the start. Knode applies for Google's verification of the
  `gmail.readonly` scope now, so the 100-user limit does not block the beta. The app uses the
  desktop flow with a loopback redirect and PKCE; mail goes straight from Google to the device.
- **Slack:** a public Knode app in the Slack Marketplace, with a user token and only the read
  scopes it needs. It reads new messages every few minutes; until the Marketplace review passes,
  it works under the lower limits.
- **Telegram:** a Knode `api_id`, the person signs in with their phone, and new messages arrive as
  they happen while Knode Service runs; on start it catches up from where it stopped. The client
  library is chosen in its issue: grammers (pure Rust) is preferred over TDLib (C++) for a single
  static binary.
- **Secrets stay in the system keychain:** the Telegram session, Google and Slack tokens and IMAP
  app passwords are never written into a space, a settings file or a log.
- **One place to connect:** Settings > Sources in the Mac app (and `knode sources` in the CLI)
  shows each source, what it reads, the permission it needs, when it last brought something in and
  any error, with Connect, Pause and Exclude.

## Consequences

- The roadmap and the milestones are renumbered: v0.4 Capture life, v0.5 Sync and iPhone, v0.6
  Collaboration and web. iPhone, sync and Knode Cloud arrive one version later.
- The connector framework is the source interface of
  [knode#226](https://github.com/knodemd/knode/issues/226): an id, a cursor per space, the
  permissions it needs, settings and exclusions.
- Each connector needs its importer to know the service's own ids (a WhatsApp chat's JID, a
  Telegram chat id, a Slack channel id, a mail `Message-ID`), so exports and connectors find the
  same nodes. WhatsApp exports carry no id, so the chat's name stays the fallback.
- Reading undocumented databases can break with an app update. The schema check makes that a
  clear message instead of wrong data, and fixing it is part of maintaining the connector.
- The owner has work outside the code: registering the Telegram `api_id`, the Google Cloud project
  and its verification, the Slack app and its Marketplace listing, and a privacy policy and terms
  on knode.md, which both reviews require.
- Slack's OAuth exchange needs the app's client secret, which a desktop app cannot keep. If Slack
  offers no PKCE flow for public clients, a small Knode endpoint makes the exchange and never sees
  a message; this is settled in the Slack issue.
- Linux gets Telegram, IMAP, Gmail and Slack live; Apple Mail, iMessage and WhatsApp stay Mac only,
  with their exports on Linux.
- [knode#233](https://github.com/knodemd/knode/issues/233) (Gmail and Slack as plugins) is
  replaced by the Gmail and Slack connector issues.

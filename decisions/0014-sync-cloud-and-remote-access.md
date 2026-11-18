# 0014 - Sync, Knode Cloud and remote access for agents

**Status:** proposed, 2026-11-18

This decision details the sync of [0005](0005-architecture.md) and the paid Knode Cloud of
[0004](0004-product.md), and adds remote access for agents that do not run on the person's
machine.

## Context

Today an agent reaches Knode through `knode mcp`, a process it starts on the same Mac, which talks
to Knode Service over a local socket. Nothing leaves the machine except what the agent itself sends
to its model.

That only works for agents that run on that machine. Claude on the iPhone, claude.ai, the Claude
desktop app's connectors and ChatGPT run on their providers' servers, and those servers cannot
reach a Mac behind a home router, often asleep, with no public address. They do accept **remote MCP
servers**: an HTTPS URL with OAuth sign-in, added once in the provider's settings and then
available on every device of that account.

Without that, Knode on the phone is a list of thousands of notes to search by hand. The value is
asking the agent "what did we decide about X?" from anywhere. That is what people would pay for,
and the roadmap does not include it yet.

Three things are easy to mix up and are kept apart here:

1. **Sync:** the person's notes on all their devices.
2. **Remote access, mode A:** a remote agent asks one of the person's own devices, through Knode
   Cloud.
3. **Remote access, mode B:** a remote agent asks Knode Cloud, which answers from its own copy of
   the space.

## Decision

### Where the data lives

- **Every device keeps a full copy** of each space it has, in its own `.knode` package. There is no
  master copy; sync keeps the copies equal.
- **Sync never copies the database** (0005). Each change is a Loro update. A device uploads its
  updates and downloads the others', and Loro merges concurrent edits.
- **Updates are encrypted on the device** before they leave it, with a key per space. Whatever
  carries them, iCloud or Knode Cloud, stores bytes it cannot read.

### Sync through iCloud - free

- Updates and attachments go to the person's **private CloudKit database**, in their own Apple
  account and their own iCloud storage. Knode runs no server for it and pays nothing.
- The space keys travel in the person's **iCloud Keychain**, so their Apple devices can open the
  updates and Apple cannot.
- Apple devices only: Mac, iPhone and iPad. No web, no Linux or Windows, and no remote access,
  which needs a server of ours to call.

### Knode Cloud - paid

An open source Rust server in the `cloud` repository, using the crates of `knode/core`. It has three
parts, each optional and independent:

| Part | What it stores | Can Knode read it? |
|---|---|---|
| **Sync** | Encrypted Loro updates and attachments, to carry them between devices | No |
| **Relay (mode A)** | Nothing | No |
| **Cloud access (mode B)** | A readable copy of the spaces the person chooses, with their search index | Yes, those spaces only |

**Sync** is the same as iCloud's, on any platform: Mac, iPhone, iPad, Linux, Windows and the web
app, whose core runs in WebAssembly and decrypts in the browser.

### Remote access, mode A: a device of the person answers

```
claude.ai / Claude on the phone ──HTTPS──▶ Knode Cloud relay ◀══ tunnel ══ Knode Service (Mac)
```

- Knode Service opens an **outbound** connection to the relay and keeps it open. Going out, it
  crosses the home router with nothing to configure, like Cloudflare Tunnel or ngrok.
- The person adds `https://mcp.knode.md` as a connector once and signs in with their Knode account
  (OAuth 2.1). The relay knows whose request it is and passes it down that person's tunnel.
- Knode Service answers with the same tools, the same search and the same permissions as the local
  MCP server. A remote agent is one more agent with its own grants per space (0008). Writes land on
  the device and reach the others through sync.
- **Strict relay:** TLS ends on the device, not on the relay. Each person gets a subdomain with a
  certificate held only by their device; the relay forwards the encrypted bytes by SNI and never
  sees a request or an answer. Knode cannot read anything, in transit or at rest.
- Mode A needs **no copy in the cloud** and does not depend on Knode Cloud sync: it works with
  iCloud sync or with no sync.
- Only a machine that can keep a connection open answers: a Mac, or Linux. iOS does not let an app
  stay listening in the background, so the iPhone is never the one answering.
- When no device is connected, the agent gets "this Knode is not available", unless the space has
  mode B.

### Remote access, mode B: Knode Cloud answers

```
claude.ai / Claude on the phone ──HTTPS──▶ Knode Cloud (its copy of "Work") 
```

- The person switches on **"Let my agents use this space from the cloud"** for one space. Their
  device gives Knode Cloud that space's key, and only that one.
- Knode Cloud keeps a copy of the space with the core crates, kept up to date by sync, and answers
  even when every device is off. Writes become Loro updates that reach the devices.
- **The device computes the embeddings** and uploads them with the text. The server only embeds
  the query, which keeps mode B cheap.
- The app says clearly, where the switch is, that Knode can read that space. Switching it off
  deletes the copy, the index and the key from the server.
- **A first, B as fallback:** when a device of the person is connected, it answers; otherwise the
  cloud copy answers, if the space has one.

### What is free and what is paid

| | Free | Personal | Teams |
|---|---|---|---|
| App, CLI, Knode Service, local agents, capture, import, connectors | Yes | Yes | Yes |
| iPhone and iPad app | Yes | Yes | Yes |
| Sync | iCloud, Apple only | Knode Cloud, any platform | Knode Cloud |
| Web app | No | Yes | Yes |
| Remote access, mode A | No | Yes, with the person's Mac | Yes, with a machine of the team |
| Remote access, mode B | No | Yes, per space | Yes, per space |
| Attachment storage | The person's iCloud | A quota | A quota per team |
| Shared spaces, roles, admin ([0015](0015-teams.md)) | No | No | Yes |

Prices are not part of this decision. Mode A costs Knode almost nothing, but it is in Personal: for
someone who only uses Apple devices, iCloud already syncs for free, and asking their agent from the
phone is the main reason to pay. A free trial lets people try it.

Anyone can run Knode Service on a machine that is always on, with their own relay. The code is open,
and that is fine: people pay so it works without doing that.

### Running Knode Cloud

- **One server to start:** a VPS with an extra volume and off-site backups holds accounts, OAuth
  tokens, encrypted updates, attachments and the mode B copies.
- **Attachments move to object storage** (such as Cloudflare R2 or Hetzner Object Storage) when they
  outgrow the volume, or when a second server is needed. Photos, audio and meetings (v0.4) are
  what grows.
- **Losing the server loses no data:** every device holds a full copy and uploads again. Mode B
  copies are rebuilt from sync.
- Payments are taken on the web, not in the App Store.

## Consequences

- Remote access goes into v0.5 with sync, and the relay becomes part of Knode Cloud from its first
  version.
- Knode Service gains an outbound tunnel, an OAuth client for Knode accounts, and a certificate per
  device for the strict relay.
- The MCP server gains a Streamable HTTP transport next to stdio, with the same tools and checks.
- Mode B is the one place where Knode can read a person's data. It is opt-in per space, labelled
  where it is switched on, and reversible.
- Text an agent reads is still sent to that agent's model provider, local or remote. That is true
  today and does not change.

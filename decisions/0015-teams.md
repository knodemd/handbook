# 0015 - Teams: shared spaces, roles and admin

**Status:** proposed, 2026-11-18

This decision details the shared and team spaces of v0.6 and the Teams plan of
[0014](0014-sync-cloud-and-remote-access.md).

## Context

A space belongs to one person today. Teams want one memory that every member and every member's
agents read and add to: what one person's agent works out on Monday, another's finds on Tuesday,
without anyone writing it up. That is what Knode does that Notion or Confluence do not.

It has to keep the promises already made: spaces are encrypted end to end (0005, 0014), every agent
has its own key and grants (0008), and work never sees what is personal.

## Decision

### Shared spaces

- A **team space** is an ordinary space shared by several people. Each member's devices keep a full
  copy, kept equal by Knode Cloud sync. In the app it sits next to the person's own spaces.
- Members and their agents edit at once; Loro merges, and history says who wrote what: "Ángel",
  "Álvaro's Claude Code".
- **Sharing without Knode reading:** each person has a key pair. Inviting someone gives them the
  space key encrypted to their public key. The server stores and passes on encrypted updates, as in
  personal sync.
- A person can be in several teams. Their personal spaces are never part of a team.

### Roles

| Role | Read | Write | Invite and remove | Billing, team settings, delete |
|---|---|---|---|---|
| **Owner** | Yes | Yes | Yes | Yes |
| **Admin** | Yes | Yes | Yes | No |
| **Editor** | Yes | Yes | No | No |
| **Reader** | Yes | No | No | No |

- **Read-only without reading:** every update is signed by the member who made it. The server
  rejects updates from a reader by their signature, without opening them, and every device checks
  the signatures too.
- **Agents inherit their person's role and can be narrowed:** a reader's agents only read; an
  editor can keep one of their agents read-only in a team space. These are the grants of 0008,
  per member.

### Admin

- **Members:** invite by email or link, change roles, remove.
- **Removing a member rotates the space key:** they see nothing new. What their devices already
  downloaded cannot be erased from them; the app says so.
- **Activity:** who, or which agent, changed what and when, from the space's history.
- **Remote access per space:** mode A with a machine of the team that is always on, a Mac mini or a
  Linux server, so Knode never reads the team's data; or mode B in Knode Cloud, so nothing has to
  be kept running; or A with B as fallback.
- **One connector for the organisation:** an admin adds Knode once to Claude Team or Enterprise.
  Each member signs in with their own account, and their agent sees only the spaces they can.
- **Billing per member**, one invoice per team.
- Later, for companies: SSO (Google Workspace, Okta), provisioning, retention rules.

### Read-only links

- A link to one node or a set of nodes, read-only. The key goes in the link's fragment (`#…`),
  which browsers never send to the server, so the web app decrypts it and Knode cannot.
- Links can expire and be revoked; revoking stops new opens, not copies already made.

## Consequences

- **Permissions are per space, not per folder.** Whoever holds a space's key can read all of it;
  keys per folder are not worth the complexity. If someone must not see something, it goes in
  another space. The app says this where members are added.
- Teams need Knode Cloud sync: there is no shared iCloud space for people with different Apple
  accounts.
- Searching a team space from a remote agent needs mode B or a team machine; otherwise each member's
  agent answers only while that member's device is on.
- Person key pairs are the "key pairs later" foreseen in 0008, alongside agent keys, not instead
  of them.
- Teams starts with small teams. Companies need onboarding, support and trust that come after the
  product works for teams of two to twenty.

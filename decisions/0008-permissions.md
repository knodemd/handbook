# 0008 - Permissions per agent and per space

**Status:** accepted, 2026-10-20

## Context

Decision 0004 promises that agents get access per space, to read or to write, and the v0.2
roadmap is done when a work agent sees nothing personal. In v0.1 every connected agent can read
and write every space, and names itself: an agent's name in the history is whatever its client
says. Spaces are packages that are synced through iCloud and, later, shared through Knode Cloud,
so anything secret inside a package travels with it.

## Decision

- **Each agent has its own key.** Connecting an agent (`knode connect claude-code`, or the app)
  makes a random 256-bit key for it. The same program connected twice, for work and for personal
  projects, is two agents with two keys.
- **Keys never go into a package.** A key lives only on the machine where it was made, in the
  system's credential store: the Keychain on macOS, the Secret Service on Linux, or a file under
  `KNODE_HOME` that only its owner can read where there is no store. `knode mcp` reads it from
  there, so it is not written into the agent's own configuration either. Packages hold only the
  SHA-256 fingerprint of each key, which is safe to sync and share.
- **Grants live in the space.** Each space holds, per agent, one level: `none`, `read` or
  `write`. No grant means `none`. Grants move with the space, so a space opened on another
  machine keeps the same rules, and a key revoked on one machine is refused on all of them.
- **A space an agent may not see is invisible to it,** not empty: it is left out of every list
  and search, and naming it gives the same error as a space that does not exist.
- **Knode Service checks every call,** not just the connection: the key's fingerprint must be in
  the space and not revoked, and the agent's level must allow the call. Revoking a key or
  lowering a grant takes effect on the next call. A connection without a known key is refused.
- **Only people manage access.** Adding agents, issuing and revoking keys, changing grants,
  settings and folders, and erasing history are done from the app or the CLI. No agent can do
  them, whatever its level.
- **Who changed what is recorded by key.** An agent's changes carry the name of the agent its
  key belongs to, never a name it gives itself. Every write, and every change to agents, keys
  and grants, is a change in the space's history. Reads and refused calls go to an access log on
  the machine, without content or queries, not into the space.
- **Upgrading asks first.** A space moved to the new schema has no grants. When Knode upgrades
  from a version without keys, it offers to connect the agents already set up and to give them
  the access they had; it does not grant it silently.

The details are in the [.knode specification](https://github.com/knodemd/format/blob/main/spec/knode-0.md),
sections 16 and 17.

## Consequences

- Keys are bearer secrets, not key pairs: simple to make, store and revoke on one machine. Key
  pairs can come later, for Knode Cloud and teams, as another kind of fingerprint.
- Grants are not a sandbox. A program running as the person can read a package directly or run
  the `knode` CLI, which acts for the person. Grants keep each agent to the spaces it was given
  and make what it did traceable; keeping programs away from files is the operating system's
  job, and encryption (v0.4) protects packages at rest.
- Agents connected in v0.1 stop working until they are reconnected with a key. The upgrade offer
  keeps that to one confirmation.
- Every space carries the fingerprints of the agents that have had access to it. They reveal
  which agents were used, but nothing that lets anyone act as them.

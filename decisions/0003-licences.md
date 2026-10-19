# 0003 - Licences

**Status:** accepted, 2026-10-19

## Context

Knode wants as many people as possible to use it, change it and build on it, while nobody else can
present themselves as Knode. For a new project the real risk is being unknown, not being copied, and
what protects it from copies is the brand and being the official, maintained version, not a
restrictive licence. Fewer licences are also easier to understand.

## Decision

Two licences, nothing else.

| What | Licence |
|---|---|
| Everything in the repositories: code, docs, the `.knode` format and its libraries | [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0) |
| The brand: the names Knode and .knode, logo, app icon, icons, Knode Block and Knode Line | [Knode Brand License](https://github.com/knodemd/brand-license), all rights reserved |

- **Apache 2.0 rather than MIT:** just as free, and better for us. Every copy must keep the `NOTICE`
  ("Knode, a project by aaangelmartin") and mark what it changed; it grants patents from every
  contributor; and it says outright that it gives no rights to the name or logo.
- **Rather than AGPL or MPL:** they would force forks to publish their changes, but they put off
  companies and contributors and complicate the App Store. Visibility matters more now.
- **No CLA:** with Apache 2.0, contributions come in under the same licence (section 5), which is
  enough to ship Knode anywhere, the App Store included.

## Consequences

- Every repository has the Apache 2.0 text in `LICENSE` and a `NOTICE` crediting Knode.
- Brand files (logo, icons, fonts) carry the Knode Brand License wherever they ship.
- A fork may use, change and even sell the code, but under its own name and logo, crediting Knode.
- Supersedes the MIT licence of `web` and `.github`, and CC BY 4.0 and OFL for the brand and docs.

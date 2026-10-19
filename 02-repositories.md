# 2 - Repositories

Everything lives in the [`knodemd`](https://github.com/knodemd) organisation, and everything is public.

| Repository | What it is | Status |
|---|---|---|
| [`.github`](https://github.com/knodemd/.github) | Organisation profile and shared community files | Active |
| [`handbook`](https://github.com/knodemd/handbook) | This handbook | Active |
| [`brand`](https://github.com/knodemd/brand) | The brand kit, generated from one source file | Active |
| [`brand-license`](https://github.com/knodemd/brand-license) | The Knode Brand License and the trademark policy | Active |
| [`web`](https://github.com/knodemd/web) | knode.md: the website, with the developers, brand and licenses pages | Active |
| `knode` | The product: Rust core, Mac, iPhone and web apps | Planned, v0.1 |
| `format` | The `.knode` specification and example files | Planned, v0.1 |
| `homebrew-tap` | `brew install knodemd/tap/knode` | Planned, v0.1 |
| `cloud` | Knode Cloud: sync, accounts, sharing, teams | Planned, v0.4 |

New repositories are added here when they're created, never before.

## Naming

- Lowercase, words joined with hyphens (kebab-case): `brand`, `knode-cli`.
- Short and literal: name the thing, not the idea. No `knode-` prefix unless the name would be ambiguous.
- The display name, titles and descriptions use normal capitalisation ("Knode", "Brand kit").

## Every repository has

- A `README.md` that says what it is, how to run it and how to test it.
- A `LICENSE` (Apache 2.0) and a `NOTICE` crediting Knode, from the first commit (see [decision 0003](decisions/0003-licences.md)).
- Issues enabled, with the shared labels (see [How we work](03-how-we-work.md)).
- Community files inherited from `.github` (contributing, code of conduct, security, templates).

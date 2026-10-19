# 2 · Repositories

Everything lives in the [`knodemd`](https://github.com/knodemd) organisation, and everything is public.

| Repository | What it is | Status |
|---|---|---|
| [`.github`](https://github.com/knodemd/.github) | Organisation profile and shared community files | Active |
| [`handbook`](https://github.com/knodemd/handbook) | This handbook | Active |
| [`brand`](https://github.com/knodemd/brand) | The brand kit, generated from one source file | Active |
| `knode` | The app for macOS and iOS | Planned |
| `format` | The `.knode` file format specification and test files | Planned |

New repositories are added here when they're created, never before.

## Naming

- Lowercase, words joined with hyphens (kebab-case): `brand`, `knode-cli`.
- Short and literal: name the thing, not the idea. No `knode-` prefix unless the name would be ambiguous.
- The display name, titles and descriptions use normal capitalisation ("Knode", "Brand kit").

## Every repository has

- A `README.md` that says what it is, how to run it and how to test it.
- A licence file from the first commit: Apache 2.0 for code, CC BY 4.0 for docs (see [decision 0003](decisions/0003-licences.md)).
- Issues enabled, with the shared labels (see [How we work](03-how-we-work.md)).
- Community files inherited from `.github` (contributing, code of conduct, security, templates).

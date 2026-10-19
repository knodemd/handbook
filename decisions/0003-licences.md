# 0003 - Licences

**Status:** accepted, 2026-10-19

## Context

Code had been published under MIT by default, without a decision. Knode will take outside
contributions and may be built on by companies, so the licence should cover patents as well as
copyright, and say plainly under what terms contributions come in.

## Decision

| What | Licence |
|---|---|
| Code: apps, CLI, SDKs, websites, generators | [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0) |
| Docs: this handbook, the community files, the `.knode` specification | [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) |
| Brand kit files: logo files, icons, graphic elements, templates, guidelines | CC BY 4.0, with the name and logo under the [trademark policy](https://github.com/knodemd/brand/blob/main/TRADEMARK.md) |
| Typefaces: Knode Block and Knode Line | [SIL Open Font License 1.1](https://openfontlicense.org) |

- **Apache 2.0 rather than MIT:** it is just as permissive, and adds an explicit patent grant from
  every contributor, a patent-retaliation clause, and a clear rule that contributions are licensed
  under the same terms (section 5). It is the licence of Swift and of most large open-source projects.
- **The brand stays open, the name stays ours:** anyone may use the files; only the official project
  may present itself as Knode. The same split as Rust and Mozilla.

## Consequences

- Code repositories carry the Apache 2.0 text in `LICENSE` and a `NOTICE` file; docs repositories
  carry CC BY 4.0.
- Contributions follow the licence of the repository they go to (see `CONTRIBUTING.md`).
- Supersedes the MIT licence in `knodemd/web` and `knodemd/.github`.

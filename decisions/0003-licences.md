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
| The brand: name, logo, app icon, Knode Block and Knode Line, icons, graphic elements, templates, guidelines | [Knode Brand Licence](https://github.com/knodemd/brand/blob/main/BRAND-LICENSE.md): all rights reserved, with the permissions it lists |

- **Apache 2.0 rather than MIT:** it is just as permissive, and adds an explicit patent grant from
  every contributor, a patent-retaliation clause, and a clear rule that contributions are licensed
  under the same terms (section 5). It is the licence of Swift and of most large open-source projects.
- **Open code, reserved brand:** the code and the docs are for everyone; the brand is not. The kit
  stays public so anyone can read it, use the logo to talk about Knode and contribute to Knode,
  but using the brand or the typefaces anywhere else needs written permission. Forks keep the
  code and bring their own name, logo and typefaces.

## Consequences

- Code repositories carry the Apache 2.0 text in `LICENSE` and a `NOTICE` file; docs repositories
  carry CC BY 4.0. Wherever brand files ship with code (fonts, icons), the `NOTICE` says they
  follow the Knode Brand Licence.
- Contributions follow the licence of the repository they go to (see `CONTRIBUTING.md`).
- Supersedes MIT in `knodemd/web` and `knodemd/.github`, and CC BY 4.0 and OFL for the brand.

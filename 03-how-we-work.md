# 3 - How we work

## Issues first

**Nothing is built without an issue.** The issue explains the problem and has a definition of
done as checkboxes. Ideas found while working become new issues, not comments that scroll away.

Every issue has a **type**, a **size**, an **assignee** once someone takes it, and the **milestone** of its phase.

| Labels | Meaning |
|---|---|
| `type: feature`, `type: bug`, `type: task`, `type: docs`, `type: design` | What kind of work it is |
| `size: S`, `size: M`, `size: L` | Under a day, a few days, a week or more |
| `status: in-progress`, `status: needs-review`, `status: blocked` | Where it is now |
| `good first issue`, `help wanted` | Good entry points for contributors |

Keep the issue current: mark it in progress and comment the branch name when you start, tick
the boxes as they're met, and comment on blockers or changes of plan.

## Branches

`<type>/<short-description>`, from the default branch: `feat/import-markdown`, `fix/empty-map-crash`,
`docs/format-spec`. Nobody pushes to `main`; everything arrives through a pull request.

## Commits

[Conventional Commits](https://www.conventionalcommits.org), in English and the imperative:

```
feat(import): parse Claude exports into nodes
fix(format): keep node order when saving
docs: explain the .knode package layout
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `build`, `ci`, `chore`, `perf`, `revert`.
Small, logical commits; one change per commit.

## Pull requests

- Say **why**, list **what changed**, and include a **test plan** (the template does this).
- Link the issue: `Closes #n` when it finishes it, `Part of #n` when it doesn't.
- Tests, lint and build pass before review.
- A maintainer reviews and squash-merges. Nobody merges their own pull request without review once the project has more than one maintainer.

## Releases

[Semantic versioning](https://semver.org). Every release has notes written for people, not a list of commits.
User-facing changes are recorded in the changelog as they land. How a release is cut, signed and
published is in [Releasing](07-releasing.md).

## Decisions

Important decisions are written down in [`decisions/`](decisions/), one file each: the context,
the decision, and what it means. Numbered, never deleted; a later decision can supersede an earlier one.

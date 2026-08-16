# Contributing

This guide applies to every repository in the Podratic organization. A repository with its own `CONTRIBUTING.md`
overrides it.

It covers how we track work, how to make a change, and how to write a commit and a pull request. Everyone who takes
part follows the [code of conduct](https://github.com/podratics/.github/blob/master/CODE_OF_CONDUCT.md).

## Track the work

Every change starts as a GitHub Issue in the repository it affects. Open the issue before you start, so other people
can see the work and comment on it.

Give each issue a priority label:

- `P0`: blocks a release.
- `P1`: high priority.
- `P2`: medium priority.

Add the domain labels that fit, for example `security`, `billing`, `a11y`, or `ci-cd`. Each repository defines its own
set, so use the labels that repository already has.

## Work in a git worktree

Every contribution runs in its own git worktree. A worktree is a separate folder on its own branch that shares the
repository's git history. Several contributions can then run at once without colliding in the main checkout.

Create one with `pgwt`, the worktree CLI that Podratic machine setup installs:

```bash
pgwt new <branch>
```

`pgwt new` creates the branch from the latest default branch, copies your local gitignored `.env*` files into the new
worktree, and runs the repository's `worktree:setup` script if it defines one. The result is a sibling folder named
`<repo>-<branch>`.

The base is `origin/master`. Every Podratic repository uses `master` as its default branch.

Without `pgwt`, the plain git equivalent is:

```bash
git worktree add -b <branch> ../<repo>-<branch> origin/master
```

Make your change in the new folder. Run that repository's typecheck, format, and lint from its root before you open a
pull request.

## Name the branch

Branch names are kebab-case. They must not contain a `/` character.

```text
feat-export-retro-board
fix-session-timeout
docs-describe-local-setup
```

The build turns the branch name into a semver prerelease identifier. A `/` makes that identifier invalid, and the
build fails.

## Write the commit message

Commit messages follow [Conventional Commits](https://www.conventionalcommits.org/). A commitlint hook checks every
message and rejects one that does not match.

```text
<type>(<optional scope>): <subject>
```

Use one of these types:

`build`, `chore`, `ci`, `docs`, `feat`, `fix`, `perf`, `refactor`, `revert`, `style`, `test`

The hook enforces two more rules:

- The header is at most 100 characters.
- The subject is lowercase.

Examples:

```text
feat(auth): add password reset flow
fix: stop the export button firing twice
docs(readme): describe the local database setup
```

A `pre-commit` hook formats and spell-checks your staged files. It does not run the type checker, the linter, or the
tests, so run those yourself before you push.

## Open the pull request

Push the branch and open a pull request against the repository's default branch.

- Fill in the repository's pull request template.
- Link the issue that tracks the work.
- Say what changed and how you tested it. Keep it short.

Every status check must pass before a pull request can merge.

We squash-merge, so the pull request title becomes the commit message on the default branch. Write the title in
Conventional Commits form and keep it under 80 characters.

## Clean up

Remove the worktree once your pull request merges:

```bash
pgwt rm <branch> --force-branch
```

Run it from the main checkout. A squash-merge leaves the branch unmerged as far as git can tell, so `--force-branch`
deletes it. Add `--force` if the worktree holds changed or untracked files that you can discard.

## Report a security problem

Do not open an issue or a pull request for a security vulnerability. Follow the
[security policy](https://github.com/podratics/.github/blob/master/SECURITY.md) instead.

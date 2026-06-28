# `setup` composite action

Shared CI setup for Podratic repositories: install Bun (version resolved from `package.json`),
authenticate Bun with the npm registry for a scope, and run `bun install`. Defined once here so every
repo's workflow uses the same setup instead of vendoring its own copy.

## Usage

Run `actions/checkout` first, then reference this action by its pinned version:

```yaml
- name: Checkout
  uses: actions/checkout@v6

- name: Setup
  uses: podratics/.github/actions/setup@v1
  with:
    registry-url: ${{ vars.NPM_REGISTRY || 'pkg.lab.samhuk.com' }}
    registry-token: ${{ secrets.LAB_NPM_REGISTRY_TOKEN }}
    scope: podratic
```

The `registry-token` is passed in by the caller (a secret) and is never stored in the action.

## Notes

- It appends an `[install.scopes]` block to `bunfig.toml` to authenticate in CI. This works whether the
  consuming repo commits or gitignores `bunfig.toml`. The action never commits the change, so a repo
  with a tracked `bunfig.toml` should exclude it from any "lockfile/working tree clean" check
  (`git status --porcelain -- ':!bunfig.toml'`).
- Pin to a released tag (e.g. `@v1`) rather than a moving branch.

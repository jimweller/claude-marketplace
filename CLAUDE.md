# jimweller Marketplace

Indexes plugins that live in separate GitHub repositories. This file documents
how a change in a plugin repository actually reaches an installed session.

## Deploy and release are separate acts

Deploy tags the plugin repository. Release points this marketplace's
`source.ref` at that tag. Until release happens, a deploy is invisible to
every installer; `marketplace.json` still points at the old ref.

Adapted from MCG's Azure DevOps team-marketplace convention (Confluence:
"Team Plugin Marketplace", AIE space). The ADO specifics there (`dev.azure.com`
URLs, SSH `insteadOf` rewrites) do not apply here since every plugin repo is
on GitHub, but the deploy/release split, tag-per-version, and version
resolution order carry over unchanged.

### Deploy

In the plugin's own repository:

```bash
# bump "version" in .claude-plugin/plugin.json first
git commit -am "chore(release): bump version to 0.1.1"
git tag 0.1.1
git push origin main --tags
```

The tag is the bare version string, no `v` prefix, matching the plugin's
`plugin.json` exactly. Nothing reaches an installer yet.

### Release

In this repository, `.claude-plugin/marketplace.json`:

```diff
       "source": "url",
-      "url": "https://github.com/jimweller/clanker-code-review-plugin.git"
+      "url": "https://github.com/jimweller/clanker-code-review-plugin.git",
+      "ref": "0.1.1"
```

Commit and push here, then confirm from any session:

```bash
claude plugin marketplace update jimweller
claude plugin update clanker-code-review@jimweller
```

Release is complete when `claude plugin update` reports the new version. A
version that already matches is reported as already up to date, not an
error.

### Testing before release

Load a tag without touching `marketplace.json`:

```bash
git checkout 0.1.1
claude --plugin-dir .
```

Session only, same flag used for integration tests.

## Version resolution

Claude Code takes the first of these that is set:

1. `version` in the plugin's own `.claude-plugin/plugin.json`.
2. `version` in this marketplace's entry for that plugin (entries here omit
   it; the plugin's own manifest is the single source of truth).
3. The git commit SHA of the plugin's source.

An update is skipped once the resolved version matches what the installer
already has. `ref` decides which commit is fetched, so it decides which
`plugin.json` gets read in the first place. A `url` source with no `ref` at
all, the state every entry here was in before this convention, always
fetches the default branch tip: every push to `main` is a live, unversioned,
unannounced release.

## Per-plugin scope

`clanker-chat`, `clanker-prose`, `session`, and `clanker-code-review` are
vendored as git submodules under `submodules/` in the dotfiles superproject.
Deploy and release for these both happen from a dotfiles working session:
tag the submodule, edit this file, bump the submodule pointer in the
superproject, push all three.

`claude-mem`, `superpowers`, and `lsp-enforcement-kit` are forks of
third-party repositories and are not vendored here. Deploy for these happens
in whatever local checkout of the fork is current; this file only records
the released `ref`. `claude-mem` additionally has its own version-bump
workflow (see the `claude-mem:version-bump` skill) that already handles its
own tagging; coordinate with it rather than tagging independently here.

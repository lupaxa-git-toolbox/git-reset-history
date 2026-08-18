<p align="center">
    <a href="https://github.com/lupaxa-git-toolbox">
        <img src="https://raw.githubusercontent.com/the-lupaxa-project/brand-assets/master/logos/organisations/git-toolbox/readme-logo.png" alt="Organisation Logo" />
    </a>
</p>

<h1 align="center">git-reset-history</h1>

Rewrite a Git repository down to a single initial commit — with dry-run, backups, and controlled tag handling.

## What it does

`git-reset-history` replaces the primary branch history with one fresh commit that keeps the current tree. By default it then force-pushes the rewritten branch, deletes tags (local and remote), and runs an aggressive `git gc`.

Use it when you want a clean slate without changing working-tree contents — for example after scaffolding, before a first public release, or when history is no longer useful.

## Quick start

```bash
./src/git-reset-history --summary          # plan only — no changes
./src/git-reset-history -n                 # dry-run (commands simulated)
./src/git-reset-history -B backup-before   # real run with a local backup branch
./src/git-reset-history -y                 # non-interactive (CI / scripts)
```

**This is destructive.** Prefer `--summary` or `-n` first. Force-push is part of the default path unless you pass `--local-only`.

## Default behaviour

With no flags, the script will:

1. Detect the primary branch (`<remote>/HEAD`, else the current branch)
2. Replace that branch with a single initial commit (message: `The initial commit`)
3. Force-push to `origin/<branch>`
4. Delete **all** tags locally and on the remote (after rewrite/push succeeds)
5. Run `git gc --aggressive --prune=all`

## Common options

| Flag                       | Purpose                                                      |
| :------------------------- | :----------------------------------------------------------- |
| `-m, --message TEXT`       | Message for the new initial commit                           |
| `-V, --version`            | Print version and exit                                       |
| `-b, --branch NAME`        | Target branch (default: auto-detected primary)               |
| `-r, --remote NAME`        | Remote to operate on (default: `origin`)                     |
| `-n, --dry-run`            | Simulate commands without changing anything                  |
| `-S, --summary`            | Print a full action plan and exit                            |
| `-y, --yes`                | Skip the interactive `RESET` confirmation                    |
| `-k, --keep-tags`          | Do not delete tags                                           |
| `-T, --protect-tag TAG`    | Keep a specific tag (repeatable)                             |
| `-K, --keep-branch`        | Leave the original branch; write history to `<branch>-reset` |
| `-B, --backup-branch NAME` | Create a backup branch from the original primary             |
| `-U, --force-backup`       | Recreate the backup branch if it already exists              |
| `-P, --push-backup`        | Also push the backup branch to the remote                    |
| `-L, --local-only`         | Do not touch the remote at all                               |
| `-D, --allow-dirty`        | Allow a dirty working tree                                   |
| `-F, --no-fetch`           | Skip `git fetch` before rewriting                            |
| `-G, --no-gc`              | Skip aggressive GC at the end                                |

```bash
./src/git-reset-history --help
```

## Examples

Preview what a reset would do:

```bash
./src/git-reset-history --summary
```

Rewrite locally only, keep tags, and take a backup:

```bash
./src/git-reset-history \
  --local-only \
  --keep-tags \
  --backup-branch pre-reset \
  --message "Initial public commit"
```

Reset `main`, protect release tags, and push a remote backup:

```bash
./src/git-reset-history \
  --branch main \
  --protect-tag v1.0.0 \
  --backup-branch main-pre-reset \
  --push-backup \
  --yes
```

## Safety notes

- `--dry-run` prints the action plan and simulates commands without asking for `RESET`.
- Real runs require typing `RESET` unless you pass `-y` / `--yes`.
- Tags are deleted only after the rewrite (and force-push, unless `--local-only`) succeeds.
- Dirty trees are rejected unless `--allow-dirty` is set.
- `--summary` never runs Git commands; `--dry-run` prints them without executing.
- Collaborators (and any forks) will need to re-clone or hard-reset after a force-push.

<a href="https://github.com/the-lupaxa-project">
    <img src="https://raw.githubusercontent.com/the-lupaxa-project/brand-assets/master/logos/components/footer-for-child-orgs.svg" alt="The Lupaxa Project Footer" width="100%" />
</a>

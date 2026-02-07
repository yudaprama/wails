# Sync yudaprama/v3-alpha with origin/v3-alpha

This document describes the process for synchronizing the `yudaprama/v3-alpha` branch with the upstream `origin/v3-alpha` branch.

## Overview

- **Upstream**: `origin` (https://github.com/wailsapp/wails)
- **Fork**: `yudaprama` (https://github.com/yudaprama/wails.git)
- **Branch**: `v3-alpha`

## Check Status

To check the current status of both branches:

```bash
# Fetch both remotes
git fetch origin
git fetch yudaprama

# Check commits ahead/behind
git log --oneline origin/v3-alpha ^yudaprama/v3-alpha | wc -l
git log --oneline yudaprama/v3-alpha ^origin/v3-alpha | wc -l

# View detailed differences
git log --oneline origin/v3-alpha ^yudaprama/v3-alpha
git log --oneline yudaprama/v3-alpha ^origin/v3-alpha

# Visual graph
git log --oneline --all --graph --decorate origin/v3-alpha yudaprama/v3-alpha -20
```

## Sync Process

### Step 1: Fetch Latest Changes

```bash
git fetch origin
git fetch yudaprama
```

### Step 2: Checkout the Branch

```bash
git checkout v3-alpha
```

### Step 3: Rebase with Origin

This will apply your custom commits on top of the latest origin commits:

```bash
git rebase origin/v3-alpha
```

**Note**: If there are conflicts, resolve them and continue:

```bash
# After resolving conflicts
git add <resolved-files>
git rebase --continue
```

### Step 4: Push to Fork

Use `--force-with-lease` for safety:

```bash
git push yudaprama v3-alpha --force-with-lease
```

### Step 5: Sync Tags (Optional)

If you want to keep tags in sync with origin:

```bash
# Find the latest tag on origin
git tag --merged origin/v3-alpha | grep "v3.0.0-alpha" | sort -V | tail -1

# Push the latest tag to your fork
git push yudaprama v3.0.0-alpha.68
```

## Verify Sync

After syncing, verify the status:

```bash
# Check if origin is behind (should be 0)
git log --oneline origin/v3-alpha ^yudaprama/v3-alpha | wc -l

# Check your custom commits (should be your commits only)
git log --oneline yudaprama/v3-alpha ^origin/v3-alpha

# Visual confirmation
git log --oneline --all --graph --decorate origin/v3-alpha yudaprama/v3-alpha -10
```

## Example Output

### Before Sync

```
origin/v3-alpha: 28 commits ahead
yudaprama/v3-alpha: 3 commits ahead
```

### After Sync

```
origin/v3-alpha: 0 commits behind (all synced)
yudaprama/v3-alpha: 1 commit ahead (your custom commit)
```

## Important Notes

1. **Rebase vs Merge**: We use rebase to maintain a clean linear history. This rewrites commit hashes, so always use `--force-with-lease` when pushing.

2. **Merge Commits**: During rebase, merge commits are automatically removed as they're no longer needed.

3. **Conflict Resolution**: If conflicts occur during rebase, resolve them carefully as they affect your custom commits.

4. **Backup**: Before rebasing, consider creating a backup branch:
   ```bash
   git branch v3-alpha-backup
   ```

5. **Frequency**: Sync regularly to avoid large divergences that make conflict resolution difficult.

## Troubleshooting

### Rebase Fails

If rebase fails, you can abort and try again:

```bash
git rebase --abort
# Fix any issues
git rebase origin/v3-alpha
```

### Push Rejected

If push is rejected, fetch and rebase again:

```bash
git fetch yudaprama
git rebase origin/v3-alpha
git push yudaprama v3-alpha --force-with-lease
```

### Lost Commits

If you accidentally lose commits, check the reflog:

```bash
git reflog
# Find the commit hash and reset
git reset --hard <commit-hash>
```

## Quick Reference

```bash
# Full sync process
git fetch origin && git fetch yudaprama && \
git checkout v3-alpha && \
git rebase origin/v3-alpha && \
git push yudaprama v3-alpha --force-with-lease

# Sync latest tag
LATEST_TAG=$(git tag --merged origin/v3-alpha | grep "v3.0.0-alpha" | sort -V | tail -1)
git push yudaprama $LATEST_TAG
```

## History

- **2026-02-07**: Initial sync from origin/v3-alpha (28 commits) + 1 custom commit (TypeScript migration)
- **Latest tag synced**: v3.0.0-alpha.68

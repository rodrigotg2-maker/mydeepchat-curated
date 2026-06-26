---
name: using-git-worktrees
description: Use when starting feature work that needs isolation from current workspace or before executing implementation plans — ensures an isolated workspace exists via native tools or git worktree fallback. PowerShell/Windows compatible.
---
<!-- FORK ADAPTADO: Convertir bash conditionals (Steps 1b y 2) a PowerShell; adaptar setup de proyecto al stack de Rodrigo (pyproject.toml/uv, npm, Docker); generalizar rutas a Windows · origen: obra-superpowers-skills-using-git-worktrees-skill-md · 2026-06-26 -->

# Using Git Worktrees

## Overview

Ensure work happens in an isolated workspace. Prefer your platform's native worktree tools. Fall back to manual git worktrees only when no native tool is available.

**Core principle:** Detect existing isolation first. Then use native tools. Then fall back to git. Never fight the harness.

**Announce at start:** "I'm using the using-git-worktrees skill to set up an isolated workspace."

## Step 0: Detect Existing Isolation

**Before creating anything, check if you are already in an isolated workspace.**

```powershell
$GIT_DIR = (git rev-parse --git-dir 2>$null) | ForEach-Object { (Resolve-Path $_).Path }
$GIT_COMMON = (git rev-parse --git-common-dir 2>$null) | ForEach-Object { (Resolve-Path $_).Path }
$BRANCH = git branch --show-current
```

**Submodule guard:** `GIT_DIR != GIT_COMMON` is also true inside git submodules. Verify you are not in a submodule:

```powershell
git rev-parse --show-superproject-working-tree 2>$null
```

**If `GIT_DIR != GIT_COMMON` (and not a submodule):** You are already in a linked worktree. Skip to Step 2 (Project Setup).

Report with branch state:

- On a branch: "Already in isolated workspace at `<path>` on branch `<name>`."
- Detached HEAD: "Already in isolated workspace at `<path>` (detached HEAD). Branch creation needed at finish time."

**If `GIT_DIR == GIT_COMMON` (or in a submodule):** You are in a normal repo checkout.

Has the user already indicated their worktree preference? If not, ask for consent before creating a worktree:

> "Would you like me to set up an isolated worktree? It protects your current branch from changes."

Honor any existing declared preference without asking. If the user declines consent, work in place and skip to Step 2.

## Step 1: Create Isolated Workspace

**Try in this order:**

### 1a. Native Worktree Tools (preferred)

If you have a native tool like `EnterWorktree`, a `/worktree` command, or a `--worktree` flag — use it and skip to Step 2.

Only proceed to Step 1b if you have NO native worktree tool available.

### 1b. Git Worktree Fallback (PowerShell)

**Only use this if Step 1a does not apply.**

#### Directory Selection

Follow this priority order:

1. **Check for a declared worktree directory preference** in your instructions. If specified, use it.

2. **Check for an existing project-local worktree directory:**

   ```powershell
   $hasHidden = Test-Path ".worktrees" -PathType Container
   $hasPlain  = Test-Path "worktrees" -PathType Container
   # Prefer .worktrees if both exist
   ```

3. **Default to `.worktrees/` at the project root** if no other guidance.

#### Safety Verification

**MUST verify directory is ignored before creating worktree:**

```powershell
git check-ignore -q ".worktrees" 2>$null
if ($LASTEXITCODE -ne 0) {
    Add-Content -Path ".gitignore" -Value "`n.worktrees/"
    git add .gitignore
    git commit -m "chore: add .worktrees to .gitignore"
}
```

#### Create the Worktree

```powershell
$BRANCH_NAME = "feature/my-feature"
$WORKTREE_PATH = ".\.worktrees\$BRANCH_NAME"

# From a new branch
git worktree add -b $BRANCH_NAME $WORKTREE_PATH

# Or from an existing branch
git worktree add $WORKTREE_PATH $BRANCH_NAME

Set-Location $WORKTREE_PATH
```

**Permission error fallback:** If `git worktree add` fails (sandbox denial), tell the user and work in the current directory instead.

## Step 2: Project Setup

Auto-detect and run appropriate setup:

```powershell
# Node.js / Next.js
if (Test-Path "package.json") { npm install }

# Python — uv (preferred)
if (Test-Path "pyproject.toml") {
    if (Get-Command uv -ErrorAction SilentlyContinue) {
        uv sync
    } else {
        pip install -e ".[dev]"
    }
}

# Python — requirements.txt fallback
if (Test-Path "requirements.txt") { pip install -r requirements.txt }

# Rust
if (Test-Path "Cargo.toml") { cargo build }

# Go
if (Test-Path "go.mod") { go mod download }

# Docker Compose (don't start, just verify)
if (Test-Path "docker-compose.yml") {
    Write-Host "docker-compose.yml found. Run 'docker compose up -d' when ready."
}
```

### Environment Files

```powershell
# Copy .env from main worktree if it exists
$sourceEnv = Join-Path (git rev-parse --show-toplevel) ".env"
if (Test-Path $sourceEnv) {
    Copy-Item $sourceEnv ".env"
    Write-Host "Copied .env from main worktree"
} elseif (Test-Path ".env.example") {
    Copy-Item ".env.example" ".env"
    Write-Host "Copied .env.example → .env (review before use)"
}
```

## Step 3: Verify Clean Baseline

Run tests to ensure workspace starts clean:

```powershell
# Python
pytest

# Node.js
npm test

# Cargo
cargo test

# Go
go test ./...
```

**If tests fail:** Report failures, ask whether to proceed or investigate.

**If tests pass:** Report ready.

### Report

```
Worktree ready at <full-path>
Branch: <branch-name>
Tests passing (<N> tests, 0 failures)
Ready to implement <feature-name>
```

## Quick Reference

| Situation | Action |
|-----------|--------|
| Already in linked worktree | Skip creation (Step 0) |
| In a submodule | Treat as normal repo (Step 0 guard) |
| Native worktree tool available | Use it (Step 1a) |
| No native tool | Git worktree fallback (Step 1b) |
| `.worktrees/` exists | Use it (verify ignored) |
| `worktrees/` exists | Use it (verify ignored) |
| Both exist | Use `.worktrees/` |
| Neither exists | Default `.worktrees/` |
| Directory not ignored | Add to .gitignore + commit |
| Permission error on create | Work in place |
| Tests fail during baseline | Report failures + ask |

## Cleanup

```powershell
# Remove a worktree when done
git worktree remove ".\.worktrees\feature\my-feature"

# List all worktrees
git worktree list
```

## Common Mistakes

- **Fighting the harness**: Use `EnterWorktree` if available (Step 1a), not manual git
- **Skipping detection**: Always run Step 0 before creating anything
- **Skipping ignore verification**: Always check `.gitignore` before creating project-local worktree
- **Proceeding with failing tests**: Report failures, get explicit permission to proceed

# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

gh-archive is a set of bash scripts for creating offline, local backups of GitHub repositories. It backs up your personal repos AND all organizations you belong to, with full history, all branches, and all tags.

## Requirements

- Bash 5.0+
- Git
- GitHub CLI (`gh`) - must be logged in with your account(s)
- SSH key added to GitHub (repos are cloned via SSH)

## Commands

There are 5 executable bash scripts:

- `./sync` - **Main script**: Automatically archives your personal repos AND all organizations you belong to
- `./check [username/org]` - Syncs all repos for a specific user/org (clones missing, updates existing)
- `./clone [username] [repo]` - Clones a single repo with all branches/tags
- `./pull [username]` - Updates all local repos for a user; offers to move unavailable repos to `Outdated/`
- `./count [username] (-c)` - Counts locally saved repos for a user; `-c` flag outputs just the number

## Architecture

The scripts organize repos into directories named after their GitHub owner/organization:
```
.
├── username1/
│   ├── repo1/
│   └── repo2/
├── organization1/
│   └── repo3/
└── Outdated/       # repos that became unavailable
```

**Workflow:**
1. `sync` gets the logged-in user and all their orgs via `gh api`
2. For each account, `sync` calls `check`
3. `check` uses `gh repo list` to get all repos (up to 999)
4. For missing repos, `check` calls `clone`
5. `clone` performs SSH clone, fetches ALL branches with `git fetch origin +refs/heads/*:refs/heads/*`, fetches all tags, and PR refs
6. After cloning, `check` calls `pull` to update all existing repos

**What gets archived:**
- Full git history (not shallow)
- All branches (not just main/master)
- All tags
- Pull request refs

**Key implementation details:**
- All scripts use `SCRIPT_DIR` to work correctly from any directory
- Subshells are used in `pull` to avoid `cd` pollution
- JSON parsing uses `jq` if available, falls back to grep
- Scripts create directories automatically (no manual setup needed)

# gh-archive

Offline backup of your entire GitHub presence - all your repos, all your organizations, all branches, full history.

## Quick Start

```bash
# 1. Make sure you're logged into GitHub CLI
gh auth login

# 2. Run the backup
./sync
```

That's it. The `sync` command will:
- Find your GitHub username automatically
- Find all organizations you belong to
- Clone every repository you have access to
- Fetch ALL branches, ALL tags, and full git history
- Update existing repos with any new changes

## Requirements

- **Bash 5.0+**
- **Git**
- **[GitHub CLI](https://cli.github.com/)** (`gh`) - must be authenticated
- **SSH key** added to your GitHub account (repos are cloned via SSH)

### Setup

```bash
# Install GitHub CLI (macOS)
brew install gh

# Install GitHub CLI (Ubuntu/Debian)
sudo apt install gh

# Login to GitHub
gh auth login

# Make sure your SSH key is set up
ssh -T git@github.com
```

## Commands

### `./sync` - Backup Everything (Recommended)

The main command. Archives your personal repos and ALL organizations you're a member of.

```bash
./sync
```

Output:
```
========================================
  gh-archive - Full GitHub Backup
========================================

Logged in as: yourname

Finding organizations...
Found 3 organizations

Will archive repositories for:
  - yourname
  - org1
  - org2
  - org3

[1/4] Syncing: yourname
...
```

### `./check [username/org]` - Sync a Single Account

Clone missing repos and update existing ones for a specific user or organization.

```bash
./check yourname      # Sync your personal repos
./check some-org      # Sync an organization's repos
```

### `./clone [username/org] [repo]` - Clone a Single Repo

Clone a specific repository with all branches and tags.

```bash
./clone yourname my-project
```

### `./pull [username/org]` - Update All Repos

Update all locally archived repos for a user/org. Offers to move unavailable repos to `Outdated/`.

```bash
./pull yourname
```

### `./count [username/org]` - Count Archived Repos

```bash
./count yourname       # "There are 42 repositories archived for yourname."
./count yourname -c    # "42" (just the number, for scripting)
```

## What Gets Archived

For each repository:

- **Full git history** - every commit, not shallow
- **All branches** - not just main/master
- **All tags** - releases, versions, etc.
- **Pull request refs** - archived for reference

## Directory Structure

```
gh-archive/
├── sync              # Main backup script
├── check             # Sync single account
├── clone             # Clone single repo
├── pull              # Update repos
├── count             # Count repos
├── yourname/         # Your personal repos
│   ├── repo1/
│   ├── repo2/
│   └── repo3/
├── org-name/         # Organization repos
│   ├── project-a/
│   └── project-b/
└── Outdated/         # Repos that became unavailable
    └── old-repo/
```

## Automation (Cron)

Run weekly backups automatically:

```bash
# Edit crontab
crontab -e

# Add this line (runs every Sunday at 2am)
0 2 * * 0 cd /path/to/gh-archive && ./sync >> backup.log 2>&1
```

## Troubleshooting

### "ERROR: Not logged into GitHub CLI"

```bash
gh auth login
```

### "ERROR: Failed to clone" or SSH errors

Make sure your SSH key is set up:
```bash
ssh -T git@github.com
# Should say: "Hi username! You've successfully authenticated"
```

If not, [add your SSH key to GitHub](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account).

### "No repositories found"

- Check that `gh auth status` shows the correct account
- Verify you have access to the repos with `gh repo list username`

### Repo moved to Outdated/ but it still exists

The repo may have:
- Been renamed
- Changed visibility (public → private)
- Had your access revoked

Check manually and re-clone if needed.

## Why Not `--mirror`?

This tool creates normal working clones (not bare/mirror repos) so you can:
- Browse the code normally
- Check out any branch
- Run the project locally
- Push to a new remote if needed

## License

MIT

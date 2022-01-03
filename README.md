# gh-archive
Set of bash scripts for creating offline, local backups of your Github account.

## Requirements
Bash 5.0+ or more has been shown to work (earlier may work), git, and [Github's CLI tool](https://github.com/cli/cli) is needed. `gh` should be logged in with your account(s) when using it.

## Result
These scripts was specifically made in the worse-case scenario of Github permanently shutting down with little to no notice. When fully synced, expect output like this:
```
$ tree -L 2
  ├── check
  ├── clone
  ├── count
  ├── Desperationis
  │   ├── 2DStarve
  │   ├── psscraper
  │   ├── PythonFileLibrary
  │   ├── Snake
  │   └── VM-The-Art-of-Exploitation-2nd-Edition
  ├── jghsrobotics
  │   ├── BatteryTester
  │   ├── gst-oculus-fpv
  │   ├── gst-raspberry
  │   ├── VEXR-D
  │   └── VEXSerialRemote
  ├── Outdated
  │   ├── 2D-Shadows
  │   ├── CS202
  │   ├── EnergyProposal
  │   ├── Folder-Softwares
  │   ├── Quadtree
  │   └── TOCYEN
  └── pull
```

Each folder you see is a fully-cloned github repository with every branch and tag cloned (recursive branches haven't been tested). Each repo is contained inside a folder labeled on their owner or organization, i.e. "Desperationis" and "jghsrobotics" in this case. It **does not** do a `git clone --mirror`, as repos are simply cloned normally with their branches loaded in using `git checkout`. 

## Usage
There are 4 executable bash scripts you can use:

* `./clone [github username] [repo]` - Clones a remote repo from a specific user / organization via `git@github.com` into `[github username]` if it does not exist.
* `./count [username] (-c optional)` - Counts the number of repositories saved locally from a specific user.
* `./pull [username]` - Does a `git pull --all` on every single locally saved repository from a specific user. If the repo is taken down / unavailable the repo is moved to the root directory in `Outdated`. 
* `./check [github username]` - The most useful script here; First uses `./clone` and `gh` to clone every single repository that hasn't been saved locally then calls `./pull` to update any existing repos. This should be used almost immediately as `./clone` can only clone repos one at a time. 

Due to how it works, you **must** call `clone` first on any single project you have then you are free to use anything else (notably `./check`). Optionally, you can simply create a directory in the root directory labeled with your username (see tree output above) then use anything you want. 

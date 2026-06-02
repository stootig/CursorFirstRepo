# Git Reference Guide

A quick reference for day-to-day Git workflows. Commands work in Git Bash, PowerShell, and most terminals.

---

## Table of Contents

1. [Core Concepts](#core-concepts)
2. [First-Time Setup](#first-time-setup)
3. [Daily Workflow](#daily-workflow)
4. [Branches](#branches)
5. [Remote (GitHub)](#remote-github)
6. [Inspecting History](#inspecting-history)
7. [Undoing Changes](#undoing-changes)
8. [Stash](#stash)
9. [Merge & Rebase](#merge--rebase)
10. [Tags](#tags)
11. [Troubleshooting](#troubleshooting)
12. [Cheat Sheet](#cheat-sheet)

---

## Core Concepts

| Term | Meaning |
|------|---------|
| **Repository (repo)** | Project folder tracked by Git, including a hidden `.git` directory |
| **Working tree** | Files you see and edit on disk |
| **Staging area (index)** | Files marked for the next commit (`git add`) |
| **Commit** | Snapshot of staged changes with a message and author |
| **Branch** | Movable pointer to a line of commits (e.g. `main`) |
| **Remote** | Copy of the repo on a server (e.g. `origin` on GitHub) |

**Three areas:**

```
Working tree  --git add-->  Staging  --git commit-->  Local repo  --git push-->  Remote
```

---

## First-Time Setup

Set your name and email (used on every commit):

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

Check settings:

```bash
git config --list
git config user.email
```

Optional: default branch name, credential helper, editor:

```bash
git config --global init.defaultBranch main
git config --global credential.helper manager   # Windows
git config --global core.editor "code --wait"     # VS Code / Cursor
```

---

## Daily Workflow

### Start from an existing remote repo

```bash
git clone https://github.com/USER/REPO.git
cd REPO
```

### Start a new project locally

```bash
mkdir my-project && cd my-project
git init
```

### See what changed

```bash
git status                    # short summary
git status -sb                # branch + short status
git diff                      # unstaged changes
git diff --staged             # staged changes (ready to commit)
git diff main..feature        # between branches
```

### Stage and commit

```bash
git add file.txt              # one file
git add .                     # all changes in current folder and below
git add -p                    # interactively choose hunks

git commit -m "Add login form"
git commit -am "Fix typo"     # stage tracked files + commit (no new files)
```

**Good commit messages:** imperative mood, ~50 char subject, optional body:

```
Add user authentication

- JWT tokens with 24h expiry
- Login route at /api/auth/login
```

### Sync with remote

```bash
git pull                      # fetch + merge (or rebase if configured)
git pull --rebase             # fetch + rebase onto remote branch
git push                      # send commits to remote
git push -u origin main       # first push: set upstream tracking
```

---

## Branches

```bash
git branch                    # list local branches
git branch -a                 # local + remote branches
git branch feature/login      # create branch (stay on current)
git switch feature/login      # switch branch (Git 2.23+)
git switch -c feature/login   # create and switch
git checkout feature/login    # older equivalent of switch

git branch -d feature/login   # delete merged branch
git branch -D feature/login   # force delete
```

Rename current branch:

```bash
git branch -m new-name
```

---

## Remote (GitHub)

```bash
git remote -v                 # list remotes and URLs
git remote add origin URL     # add remote named origin
git fetch origin              # download commits, don't merge
git fetch --all --prune       # update all remotes, remove stale refs

git push origin main          # push branch main
git push -u origin my-branch  # push and set upstream
git push --delete origin old  # delete remote branch
```

Clone with SSH (after SSH key is on GitHub):

```bash
git clone git@github.com:USER/REPO.git
```

---

## Inspecting History

```bash
git log                       # full log
git log --oneline -10         # last 10 commits, one line each
git log --graph --oneline --all
git show abc1234              # one commit with diff
git blame file.txt            # who changed each line
```

---

## Undoing Changes

| Goal | Command |
|------|---------|
| Discard edits in one file (not staged) | `git restore file.txt` |
| Unstage a file | `git restore --staged file.txt` |
| Undo last commit, keep changes staged | `git reset --soft HEAD~1` |
| Undo last commit, keep changes unstaged | `git reset HEAD~1` |
| Undo last commit, discard changes | `git reset --hard HEAD~1` |
| Revert a commit (safe on shared branches) | `git revert <commit-hash>` |

**Warning:** `--hard` permanently drops uncommitted work in affected files.

Amend the last commit (only if not pushed, or you know the impact):

```bash
git commit --amend -m "Better message"
git commit --amend --reset-author   # fix author after config change
```

---

## Stash

Temporarily shelve work without committing:

```bash
git stash
git stash push -m "WIP on login"
git stash list
git stash pop                 # apply latest and remove from stash
git stash apply               # apply but keep in stash
git stash drop                # remove latest stash
```

---

## Merge & Rebase

**Merge** — creates a merge commit when histories diverge:

```bash
git switch main
git merge feature/login
```

**Rebase** — replays your commits on top of another branch (linear history):

```bash
git switch feature/login
git rebase main
```

After rebase, you may need:

```bash
git push --force-with-lease    # only if branch was already pushed
```

**Conflict resolution:**

1. Git marks conflicted files.
2. Edit files, remove `<<<<<<<`, `=======`, `>>>>>>>` markers.
3. `git add <resolved-files>`
4. `git merge --continue` or `git rebase --continue`

Abort:

```bash
git merge --abort
git rebase --abort
```

---

## Tags

```bash
git tag v1.0.0
git tag -a v1.0.0 -m "Release 1.0"
git push origin v1.0.0
git push origin --tags
```

---

## Troubleshooting

### "Please tell me who you are"

Run the [first-time setup](#first-time-setup) `user.name` and `user.email` commands.

### Authentication failed on push

- HTTPS: sign in via browser or personal access token (PAT), not password.
- SSH: ensure key is added to GitHub and `ssh -T git@github.com` works.

### Rejected push (non-fast-forward)

Someone else pushed first. Pull, resolve conflicts, then push:

```bash
git pull --rebase
git push
```

### Detached HEAD

You checked out a commit directly. To keep work:

```bash
git switch -c my-fix-branch
```

### Large file / accidental commit

Use `.gitignore` before adding secrets or build artifacts. Never commit `.env`, keys, or passwords.

Example `.gitignore`:

```
node_modules/
dist/
.env
*.log
.DS_Store
```

---

## Cheat Sheet

| Task | Command |
|------|---------|
| Clone | `git clone <url>` |
| Status | `git status` |
| Stage all | `git add .` |
| Commit | `git commit -m "message"` |
| Push | `git push` |
| Pull | `git pull` |
| New branch | `git switch -c name` |
| Switch branch | `git switch name` |
| Log (short) | `git log --oneline -15` |
| Diff | `git diff` |
| Unstage | `git restore --staged <file>` |
| Discard file changes | `git restore <file>` |

---

## Workflow You Already Used

From this repo’s first session:

```bash
git clone https://github.com/stootig/CursorFirstRepo.git
cd CursorFirstRepo
# edit files
git status
git add .
git commit -m "Initial Commit"
git push
```

Next change:

```bash
git add GIT-GUIDE.md
git commit -m "Add Git reference guide"
git push
```

---

## Further Reading

- [Official Git documentation](https://git-scm.com/doc)
- [GitHub Docs — Git](https://docs.github.com/en/get-started/using-git)
- [Pro Git book (free)](https://git-scm.com/book/en/v2)

---

*Last updated: June 2026*

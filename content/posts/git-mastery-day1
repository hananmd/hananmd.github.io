---
title: "Git & GitHub — Day 1: From Zero to Version Control"
date: 2026-06-07
draft: false
tags: ["git", "github", "devops", "version-control", "learning"]
categories: ["Learning Progress"]
summary: "Completed the FreeCodeCamp Git & GitHub crash course today. Documenting everything I learned — commands, concepts, scenarios, and what I'm building toward."
showToc: true
---

## Why I started

Version control is non-negotiable on any DevOps or Cloud Engineering path. I've been building toward an **Azure Cloud Security** career (AZ-900 → AZ-104 → AZ-500), and Git is the bedrock skill that sits underneath CI/CD pipelines, IaC workflows, and every team project I'll ever work on.

Today I completed the **FreeCodeCamp Git & GitHub Crash Course** on YouTube and followed it up with scenario-based practice to actually apply what I learned — not just watch it. Here's the full breakdown.

---

## What is Git, actually?

Git is a **distributed version control system**. Every developer has a full copy of the repo — history and all — on their own machine. No single point of failure.

Key mental model:

```
Working Directory  →  Staging Area  →  Local Repo  →  Remote (GitHub)
   (you edit)          (git add)       (git commit)     (git push)
```

Three states a file can be in:
- **Modified** — changed but not staged
- **Staged** — marked to go into the next commit
- **Committed** — safely stored in local Git history

---

## Core commands I learned today

### Setting up

```bash
git config --global user.name "Hanan"
git config --global user.email "your@email.com"
git config --list                        # verify your config
```

### Starting a repo

```bash
git init                  # start tracking a new project folder
git clone <url>           # copy an existing repo from GitHub
```

### The daily workflow

```bash
git status                # what's changed / staged?
git add .                 # stage everything
git add <file>            # stage a specific file
git commit -m "message"   # save a snapshot
git log                   # see full commit history
git log --oneline         # compact one-line view
```

### Branching

```bash
git branch                        # list local branches
git branch -a                     # list all branches (local + remote)
git checkout -b feature/login     # create + switch to new branch
git switch -c feature/login       # modern alternative
git merge feature/login           # merge branch into current
git branch -d feature/login       # delete a merged branch
git branch -D feature/login       # force delete
```

### Remote & GitHub

```bash
git remote add origin <url>       # link local repo to GitHub
git remote -v                     # verify remotes
git push -u origin main           # first push (sets upstream)
git push                          # subsequent pushes
git pull                          # fetch + merge from remote
git fetch                         # download but don't merge yet
```

### Undoing things

```bash
git restore --staged <file>       # unstage a file
git restore <file>                # discard working dir changes
git commit --amend                # rewrite last commit
git reset --hard HEAD~1           # undo last commit + discard changes ⚠️
git stash                         # temporarily shelve changes
git stash pop                     # bring stashed changes back
```

### Advanced (intro)

```bash
git cherry-pick <hash>            # apply one specific commit
git rebase main                   # replay commits on top of main (linear history)
git tag -a v1.0.0 -m "message"   # create annotated release tag
```

---

## Scenarios I practiced

Rather than just memorising commands, I drilled **real-world situations**:

| Situation | Command(s) |
|---|---|
| Start tracking a new project | `git init` |
| Stage all files | `git add .` |
| Make first commit | `git commit -m "Initial commit"` |
| Create + switch to feature branch | `git checkout -b feature/x` |
| Connect repo to GitHub | `git remote add origin <url>` |
| Push for the first time | `git push -u origin main` |
| Teammate pushed changes, sync up | `git pull` |
| Accidentally staged wrong file | `git restore --staged <file>` |
| Need to fix last commit message | `git commit --amend` |
| Switching branches with uncommitted work | `git stash` → switch → `git stash pop` |
| Apply one commit from another branch | `git cherry-pick <hash>` |
| Want linear history instead of merge commits | `git rebase main` |

---

## The mental model that clicked

The thing that made everything click was thinking of Git like **save points in a game**:

- `git add` = selecting what goes into the save
- `git commit` = actually saving
- `git branch` = a parallel timeline
- `git merge` = bringing two timelines back together
- `git stash` = quicksave before a boss fight
- `git rebase` = rewriting history to look cleaner

And GitHub is just **cloud storage for your save files** that your whole team can access.

---

## Merge conflicts — what actually happens

When two people edit the same line, Git can't decide which version to keep. It marks the conflict in the file:

```
<<<<<<< HEAD
your version of the line
=======
teammate's version of the line
>>>>>>> feature/their-branch
```

Fix it manually → `git add <file>` → `git commit`. Done.

---

## What I'm building toward

Today was Day 1. Git is a skill I want to **master**, not just know. The roadmap:

- [x] Core commands and daily workflow
- [x] Branching and merging
- [x] Remote / GitHub basics
- [x] Undoing mistakes
- [ ] Git in CI/CD pipelines (GitHub Actions — actively working on this for IEEE hackathon)
- [ ] GitOps workflows (ArgoCD, Flux)
- [ ] Git hooks and automation
- [ ] Contributing to open source via PRs and forks

---

## Resources used today

- 📺 [FreeCodeCamp Git & GitHub Crash Course](https://www.youtube.com/watch?v=RGOj5yH7evk)
- 🛠️ Scenario-based practice bank (20 real-world Git situations)
- 📖 [Pro Git Book](https://git-scm.com/book/en/v2) — bookmarked for deep dives

---

## Key takeaway

> Git isn't about memorising commands. It's about understanding **what state your code is in** at any moment — and knowing how to move it where you want.

The commands follow naturally once that mental model is solid.

---

*Learning in public. One commit at a time. 🚀*

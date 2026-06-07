---
title: "How I Built My Hugo Blog with GitHub Actions — And Fixed Every Mistake Along the Way"
date: 2026-05-16
description: "A step-by-step walkthrough of my journey from localhost to live deployment — including every error, fix, and lesson learned."
tags: ["hugo", "github-actions", "devops", "web", "beginner"]
categories: ["Projects"]
author: "Hanan"
showToc: true
TocOpen: false
draft: false
cover:
  alt: "Hugo blog setup with GitHub Actions"
---

As a 2nd-year Computer Science student at UCSC, I wanted more than just a GitHub repo or LinkedIn profile. I wanted a **personal tech blog** — something that shows not just *what* I built, but *how I think*, *what I learned*, and *how I solve problems*.

So I chose **Hugo + GitHub Pages + GitHub Actions** — a modern, fast, static site generator stack that's perfect for developers who want full control without server management.

But it wasn't smooth sailing.

I hit version mismatches, Git conflicts, authentication errors, theme incompatibilities, and workflow failures. This post documents **every mistake I made**, **why it happened**, and **exactly how I fixed it** — so you don't have to go through the same pain.

---

## Goal

Build a personal blog using:
- **Hugo** (static site generator)
- **PaperMod theme** (clean, minimal, developer-friendly)
- **GitHub Pages** (free hosting)
- **GitHub Actions** (automated build & deploy)

Live at → `https://hananmd.github.io`

---

## Step 1: Local Setup — The Easy Part (Or So I Thought)

### What I Did Right

- Installed Hugo via `snap install hugo` → got v0.128.0
- Created a new site: `hugo new site my-blog`
- Added PaperMod theme as a submodule:

```bash
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

- Wrote my first post: `content/posts/my-first-post.md`
- Ran local server: `hugo server -D` → worked perfectly on `http://localhost:1313`

### ❌ Mistake #1 — Using an Escaped Apostrophe in TOML

In `hugo.toml`, I wrote:

```toml
title = "Hanan\'s Tech Blog"
```

**Error:**
```
unmarshal failed: toml: invalid escaped character U+0027 '''
```

**Why?** TOML doesn't allow `\'` as an escape sequence inside double-quoted strings. The backslash is unnecessary and invalid.

**Fix:**
```toml
title = "Hanan's Tech Blog"
```

Simple. Double quotes handle apostrophes just fine.

---

## Step 2: Pushing to GitHub — Authentication Hell

### ❌ Mistake #2 — Using GitHub Password Instead of a PAT

When I ran:

```bash
git push origin main
```

GitHub prompted for username/password. I entered my account password and got:

```
remote: Support for password authentication was removed on August 13, 2021.
```

**Why?** GitHub deprecated HTTPS password auth in 2021. You now need a **Personal Access Token (PAT)** or SSH keys.

**Fix:**

1. Go to [https://github.com/settings/tokens](https://github.com/settings/tokens)
2. Generate a new classic token → select the `repo` scope
3. Copy the token (starts with `ghp_...`)
4. When prompted for "password" during `git push`, paste the token

> 💡 Store your token in a password manager. Never hardcode it in your project.

---

## Step 3: Git Conflicts — "Non-Fast-Forward" Errors

### ❌ Mistake #3 — Editing Files Directly on GitHub's Web UI

After setting up locally, I edited `.github/workflows/hugo.yml` directly in the browser. This created a new commit on GitHub that my local branch didn't have.

When I tried to push again:

```
! [rejected] main -> main (non-fast-forward)
hint: Updates were rejected because the tip of your current branch is behind
its remote counterpart.
```

**Why?** Git protects you from accidentally overwriting remote work. If the remote has commits your local doesn't, you must pull first.

**Fix:**

```bash
git pull origin main --rebase
```

This fetches the remote changes and replays your local commits cleanly on top. Then push again — success.

> 💡 Lesson: Avoid editing files via the GitHub web UI if you're working locally. Do all edits locally and push once.

---

## Step 4: GitHub Actions — Submodules & Version Mismatch

### ❌ Mistake #4 — Not Loading Submodules in the Workflow

My initial workflow had:

```yaml
- uses: actions/checkout@v4
```

But PaperMod is a **Git submodule**. Without instructing the checkout action to load submodules, the `themes/PaperMod` folder came in empty — build failed.

**Fix:**

```yaml
- uses: actions/checkout@v4
  with:
    submodules: recursive
```

This tells GitHub Actions to clone the theme along with the rest of the repo.

---

### ❌ Mistake #5 — Hugo Version vs Theme Compatibility

Even after fixing submodules, the build still failed:

```
ERROR => hugo v0.146.0 or greater is required for hugo-PaperMod to build
```

I was using Hugo v0.128.0 in the workflow, but the latest PaperMod requires ≥ v0.146.0.

**Fix — Downgrade PaperMod to a Compatible Version**

Instead of jumping to an unstable Hugo release, I pinned PaperMod to v7.0, which works with Hugo 0.128:

```bash
# Remove the current submodule
git submodule deinit -f themes/PaperMod
rm -rf .git/modules/themes/PaperMod
rm -rf themes/PaperMod

# Re-add and pin to v7.0
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
cd themes/PaperMod
git checkout v7.0
cd ../..

# Commit the pinned version
git add themes/PaperMod
git commit -m "Downgrade PaperMod to v7.0 for Hugo 0.128 compatibility"
git push origin main
```

Build succeeded ✅

> 💡 Alternatively, you can upgrade Hugo in your workflow to v0.146+. I chose stability since v0.146 was very new at the time.

---

## Step 5: baseURL — Localhost vs Production

### ❌ Mistake #6 — Forgetting to Update baseURL

My `hugo.toml` still had:

```toml
baseURL = "http://localhost:1313/"
```

All internal links pointed to localhost → broken navigation on the live site.

**Fix:**

```toml
baseURL = "https://hananmd.github.io/"
```

Commit, push, let GitHub Actions rebuild. Done.

> 💡 Always run `hugo --minify` locally before pushing. It catches config issues before they reach CI.

---

## Final Result

After fixing all of the above, the GitHub Actions workflow went green ✅

**Live blog:** [https://hananmd.github.io](https://hananmd.github.io)

What's there:
- Clean, responsive design via PaperMod
- Working GitHub and LinkedIn links
- First post live
- Tags, reading time, and share buttons all functional

---

## Lessons at a Glance

| Mistake | Lesson |
|---|---|
| `\'` in TOML string | Double quotes handle apostrophes — no backslash needed |
| GitHub password rejected | Use Personal Access Tokens for HTTPS Git operations |
| Non-fast-forward push error | Always pull (`--rebase`) before pushing if remote has new commits |
| Empty theme folder in CI | Set `submodules: recursive` in the checkout action |
| Hugo/PaperMod version clash | Check theme requirements; pin the theme or upgrade Hugo accordingly |
| `baseURL` left as localhost | Set the production URL before deploying |

---

## What's Next

Now that the blog is live, I'm planning to:

1. Write detailed posts on projects — Port Scanner, Mail Client, Log Analyzer
2. Add privacy-friendly analytics with Plausible.io
3. Enable comments via Giscus
4. Possibly get a custom domain (`hanan.dev`)

---

## Final Thoughts

Building this blog taught me more than Hugo or GitHub Actions. It taught me how to read CI/CD logs critically, manage Git submodules, and think about the gap between local dev and production environments.

Most importantly — **shipping matters**. Even if it's imperfect, getting something live is better than waiting for perfection.

Every bug is a lesson. Every failure is feedback.

If you found this useful, share it — it might save someone else a few hours of frustration.

---

## Links

- [My Blog](https://hananmd.github.io)
- [GitHub Repo](https://github.com/hananmd/hananmd.github.io)
- [PaperMod Theme](https://github.com/adityatelange/hugo-PaperMod)
- [Hugo Docs](https://gohugo.io/)
- [GitHub Actions Docs](https://docs.github.com/en/actions)

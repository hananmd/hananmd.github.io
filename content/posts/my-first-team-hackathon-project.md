---
title: "ML Opsidian: Genesis — Day 1 Infrastructure Setup"
date: 2026-05-20
draft: false
tags: ["mlops", "github-actions", "ci-cd", "hackathon", "ieee-ucsc", "devops"]
categories: ["Hackathon", "MLOps"]
description: "How I set up the entire GitHub infrastructure, CI/CD pipeline and team collaboration workflow for IEEE UCSC's ML Opsidian: Genesis competition — all on Day 1."
cover:
  image: ""
  alt: "ML Opsidian Genesis"
---

# ML Opsidian: Genesis — Day 1 Infrastructure Setup

> **Competition:** ML Opsidian: Genesis — IEEE Student Branch, UCSC  
> **My Role:** Person 4 — Infrastructure & Integration  
> **Date:** May 20, 2026  
> **Team:** OHIO ML GENESIS ARC

---

## What is ML Opsidian: Genesis?

ML Opsidian is a two-phase ML competition organized by the IEEE Student Branch at the University of Colombo School of Computing (UCSC).

| Phase | Date | Goal |
|-------|------|------|
| Initial Round | 30 May 2026 | Build the best ML model from scratch on a provided dataset |
| Final Round | 9 June 2026 | Wrap that model into a production MLOps pipeline |

The key insight the competition guide gave us: *everyone will use AI tools for coding — the real differentiator is knowing enough to direct AI well, debug its output, and make smart design decisions.*

Our team of 4 split into parallel tracks:
- **Abdullah & Lawsan** — Core ML / Data Science (Kaggle leaderboard focus)
- **Janagan** — MLOps / Model Serving (FastAPI + Docker + MLflow)
- **Hanan (me)** — Infrastructure & Integration (GitHub Actions + Render deployment)

---

## My Scope as Person 4

My job throughout the competition:

1. **Git & GitHub setup** — branching strategy so 4 people don't overwrite each other
2. **CI/CD with GitHub Actions** — auto-run tests on every push
3. **Cloud Deployment** — deploy the Dockerized model to Render.com
4. **Architecture Diagram + Viva** — own the pipeline story for judges

Day 1 was about getting the foundation right before anyone writes a single line of code.

---

## What I Did on Day 1

### 1. Repository Setup

Created a public GitHub repo: `ml-opsidian-genesis` and added all 4 team members as collaborators.

**Branch protection on `main`:**
- Direct pushes blocked
- All changes must go through Pull Requests
- Requires at least 1 approval before merging
- Stale approvals dismissed when new code is pushed

This physically prevents anyone from accidentally breaking the main branch.

### 2. Folder Structure

Pushed a standardized project structure to `main` before anyone starts coding:

```
ml-opsidian-genesis/
├── data/
│   ├── raw/              # Competition dataset (git-ignored)
│   └── processed/        # Cleaned data (git-ignored)
├── notebooks/            # EDA and experiments
├── src/
│   ├── features/         # Feature engineering
│   ├── models/           # Training scripts
│   └── utils/            # Helper functions
├── api/                  # FastAPI app (Person 3's territory)
├── models/               # Saved .pkl files (git-ignored)
├── .github/workflows/    # CI/CD pipelines
├── requirements.txt
├── .gitignore
└── README.md
```

Having this structure from Day 1 means nobody has to think about "where do I put this file?" — it's already decided.

### 3. `.gitignore` — What We Exclude

```gitignore
__pycache__/
*.pyc
.env
data/raw/
data/processed/
models/*.pkl
*.csv
.DS_Store
```

Key reasons:
- `data/raw/` — competition datasets can be large and are not supposed to be shared
- `.env` — never commit API keys or secrets
- `*.pkl` — saved model files are large binary files, not suited for Git

### 4. `requirements.txt` — Track-Specific Install

An important thing I realized: **not everyone needs all libraries**.

```
# Core Data Science
pandas==2.2.2
numpy==1.26.4
scikit-learn==1.4.2

# Modeling
xgboost==2.0.3
lightgbm==4.3.0

# MLOps & Serving
fastapi==0.110.0
uvicorn==0.29.0
mlflow==2.12.1

# Testing & Linting
flake8==7.0.0
pytest==8.1.1
```

> 💡 **For juniors reading this:** The full `requirements.txt` is for the CI server and final deployment — not your laptop. Install only what your track needs locally.
>
> - ML Track → `pip install pandas numpy scikit-learn xgboost lightgbm`
> - MLOps Track → `pip install fastapi uvicorn mlflow`

Pin your versions (e.g. `pandas==2.2.2` not just `pandas`). An unpinned dependency can break your CI mid-competition when a new version releases.

### 5. GitHub Actions CI/CD Pipeline

This was ahead of schedule (originally planned for Days 3–6) but I got it done on Day 1.

Every push and every Pull Request to `main` now automatically:
1. Sets up a Python 3.9 environment
2. Restores cached pip dependencies (faster runs)
3. Installs all requirements
4. Runs Flake8 linting — catches syntax errors and undefined names
5. Runs an import sanity check on all 8 core libraries

```yaml
name: ML Opsidian CI Pipeline

on:
  push:
    branches: [ main, 'feature/**' ]
  pull_request:
    branches: [ main ]

jobs:
  build-and-test:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Code
      uses: actions/checkout@v4

    - name: Set up Python 3.9
      uses: actions/setup-python@v4
      with:
        python-version: '3.9'

    - name: Cache pip dependencies
      uses: actions/cache@v3
      with:
        path: ~/.cache/pip
        key: ${{ runner.os }}-pip-${{ hashFiles('requirements.txt') }}
        restore-keys: |
          ${{ runner.os }}-pip-

    - name: Install Dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt

    - name: Lint with Flake8
      run: |
        flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
        flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics

    - name: Test Imports (Sanity Check)
      run: |
        python -c "
        import pandas
        import numpy
        import sklearn
        import xgboost
        import lightgbm
        import fastapi
        import uvicorn
        import mlflow
        print('All libraries imported successfully')
        "
```

**Status: ✅ Green — pipeline is active and passing.**

---

## The Branching Strategy

```
main (protected — always stable)
  │
  ├── feature/data-preprocessing   ← Abdullah
  ├── feature/model-training        ← Lawsan
  ├── feature/docker-fastapi        ← Janagan
  └── feature/ci-cd-pipeline        ← Hanan (me)
```

**Daily workflow for every team member:**

```bash
# 1. Sync latest main
git checkout main
git pull origin main

# 2. Switch to your branch
git checkout feature/your-branch

# 3. Do your work, then commit
git add .
git commit -m "add: your description here"

# 4. Push and open a PR
git push origin feature/your-branch
```

**Commit message convention:**
```
add:     new feature or file
fix:     bug fix
update:  modifying existing code
remove:  deleting something
docs:    README or comments
```

---

## Key Lessons from Day 1

**1. Set up infrastructure before anyone codes.**
If you wait until people have already pushed random files, reorganizing is painful. Structure first, code second.

**2. Pin your dependency versions.**
`pandas` vs `pandas==2.2.2` seems like a small difference. Mid-competition it's the difference between a green and a red CI pipeline.

**3. `docker` is not a pip package.**
I initially had `docker` in `requirements.txt`. That installs a Python SDK for programmatically controlling Docker — not Docker itself. Docker is a system tool installed separately. Remove it from pip requirements.

**4. Not everyone needs everything.**
A 4-person team doesn't all need to install the full ML + MLOps stack. Split by track, install what you need.

**5. CI/CD on Day 1 is worth it.**
Getting the pipeline green early means every future push is automatically validated. It takes 1–2 hours to set up and saves hours of "why is this broken on the server" debugging later.

---

## What's Next

- **Days 3–6:** GitHub Actions improvements as the codebase grows
- **Days 5–8:** Cloud deployment on Render.com once Person 3 finishes Docker + FastAPI
- **Days 9–10:** Architecture diagram + viva preparation

I'll document each phase here as we go. If you're a junior at UCSC looking to participate in future ML Opsidian rounds — start with infrastructure. A clean repo and working CI/CD pipeline from Day 1 gives your whole team confidence to move fast.

---

*More posts coming as the competition progresses.*  
*— Hanan MD, Year 2 CS, UCSC*

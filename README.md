# Task 4: Version-Controlled DevOps Project with Git

A hands-on implementation of Git version control best practices, multi-branching release strategies, Pull Request (PR) review workflows, semantic tagging, and repository hygiene for DevOps engineering.

---

## 📌 Project Overview

- **Internship:** Elevate Labs DevOps Internship
- **Task:** Task 4 — Build a Version-Controlled DevOps Project with Git
- **Core Tools:** Git CLI, GitHub
- **Branching Architecture:** `main` $\leftarrow$ `dev` $\leftarrow$ `feature/*`
- **Release Version:** `v1.0.0` (Annotated Git Tag)
- **Primary Deliverables:** Structured repository history, PR merges, `.gitignore`, release tag, and complete Markdown documentation.

---

## 🌳 Branching Strategy & Workflow Diagram

```text
[feature/setup-app] ───────────────┐
                                   │ (Pull Request #1)
                                   ▼
[dev]               ───────────────────────────────┐
                                                   │ (Pull Request #2)
                                                   ▼
[main]              ───────────────────────────────────────● (Tag: v1.0.0)
```

### Branch Responsibilities

- **`main`**: Production-ready, stable codebase. Direct pushes are restricted; updates arrive strictly through reviewed Pull Requests from `dev`.
- **`dev`**: Integration and pre-release testing branch where individual feature branches merge and undergo validation.
- **`feature/*`**: Short-lived, focused task branches created off `dev` for developing individual capabilities or fixes without destabilizing shared branches.

---

## 📁 Repository Structure

```text
devops-git-workflow/
├── docs/               # Markdown documentation, diagrams, and reference materials
├── .gitignore          # Rules excluding build caches, OS files, and secrets
├── app.sh              # Sample application bootstrap script
└── README.md           # Full task documentation & interview answers
```

---

## 🛠️ Step-by-Step Implementation Guide

### 1. Initialize Local Git Repository

Initialize a new local repository with `main` as the default branch:

```bash
mkdir -p devops-git-workflow
cd devops-git-workflow
git init -b main
```

### 2. Configure Repository Hygiene (`.gitignore`)

Create a standard `.gitignore` preventing logs, sensitive environment configs, and runtime caches from entering version control:

```bash
cat << 'EOF' > .gitignore
# Logs & Runtime
*.log
logs/

# Secrets & Environment variables
.env
.env.*
*.pem
*.key

# Operating System Files
.DS_Store
Thumbs.db

# IDE Configurations
.vscode/
.idea/

# Language & Dependency Caches
node_modules/
__pycache__/
*.pyc
dist/
build/
EOF

git add .gitignore
git commit -m "chore: initialize repository with standard .gitignore"
```

### 3. Establish the Branching Hierarchy

Create the development baseline (`dev`) and a feature branch (`feature/setup-app`):

```bash
# Branch out dev from main
git checkout -b dev

# Branch out feature branch from dev
git checkout -b feature/setup-app
```

### 4. Implement Feature & Commit

Develop the sample DevOps utility script on the feature branch:

```bash
cat << 'EOF' > app.sh
#!/bin/bash
set -euo pipefail

echo "=========================================="
echo " Elevate Labs - DevOps Automation Service "
echo " Environment: ${APP_ENV:-development}     "
echo " Version:     1.0.0                       "
echo "=========================================="
EOF
chmod +x app.sh

git add app.sh
git commit -m "feat: add application bootstrap script with environment support"
```

### 5. Publish to GitHub Remote

Connect the local workspace to GitHub and publish all active branches:

```bash
git remote add origin https://github.com/cloudwithpreetham/devops-git-workflow.git

git push -u origin main
git push -u origin dev
git push -u origin feature/setup-app
```

### 6. Code Review & Merge Workflow via Pull Requests

1. **Pull Request 1 (`feature/setup-app` $\rightarrow$ `dev`):**
   - Open a PR on GitHub comparing `dev` $\leftarrow$ `feature/setup-app`.
   - Inspect the file diffs (`app.sh`).
   - Merge the pull request into `dev`.

2. **Pull Request 2 (`dev` $\rightarrow$ `main`):**
   - Open a second PR comparing `main` $\leftarrow$ `dev`.
   - Verify all release-ready integration changes.
   - Merge the pull request into `main`.

3. **Synchronize Local State:**

   ```bash
   git checkout dev && git pull origin dev
   git checkout main && git pull origin main
   ```

### 7. Create and Push Release Tag

Tag the production baseline on `main` with an annotated tag:

```bash
git checkout main
git tag -a v1.0.0 -m "Release v1.0.0: Version-controlled baseline deployment"
git push origin v1.0.0
```

Verify tag details:

```bash
git show v1.0.0
```

---

## 💡 Technical Interview Q&A

### 1. What is Git?

Git is an open-source, distributed version control system (DVCS) designed for speed, data integrity, and support for distributed, non-linear workflows. Key characteristics include:

- **Distributed Architecture:** Every developer retains a full clone of the repository history locally, allowing full offline operations.
- **Cryptographic Hashing:** Every file, tree, and commit is immutably indexed via SHA-1 or SHA-256 hashes, ensuring tamper-proof integrity.
- **Three-Tree Architecture:** Git tracks files across three primary states: the **Working Directory**, the **Staging Area (Index)**, and the **Commit History (`.git` repository)**.

### 2. What is the difference between `git merge` and `git rebase`?

| Feature              | `git merge`                                                               | `git rebase`                                                                                          |
| :------------------- | :------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------- |
| **History Behavior** | Creates a non-destructive merge commit tying divergent branches together. | Re-plays commits from the current branch on top of the base branch, producing a flat, linear history. |
| **Commit Hashes**    | Preserves original commit hashes and timestamps.                          | Rewrites commit hashes for all replayed commits.                                                      |
| **Traceability**     | Preserves historical context of branch lifecycles and PR merges.          | Produces a clean log without extra merge commits, but loses branch lifecycle context.                 |
| **Golden Rule**      | Safe for shared, remote-tracking branches.                                | **Never rebase commits that have already been pushed to public/shared branches.**                     |

### 3. What is a pull request?

A Pull Request (PR) is a collaboration and governance workflow provided by Git hosting platforms (GitHub, GitLab, Bitbucket). It allows a developer to request that changes from an isolated branch be reviewed and pulled into a target branch.

**Key functions of a PR:**

- Displays line-by-line diffs for thorough peer code review.
- Triggers automated CI/CD checks (unit testing, SAST scans, container builds).
- Enables branch protection rules (e.g., requiring passing checks and approval reviews before merge).

### 4. How do you resolve merge conflicts?

A merge conflict occurs when two branches modify the same lines of a file concurrently or when a file is deleted on one branch and modified on another.

**Resolution Steps:**

1. Check conflicted status using `git status` (shows files under `Unmerged paths`).
2. Open the affected files and locate standard conflict markers:

   ```text
   <<<<<<< HEAD (Current branch code)
   echo "Running in production mode"
   =======
   echo "Running in automated container mode"
   >>>>>>> feature/setup-app (Incoming branch code)
   ```

3. Edit the file manually: remove marker delimiters (`<<<<<<<`, `=======`, `>>>>>>>`) and combine or select the correct logic.
4. Mark the conflict resolved by staging the file:

   ```bash
   git add <filename>
   ```

5. Finalize the resolution commit:

   ```bash
   git commit -m "fix: resolve merge conflict between dev and main"
   ```

### 5. What are Git tags?

Git tags are static reference pointers targeting specific commits in history, primarily used to mark release milestones (e.g., semantic versions like `v1.0.0` or release candidates).

- **Lightweight Tags:** Simple, fast pointers targeting a commit hash with no extra metadata:

  ```bash
  git tag v1.0.0-lw
  ```

- **Annotated Tags (Best Practice):** Stored as full objects in the Git database. They include the tagger's name, email, creation timestamp, a dedicated tagging message, and optional GPG signature verification:

  ```bash
  git tag -a v1.0.0 -m "Release version 1.0.0 production deployment"
  ```

- **Publishing Tags:** By default, `git push` does not push tags to remotes. They must be pushed explicitly:

  ```bash
  git push origin v1.0.0
  # Or push all local tags:
  git push origin --tags
  ```

### 6. What is a Git workflow?

A Git workflow is a formalized branching and deployment strategy adopted by engineering teams to ensure code stability, seamless parallel development, and clear release cadences.

**Common Git Workflows:**

- **Trunk-Based Development:** Developers commit small, frequent changes directly to `main` (or very short-lived feature branches) backed by comprehensive automated test suites and feature flags. This is the industry standard for fast-paced DevOps/CI/CD pipelines.
- **GitHub Flow:** A lightweight workflow where feature branches branch directly off `main`, undergo code review and automated testing via PRs, and are immediately merged and deployed to production upon approval.
- **Gitflow:** A traditional, structured workflow maintaining long-lived `main` and `develop` branches alongside specialized `feature/*`, `release/*`, and `hotfix/*` branches.

### 7. Explain `git stash`

`git stash` temporarily records and shelves the current uncommitted state of tracked files (both staged and unstaged working modifications) without making a commit, reverting the working directory back to the clean `HEAD` state.

**Essential Stash Commands:**

- `git stash push -m "wip: authentication logic"`: Shelves current working changes with a descriptive label.
- `git stash list`: Shows all saved stashes with their indexes (`stash@{0}`, `stash@{1}`).
- `git stash pop`: Re-applies the most recent stash onto the active working directory and drops it from the stash stack.
- `git stash apply`: Re-applies changes while keeping the stash preserved in the list.
- `git stash drop stash@{0}`: Discards a specific stash index.
- `git stash clear`: Empties all stashed entries.

### 8. What is the use of `.gitignore`?

A `.gitignore` text file defines path patterns and wildcards that Git intentionally ignores and prevents from being tracked.

**Primary use cases in DevOps:**

- **Security:** Prevents accidental leakage of credentials, SSH keys, certificates, API tokens, and `.env` files into source control.
- **Clean Repository Size:** Excludes heavy runtime build outputs, dependency directories (e.g., `node_modules/`, Python virtual environments), and compiler artifacts (`*.o`, `*.class`, `*.pyc`).
- **OS & Editor Cleanliness:** Excludes platform-specific metadata files (e.g., `.DS_Store`, `Thumbs.db`) and IDE configuration folders (`.vscode/`, `.idea/`).

---

## 📋 Task 4 Verification Checklist

- [x] Initialized Git repository with clean commit history.
- [x] Implemented multi-branch workflow (`main`, `dev`, `feature/setup-app`).
- [x] Managed changes through GitHub Pull Requests.
- [x] Created and pushed annotated tag `v1.0.0`.
- [x] Implemented comprehensive `.gitignore`.
- [x] Documented repository architecture and answered all 8 interview questions.
- [x] Collaborated with engineering teams to ensure code stability, seamless parallel development, and clear release cadences.

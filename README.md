# DevSecOps Learning Journey — Module 03: Git

> **How Git actually works underneath — and how a security engineer uses it.**

Part of my hands-on journey into **DevSecOps & Cloud Security**. Each module pairs the core skill with its **security and cloud dimensions**, because in DevSecOps the two aren't separate.

![Signed commits](https://img.shields.io/badge/commits-signed-brightgreen) ![Workflow](https://img.shields.io/badge/workflow-PR%20%2B%20branch%20protection-blue) ![Focus](https://img.shields.io/badge/focus-DevSecOps-purple)

---

## 📌 Overview

Most people learn Git as a bag of memorized commands. This module goes under the hood:

> **Git is a graph of snapshots connected by pointers.**

Once you see the object model, every confusing behavior — detached HEAD, why rebase rewrites history, why a leaked secret won't die — becomes obvious. Everything here I worked **hands-on**: exploring Git's internals with plumbing commands, and running real collaboration and recovery workflows on a live GitHub repo with **signed commits** and **branch protection**.

---

## 🧠 What I learned

### 3.1 — How Git stores data (the object model)
- Three object types: **blob** (file *contents*, no name), **tree** (a directory listing — where *filenames* live), **commit** (a snapshot pointing to one tree + its parent).
- **Content-addressing:** every object is named by the SHA of its content, so two identical files dedupe to the *same* blob — I proved it by hand with `git cat-file`.
- Git is a **Merkle chain**, so history is **tamper-evident**: change one byte and every downstream hash changes.
- A **branch** is just a movable pointer (a 41-byte file); **HEAD** marks where the next commit lands.
- 🔐 **Security:** a commit's author is spoofable plain text. **Signed commits** (`git commit -S`) are cryptographic proof of origin — the same supply-chain trust as GPG-signed packages.

### 3.2 — Workflows & pull requests
- Branching strategies balance **isolation vs. integration frequency**; high performers integrate small changes often (GitHub Flow).
- A **pull request is not a Git feature** — it's a platform wrapper around a merge that adds a review surface, CI gates, and an approval gate.
- 🔐 **Security:** **branch protection** on `main` (require PR, required approvals = four-eyes / separation of duties, required status checks = shift-left scanning, required signed commits, block force-push) + **CODEOWNERS** (right experts review the right paths). Maps to NIST **AC-5** (separation of duties) and **CM-3** (change control).

### 3.3 — Remotes
- `clone` / `push` / `pull` / `fetch`, and `origin` as a nickname for the remote URL.
- The remote is the **shared source of truth + backup** — your local `.git` dies with the laptop.
- 🔐 **Security:** push auth (SSH keys / tokens) is a *separate gate* from branch protection. **Authentication ≠ permission to push to a protected branch.**

### 3.4 — Undoing mistakes
- One question picks the tool: **have you pushed it?** → `restore` (uncommitted) · `reset` (committed, not pushed) · `revert` (already pushed).
- `reset` **rewrites** history (safe local, dangerous shared); `revert` **adds** a new inverse commit (safe to push).
- 🔐 **Security:** `revert`-not-`reset` on shared branches preserves the **audit trail** (like CloudTrail). GitOps rollback = `git revert` the infra commit.

### 3.5 — Secrets in history *(in progress)*
- Why deleting a file doesn't remove a secret from history (the Merkle chain again).
- Detecting secrets with **Gitleaks** (local + pre-commit hook + CI).
- The only real fix for a leaked credential: **rotate it.** Maps to IAM access-key rotation / short-lived STS credentials.

---

## 🔧 Hands-on labs

See [`labs/`](./labs). Highlights:
- Explored the object model directly with `git cat-file` / `git hash-object` (saw blobs, trees, commits, and content-addressed dedup).
- Made verified **SSH-signed commits** and confirmed `Good "git" signature`.
- Ran a **branch-protection + PR** workflow end to end — watched a direct push to a protected `main` get rejected (`GH013`), then merged the change via PR.
- Practiced **restore / reset / revert**, including resolving a **live merge conflict** mid-revert and preserving the audit trail.

---

## 🎯 End-of-module lab

A single end-to-end DevSecOps scenario — **"Secure Feature Delivery"** — that chains the entire module: signed commits, protected `main` + CODEOWNERS, an accidentally committed secret caught with Gitleaks, credential rotation, a clean revert, and a verified audit trail.

➡️ [`end-of-module-lab/`](./end-of-module-lab)

---

## ⚡ Command reference

| Area | Command | What it does |
|---|---|---|
| Inspect | `git cat-file -p HEAD` | Print a commit's tree, parent(s), author |
| Inspect | `git hash-object <file>` | The SHA of a file's contents (its blob name) |
| Sign | `git commit -S -m "..."` | Make a cryptographically signed commit |
| Remotes | `git clone / push -u origin / pull` | Collaborate through the shared remote |
| Branch | `git switch -c <name>` | Create + move onto a branch |
| Merge | `git merge <branch>` | Combine branches (two-parent commit) |
| Undo | `git restore <file>` | Discard uncommitted edits |
| Undo | `git reset --soft/--mixed/--hard HEAD~1` | Undo a local, unpushed commit |
| Undo | `git revert <hash>` | Safely undo a pushed commit (new inverse commit) |

---

## 🔐 Security & cloud takeaways

| Git concept | Security / cloud mapping |
|---|---|
| Signed commits | Supply-chain trust (SLSA, provenance) → signed container images |
| Branch protection | Deny-by-default + separation of duties → IAM / SCPs; NIST AC-5, CM-3 |
| `revert` not `reset` on shared branches | Immutable audit log → CloudTrail |
| Rotate a leaked secret | IAM access-key rotation / short-lived STS credentials |
| Git = the CI/CD entry point | Repo security = pipeline security |

---

## 🧭 About this journey

I'm transitioning into a **Cloud Security Engineer / DevSecOps** role. This repo is Module 3 of a structured, security-first learning path.

- **Certifications:** AWS Cloud Practitioner · CompTIA Security+ · Microsoft Azure Fundamentals *(working toward RHCSA, AWS CloudOps, HashiCorp Terraform Associate)*
- **Other modules:** _link your other `devsecops-NN-*` repos here as you build them_

*Built with signed commits and a PR-gated `main` — the repo's own history is part of the demonstration.*

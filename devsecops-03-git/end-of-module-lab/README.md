# End-of-Module Lab — "Secure Feature Delivery"

A single end-to-end DevSecOps workflow that chains **every** lesson in Module 3. You'll ship a feature the professional way, make a realistic security mistake, detect it, respond correctly, and prove the whole thing held up.

> Do this on a dedicated GitHub repo (this one works). Use **Git Bash** on Windows. Complete it **after Lesson 3.5** so you have Gitleaks.

## Scope it exercises
3.1 object model + signing · 3.2 PRs + branch protection + CODEOWNERS · 3.3 remotes · 3.4 undoing/revert/conflict · 3.5 secret detection + rotation.

---

## Tasks

### 1. Lock down the repo (3.2 / 3.3)
- Protect `main`: require a pull request, **require signed commits**, block force-pushes.
- Add `.github/CODEOWNERS` with yourself as owner.
- **Verify:** a direct `git push origin main` is rejected (`GH013`).

### 2. Build a feature with signed commits (3.1 / 3.3)
- Branch `feature/app-config`.
- Add a small `app/config.yaml` (any sample app settings).
- Make **signed** commits (`git commit -S`); push the branch.
- **Verify:** `git log --show-signature` shows `Good "git" signature`.

### 3. Make the mistake (the setup)
- In one commit on the branch, **accidentally commit a secret** — e.g. put `API_KEY=sk_live_fake12345` in a `.env` or directly in `config.yaml`. Push it.

### 4. Detect it with Gitleaks (3.5)
- Install Gitleaks (Windows: `scoop install gitleaks`, a release binary, or Docker).
- Run `gitleaks detect` against the repo — it should flag the secret.
- **Verify:** Gitleaks reports the leaked key, its file, and commit.

### 5. Respond correctly — rotate + clean (3.4 / 3.5)
- **Rotate first:** treat the key as compromised; note how you'd invalidate the old one and issue a new one. *(This is the real fix — the key is in history forever.)*
- Move the secret out of the repo: add `.env` to `.gitignore`, load the value from an untracked file / env var instead.
- Use `git revert` (or a corrective commit) to undo the bad change going forward.
- **Understand & note:** `git revert` removes the *effect*, not the secret from *history*. Scrubbing history needs `git filter-repo`/BFG + force-push — and you **still** rotate.

### 6. Ship it through the gate (3.2)
- Open a PR for the cleaned feature branch → it must go through the protected-`main` gate → merge it.
- **Verify:** `main` was only updated via the PR, with signed commits.

### 7. Prove the audit trail (3.4)
- `git log --oneline` on `main` shows the mistake **and** its corrective commit — nothing erased.
- Re-run `gitleaks detect` on the working tree → clean.

---

## Deliverable
Document your run in [`SOLUTION.md`](./SOLUTION.md): the commands, the Gitleaks output, the rotation decision, and screenshots. Narrate *why* each step is the right DevSecOps move — that write-up is what makes this a portfolio piece, not just a lab.

## Success criteria
- [ ] `main` is protected; direct push rejected
- [ ] Signed, verified commits throughout
- [ ] Secret committed, then **detected** by Gitleaks
- [ ] Credential rotation reasoned through and documented
- [ ] Secret removed going forward; `.gitignore` + CODEOWNERS in place
- [ ] Feature merged via PR; audit trail intact

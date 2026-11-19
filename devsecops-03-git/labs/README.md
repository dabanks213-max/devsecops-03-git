# Labs — Module 03 (Git)

Hands-on work for this module. Each lab below I ran myself; add your terminal screenshots/output under each as you go.

## Lab 1 — Explore the object model (plumbing)
Goal: *see* blobs, trees, and commits directly.
```bash
git init && echo "hello" > a.txt
git add a.txt && git commit -m "first"
git cat-file -t HEAD            # -> commit
git cat-file -p HEAD            # tree, parent, author, message
git cat-file -p <tree-hash>     # "blob <hash>  a.txt" (filename lives here)
git cat-file -p <blob-hash>     # -> hello (contents only)
git hash-object a.txt           # the SHA of the contents = the blob name
```
**Proved:** two files with identical content share one blob hash (content-addressing).

## Lab 2 — Signed commits (SSH signing)
```bash
ssh-keygen -t ed25519 -C "git signing key"
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519.pub
git config --global commit.gpgsign true
git commit -S -m "a signed commit"
git log --show-signature -1     # -> Good "git" signature
```
**Point:** the author field is spoofable; the signature is cryptographic proof.

## Lab 3 — Branch protection + PR workflow
1. Enable branch protection on `main` (require PR, block force-push).
2. Direct push to `main` → **rejected** (`GH013`) — the gate working.
3. Branch → commit → push → open PR → merge.
**Learned:** protection is a *remote* gate; local commits always succeed; auth ≠ push permission.

## Lab 4 — Remotes
`clone`, `push -u origin <branch>`, `pull`, `remote -v`, tracking branches — collaboration plumbing. Local view lags the remote until `pull`.

## Lab 5 — Undoing (restore / reset / revert)
- `git restore` to discard uncommitted edits.
- `git reset --soft HEAD~1` to undo a local unpushed commit.
- `git revert <hash>` to undo a **pushed** commit — including resolving a live **merge conflict** mid-revert.
**Learned:** pick by "have you pushed?"; `reset` rewrites history, `revert` adds forward (safe to push).

---
_Lab 6 (Gitleaks secret detection) lands with Lesson 3.5._

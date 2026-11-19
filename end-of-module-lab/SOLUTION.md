# Solution — Secure Feature Delivery

> My write-up of the end-of-module lab. (Fill in as you complete each task.)

## 1. Repo lockdown
_Branch protection settings used + screenshot of the rejected direct push (`GH013`)._

## 2. Signed feature commits
_Commands + `git log --show-signature` output._

## 3. The committed secret
_What I committed and where._

## 4. Gitleaks detection
_Install method + `gitleaks detect` output showing the finding._

## 5. Response — rotate + clean
_Rotation decision (what I'd invalidate/reissue). How I removed the secret going forward. Why `revert` doesn't scrub history._

## 6. PR merge
_The PR + merge, with signed commits through the gate._

## 7. Audit trail
_`git log --oneline` showing mistake + fix; clean Gitleaks re-run._

## Reflection
_What this exercise taught me about how secure delivery actually works, and how it maps to cloud (IAM rotation / STS, signed artifacts, CloudTrail-style immutable audit)._

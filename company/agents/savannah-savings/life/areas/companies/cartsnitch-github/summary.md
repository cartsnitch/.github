# CartSnitch GitHub Infrastructure

GitHub org: `cartsnitch`. Branch protection on `cartsnitch/cartsnitch` requires 2 approving reviews (CTO + QA) and dismisses stale reviews on push.

## GitHub Apps
- **cartsnitch-cto** (App ID 3140751, Installation 117768296) — used by CTO (Savannah Savings). Has `pull_requests:write`, `contents:write`. Can submit PR reviews.
- **cartsnitch-engineer** — used by engineering agents (Betty, Steve). Has `contents:write`. Authors PRs.
- **cpfarhood-k8s** — used by CI. Has `contents:write` but missing `pull_requests:write`.

## Known Gap (as of 2026-03-30)
Checkout Charlie (QA) has NO GitHub App. Cannot submit GitHub PR review approvals. This blocks every PR requiring QA approval on GitHub. Tracked in CAR-144.

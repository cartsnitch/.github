# Tacit Knowledge — Savannah Savings (CTO)

How I operate and patterns I've learned.

## Organization

- Manager: Coupon Carl (CEO, `f2395b62-cb26-4595-b026-d506fde1c2c1`)
- Direct reports: Barcode Betty (`71f37521-8e62-4d27-bd9c-cfd52b5b3a07`), Stockboy Steve (`01dfbf79-c93d-4224-a7d9-05b2779e425e`), Checkout Charlie (`b8b294e3-a12d-4bff-b321-6f020792b21c`)
- Deal Dottie (`ff0b8079-5823-4c4f-ad40-6a5147246594`) — QA role, UAT tester
- Rollback Rhonda — **TERMINATED** as of 2026-04-01. Cannot assign tasks.
- Handoff chain: Engineer → QA (Checkout Charlie) → CTO (me). UAT run by Deal Dottie.
- Markdown Martha (`9becc57b`) — CMO, idle. Not a direct report.

## Memory System Notes

- Layer 1 (PARA): `$AGENT_HOME/life/` — entity knowledge graph
- Layer 2 (Daily Notes): `$AGENT_HOME/memory/YYYY-MM-DD.md`
- Layer 3 (Tacit): this file (`$AGENT_HOME/MEMORY.md`)
- Memory bootstrapped 2026-03-28 by CEO (CAR-64)

## UAT Ownership Model (CEO Directive, 2026-03-30; updated 2026-04-02)

- **CTO owns ALL UAT knowledge** in a playbook at `$AGENT_HOME/playbooks/uat-playbook.md`
- Rollback Rhonda terminated 2026-04-01. Deal Dottie (`ff0b8079-5823-4c4f-ad40-6a5147246594`) is the active UAT tester.
- HEARTBEAT.md still references Rhonda for UAT assignment — route UAT tasks to Deal Dottie instead.
- When UAT work arrives, CTO decomposes into atomic tasks — one URL, one action, one verification per task
- Playbook matures continuously after every UAT cycle
- Plan: CAR-198

## GitHub App Review Pattern

- CTO GitHub App (`cartsnitch-cto`) CAN submit formal `APPROVE` reviews via API: `gh api repos/{owner}/{repo}/pulls/{n}/reviews -X POST -f event=APPROVE -f body="..."`
- Do NOT use `gh pr review --comment` — that creates `COMMENTED` state, not `APPROVED`
- Branch protection requires 2 `APPROVED` reviews before merge — comment reviews don't count
- QA has a separate app (`cartsnitch-qa`) that can also submit approvals
- Always submit formal approvals, not just comment-based reviews
- **Self-approval blocker:** If CTO app pushed commits to a branch, GitHub blocks `APPROVE` review from that same app ("Can not approve your own pull request"). In this case, submit a COMMENT review noting CTO approval and hand off to CEO with a note about the constraint.

## GitHub CLI Multi-Account Auth

- Multiple gh accounts exist in `/paperclip/.config/gh/hosts.yml` (CEO, CTO, engineer, QA bots + groombook bots)
- `gh auth login --with-token` adds/updates account but does NOT switch active account
- After login, MUST run `gh auth switch --user cartsnitch-cto[bot]` to make CTO account active
- Check with `gh auth status` before any GitHub operations
- Active account determines which bot identity is used for API calls

## Task Decomposition Lessons

- Never delegate "investigate and fix" — investigate first, then delegate the specific fix
- MiniMax M2.7 agents cannot follow 500-line instruction docs — keep under 120 lines
- Each delegated task: 1 file change, exact repo/branch/path, step-by-step, no ambiguity
- Include full context in every task — don't assume delegate has context from previous tasks

## Playwright MCP

- Correct Playwright URL for CartSnitch: `http://playwright-cartsnitch:8931/sse` (NOT `http://playwright:8931/mcp`)
- Board confirmed 2026-03-31: Playwright was never broken, just misconfigured URL
- Each app gets its own Playwright sidecar with naming convention `playwright-<app>`

## Infra & CI Lessons

- Never use `:latest` image tags in production k8s manifests — containerd caches can serve stale digests. Always pin to CalVer or SHA tags.
- The k3s cluster has nodes: dot (control-plane), mindy (worker), wakko (control-plane), yakko (control-plane), buttons (gpu/worker).
- When debugging pod CrashLoops, always exec into the container and check the actual config files.
- GitHub App token script needs `chmod +x` before each session — permissions don't persist.
- **Merge-conflict silent drops:** When GitHub reports a PR as "merged", always verify with `git merge-base --is-ancestor <sha> main`. Merge conflicts can silently drop changes. PR #76's auth session fix was lost this way during PR #61 merge — the merge resolved auth.ts without the session mapping. After any merge, verify target file content on main matches expectations.
- **Foreign commit in infra PR #105** (2026-04-02): "Flea Flicker" (`flea-flicker@groombook.io`) committed to cartsnitch/infra. Not a CartSnitch identity — groombook is a different org. Check all PRs for non-CartSnitch commit authors going forward. Investigate how cross-org push access occurred.
- **Infra repo IS Flux GitOps:** `cartsnitch/infra` is referenced by the `cartsnitch` GitRepository in the cluster. It IS the Flux GitOps source of truth for CartSnitch. Do NOT escalate Flux CRD/RBAC changes to the bootstrap repo (`cpfarhood/kubernetes`). Add Flux resources (Kustomization CRDs, RBAC) directly to the infra repo. Board was clear (2026-04-03): "the infra repo is flux gitops."
- **Flux architecture:** Bootstrap (`flux-system/cartsnitch`) creates GitRepository + Kustomizations in `cartsnitch` namespace. Existing Kustomizations: `cartsnitch-dev` (path `./apps/overlays/dev`), `cartsnitch-prod` (path `./apps/overlays/prod`). All use `cartsnitch-flux` SA. New environment overlays need: a Flux Kustomization CRD + RBAC in the infra repo.

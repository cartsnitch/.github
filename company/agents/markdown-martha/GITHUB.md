# GitHub

#### GitHub is the primary source of truth. Paperclip issues must have a corresponding GitHub issue — create one if it doesn't exist. Both stay open until work is completed, reviewed, approved, merged, and QA'd.

### You have GitHub access via a GitHub App with credentials stored in a file and environment variables. A GitHub MCP server and the gh cli are available.
All changes must happen via pull request.
Tag @cpfarhood in all pull requests for **visibility only** (cc, not review request).

### GitHub Authentication

Use the github-app-token skill to create the `GH_TOKEN` env var. The `gh` CLI and GitHub API respect this env var automatically.

**NEVER run `gh auth login`.** It triggers an interactive device-auth flow that hangs headless agents for minutes. Always use the github-app-token skill instead.

### Creating Pull Requests

Use the `gh` CLI or the GitHub MCP server to create pull requests. Always cc @cpfarhood for visibility — do **not** request review from @cpfarhood.

```bash
gh pr create --title "..." --body "... cc @cpfarhood"
```

### PR Review & Merge Policy

Branch protection requires **2 approving GitHub reviews** before merge. The required reviewers are:

1. **CTO** (Savannah Savings) — technical review and approval
2. **QA** (Checkout Charlie) — code quality review and GitHub approval

Additionally, **Rollback Rhonda** (User Acceptance Tester) must complete UAT and sign off via Paperclip/PR comment before the CTO will review.

**@cpfarhood is not a reviewer.** Do not request review from or tag @cpfarhood as a required approver. The board is cc'd for visibility only.

When a PR is ready for review:
- Request review from the CTO and QA agents on GitHub
- If reviews are dismissed (e.g., after a force-push or rebase), request fresh reviews from CTO and QA — not from the board
- Once both GitHub approvals are in place (CTO + Checkout Charlie) and UAT sign-off is confirmed, the CTO or CEO may merge

### CMO Repos

Work primarily in:

* `cartsnitch/.github` — community health files, issue templates, contribution guides
* Any CartSnitch marketing or public site repositories as directed by the CEO

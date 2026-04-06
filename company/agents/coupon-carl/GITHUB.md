# GitHub

#### GitHub is the primary source of truth.  Paperclip issues must have a corresponding GitHub issue, if one does not exist it should be created. Both GitHub and Paperclip issues should remain open until the work is completed, reviewed, approved, merged, and quality assurance has been performed.

### You have GitHub access via a GitHub App with credentials stored in a file and environment variables. A GitHub MCP server and the gh cli are available.
All changes must happen via pull request.
Tag @cpfarhood in all pull requests for **visibility only** (cc, not review request).

### GitHub Authentication

Use the `github-app-token` skill for GitHub access. The skill is **instructions only** — there is no script to run. Invoke it via the Skill tool to load the instructions into context, then execute the bash steps yourself to write the token to `$AGENT_HOME/.gh-token` and authenticate with `gh auth login --with-token`. Clean up the token file after use.

### Creating Pull Requests

Use the `gh` CLI or the GitHub MCP server to create pull requests. Always cc @cpfarhood for visibility — do **not** request review from @cpfarhood.

```bash
gh pr create --title "..." --body "... cc @cpfarhood"
```

### PR Review & Merge Policy

GitHub branch protection requires **CI checks to pass** (lint, test, build-and-push) but **no GitHub review approvals** are required. Governance is enforced entirely through the Paperclip SDLC.

The Paperclip SDLC review sequence is:
1. **QA** (Checkout Charlie) — reviews and approves in Paperclip
2. **CTO** (Savannah Savings) — technical review and Paperclip approval
3. **CEO** (Coupon Carl) — final review and **sole merger of all PRs**

Additionally, **Rollback Rhonda** (User Acceptance Tester) must complete UAT and sign off via Paperclip/PR comment before the CTO will review.

**@cpfarhood is not a reviewer.** Do not request review from or tag @cpfarhood as a required approver. The board is cc'd for visibility only.

**Note:** All agents share a GitHub App identity, so GitHub-native PR reviews submitted by agents are from the same actor. Paperclip approval tracking is the authoritative record. CEO is the sole merger — do not merge without QA + CTO Paperclip approvals in place.

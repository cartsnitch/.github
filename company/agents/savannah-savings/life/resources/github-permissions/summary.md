# GitHub CTO App Permissions

The `cartsnitch-cto` GitHub App (App ID 3140751, Installation 117768296) has **read-only** permissions on all CartSnitch repos.

## Cannot Do
- Post PR reviews (`addPullRequestReview` → 403)
- Post issue/PR comments (`addComment` → 403)
- Close PRs (`closePullRequest` → 403)
- Push code

## Can Do
- Read PR diffs, status checks, reviews
- Read repo contents, CI runs
- Authenticate via `gh auth login`

## Workaround
Route all code review feedback through **Paperclip comments** on the relevant task. Engineers and QA see the feedback there. Tag with `@BarcodeBetty` or `@CheckoutCharlie` as needed.

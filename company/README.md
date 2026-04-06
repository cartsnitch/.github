# CartSnitch

> A lookout for savings at the grocery store.

![Org Chart](images/org-chart.png)

## What's Inside

> This is an [Agent Company](https://agentcompanies.io) package from [Paperclip](https://paperclip.ing)

| Content | Count |
|---------|-------|
| Agents | 7 |
| Skills | 10 |

### Agents

| Agent | Role | Reports To |
|-------|------|------------|
| Barcode Betty | Engineer | savannah-savings |
| Checkout Charlie | qa | savannah-savings |
| Coupon Carl | CEO | — |
| Deal Dottie | qa | savannah-savings |
| Markdown Martha | CMO | coupon-carl |
| Savannah Savings | CTO | coupon-carl |
| Stockboy Steve | Engineer | savannah-savings |

### Skills

| Skill | Description | Source |
|-------|-------------|--------|
| github-app-token | Generate a GitHub installation access token from a GitHub App PEM key, App ID, and Installation ID, write it to a per-agent file, then authenticate the gh CLI with it. | [github](https://github.com/farhoodliquor/skills) |
| playwright-ephemeral | Provision and tear down ephemeral Playwright MCP browser sessions as Kubernetes Jobs for E2E testing. | [github](https://github.com/farhoodliquor/skills) |
| shannon | Autonomous AI pentester for web apps and APIs. Run white-box security assessments with Shannon — analyzes source code, identifies attack vectors, and executes real exploits to prove vulnerabilities. Triggered by 'shannon', 'pentest', 'security audit', 'vuln scan'. | [github](https://github.com/farhoodliquor/skills) |
| gitops-knowledge | > | [github](https://github.com/fluxcd/agent-skills) |
| gitops-repo-audit | > | [github](https://github.com/fluxcd/agent-skills) |
| minimax-multimodal-toolkit | > | [github](https://github.com/MiniMax-AI/skills) |
| paperclip-create-agent | > | [github](https://github.com/paperclipai/paperclip/tree/master/skills/paperclip-create-agent) |
| paperclip-create-plugin | > | [github](https://github.com/paperclipai/paperclip/tree/master/skills/paperclip-create-plugin) |
| paperclip | > | [github](https://github.com/paperclipai/paperclip/tree/master/skills/paperclip) |
| para-memory-files | > | [github](https://github.com/paperclipai/paperclip/tree/master/skills/para-memory-files) |

## Getting Started

```bash
pnpm paperclipai company import this-github-url-or-folder
```

See [Paperclip](https://paperclip.ing) for more information.

---
Exported from [Paperclip](https://paperclip.ing) on 2026-04-06

# CartSnitch Organization Repository

This is the [CartSnitch](https://github.com/cartsnitch) organization health repository. It contains organization-wide configuration, company metadata, agent definitions, and shared skills.

## Repository Purpose

- **Organization Profile**: Public-facing profile at `profile/README.md`
- **Company Metadata**: Internal company information at `company/`
- **Agent Definitions**: Paperclip agent configurations at `company/agents/`
- **Shared Skills**: Reusable skills at `company/skills/`
- **Dependency Management**: Renovate configuration at `renovate-config.json`

## Folder Structure

```
.github/
├── profile/
│   ├── README.md           # Organization public profile (shown on github.com/cartsnitch)
│   └── cartsnitch-logo.png # Organization logo
├── company/
│   ├── README.md           # Company overview and agent/skill inventory
│   ├── .paperclip.yaml     # Paperclip configuration (agents, skills, company settings)
│   ├── agents/             # Per-agent configuration and instructions
│   │   ├── <agent-name>/
│   │   │   ├── AGENTS.md   # Agent-specific instructions
│   │   │   ├── MEMORY.md   # Agent memory/knowledge base
│   │   │   └── ...         # Agent-specific files (life/, memory/, etc.)
│   └── skills/             # Shared skill definitions
│       ├── farhoodliquor/  # Third-party skills (playwright-ephemeral, shannon, github-app-token)
│       ├── fluxcd/          # Flux CD skills (gitops-knowledge, gitops-repo-audit)
│       ├── minimax-ai/      # MiniMax AI skills
│       └── paperclipai/    # Paperclip platform skills (paperclip, para-memory-files, etc.)
├── renovate-config.json    # Renovate dependency update configuration
└── cartsnitch-logo.png     # Organization logo (also in profile/)
```

## Key Components

### Organization Profile (`profile/`)

The `profile/README.md` is displayed on the CartSnitch GitHub organization page at https://github.com/cartsnitch. It includes:
- Organization branding and logo
- Links to key repositories
- Tech stack overview
- Getting started information for developers

### Paperclip Configuration (`company/.paperclip.yaml`)

This file defines:
- All agents in the CartSnitch organization (role, capabilities, adapter config, runtime settings)
- Company branding settings (brand color, logo)
- Agent sidebar ordering

### Agent Definitions (`company/agents/`)

Each agent has its own folder containing:
- `AGENTS.md` - Agent-specific instructions and role definition
- `MEMORY.md` - Agent's persistent knowledge base
- Additional agent-specific files (heartbeat configs, github settings, etc.)

### Skills (`company/skills/`)

Shared skills are organized by source:
- `farhoodliquor/` - Third-party skills from Farhood Liquor
- `fluxcd/` - Flux CD operational skills
- `minimax-ai/` - MiniMax AI capabilities
- `paperclipai/` - Paperclip platform skills

## Maintenance

### Updating Agent Configurations

Agent configurations are defined in `company/.paperclip.yaml`. To update an agent:

1. Edit the agent section in `.paperclip.yaml`
2. Update the agent's `AGENTS.md` file if role/capabilities changed
3. Submit changes via PR following the contribution guidelines

### Adding New Agents

New agents should be added to `company/.paperclip.yaml` with:
- Unique agent name
- Role (engineer, qa, ceo, cto, cmo)
- Icon selection
- Capabilities description
- Adapter configuration
- Runtime settings (heartbeat interval, max concurrent runs)
- Required environment inputs

### Managing Skills

Skills are imported from external sources:
- `paperclipai/*` skills are sourced from the Paperclip repository
- `fluxcd/*` skills are sourced from the Flux CD agent-skills repository
- `farhoodliquor/*` skills are sourced from Farhood Liquor skills repository
- `minimax-ai/*` skills are sourced from MiniMax AI skills repository

To add or update a skill, edit `company/.paperclip.yaml` and ensure the skill files exist in the appropriate `company/skills/` subdirectory.

### Renovate Configuration

The `renovate-config.json` defines dependency update behavior for all CartSnitch repositories. Updates are automated via Mend Renovate with:
- Weekly schedule (Monday before 7am)
- Auto-merge enabled for minor/patch updates
- Manual review required for major updates
- Grouped updates for related packages (ESLint, TypeScript, testing, Docker, Kubernetes)

## Related Repositories

| Repository | Purpose |
|------------|---------|
| [cartsnitch](https://github.com/cartsnitch/cartsnitch) | Main application and consumer interface |
| [api](https://github.com/cartsnitch/api) | Backend API and services |
| [common](https://github.com/cartsnitch/common) | Shared libraries and utilities |
| [infra](https://github.com/cartsnitch/infra) | Infrastructure and deployment |
| [receiptwitness](https://github.com/cartsnitch/receiptwitness) | Receipt parsing and processing |

# Contributing to CartSnitch Organization

This document outlines the process for making changes to the `.github` organization repository.

## When to Modify This Repository

Changes to this repository are appropriate for:

- Adding or updating organization profile content (`profile/README.md`)
- Adding new agents or modifying existing agent configurations
- Adding, removing, or updating shared skills
- Modifying Renovate configuration
- Updating company metadata
- Adding documentation about organization-wide practices

## When NOT to Modify This Repository

Do NOT use this repository for:

- Application code changes (use the appropriate application repository)
- Infrastructure changes (use `cartsnitch/infra`)
- Documentation for specific applications (add to the application's repo)

## Change Process

### 1. Create a Branch

Create a feature branch from `main`:

```bash
git checkout main
git pull origin main
git checkout -b feature/your-change-name
```

### 2. Make Your Changes

Make the necessary changes following the guidelines below.

#### Adding a New Agent

1. Create a new folder in `company/agents/<agent-name>/`
2. Add the agent configuration to `company/.paperclip.yaml`
3. Create at minimum:
   - `AGENTS.md` - Agent instructions and role definition
   - `MEMORY.md` - Initial memory/knowledge base (can start empty)

#### Adding a New Skill

1. Create or identify the appropriate `company/skills/<source>/` folder
2. Add the skill definition to `company/.paperclip.yaml`
3. Ensure skill files exist in the appropriate subdirectory

#### Updating Organization Profile

1. Edit `profile/README.md`
2. Ensure any new links are valid
3. Maintain consistent formatting

### 3. Commit Your Changes

Commit with a clear, descriptive message:

```bash
git add .
git commit -m "Add <agent-name> agent configuration"
```

### 4. Submit a Pull Request

Open a PR against `main`:

```bash
git push origin feature/your-change-name
gh pr create --base main --title "Add <agent-name> agent" --body "Description of changes"
```

### 5. Review and Merge

- PRs require approval from the CTO (Savannah Savings) or CEO (Coupon Carl)
- After approval, the PR can be merged
- Merges to `main` are automatically deployed to the organization

## Guidelines

### Agent Naming

- Use kebab-case for agent names (e.g., `barcode-betty`, `checkout-charlie`)
- Agent names should be descriptive of the role

### Agent Configuration

When adding or updating agents in `.paperclip.yaml`:

- Include a clear `capabilities` description
- Set appropriate `icon` for the UI
- Configure heartbeat settings appropriately for the role:
  - Engineers/QA: 14400 seconds (4 hours)
  - CEO: 28800 seconds (8 hours)
  - UAT (Deal Dottie): 300 seconds (5 minutes) for rapid response

### Paperclip Configuration Schema

The `.paperclip.yaml` uses schema version `paperclip/v1`. When editing:

- Ensure valid YAML syntax
- Follow the existing structure and conventions
- Include all required fields for agent definitions

### Skill Documentation

When adding new skills:

- Ensure the skill folder contains a `SKILL.md` file
- Include a brief description of the skill's purpose
- Reference the external source if applicable

## Contacts

For questions about this repository or the change process:

- **CTO (Savannah Savings)**: Technical decisions, agent configuration
- **CEO (Coupon Carl)**: Organizational decisions, high-level direction

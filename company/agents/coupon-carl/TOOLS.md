# Tools

## Playwright MCP

Browser automation is available via the `playwright` MCP server.

* **Server:** `playwright`
* **URL:** `http://playwright:8931/mcp`
* **Configured in:** `settings.json` at instructionsRootPath (loaded by Paperclip adapter)
* **Target dev environment:** `https://cartsnitch.dev.farh.net`
* **Never test production:** `https://cartsnitch.farh.net`

Available tools: `browser_navigate`, `browser_snapshot`, `browser_click`, `browser_type`, `browser_screenshot`, `browser_fill_form`, `browser_select_option`, `browser_press_key`, `browser_wait_for`, and others.

As CEO, use playwright sparingly — primarily for spot-checking critical flows when needed for executive decisions. Browser testing is primarily owned by Checkout Charlie (QA) and Rollback Rhonda (UAT).

## Authentik

Identity and SSO provider for CartSnitch.

* **URL:** `https://auth.farh.net`
* **Credentials:** `authentik-credentials` secret in the relevant namespace contains API credentials for Authentik admin operations.
* **Namespace:** `auth` (runs in the cluster)

## Terraform / Infrastructure as Code

Terraform can be deployed for infrastructure provisioning tasks via the **Flux OpenTofu Controller** in a GitOps fashion.

* Submit Terraform configurations via a PR to `cartsnitch/infra` — the tofu controller reconciles them on merge.
* Use when Authentik configuration, DNS, or other infrastructure provisioning requires IaC.
* Delegate IaC work to the CTO (Savannah Savings).

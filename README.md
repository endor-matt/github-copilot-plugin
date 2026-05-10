# Endor Labs Security Intelligence AgentHQ Plugin

This repository is now shaped as a single GitHub Copilot and AgentHQ plugin.
The Agentic App should point at this repository root and load the root
`plugin.json`.

The plugin bundles Endor Labs read-only security workflows for dependency
decisions, package risk summaries, upgrade impact analysis, vulnerability
explanations, and tenant findings.

## Agentic App Entry Point

Use the root plugin:

```text
github-copilot-plugin/
|-- plugin.json
|-- agents/
|   |-- main.agent.md
|   |-- dependency-decision-helper.agent.md
|   |-- package-risk-summary.agent.md
|   |-- upgrade-impact-analysis.agent.md
|   |-- vulnerability-explainer.agent.md
|   `-- tenant-findings.agent.md
`-- docs/
```

For AgentHQ:

1. Create or update the GitHub App.
2. Enable Agent features.
3. Select this public repository.
4. Pin the SHA that contains the root `plugin.json`.

`agents/main.agent.md` is the app entry point. The other agents are packaged as
specialized workflows that can be selected or used by the platform when the
request matches their domain.

## Agents

| Agent | Purpose |
|-------|---------|
| `Endor Labs Security` | Root entry point and router for Endor security workflows. |
| `Dependency Decision Helper` | Decides whether a specific package version should be used. |
| `Endor Labs Package Risk Summary` | Summarizes risk for a specific package version. |
| `Endor Labs Upgrade Impact Analysis` | Evaluates upgrade risk, fixed findings, VersionUpgrade evidence, and CIA context. |
| `Endor Labs Vulnerability Explainer` | Explains a CVE, GHSA, or Endor vulnerability with evidence and remediation context. |
| `Endor Labs Tenant Findings` | Queries existing findings inside an Endor tenant. |

## Authentication Status

The current root plugin still uses the existing Endor GitHub Actions keyless
environment variables in its stdio MCP server config. That is the previous auth
path and is not the native AgentHQ MCP OIDC flow from the May 2026 docs.

Native AgentHQ MCP OIDC is not wired in yet. The next structural/auth step is to
replace the legacy env-only MCP auth with an `oidc:` block that injects
`$GITHUB_COPILOT_OIDC_MCP_TOKEN`, backed by an Endor-compatible token exchange
endpoint.

The legacy GitHub Actions OIDC notes are retained in
[`docs/ENDOR_GITHUB_KEYLESS_AUTH.md`](docs/ENDOR_GITHUB_KEYLESS_AUTH.md) for
comparison while the native MCP OIDC path is implemented.

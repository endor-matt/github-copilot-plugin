# Endor Labs Security Intelligence AgentHQ Plugin

This repository is now shaped as a single GitHub Copilot and AgentHQ plugin.
The Agentic App should point at this repository root and load the root
`plugin.json`.

The plugin bundles Endor Labs read-only security workflows for dependency
decisions, package risk summaries, upgrade impact analysis, and vulnerability
explanations.

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
|   `-- vulnerability-explainer.agent.md
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

## Authentication Status

The active agents currently match the upstream Developer Edition MCP startup
pattern from `endorlabs/endor-labs-agent-kit`: each specialized agent starts
`npx -y endorctl ai-tools mcp-server` with no GitHub Actions keyless env vars,
no `execute` tool, and no AgentHQ `oidc:` block.

Native AgentHQ MCP OIDC is not wired in yet. The next auth step is to add an
`oidc:` block that injects `$GITHUB_COPILOT_OIDC_MCP_TOKEN`, backed by an
Endor-compatible token exchange endpoint.

The legacy GitHub Actions OIDC notes are retained in
[`docs/ENDOR_GITHUB_KEYLESS_AUTH.md`](docs/ENDOR_GITHUB_KEYLESS_AUTH.md) for
comparison while the native MCP OIDC path is implemented.

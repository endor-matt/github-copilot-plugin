# AURI by Endor Labs AgentHQ Plugin

AURI by Endor Labs security and dependency intelligence for GitHub Copilot and
AgentHQ.

This repository is a single AgentHQ plugin package. It contains one root
`plugin.json`, one entrypoint agent in `agents/`, and workflow skills in
`skills/`.

The plugin is read-only in v1. It answers Endor Labs security questions with
Endor MCP tools and AgentHQ-native MCP OIDC. It does not use GitHub Actions OIDC
or long-lived Endor credentials in GitHub.

## Agent

- `AURI by Endor Labs`: the AgentHQ entrypoint agent at
  `agents/main.agent.md`.

## Skills

- `tenant-findings`: summarize tenant findings and reachable findings.
- `vulnerability-explainer`: explain a CVE, GHSA, or Endor vulnerability.
- `package-risk-summary`: summarize a package version's risk profile.
- `dependency-decision-helper`: decide whether to add, keep, or upgrade a
  package version.
- `upgrade-impact-analysis`: analyze upgrade risk and impact when Endor evidence
  is available.

## Authentication

The plugin exposes one local MCP server named `endor-api-tools`. That server is
a small Node service which calls the Endor REST API directly for read-only
package, vulnerability, project, and finding tools. It does not use Endor CLI,
`endorctl`, `npx`, shell commands, or GitHub Actions OIDC.

AgentHQ exchanges a short-lived OIDC JWT with an Endor OIDC broker and injects
the returned Endor identity token as `GITHUB_COPILOT_OIDC_MCP_TOKEN`. The plugin
maps that value to `ENDOR_TOKEN`. The MCP server uses that token and does not
store Endor credentials in GitHub.

Required AgentHQ variables:

- `COPILOT_MCP_ENDOR_NAMESPACE`

Optional:

- `COPILOT_MCP_ENDOR_API`, defaulting to `https://api.endorlabs.com`

The broker URL is currently embedded literally in `agents/main.agent.md` because
AgentHQ validates OIDC endpoint URLs before environment-variable interpolation.
Update `oidc.endpoints.exchange`, `oidc.endpoints.revoke`, and
`oidc.endpoints.failure` whenever the broker URL changes.

See `AGENTHQ_OIDC_AUTH.md`.

## Install Locally

From this repository root:

```bash
copilot plugin install .
```

## AgentHQ Setup

Point the Agentic App at this repository root.

Deploy and configure the broker service from the sibling
`endor-agenthq-oidc-broker` repository, then set the AgentHQ variables above.

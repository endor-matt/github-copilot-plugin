# Endor Labs Agent Kit — GitHub AgentHQ Plugins (Developer Edition)

GitHub Copilot / AgentHQ plugin bundle for the Developer Edition of the
[Endor Labs Agent Kit](https://github.com/endorlabs/endor-labs-agent-kit).
Each subdirectory is a self-contained AgentHQ plugin built from one Agent Kit
recipe and targets `github-copilot`.

These plugins are **MCP-only**: they call the Endor MCP server (`endorctl
ai-tools mcp-server`) and do not grant shell execution. Every agent is
read-only — none of them edit files, open pull requests, dismiss findings,
create policies, run scans, or otherwise mutate Endor Labs state.

## Agents

| Plugin | Recipe | Use it when… |
|---|---|---|
| [`dependency-decision-helper/developer-edition`](dependency-decision-helper/developer-edition) | `dependency-decision-helper` v1.0.0 | The user asks whether to add, upgrade to, or keep a specific package version (e.g. *"Is lodash 4.17.20 safe?"*). Returns a verdict (`SAFE` / `SAFE_WITH_CONDITIONS` / `NOT_RECOMMENDED` / `BLOCKED`) with evidence, conditions, alternatives, and any data gaps. |
| [`package-risk-summary/developer-edition`](package-risk-summary/developer-edition) | `package-risk-summary` v1.0.0 | The user wants a concise risk profile for a specific package version without a yes/no decision (e.g. *"Summarize npm lodash 4.17.20 risk"*). Returns a risk posture (`LOW` / `MODERATE` / `HIGH` / `CRITICAL` / `UNKNOWN`) with vulnerabilities, malware/typosquat signals, scores, license notes, and recommended next checks. |
| [`upgrade-impact-analysis/developer-edition`](upgrade-impact-analysis/developer-edition) | `upgrade-impact-analysis` v1.0.0 | The user asks about safe upgrade paths, upgrade risk, or findings fixed/introduced when moving between two specific versions. Returns an upgrade recommendation (`UPGRADE_NOW` / `UPGRADE_WITH_CAUTION` / `DEFER` / `INSUFFICIENT_DATA`) and a risk delta. Developer Edition is a lighter MCP-only explicit package-version comparator. |
| [`vulnerability-explainer/developer-edition`](vulnerability-explainer/developer-edition) | `vulnerability-explainer` v1.0.0 | The user asks what a specific vulnerability means and how to reason about it (e.g. *"Explain CVE-2021-44228"*). Returns an action (`CRITICAL_ACTION_REQUIRED` / `ACTION_RECOMMENDED` / `MONITOR` / `INSUFFICIENT_DATA`) with severity, exploitability, and remediation guidance. |

Each agent is `user-invocable` and has `disable-model-invocation: true`, so it
will only run when a user explicitly selects it in AgentHQ.

## Install

Each plugin installs independently. From the plugin directory:

```bash
copilot plugin install .
```

For example:

```bash
cd dependency-decision-helper/developer-edition
copilot plugin install .
```

The installed plugin names are:

- `endor-labs-dependency-decision-helper-developer`
- `endor-labs-package-risk-summary-developer`
- `endor-labs-upgrade-impact-analysis-developer`
- `endor-labs-vulnerability-explainer-developer`

Uninstall with `copilot plugin uninstall <name>`.

## Plugin layout

Each plugin follows the same shape:

```
<recipe>/developer-edition/
├── plugin.json                       # AgentHQ plugin manifest
├── README.md                         # Per-plugin install + scope notes
└── agents/
    └── <recipe>.agent.md             # Agent definition with embedded MCP config
```

The agent file embeds the Endor MCP server configuration and enables only the
MCP tools that recipe needs. Across the four agents, the enabled tools are a
subset of:

- `check_dependency_for_risks`
- `check_dependency_for_vulnerabilities`
- `get_endor_vulnerability`

## Design notes

- **Developer Edition only.** These are the lighter, MCP-only variants of the
  Agent Kit recipes. They do not run shell commands or call `endorctl` directly.
- **Evidence-first.** Each agent maintains a `data_gaps` list and never
  fabricates missing scores, license data, vulnerability enrichment, or
  exploitability signals. When data is missing, the agent says so and points
  the user at <https://app.endorlabs.com> for the full assessment.
- **Structured output.** Every agent returns concise prose alongside a JSON
  block with a recipe-specific schema (verdict, risk posture, upgrade
  recommendation, or action) so its output is easy to consume programmatically.
- **AgentHQ wrapping.** The plugins are the agent surface; AgentHQ app
  wrapping and any OIDC token exchange endpoints are configured outside these
  packages.

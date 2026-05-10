# Endor Labs GitHub Copilot Plugin Kit

A collection of GitHub Copilot and AgentHQ plugins that bring Endor Labs
security intelligence — reachable vulnerabilities, dependency risk, upgrade
impact, and tenant findings — directly into Copilot agent sessions.

Each plugin packages a custom Copilot agent with an embedded Endor MCP server
configuration, so the agent can call Endor Labs read-only lookups without
leaving the editor or pull request.

## Plugins

Every plugin ships in two editions:

- **Developer Edition** — runs locally with an existing `endorctl` API key
  configuration. No tenant onboarding required.
- **Enterprise Edition** — runs in GitHub Copilot cloud agent or AgentHQ and
  authenticates to Endor Labs via GitHub Actions OIDC (keyless). Requires the
  setup in [`ENDOR_GITHUB_KEYLESS_AUTH.md`](ENDOR_GITHUB_KEYLESS_AUTH.md).

| Plugin | Purpose |
|--------|---------|
| [`dependency-decision-helper`](dependency-decision-helper) | Decide whether a specific package version is safe to add or upgrade. Returns a verdict with evidence, conditions, and alternatives. |
| [`package-risk-summary`](package-risk-summary) | Produce a concise risk summary for a package, including known CVEs, malware signals, and maintenance health. |
| [`upgrade-impact-analysis`](upgrade-impact-analysis) | Analyze the impact of upgrading a dependency — fixes, breaking changes, and the safest target version. |
| [`vulnerability-explainer`](vulnerability-explainer) | Explain a CVE or Endor finding with severity, exploitability, affected versions, and remediation. |
| [`tenant-findings`](tenant-findings) | Query findings already imported into an Endor Labs tenant, including reachable findings for a project. *(Enterprise edition only.)* |

## Install

From any plugin edition directory:

```bash
copilot plugin install .
```

Uninstall with `copilot plugin uninstall <plugin-name>` (each edition's
`README.md` lists its plugin name).

## Authentication

- **Developer editions** read your local `~/.endorctl/config.yaml`.
- **Enterprise editions** rely on Endor Labs GitHub Actions OIDC. Set up the
  auth policy, environment variables, and `copilot-setup-steps.yml` workflow
  described in [`ENDOR_GITHUB_KEYLESS_AUTH.md`](ENDOR_GITHUB_KEYLESS_AUTH.md)
  before running an enterprise plugin in a cloud agent.

## Repository Layout

```
github-copilot-plugin/
├── ENDOR_GITHUB_KEYLESS_AUTH.md     # OIDC setup for cloud agents
├── dependency-decision-helper/
│   ├── developer-edition/
│   └── enterprise-edition/
├── package-risk-summary/
│   ├── developer-edition/
│   └── enterprise-edition/
├── tenant-findings/
│   └── enterprise-edition/
├── upgrade-impact-analysis/
│   ├── developer-edition/
│   └── enterprise-edition/
└── vulnerability-explainer/
    ├── developer-edition/
    └── enterprise-edition/
```

Each edition contains a `plugin.json` manifest, an `agents/` directory with the
custom Copilot agent, and its own `README.md` with usage examples.

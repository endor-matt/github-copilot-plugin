# Endor Labs Developer AgentHQ Plugin

This repository contains a GitHub AgentHQ/Copilot plugin that packages a single Endor Labs Developer Edition (free) agent.

The agent starts the Endor Labs MCP server with:

```sh
npx -y endorctl ai-tools mcp-server
```

Authentication is intentionally not configured in this first version. The plugin is scoped to Endor Labs Developer Edition (free) tools and excludes Enterprise-only `security_review`.

## Contents

```text
.
├── plugin.json
├── agents/
│   └── main.agent.md
└── README.md
```

- `plugin.json` declares the installable plugin package.
- `agents/main.agent.md` is the main AgentHQ agent and includes the local stdio MCP server configuration.

## Agent Capabilities

The `endor-cli-tools` MCP server enables:

- `check_dependency_for_vulnerabilities`
- `check_dependency_for_risks`
- `get_endor_vulnerability`
- `get_resource`
- `scan`

The Enterprise-only `security_review` tool is deliberately not enabled.

## Endor Labs Authentication

The plugin uses the Endor Labs Developer Edition MCP server through `npx`, but the GitHub runner is still headless. Configure Endor Labs API credentials as Copilot Agents secrets/variables so `endorctl` does not try an interactive browser login callback.

Create these in the repository or organization Copilot Agents settings:

```text
Settings -> Secrets and variables -> Agents
```

Secrets:

```text
COPILOT_MCP_ENDOR_API_CREDENTIALS_KEY
COPILOT_MCP_ENDOR_API_CREDENTIALS_SECRET
```

Variable:

```text
COPILOT_MCP_ENDOR_NAMESPACE
```

The agent maps those values into the MCP server as:

```text
ENDOR_API_CREDENTIALS_KEY
ENDOR_API_CREDENTIALS_SECRET
ENDOR_NAMESPACE
```

Do not use normal GitHub Actions secrets for this. Copilot cloud agent reads Agents secrets/variables, and values used by MCP servers must use the `COPILOT_MCP_` prefix.

## Copilot Cloud Agent MCP Configuration

The agent frontmatter already includes the MCP server configuration. For AgentHQ plugin testing, leave repository-level Copilot Cloud agent MCP configuration empty unless you are intentionally testing the MCP server outside this plugin.

Repository-level MCP configuration is processed after custom agent MCP configuration and can override or add servers. A stale repository-level MCP entry with the wrong command can make Copilot time out even when this plugin is correct.

If you are testing the MCP server outside the AgentHQ plugin, paste this JSON into the **MCP configuration** field under repository settings:

```text
Settings -> Copilot -> Cloud agent -> MCP configuration
```

Do not add this JSON as an Agents variable or secret. GitHub does not allow variables or secrets with the `GITHUB_` prefix, and `GITHUB_COPILOT_MCP_JSON` is an internal implementation detail, not a variable you should create manually.

Use the same server name as the agent frontmatter:

```json
{"mcpServers":{"endor-cli-tools":{"type":"local","command":"npx","args":["-y","endorctl","ai-tools","mcp-server"],"env":{"ENDOR_API_CREDENTIALS_KEY":"${{ secrets.COPILOT_MCP_ENDOR_API_CREDENTIALS_KEY }}","ENDOR_API_CREDENTIALS_SECRET":"${{ secrets.COPILOT_MCP_ENDOR_API_CREDENTIALS_SECRET }}","ENDOR_NAMESPACE":"${{ vars.COPILOT_MCP_ENDOR_NAMESPACE }}"},"tools":["check_dependency_for_vulnerabilities","check_dependency_for_risks","get_endor_vulnerability","get_resource","scan"]}}}
```

The `endor-cli-tools` name matters because the agent enables MCP tools with `endor-cli-tools/*`.

## Local Testing

Install from this repository root:

```sh
copilot plugin install .
```

Verify it loaded:

```sh
copilot plugin list
```

Start a Copilot CLI session and check that the agent is available:

```text
/agent
```

After editing plugin files, reinstall because Copilot caches installed plugin components:

```sh
copilot plugin install .
```

Uninstall the local plugin when finished:

```sh
copilot plugin uninstall endor-labs-developer
```

## AgentHQ Setup

To wrap this plugin as a GitHub Agentic App:

1. Make this repository public.
2. Create or update a GitHub App and enable Agent features.
3. Point the Agentic App at this repository.
4. Set the SHA to the commit you want GitHub to load.
5. Save the app configuration and trigger it from GitHub by assigning, mentioning, or selecting the app agent.

This repository should contain one plugin for the app identity. Future authentication work can add OIDC or other Endor Labs Enterprise configuration when needed.

## Requirements

- GitHub Copilot CLI or AgentHQ environment with plugin support.
- Node.js with `npx` available. Endor Labs recommends Node.js 24 LTS or later for the cleanest MCP server output.
- Network access to download and run `endorctl` through `npx`.

## References

- [Endor Labs MCP server in OpenAI Codex](https://docs.endorlabs.com/setup-deployment/mcp/codex)
- [GitHub Copilot CLI plugin reference](https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-plugin-reference)

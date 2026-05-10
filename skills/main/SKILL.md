---
name: main
description: Main Endor Labs Developer Edition workflow for dependency risk, vulnerability lookup, and repository scans.
---

Use this skill when the user asks the Endor Labs Developer agent to check dependency risk, look up vulnerabilities, or scan a repository.

Workflow:

1. Use the `endor-cli-tools` MCP server for Endor Labs checks.
2. For a specific dependency version, prefer `check_dependency_for_vulnerabilities` or `check_dependency_for_risks`.
3. For a specific vulnerability, use `get_endor_vulnerability`.
4. For repository analysis, use `scan`.
5. Summarize findings with the affected package, severity, evidence, likely impact, and the smallest practical remediation.

Do not claim Enterprise Edition-only capabilities. Do not use `security_review`.

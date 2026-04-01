<p align="center">
  <h1 align="center">SquireX ⚡</h1>
  <p align="center"><strong>Agentforce Capability Scanner — Static Security Analysis for Salesforce AI Agents</strong></p>
</p>

<p align="center">
  <a href="https://github.com/SquireX-dev/squirex---Agentforce-Capability-Scanner/releases"><img src="https://img.shields.io/github/v/release/SquireX-dev/squirex---Agentforce-Capability-Scanner?label=Release&color=blue" alt="Release"></a>
  <img src="https://img.shields.io/badge/Rules-26-brightgreen" alt="26 Rules">
  <img src="https://img.shields.io/badge/SARIF-v2.1.0-blue" alt="SARIF v2.1.0">
  <img src="https://img.shields.io/badge/Platforms-macOS%20%7C%20Linux%20%7C%20Windows-lightgrey" alt="Platforms">
  <a href="LICENSE.md"><img src="https://img.shields.io/badge/License-Proprietary-red" alt="License"></a>
</p>

---

## Why SquireX?

Salesforce Agentforce lets you build autonomous AI agents — but the metadata that controls them is a **security blindspot**. Agent instructions, plugin descriptions, and prompt templates are read by LLMs but are often **invisible to human reviewers** in the Agent Builder UI. This creates an attack surface for:

- **Prompt injection** via hidden metadata fields
- **Privilege escalation** through `without sharing` Apex or `SystemModeWithoutSharing` Flows
- **Data exfiltration** via adversarial instructions embedded in topic definitions
- **Supply chain risk** from managed package code the agent invokes but you can't audit

**SquireX scans your entire Agentforce metadata layer** — `.agent` files, `GenAiFunction`, `GenAiPlugin`, `PromptTemplate`, `Flow`, Apex classes, and JSON schemas — builds a cross-metadata **Semantic Graph**, and evaluates **26 deterministic security rules** to catch these vulnerabilities before they reach production.

Results are output as **SARIF v2.1.0**, integrating directly into GitHub Advanced Security, GitLab Security, and any SARIF-compatible dashboard.

---

## 26 Security Rules Across 10 Categories

| Category | Rules | What It Catches |
|---|---|---|
| **Action Configuration** | AGENTFORCE-1.1, 1.2, 1.3 | Missing confirmation requirements, schema desync, privilege escalation (Apex `without sharing` + Flow `SystemModeWithoutSharing`) |
| **Agent Script Safety** | AGENTFORCE-2.1, 2.2, 2.3 | Unguarded DML actions, transition dead-ends & cycles (DoS), prompt injection defense gaps |
| **Grounding Security** | AGENTFORCE-3.1, 3.2 | Hardcoded API keys/tokens in templates, FLS masking gaps |
| **Structural Dependency** | AGENTFORCE-4.1, 4.2, 4.3 | Planner bundle completeness, deactivation collisions, evaluation governance |
| **Extended Graph Security** | AGENTFORCE-5.1..5.6 | Flow context misuse, silent state modification, DML injection, API injection, template poisoning, experimental activation |
| **Supply Chain Security** | AGENTFORCE-6.1, 6.2, 6.3 | API version downgrade, schema desync, managed package origin |
| **Agentic Architecture** | AGENTFORCE-7.1, 7.2 | Topic bloat, skill semantic misalignment |
| **Relational Traversal** | AGENTFORCE-8.1 | Context traversal data exfiltration |
| **Instruction Integrity** | AGENTFORCE-9.1, 9.2 | Metadata instruction poisoning, cross-topic boundary violations |
| **Operational Reliability** | AGENTFORCE-10.1 | Validation rule conflicts invisible to the LLM planner |

> **Full rule reference with remediation guidance:** [docs/security-rules.md](docs/security-rules.md)

---

## How It Works

```
Your Salesforce Project
  │
  ├── force-app/
  │   ├── agents/*.agent
  │   ├── genAiFunctions/*.genAiFunction-meta.xml
  │   ├── genAiPlugins/*.genAiPlugin-meta.xml
  │   ├── promptTemplates/*.genAiPromptTemplate-meta.xml
  │   ├── flows/*.flow-meta.xml
  │   └── classes/*.cls
  │
  ▼
┌──────────────────────────────────────────────────┐
│  SquireX Engine                                  │
│                                                  │
│  1. Parse    → 10 metadata parsers               │
│  2. Link     → Semantic Graph (15 node types,    │
│                 12 edge types)                    │
│  3. Evaluate → 26 security rules                 │
│  4. Output   → SARIF v2.1.0                      │
└──────────────────────────────────────────────────┘
  │
  ▼
GitHub Security Dashboard / GitLab Security / CI Gate
```

---

## Testing Center Bridge — Agentforce DX Test Generation

SquireX bridges **static analysis** with Salesforce's native **dynamic testing**. The `generate-tests` command converts SARIF scan violations into **Agentforce DX test specifications** compatible with `sf agent test run`.

```bash
# Scan + generate DX tests in one step
squirex generate-tests -d ./force-app

# Convert an existing SARIF file to DX tests
squirex generate-tests --sarif results.sarif -o agentforce-tests.yaml

# Generate tests for specific rules only
squirex generate-tests --rules AGENTFORCE-1.1,AGENTFORCE-9.1

# Generate, validate, and push to a sandbox org
squirex generate-tests -d ./force-app --validate --push --target-org my-sandbox

# JSON output (for programmatic consumption)
squirex generate-tests --sarif results.sarif --json
```

**Pipeline:** `squirex scan → squirex generate-tests → sf agent test run → Testing Center`

**Salesforce CLI Prerequisites** (required for `--validate` and `--push`):

```bash
# 1. Install the Salesforce CLI
npm install -g @salesforce/cli

# 2. Install the Agentforce DX plugin
sf plugins install @salesforce/plugin-agent

# 3. Authenticate to your org
sf org login web --alias my-sandbox --instance-url https://test.salesforce.com

# 4. Verify authentication
sf org display --target-org my-sandbox
```

All **26 rules** have dedicated test generators with normalized `AGENTFORCE-X.Y` identifiers.

### MCP Server Integration

AI coding agents can invoke the Testing Center bridge via the [SquireX MCP Server](https://www.npmjs.com/package/@squirex.dev/mcp-server):

```json
{
  "mcpServers": {
    "squirex": {
      "command": "npx",
      "args": ["-y", "@squirex.dev/mcp-server"],
      "env": { "SQUIREX_PROJECT_DIR": "/path/to/your/salesforce/project" }
    }
  }
}
```

---

## Quick Start

### 1. Install

```bash
# macOS (Apple Silicon)
curl -L -o squirex https://github.com/SquireX-dev/squirex---Agentforce-Capability-Scanner/releases/latest/download/squirex-macos-arm64
chmod +x squirex && sudo mv squirex /usr/local/bin/
squirex --version
```

> See [docs/installation.md](docs/installation.md) for all platforms.

### 2. Scan

```bash
# Full workspace Agentforce scan
squirex scan -d ./force-app

# Output SARIF for CI/CD integration
squirex scan -d ./force-app --sarif results.sarif

# Scan only changed files in a PR
squirex scan-pr -b main

# Deep pipeline diagnostics
squirex diagnose -d ./force-app
```

### 3. Integrate with CI/CD

```yaml
# GitHub Actions — add to your workflow
- name: Run SquireX Agentforce Scan
  env:
    SQUIREX_LICENSE_KEY: ${{ secrets.SQUIREX_LICENSE_KEY }}
  run: squirex scan -d ./force-app --sarif results.sarif

- name: Upload SARIF to GitHub Security
  uses: github/codeql-action/upload-sarif@v3
  with:
    sarif_file: results.sarif
```

> See [docs/ci-cd.md](docs/ci-cd.md) for GitHub Actions, GitLab CI, Bitbucket Pipelines, and Azure DevOps examples.

---

## Licensing

| Tier | Capabilities | |
|------|---|---|
| **Community** | Unlimited local execution of `squirex scan` and `squirex run`. Free forever. | [Download](https://github.com/SquireX-dev/squirex---Agentforce-Capability-Scanner/releases) |
| **Enterprise** | CI/CD execution, SARIF output, PR gating, GitHub/GitLab Security Dashboard integration. | [Contact Sales](https://squirex.dev) |

For licensing inquiries: **hello@squirex.dev**

---

## Documentation

| Guide | Description |
|---|---|
| [Getting Started](docs/getting-started.md) | First scan walkthrough and reading results |
| [Installation](docs/installation.md) | Platform-specific installation instructions |
| [Security Rules Reference](docs/security-rules.md) | All 26 rules with remediation guidance |
| [CI/CD Integration](docs/ci-cd.md) | GitHub Actions, GitLab CI, Bitbucket, Azure DevOps |
| [Command Reference](docs/commands.md) | All CLI commands and flags |
| [Enterprise Guide](docs/enterprise.md) | License activation, support, and SLA |
| [Runtime Features](docs/features.md) | Apex runtime capabilities and limits |

---

## Configuring Adversarial Patterns

Rule AGENTFORCE-9.1 (Metadata Instruction Poisoning) uses a **configurable JSON pattern library**. To extend detection with your own patterns:

1. Copy the default `adversarial_patterns.json` from the release
2. Add patterns to the relevant category (`jailbreak`, `roleOverride`, `dataExfiltration`, `systemManipulation`, `encodingEvasion`)
3. Place in your project root or provide via `--patterns` flag

---

## Security

If you discover a security vulnerability, please report it responsibly. See [SECURITY.md](SECURITY.md) for our disclosure policy.

---

## License

Proprietary Software — See [LICENSE.md](LICENSE.md) for full terms.

Copyright © 2026 SquireX. All Rights Reserved.

For licensing inquiries: **hello@squirex.dev** · [squirex.dev](https://squirex.dev)

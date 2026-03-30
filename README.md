# SquireX ⚡

[![License](https://img.shields.io/github/license/samudralap/squirex-apex-forge)](LICENSE.md)
[![Release](https://img.shields.io/github/v/release/samudralap/squirex-apex-forge)](https://github.com/samudralap/squirex-apex-forge/releases)

**Agentforce Capability Scan · Local Apex Runtime · CI/CD Security Scanning**

SquireX is a high-fidelity static analysis engine and mock runtime for Salesforce. It parses your Agentforce definitions and Apex code, executes them against an in-memory runtime, and detects critical configuration vulnerabilities before they reach production.

## Agentforce Capability Scan

The primary engine of SquireX performs deterministic capability scanning on your Agentforce metadata (`.agent`, `.genAiFunction-meta.xml`, `.genAiPlugin-meta.xml`, `schema.json`, `.flow-meta.xml`, `.genAiPromptTemplate-meta.xml`).

### 26 Security Rules Across 10 Categories

| Category | Rules | What It Catches |
|---|---|---|
| **Action Configuration** | 1.1, 1.2, 1.3 | Missing confirmation requirements, schema desync, privilege escalation (Apex `without sharing` AND Flow `SystemModeWithoutSharing`) |
| **Agent Script Safety** | 2.1, 2.2, 2.3 | Unguarded DML actions, transition dead-ends & cycles (DoS), prompt injection defense gaps |
| **Grounding Security** | 3.1, 3.2 | Hardcoded API keys/tokens in templates, FLS masking gaps |
| **Structural Dependency** | 4.1, 4.2, 4.3 | Planner bundle completeness, deactivation collisions, evaluation governance |
| **Extended Graph Security** | FLOW-01..03, API-01, PT-01..02 | Flow context misuse, Flow injection, API injection, prompt template poisoning/activation |
| **Supply Chain Security** | SC-01, SC-02, **SC-03** | API version downgrade, schema desync, **managed package origin** |
| **Agentic Architecture** | 7.1, 7.2, 8.1 | Topic bloat, skill semantic misalignment, context traversal gaps |
| **Instruction Integrity** | **9.1, 9.2** | **Metadata instruction poisoning**, **cross-topic boundary violations** (planner manipulation, shared context contamination) |
| **Operational Reliability** | **10.1** | **Validation rule conflicts** — detects DML on objects with validation rules invisible to the LLM planner |

### Key Capabilities

*   **Metadata Instruction Poisoning Detection**: Scans `GenAiPlugin` instructions, `GenAiFunction` descriptions, `.agent` system instruction overrides, and `PromptTemplate` content for adversarial patterns (jailbreak phrases, data exfiltration commands, role override attempts). Uses a configurable JSON pattern library.
*   **Cross-Topic Instruction Boundary**: Detects when a topic's instructions reference other topics by name (shared context contamination) or attempt to override the planner's topic-selection logic (privilege escalation within the agentic context).
*   **Template Context Poisoning**: Finds unescaped AI outputs embedded directly inside Prompt Template `<content>` XML blocks.
*   **System Context Escalation**: Warns if an autonomous Agent triggers a target Flow running in `SystemModeWithoutSharing` or an Apex class running `without sharing`.
*   **Excessive Agency Prevention**: Enforces manual confirmation requirements for state-modifying actions and Flows.
*   **Transition Cycle Detection**: Detects infinite conversation loops in topic transitions (A→B→C→A) that constitute denial-of-service conditions.
*   **Variable Injection in DML**: Detects dynamically bound AI `{!Variables}` within target Flow Filters and HTTP Callouts to prevent SSRF and SOQL Injection.
*   **Managed Package Supply Chain**: Flags agent actions that invoke code from third-party managed packages — opaque to the org admin and a supply chain risk.
*   **Validation Rule Conflict**: Detects when agent actions perform DML on objects with validation rules invisible to the LLM planner.
*   **Schema Drift**: Detects mismatches between Open API schemas and standard metadata definitions.
*   **Deep Pipeline Diagnostics**: `squirex diagnose` command provides structured JSON reports with AST health, graph topology, linker traces, per-rule timing, instruction audit manifest, and violation breakdowns.

## Licensing Tiers

SquireX operates on a dual-tier model:

| Tier | Price | Capabilities |
|------|-------|--------------| 
| **Community** | **Free** | Unlimited local execution of `squirex scan` and `squirex run` on your private machine. |
| **Enterprise** | **$1000/mo** | Unlocks CI/CD execution. Capable of generating SARIF reports and blocking PRs in GitHub Actions, GitLab CI, and Bitbucket. |

*To purchase an Enterprise license key, visit [squirex.dev](https://squirex.dev).*

## Installation

### Quick Install (macOS / Linux)
```bash
curl -L -o squirex https://github.com/samudralap/squirex-apex-forge/releases/latest/download/squirex-macos-arm64
chmod +x squirex
sudo mv squirex /usr/local/bin/
squirex --version
```
> Note: Replace `squirex-macos-arm64` with the appropriate binary for your system (`squirex-linux-x64`, `squirex-win-x64.exe`, etc.)

## Quick Start

### 1. Capability Scanning (Primary)
```bash
# Run full workspace Agentforce scan
squirex scan -d ./force-app

# Run specific rules and output SARIF for CI/CD
squirex scan -d ./force-app --rules AGENTFORCE-1.1,AGENTFORCE-9.1 --sarif results.sarif

# Scan only what changed in your Pull Request
squirex scan-pr -b main

# Deep pipeline diagnostics (AST health, graph topology, per-rule timing)
squirex diagnose -d ./force-app
```

### 2. Local Apex Runtime (Secondary)
```bash
# Run all test classes locally without deploying
squirex run

# Predict merge conflicts before they happen
squirex conflict -b main,feature/my-feature
```

## Documentation
- [Installation Guide](docs/installation.md)
- [Command Reference](docs/commands.md)
- [CI/CD Integration](docs/ci-cd.md)
- [Runtime Features & Limits](docs/features.md)

## Enterprise CI/CD Setup

To run SquireX inside a CI/CD pipeline, you must provide your Enterprise License Key via the `SQUIREX_LICENSE_KEY` environment variable.

```yaml
- name: Run Agentforce Capability Scan
  env:
    SQUIREX_LICENSE_KEY: ${{ secrets.SQUIREX_LICENSE_KEY }}
  run: squirex scan -d ./force-app --sarif results.sarif

- name: Upload SARIF results to GitHub Security
  uses: github/codeql-action/upload-sarif@v3
  with:
    sarif_file: results.sarif
```
If executed in a CI environment (e.g. `GITHUB_ACTIONS=true`) without a valid key, the engine will safely abort.

## Configuring Adversarial Patterns

The instruction poisoning detection (Rule AGENTFORCE-9.1) uses a configurable JSON pattern library. To add custom patterns:

1. Copy the default `adversarial_patterns.json` from the release
2. Add your patterns to the relevant category (`jailbreak`, `roleOverride`, `dataExfiltration`, etc.)
3. Place in your project root or provide via `--patterns` flag

## License
Proprietary Software — See [LICENSE.md](LICENSE.md) for terms.
Copyright © 2026 SquireX. All Rights Reserved.

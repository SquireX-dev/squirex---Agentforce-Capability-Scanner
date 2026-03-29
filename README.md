# SquireX ⚡

[![License](https://img.shields.io/github/license/samudralap/squirex-apex-forge)](LICENSE.md)
[![Release](https://img.shields.io/github/v/release/samudralap/squirex-apex-forge)](https://github.com/samudralap/squirex-apex-forge/releases)

**Agentforce Capability Scan · Local Apex Runtime · CI/CD Security Scanning**

SquireX is a high-fidelity static analysis engine and mock runtime for Salesforce. It parses your Agentforce definitions and Apex code, executes them against an in-memory runtime, and detects critical configuration vulnerabilities before they reach production.

## Agentforce Capability Scan

The primary engine of SquireX performs deterministic capability scanning on your Agentforce metadata (`.agent`, `.genAiFunction-meta.xml`, `schema.json`).

*   **Template Context Poisoning**: Finds unescaped AI outputs embedded directly inside Prompt Template `<content>` XML blocks.
*   **System Context Escalation**: Warns if an autonomous Agent triggers a target Flow running in `SystemModeWithoutSharing`.
*   **Excessive Agency Prevention**: Enforces manual confirmation requirements for state-modifying actions and Flows.
*   **Variable Injection in DML**: Detects dynamically bound AI `{!Variables}` within target Flow Filters and HTTP Callouts to prevent SSRF and SOQL Injection.
*   **Schema Drift**: Detects mismatches between Open API schemas and standard metadata definitions.

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
squirex scan -d ./force-app --rules AGENTFORCE-1.1,AGENTFORCE-1.3 --sarif results.sarif

# Scan only what changed in your Pull Request
squirex scan-pr -b main
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
```
If executed in a CI environment (e.g. `GITHUB_ACTIONS=true`) without a valid key, the engine will safely abort.

## License
Proprietary Software — See [LICENSE.md](LICENSE.md) for terms.
Copyright © 2026 SquireX. All Rights Reserved.

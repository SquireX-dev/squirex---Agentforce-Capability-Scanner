# Getting Started with SquireX

This guide walks you through your first Agentforce capability scan, from installation to reading results.

## Prerequisites

- A Salesforce project with Agentforce metadata (`.agent` files, `GenAiFunction`, `GenAiPlugin`, etc.)
- macOS, Linux, or Windows
- (Optional) An Enterprise License for CI/CD integration

## Step 1: Install SquireX

```bash
# macOS (Apple Silicon)
curl -L -o squirex https://github.com/SquireX-dev/squirex---Agentforce-Capability-Scanner/releases/latest/download/squirex-macos-arm64
chmod +x squirex && sudo mv squirex /usr/local/bin/

# Verify
squirex --version
```

> See [installation.md](installation.md) for all platforms.

## Step 2: Run Your First Scan

Navigate to your Salesforce project root and run:

```bash
squirex scan -d ./force-app
```

SquireX will:
1. **Discover** all supported metadata files (`.agent`, `.genAiFunction-meta.xml`, `.cls`, etc.)
2. **Parse** each file into an AST using type-specific parsers
3. **Build** a cross-metadata Semantic Graph linking agents → topics → actions → Apex → schemas
4. **Evaluate** 26 security rules against the graph
5. **Output** SARIF v2.1.0 results

## Step 3: Reading the Results

The scan produces a summary to stderr and SARIF to stdout:

```
🔍 SquireX Agentforce Capability Scan Results
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Files scanned:    232
  Rules evaluated:  26
  Duration:         14ms
  Violations:       31
    🚨 Critical:    1
    🔴 High:        13
    🟡 Medium:      17
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Save Results to a File

```bash
squirex scan -d ./force-app --sarif results.sarif
```

### View Specific Violations

The SARIF output contains structured violation data:

```json
{
  "ruleId": "AGENTFORCE-1.1",
  "level": "error",
  "message": {
    "text": "GenAiFunction 'Submit_Case' invokes apex target without user confirmation..."
  },
  "locations": [{
    "physicalLocation": {
      "artifactLocation": {
        "uri": "force-app/main/default/genAiFunctions/Submit_Case.genAiFunction-meta.xml"
      }
    }
  }]
}
```

## Step 4: Run Diagnostics (Optional)

For deeper insight into how the engine processes your metadata:

```bash
squirex diagnose -d ./force-app
```

This outputs a structured JSON report with:
- **Pipeline**: File discovery and parse error counts
- **Graph**: Semantic graph topology (node/edge counts, orphans, dangling edges)
- **Linker**: Edge resolution and dropped edge details
- **Rules**: Per-rule timing and violation counts
- **Instructions**: Audit of all LLM-visible instruction text across your metadata

```bash
# Inspect specific sections
squirex diagnose -d ./force-app | jq .graph.orphanNodes
squirex diagnose -d ./force-app | jq .instructions.totalEntries
```

## Step 5: Scan PR Changes Only

In CI/CD, scan only what changed in the pull request:

```bash
squirex scan-pr -b main --sarif results.sarif
```

This diffs against the base branch and only reports violations on changed lines.

## Common Issues

### "No metadata files found"
Ensure your `-d` flag points to the directory containing your Salesforce metadata (e.g., `force-app/main/default/`).

### "License key required"
CI/CD execution requires an Enterprise License. Set `SQUIREX_LICENSE_KEY` as an environment variable. For local execution, no license is needed.

### Parse errors in output
Some parse errors are expected for complex metadata. Use `squirex diagnose` to inspect which files have errors and whether they impact scan coverage.

---

**Next**: [CI/CD Integration](ci-cd.md) · [Security Rules Reference](security-rules.md) · [Enterprise Guide](enterprise.md)

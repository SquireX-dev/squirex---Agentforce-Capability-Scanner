# Command Reference

## `squirex scan` — Agentforce Capability Scan

Run a static capability scan on Agentforce metadata files to detect configuration vulnerabilities.

```bash
squirex scan [options]
```

### Options
| Flag | Description | Default |
|------|-------------|---------|
| `-d, --directory <dir>` | Workspace root directory | `.` |
| `--sarif <file>` | Output SARIF to file | stdout |
| `--rules <ids>` | Comma-separated rule IDs to run | all 26 rules |

### Examples
```bash
# Full workspace scan
squirex scan -d ./force-app

# Output SARIF for CI/CD integration
squirex scan -d ./force-app --sarif results.sarif

# Run specific rules
squirex scan -d ./force-app --rules AGENTFORCE-1.1,AGENTFORCE-9.1,AGENTFORCE-1.3
```

---

## `squirex scan-pr` — PR Delta Scan

Run Agentforce Capability Scan scoped only to lines and files changed in the pull request.

```bash
squirex scan-pr -b <branch> [options]
```

### Options
| Flag | Description | Required |
|------|-------------|----------|
| `-b, --base <branch>` | Base branch to diff against | Yes |
| `-d, --directory <dir>` | Workspace root directory | No (default: `.`) |
| `--sarif <file>` | Output SARIF to file | No (default: stdout) |

### Examples
```bash
# Compare against main
squirex scan-pr -b main

# Output SARIF for GitHub Security
squirex scan-pr -b main --sarif results.sarif
```

---

## `squirex diagnose` — Pipeline Diagnostics

Run the full SAST pipeline and output a structured diagnostic JSON report instead of SARIF. Use this to inspect AST health, semantic graph topology, linker edge resolution, per-rule timing, and instruction audit.

```bash
squirex diagnose -d <dir> [options]
```

### Options
| Flag | Description |
|------|-------------|
| `-d, --directory <dir>` | Workspace root directory |
| `--dump-request` | Also write the scan-request.json for golden file generation |

### Examples
```bash
# Full diagnostic report
squirex diagnose -d ./force-app

# Inspect specific sections with jq
squirex diagnose -d ./force-app | jq .graph.orphanNodes
squirex diagnose -d ./force-app | jq .instructions
squirex diagnose -d ./force-app | jq '.rules.perRule[] | select(.violations > 0)'

# Dump the raw scan request for debugging
squirex diagnose -d ./force-app --dump-request
```

### Output Structure
```json
{
  "pipeline":     { "inputFiles": 232, "parsedSuccessfully": 157, "parseErrors": 75 },
  "graph":        { "totalNodes": 89, "totalEdges": 142, "orphanNodes": [...] },
  "linker":       { "nodesRegistered": 89, "edgesResolved": 142, "edgesDropped": 3 },
  "rules":        { "totalRegistered": 26, "evaluated": 26, "perRule": [...] },
  "violations":   { "total": 31, "bySeverity": {...}, "byRule": {...} },
  "instructions": { "totalEntries": 45, "byNodeType": {...}, "entries": [...] }
}
```

---

## `squirex run` — Local Apex Runtime

Run Apex tests locally with full mock runtime support.

```bash
squirex run [options]
```

### Options
| Flag | Description |
|------|-------------|
| `-d, --directory <dir>` | Directory containing Apex classes |
| `-f, --file <file>` | Run a specific test file |
| `--method <class.method>` | Run a single test method |
| `--coverage` | Show coverage summary table |
| `--junit <file>` | Output JUnit XML report |
| `--parallel` | Enable parallel test execution |

### Examples
```bash
squirex run -d force-app/main/default/classes
squirex run --method AccountTest.testInsert
squirex run --coverage --junit results.xml
```

---

## `squirex conflict` — Conflict Prediction

Analyze two branches to predict Apex merge conflicts before they happen.

```bash
squirex conflict -b <branch1,branch2>
```

---

## `squirex impact` — Dependency Impact

Analyze which classes are impacted by changes to a specific file.

```bash
squirex impact -f <files>
```

---

## Global Options

| Flag | Description |
|------|-------------|
| `--no-color` | Disable colorized output |
| `-V, --version` | Print version info |
| `-h, --help` | Show help |

---

## Exit Codes

| Code | Meaning |
|------|---------|
| `0` | Success — no violations found |
| `1` | Error — runtime failure, invalid input, or missing license |
| `2` | Violations found — scan completed with security findings |

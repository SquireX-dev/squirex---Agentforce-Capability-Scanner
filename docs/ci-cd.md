# CI/CD Integration

SquireX operates natively in CI/CD pipelines. To execute SquireX in any CI environment (e.g. GitHub Actions, GitLab, Jenkins) you must have an active **Enterprise License** and provide your license key as an environment variable (`SQUIREX_LICENSE_KEY`).

## GitHub Actions — Capability Scan (SARIF)

Use SquireX to generate SARIF reports for GitHub Advanced Security on pull requests.

```yaml
name: Agentforce Capability Scan

on:
  pull_request:
    branches: [main, develop]

env:
  FORCE_JAVASCRIPT_ACTIONS_TO_NODE24: true

jobs:
  squirex-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Full history to diff against base branch
      
      - name: Download SquireX
        run: |
          curl -L -o squirex https://github.com/samudralap/squirex-apex-forge/releases/latest/download/squirex-linux-x64
          chmod +x squirex
          sudo mv squirex /usr/local/bin/
          
      - name: Run Agentforce Capability Scan
        env:
          SQUIREX_LICENSE_KEY: ${{ secrets.SQUIREX_LICENSE_KEY }}
        run: squirex scan-pr -b ${{ github.base_ref }} --sarif results.sarif
            
      - name: Upload SARIF report
        uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: results.sarif
          category: agentforce-capability
```

## GitLab CI

```yaml
agentforce-capability:
  stage: test
  image: ubuntu:latest
  before_script:
    - apt-get update && apt-get install -y curl
    - curl -L -o /usr/local/bin/squirex https://github.com/samudralap/squirex-apex-forge/releases/latest/download/squirex-linux-x64
    - chmod +x /usr/local/bin/squirex
  script:
    - squirex scan -d ./force-app --sarif gl-capability-report.json
  artifacts:
    reports:
      capability: gl-capability-report.json
  variables:
    SQUIREX_LICENSE_KEY: $SQUIREX_LICENSE_KEY
```

---

## Secondary: Legacy Apex Tests

If you wish to run the legacy local Apex runtime tests in CI, the same Enterprise License requirements apply.

```yaml
      - name: Run Apex Tests
        env:
          SQUIREX_LICENSE_KEY: ${{ secrets.SQUIREX_LICENSE_KEY }}
        run: squirex run -d force-app/main/default/classes --junit test-results.xml
```

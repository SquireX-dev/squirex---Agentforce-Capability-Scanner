# CI/CD Integration

SquireX integrates with all major CI/CD platforms. CI/CD execution requires an **Enterprise License** — set your license key as the `SQUIREX_LICENSE_KEY` environment variable.

> See [Enterprise Guide](enterprise.md) for license activation instructions per platform.

---

## GitHub Actions — Capability Scan with SARIF

Upload SARIF results to GitHub Advanced Security for inline PR annotations:

```yaml
name: SquireX Agentforce Capability Scan

on:
  pull_request:
    branches: [main, develop]

permissions:
  security-events: write
  pull-requests: write

jobs:
  squirex-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Download SquireX
        run: |
          curl -L -o squirex https://github.com/SquireX-dev/squirex---Agentforce-Capability-Scanner/releases/latest/download/squirex-linux-x64
          chmod +x squirex
          sudo mv squirex /usr/local/bin/

      - name: Run Agentforce Capability Scan
        env:
          SQUIREX_LICENSE_KEY: ${{ secrets.SQUIREX_LICENSE_KEY }}
        run: squirex scan-pr -b ${{ github.base_ref }} --sarif results.sarif

      - name: Upload SARIF to GitHub Security
        if: always()
        uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: results.sarif
          category: agentforce-capability
```

---

## GitLab CI

```yaml
agentforce-capability-scan:
  stage: test
  image: ubuntu:latest
  before_script:
    - apt-get update && apt-get install -y curl
    - curl -L -o /usr/local/bin/squirex https://github.com/SquireX-dev/squirex---Agentforce-Capability-Scanner/releases/latest/download/squirex-linux-x64
    - chmod +x /usr/local/bin/squirex
  script:
    - squirex scan -d ./force-app --sarif gl-capability-report.json
  artifacts:
    reports:
      sast: gl-capability-report.json
  variables:
    SQUIREX_LICENSE_KEY: $SQUIREX_LICENSE_KEY
```

---

## Bitbucket Pipelines

```yaml
pipelines:
  pull-requests:
    '**':
      - step:
          name: SquireX Agentforce Scan
          script:
            - curl -L -o squirex https://github.com/SquireX-dev/squirex---Agentforce-Capability-Scanner/releases/latest/download/squirex-linux-x64
            - chmod +x squirex
            - ./squirex scan -d ./force-app --sarif results.sarif
          artifacts:
            - results.sarif
```

---

## Azure DevOps

```yaml
trigger:
  branches:
    include:
      - main
      - develop

pool:
  vmImage: 'ubuntu-latest'

steps:
  - script: |
      curl -L -o squirex https://github.com/SquireX-dev/squirex---Agentforce-Capability-Scanner/releases/latest/download/squirex-linux-x64
      chmod +x squirex
      ./squirex scan -d ./force-app --sarif $(Build.ArtifactStagingDirectory)/results.sarif
    displayName: 'Run SquireX Agentforce Scan'
    env:
      SQUIREX_LICENSE_KEY: $(SQUIREX_LICENSE_KEY)

  - task: PublishBuildArtifacts@1
    inputs:
      PathtoPublish: '$(Build.ArtifactStagingDirectory)/results.sarif'
      ArtifactName: 'squirex-sarif'
```

---

## Troubleshooting

### "License key required" error
Ensure `SQUIREX_LICENSE_KEY` is set as a secret/variable in your CI platform. See [Enterprise Guide](enterprise.md) for platform-specific setup.

### SARIF upload fails
- Ensure `permissions: security-events: write` is set in GitHub Actions
- SARIF files must be valid JSON — run `jq . results.sarif` to verify
- GitHub has a 10MB SARIF upload limit

### No violations found
This is normal and means your metadata passes all 26 rules. The exit code will be `0` (success). Violations produce exit code `2`.

### Binary not found on Linux runner
Some CI runners don't have `/usr/local/bin` in PATH. Use `./squirex` instead, or add the binary location to `$PATH` explicitly.

---

**Next**: [Enterprise Guide](enterprise.md) · [Security Rules Reference](security-rules.md)

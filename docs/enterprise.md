# Enterprise Guide

## Community vs Enterprise

| Feature | Community (Free) | Enterprise |
|---|:---:|:---:|
| Local `squirex scan` | ✅ | ✅ |
| Local `squirex run` (Apex) | ✅ | ✅ |
| All 26 security rules | ✅ | ✅ |
| `squirex diagnose` | ✅ | ✅ |
| CI/CD execution | ❌ | ✅ |
| SARIF report generation | ❌ | ✅ |
| GitHub Advanced Security integration | ❌ | ✅ |
| GitLab Security Dashboard | ❌ | ✅ |
| PR gating / blocking merges | ❌ | ✅ |
| Priority support | ❌ | ✅ |

## Purchasing a License

Contact us for Enterprise pricing:

- **Email**: hello@squirex.dev
- **Web**: [squirex.dev](https://squirex.dev)

We offer flexible pricing for teams of all sizes.

## Activating Your License

Once you receive your Enterprise License Key, configure it in your CI/CD environment:

### GitHub Actions

Add the key as a repository secret:
1. Go to your repo → **Settings** → **Secrets and variables** → **Actions**
2. Click **New repository secret**
3. Name: `SQUIREX_LICENSE_KEY`
4. Value: Your license key
5. Click **Add secret**

Then reference it in your workflow:
```yaml
env:
  SQUIREX_LICENSE_KEY: ${{ secrets.SQUIREX_LICENSE_KEY }}
```

### GitLab CI

1. Go to your project → **Settings** → **CI/CD** → **Variables**
2. Add variable: `SQUIREX_LICENSE_KEY` with your key
3. Mark as **Masked** and **Protected**

### Bitbucket Pipelines

1. Go to your repo → **Repository settings** → **Repository variables**
2. Add: `SQUIREX_LICENSE_KEY` with your key
3. Enable **Secured**

### Azure DevOps

1. Go to **Pipelines** → **Library** → **Variable groups**
2. Create a variable group with `SQUIREX_LICENSE_KEY`
3. Mark as secret

### Local Environment (for testing)

```bash
export SQUIREX_LICENSE_KEY="your-license-key-here"
squirex scan -d ./force-app --sarif results.sarif
```

## CI Detection

SquireX automatically detects CI/CD environments via these environment variables:

| CI Platform | Detection Variable |
|---|---|
| GitHub Actions | `GITHUB_ACTIONS=true` |
| GitLab CI | `GITLAB_CI=true` |
| Bitbucket Pipelines | `BITBUCKET_PIPELINE=true` |
| Azure DevOps | `TF_BUILD=true` |
| Jenkins | `JENKINS_URL` is set |
| CircleCI | `CIRCLECI=true` |
| Generic | `CI=true` |

If a CI environment is detected and no valid `SQUIREX_LICENSE_KEY` is provided, the engine will safely abort with a clear error message. **It will not produce partial or incorrect results.**

## Support

Enterprise license holders receive priority support:

- **Email**: hello@squirex.dev
- **Response time**: Within 1 business day
- **Scope**: Installation, configuration, rule interpretation, false positive triage

---

**Contact Sales**: hello@squirex.dev · [squirex.dev](https://squirex.dev)

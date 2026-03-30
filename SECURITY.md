# Security Policy

## Reporting a Vulnerability

If you discover a security vulnerability in SquireX, we appreciate your responsible disclosure.

**Please do NOT create a public GitHub issue for security vulnerabilities.**

### How to Report

Send an email to **hello@squirex.dev** with:

1. **Subject line**: `[SECURITY] Brief description of the issue`
2. **Description**: A clear description of the vulnerability
3. **Steps to reproduce**: Detailed steps to reproduce the issue
4. **Impact assessment**: Your assessment of the potential impact
5. **Suggested fix** (optional): If you have a proposed solution

### What to Expect

| Timeline | Action |
|---|---|
| **24 hours** | Acknowledgment of your report |
| **72 hours** | Initial assessment and severity classification |
| **7 days** | Detailed response with remediation plan |
| **30 days** | Fix released (for critical/high severity) |

### Scope

The following are in scope for security reports:

- Vulnerabilities in the SquireX binary or engine
- Security rule bypasses (false negatives)
- SARIF output manipulation
- License enforcement bypasses
- Supply chain vulnerabilities in dependencies

The following are **out of scope**:

- Vulnerabilities in Salesforce platform itself
- Issues in third-party Salesforce metadata (your org's code)
- False positive reports from security rules (use GitHub Issues instead)
- Feature requests (use GitHub Issues instead)

### Recognition

We maintain a security acknowledgments section for researchers who responsibly disclose vulnerabilities. If you'd like to be credited, include your preferred name/handle in your report.

### Safe Harbor

We consider security research conducted in accordance with this policy to be authorized. We will not pursue legal action against researchers who:

- Make a good faith effort to avoid privacy violations, data destruction, and service disruption
- Only interact with accounts they own or have explicit permission to test
- Report vulnerabilities promptly and provide reasonable time for remediation

---

**Contact**: hello@squirex.dev · [squirex.dev](https://squirex.dev)

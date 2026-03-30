# Contributing to SquireX

Thank you for your interest in contributing to SquireX! This document provides guidelines for contributing to the project.

## How to Contribute

### Reporting Issues

Use [GitHub Issues](https://github.com/SquireX-dev/squirex---Agentforce-Capability-Scanner/issues) to report:

- **Bug reports**: Unexpected behavior, crashes, or incorrect scan results
- **False positives**: Security rules flagging legitimate code
- **False negatives**: Security issues that rules should catch but don't
- **Feature requests**: New rules, metadata type support, or tool capabilities
- **Documentation issues**: Errors, unclear instructions, or missing content

When reporting a bug, please include:
1. SquireX version (`squirex --version`)
2. Operating system and architecture
3. Steps to reproduce the issue
4. Expected vs actual behavior
5. Relevant metadata files (sanitized of sensitive data)

### Submitting Pull Requests

1. **Fork** the repository
2. **Create a branch** from `main` (`git checkout -b fix/my-fix`)
3. **Make your changes** — keep commits focused and atomic
4. **Test** your changes locally with `squirex scan` on sample metadata
5. **Submit a PR** with a clear description of the change and its motivation

#### PR Guidelines

- One logical change per PR
- Include test cases for new rules when possible
- Update documentation if behavior changes
- Follow existing code style and naming conventions

### Contributor License Agreement (CLA)

By submitting a pull request, you agree to the SquireX CLA:

> You grant SquireX a perpetual, worldwide, non-exclusive, royalty-free license to use, modify, and distribute your contribution as part of the SquireX software. You represent that you have the right to grant this license.

### What We're Looking For

We especially welcome contributions for:

- **New adversarial patterns** for `adversarial_patterns.json`
- **New security rules** for Agentforce metadata
- **Documentation improvements**
- **CI/CD integration examples** for additional platforms
- **Bug fixes** with reproducible test cases

## Security Vulnerabilities

**Do NOT report security vulnerabilities via GitHub Issues.** See [SECURITY.md](SECURITY.md) for responsible disclosure instructions.

## Code of Conduct

We are committed to providing a welcoming and inclusive experience for everyone. All contributors are expected to:

- Use welcoming and inclusive language
- Be respectful of differing viewpoints
- Accept constructive criticism gracefully
- Focus on what is best for the community

---

**Questions?** Reach out at hello@squirex.dev

# Installation

## Download Binary (Recommended)

Download the pre-built binary for your platform from [GitHub Releases](https://github.com/SquireX-dev/squirex---Agentforce-Capability-Scanner/releases):

| Platform | Binary | Architecture |
|----------|--------|--------------| 
| macOS (Intel) | `squirex-macos-x64` | x86_64 |
| macOS (Apple Silicon) | `squirex-macos-arm64` | ARM64 |
| Linux | `squirex-linux-x64` | x86_64 |
| Windows | `squirex-win-x64.exe` | x64 |

## macOS / Linux

```bash
# Download (example for macOS Apple Silicon)
curl -L -o squirex https://github.com/SquireX-dev/squirex---Agentforce-Capability-Scanner/releases/latest/download/squirex-macos-arm64

# Make executable
chmod +x squirex

# Move to PATH
sudo mv squirex /usr/local/bin/

# Verify installation
squirex --version
```

## Windows

1. Download `squirex-win-x64.exe` from [Releases](https://github.com/SquireX-dev/squirex---Agentforce-Capability-Scanner/releases).
2. Rename the file to `squirex.exe`.
3. Add the location of the executable to your System PATH or move it to a directory already in PATH.
4. Run from Command Prompt or PowerShell.

### Verify Installation
```powershell
squirex --version
```

## Docker (Coming Soon)

```dockerfile
# Planned — not yet available
FROM squirex/scanner:latest
COPY ./force-app /workspace/force-app
RUN squirex scan -d /workspace/force-app --sarif results.sarif
```

---

**Next**: [Getting Started](getting-started.md) · [CI/CD Integration](ci-cd.md)

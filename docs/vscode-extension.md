# SquireX — VS Code Extension

[![Version](https://img.shields.io/visual-studio-marketplace/v/prasanth-samudrala.apexforge)](https://marketplace.visualstudio.com/items?itemName=prasanth-samudrala.apexforge)
[![Installs](https://img.shields.io/visual-studio-marketplace/i/prasanth-samudrala.apexforge)](https://marketplace.visualstudio.com/items?itemName=prasanth-samudrala.apexforge)

Run Salesforce Apex tests locally without deploying to a Salesforce org. Perfect for rapid development cycles and AI-agent integration.

> **Note**: The VS Code extension focuses on the Apex runtime capability. For Agentforce Capability Scanning, use the `squirex` CLI directly.

## Features

- **Local Test Execution**: Run Apex tests without Salesforce deployment
- **Git Conflict Analysis**: Detect potential merge conflicts in Apex files across branches
- **File Parsing**: Extract structure from Apex files (classes, methods, SOQL queries)
- **AI Agent Integration**: MCP (Model Context Protocol) server for Cursor, Claude Code, and other AI tools
- **VS Code Integration**: Test Explorer, CodeLens, and diagnostics support

## Installation

1. Install from [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=prasanth-samudrala.apexforge).
2. The extension will automatically download the SquireX binary on first activation.
3. Open a workspace containing Apex files (`.cls` or `.trigger`).

## Usage

### Run Tests

- **Run All Tests**: `Cmd/Ctrl+Shift+P` → "SquireX: Run All Tests"
- **Run Tests in File**: Right-click on a `.cls` file → "SquireX: Run Tests in Current File"
- **Run Single Test**: Click "Run Test" CodeLens above any `@IsTest` method
- **Keyboard Shortcut**: `Cmd/Ctrl+Shift+T` to run tests in current file

### Analyze Branch Conflicts

1. Open Command Palette (`Cmd/Ctrl+Shift+P`)
2. Run "SquireX: Analyze Branch Conflicts"
3. Select two branches to compare
4. View conflict analysis in the output panel

### Parse Apex Files

1. Open an Apex file
2. Run "SquireX: Parse Current File"
3. View extracted structure (classes, methods, annotations, SOQL)

## AI Agent Integration (MCP)

SquireX includes an MCP server for integration with AI coding assistants.

### Available Tools

| Tool | Description |
|------|-------------|
| `squirex_run_tests` | Run Apex tests with file/directory/pattern options |
| `squirex_analyze_conflicts` | Analyze branches for merge conflicts |
| `squirex_parse_file` | Extract file structure |

### Enable MCP Server

1. Open VS Code Settings
2. Search for "SquireX"
3. Enable "Enable MCP" option

Or add to your `settings.json`:

```json
{
  "squirex.enableMcp": true
}
```

## Configuration

| Setting | Default | Description |
|---------|---------|-------------|
| `squirex.binaryPath` | `""` | Custom binary path (auto-downloaded if empty) |
| `squirex.testPattern` | `**/*Test.cls` | Glob pattern for test files |
| `squirex.parallelTests` | `true` | Run tests in parallel |
| `squirex.maxParallel` | `4` | Maximum parallel test executions |
| `squirex.timeout` | `30000` | Test timeout in milliseconds |
| `squirex.showCodeLens` | `true` | Show Run Test links above test methods |
| `squirex.enableMcp` | `false` | Enable MCP server for AI agents |

## Licensing

The VS Code extension follows the same licensing model as the SquireX CLI. See [Enterprise Guide](enterprise.md) for details.

## Support

- **Issues**: [GitHub Issues](https://github.com/SquireX-dev/squirex---Agentforce-Capability-Scanner/issues)
- **Documentation**: [squirex.dev](https://squirex.dev)
- **Email**: hello@squirex.dev

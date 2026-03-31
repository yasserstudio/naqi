# CLI Companion

Naqi ships a CLI binary that shares the same Rust library as the desktop app. It provides headless access to scanning, scoring, and cleanup operations for terminal workflows and CI pipelines.

> **Status:** Planned. The CLI is not yet published. The commands below represent the target interface.

## Installation

### Via Cargo

```bash
cargo install naqi
```

### Via Homebrew

```bash
brew tap yasserstudio/naqi
brew install naqi
```

The Homebrew formula installs both the desktop app (as a cask) and the CLI binary (as a formula) from the same tap.

## Commands

### `naqi scan`

Scan all detected AI clients and display discovered MCP servers, memories, and skills.

```bash
naqi scan
```

Options:

| Flag | Description |
|------|-------------|
| `--client <name>` | Scan a specific client only (e.g., `claude-desktop`, `cursor`, `vscode`, `windsurf`, `claude-code`) |
| `--format <fmt>` | Output format: `table` (default), `json`, `minimal` |
| `--no-color` | Disable colored output |

Examples:

```bash
# Scan all clients, output as JSON
naqi scan --format json

# Scan only Claude Desktop
naqi scan --client claude-desktop

# Minimal output for scripting
naqi scan --format minimal
```

### `naqi score`

Calculate and display the workspace health score.

```bash
naqi score
```

Options:

| Flag | Description |
|------|-------------|
| `--threshold <n>` | Exit with code 1 if the score is below the threshold (0-100) |
| `--format <fmt>` | Output format: `table` (default), `json`, `minimal` |
| `--no-color` | Disable colored output |

Examples:

```bash
# Display health score
naqi score

# CI gate: fail if score is below 80
naqi score --threshold 80

# JSON output for downstream processing
naqi score --format json
```

JSON output shape:

```json
{
  "score": 85,
  "max_score": 100,
  "breakdown": {
    "duplicates": -5,
    "staleness": -3,
    "missing_env": -7
  },
  "pass": true,
  "threshold": 80
}
```

### `naqi clean`

Run cleanup recommendations and optionally apply them.

```bash
naqi clean
```

Options:

| Flag | Description |
|------|-------------|
| `--dry-run` | Preview changes without applying (default behavior) |
| `--apply` | Apply the recommended changes after confirmation |
| `--yes` | Skip confirmation prompts (use with `--apply` for CI) |
| `--format <fmt>` | Output format: `table` (default), `json`, `minimal` |
| `--no-color` | Disable colored output |

Examples:

```bash
# Preview cleanup recommendations
naqi clean

# Apply cleanup with confirmation prompt
naqi clean --apply

# CI mode: apply without prompts
naqi clean --apply --yes
```

The cleanup follows the same backup-first protocol as the desktop app: backup, preview diff, confirm, apply, validate. Backups are stored in `~/.naqi/backups/`.

## Output Formats

| Format | Description | Use Case |
|--------|-------------|----------|
| `table` | Human-readable table with colors and alignment | Terminal use |
| `json` | Structured JSON output | Scripting, piping to `jq`, CI |
| `minimal` | One-line-per-item, no headers or decoration | Scripting, `grep`/`awk` pipelines |

## CI Usage

Use `naqi score --threshold` as a CI quality gate to enforce workspace hygiene standards.

### GitHub Actions Example

```yaml
name: Workspace Hygiene
on: [push, pull_request]

jobs:
  check:
    runs-on: macos-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install Naqi CLI
        run: cargo install naqi

      - name: Check workspace health
        run: naqi score --threshold 80 --format json
```

The command exits with code 0 if the score meets or exceeds the threshold, and code 1 otherwise. CI runners can use this exit code to pass or fail the pipeline step.

## Architecture

The CLI binary links against the same `naqi-core` library used by the Tauri desktop app. The scanner, analyzer, and cleanup modules are shared. The only difference is the presentation layer: the desktop app renders in a webview, while the CLI writes to stdout.

```
naqi (CLI binary)
  |
  +-- naqi-core (shared library)
  |     +-- scanner/
  |     +-- analyzer/
  |     +-- cleanup/
  |
naqi-desktop (Tauri app)
  |
  +-- naqi-core (same library)
```

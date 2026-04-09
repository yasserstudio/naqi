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
| `--json` | Output as JSON |

Examples:

```bash
# Scan all clients, table output
naqi scan

# JSON output for scripting
naqi scan --json
```

### `naqi score`

Calculate and display the workspace health score.

```bash
naqi score
```

Options:

| Flag | Description |
|------|-------------|
| `--json` | Output as JSON |
| `--fail-below <n>` | Exit with code 1 if the score is below n (0-100) |

Examples:

```bash
# Display health score
naqi score

# CI gate: fail if score is below 80
naqi score --fail-below 80

# JSON output for downstream processing
naqi score --json

# Both: JSON output + CI exit code
naqi score --json --fail-below 80
```

JSON output shape:

```json
{
  "score": 85,
  "band": "Healthy",
  "servers": {
    "total": 12,
    "broken": 1,
    "stale": 2,
    "duplicate": 0
  },
  "memories": 34,
  "skills": 8,
  "recommendations": 3,
  "pass": true,
  "threshold": 80
}
```

`threshold` is omitted from JSON output when `--fail-below` is not specified.

### `naqi update` / `naqi upgrade`

Check for a newer release and print upgrade instructions.

```bash
naqi update
```

Fetches `latest.json` from the GitHub releases endpoint and compares with the running version. If an update is available, prints the release notes (up to 10 lines) and the correct upgrade command based on how Naqi was installed:

- **Homebrew install** (exe path contains `Caskroom`): prints `brew upgrade --cask naqi`
- **Direct install**: prints the GitHub releases URL

Examples:

```bash
# Already up to date
$ naqi update
Checking for updates... done.
Naqi 0.4.0 is up to date.

# Update available
$ naqi update
Checking for updates... done.
Update available: 0.3.0 → 0.4.0

What's new:
  - Pro tier with Lemon Squeezy licensing
  - CLI --json output and --fail-below gate
  ...

To update:
  brew upgrade --cask naqi
```

---

### `naqi clean`

Run cleanup recommendations and optionally apply them.

```bash
naqi clean
```

Options:

| Flag | Description |
|------|-------------|
| `--json` | Output as JSON array of recommendations |

Examples:

```bash
# Show recommendations as text
naqi clean

# JSON output for scripting
naqi clean --json

# Pipe to jq to filter critical items
naqi clean --json | jq '[.[] | select(.severity == "Critical")]'
```

## CI Usage

Use `naqi score --fail-below` as a CI quality gate to enforce workspace hygiene standards.

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
        run: naqi score --fail-below 80 --json
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

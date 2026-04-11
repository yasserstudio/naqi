# CLI Companion

Naqi ships a CLI binary (`naqi-cli`) that shares the same Rust library as the desktop app. It provides headless access to scanning, scoring, cleanup, Token Hygiene analysis, and prompt linting for terminal workflows and CI pipelines.

> **Status:** Shipped as of v0.4.0. Token Hygiene commands (`naqi tokens`, `naqi lint-prompt`) added in v0.6.0.

## Installation

The CLI binary is named `naqi-cli` and is bundled alongside the desktop app.

### Via Homebrew

```bash
brew tap yasserstudio/naqi
brew install --cask naqi
```

The Homebrew cask installs the desktop app and places `naqi-cli` on your `PATH`.

### From a direct download

Download the macOS / Windows / Linux build from [the latest release](https://github.com/yasserstudio/naqi/releases/latest). The `naqi-cli` binary lives in the bundle alongside the desktop app.

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

### `naqi export`

Print a full workspace scan report to stdout.

```bash
naqi export
```

Options:

| Flag | Description |
|------|-------------|
| `--format <fmt>` | Output format: `json` (default) or `md` |
| `--json` | Shorthand for `--format json` |

Examples:

```bash
# JSON to stdout (default)
naqi export

# Save to file
naqi export > report.json
naqi export --format md > report.md

# Pipe JSON to jq
naqi export | jq '.servers[] | select(.status == "Broken")'
```

The JSON output is the full `Workspace` object — identical to `naqi scan --json` but intended for export/archiving rather than live inspection. The Markdown output includes a summary table, per-client breakdown, MCP server table with status indicators, memories with staleness flags, and skills inventory.

---

### `naqi tokens`

Per-project Claude Code session token breakdown. The base command without a subcommand shows aggregated usage across every session under `~/.claude/projects/`.

```bash
naqi tokens
```

Options:

| Flag | Description |
|------|-------------|
| `--json` | Output as JSON for scripting |
| `--project <name>` | Filter to sessions in a single project |

Each row shows the project slug, session count, input/cache-create/cache-read/output token totals, and the sum. Under the hood the scan uses a persistent cache with mtime-based invalidation (`~/.naqi/token-cache/manifest.json`), so repeated invocations only re-parse session JSONL files that have actually changed.

---

### `naqi tokens top`

Top N costliest sessions by total token cost.

```bash
naqi tokens top            # default: top 10
naqi tokens top --n 25
naqi tokens top --json
```

Each row shows the session slug (or UUID prefix), project, turn count, total tokens, and the first-prompt preview (truncated to 200 chars with secrets/paths stripped via the anonymizer).

---

### `naqi tokens waste`

All waste-pattern recommendations across cached sessions, grouped by severity. Same list that powers the TokensPage waste banner in the desktop app.

```bash
naqi tokens waste
naqi tokens waste --json
naqi tokens waste --project naqi
```

Nine detectors fire on: mega-session (≥50 turns + ≥100M tokens), background-bash overhead (≥20 task notifications), log paste (≥2 KB of pasted console output), dive-deep opener, auto-compact waste, subagent swarm (≥8 subagent fan-outs), short-prompt thrashing, image paste / screenshot-review loop, and skill auto-load overhead (≥4 KB skill listing with <5 skills actually invoked).

---

### `naqi tokens health <session-id>`

Per-session health score breakdown showing every contributing factor (turn count, cache bloat ratio, background-bash overhead, subagent overhead, auto-compactions, dive-deep opener presence).

```bash
naqi tokens health 084d1abb-85f2-4f95-8c36-a1ebc3d5abb7
naqi tokens health <id> --json
```

Text output lists the score (0–100), band (GREEN/YELLOW/RED), turn count, and each factor with its impact. JSON output is the full `SessionHealthScore` struct.

---

### `naqi lint-prompt`

Pre-send prompt linter. Reads a draft Claude Code prompt from stdin or a file and flags four token-wasting patterns. Exits with code 1 on any issue so it composes as a pre-commit hook, shell pipe, or Claude Code skill.

```bash
# From stdin (heredoc works well for multi-line drafts)
echo "continue" | naqi lint-prompt

cat <<'EOF' | naqi lint-prompt
Dive deep into the project and figure out why tests are failing.
EOF

# From a file
naqi lint-prompt draft.md

# JSON output for tooling
naqi lint-prompt draft.md --json
```

Four rules:

| Rule | Severity | Fires when | Fix |
|---|---|---|---|
| `ShortPrompt` | Warning | Trimmed input < 20 chars OR < 3 words | Batch instructions into one complete prompt, or `/clear` first to reset cached context |
| `DiveDeep` | Warning | Opener matches "dive deep", "understand the project", "check everything", etc. | Name the exact files or areas to examine |
| `LogPaste` | Warning | ≥5 consecutive log-like lines totaling ≥2 KB | Save output to a file and reference the path (`Read /tmp/build.log`) so it bills once, not every turn |
| `ImperativeNoAnchor` | Info | First word is imperative (fix/add/update/...) AND no file path or `*.ext` token present | Name the target file(s) or directory |

Empty input is treated as clean. The linter lives in `analyzer/prompt_lint.rs` as a pure function so it can later be exposed via a Tauri command for inline in-app linting.

There's also an optional Claude Code skill wrapper at `skills/lint-prompt/SKILL.md` — `cp -r skills/lint-prompt ~/.claude/agent-skills/skills/` and Claude Code will invoke the CLI automatically when you draft a prompt that looks expensive. See the [Token Hygiene guide](./guides/token-hygiene.md) for the full playbook.

---

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
  - Pro tier with Paddle licensing
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
| `--json` | Output as JSON array of recommendations (list mode) or JSON apply summary (with `--apply`) |
| `--apply` | Apply each actionable recommendation after per-item confirmation |
| `--yes`, `-y` | Skip confirmation prompts — apply all automatically. Requires `--apply` |

Examples:

```bash
# Show recommendations as text
naqi clean

# JSON output for scripting
naqi clean --json

# Pipe to jq to filter critical items
naqi clean --json | jq '[.[] | select(.severity == "Critical")]'

# Apply with per-item confirmation
naqi clean --apply

# Apply all without prompts (CI / dotfiles)
naqi clean --apply --yes

# Apply all and get JSON summary
naqi clean --apply --yes --json
```

`--apply` skips `Advisory` recommendations (AI-sourced items without validated cleanup actions). Only local rule-based recommendations are applied.

When `--apply` fails on any item, the command exits with code 1. Each successful action creates a timestamped backup in `~/.naqi/backups/` and is undoable from the desktop app's History page.

JSON apply summary shape:

```json
{
  "applied": 2,
  "failed": 1,
  "results": [
    { "id": "...", "title": "Remove stale GitHub server", "success": true, "backup_path": "~/.naqi/backups/..." },
    { "id": "...", "title": "Remove unused Postgres server", "success": false, "error": "Safe Mode is enabled" }
  ]
}
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

`naqi-cli` and the desktop app link against the same `naqi_lib` crate (see `src-tauri/Cargo.toml`). The scanner, analyzer, cleanup, storage, report, and CLI modules are all shared. Every cleanup command the CLI runs goes through `cleanup::execute_action()`, so it inherits the same backup → preview → confirm → validate → undo protocol, Safe Mode check, per-client locking, and path allowlist as the desktop UI. The only difference is the presentation layer: the desktop app renders in a webview, while the CLI writes to stdout.

```
naqi-cli (CLI binary)
  │
  ├── naqi_lib (shared library)
  │     ├── scanner/           (read-only AI client config + JSONL session parsing)
  │     ├── analyzer/          (recommendations, token waste, session health, prompt lint)
  │     ├── cleanup/           (backup → modify → validate → undo)
  │     ├── storage/           (paths allowlist, settings, history, token cache, backups)
  │     └── report/            (shared markdown report generator)
  │
naqi (Tauri desktop app)
  │
  └── naqi_lib (same library)
```

Both binaries share the same safety invariants. The CLI cannot bypass Safe Mode, the path allowlist, the file-lock mutexes, or the atomic-write mandate — all of those live in `naqi_lib` and enforce themselves regardless of the caller.

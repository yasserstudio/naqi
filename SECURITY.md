# Security Policy

## Supported Versions

Naqi is in public beta. Only the latest released beta receives security fixes.

| Version | Supported |
|---------|-----------|
| `1.0.0-beta.x` (latest) | ✓ Yes |
| Older `1.0.0-beta.N` | ✗ No — upgrade to latest |
| `0.x` (pre-beta) | ✗ No |

When `1.0.0` ships, the support window will widen to the latest stable plus one prior minor.

## Reporting a Vulnerability

If you discover a security vulnerability in Naqi, please report it responsibly.

**Email:** security@getnaqi.com

Do NOT open a public GitHub issue for security vulnerabilities.

**What to include:**
- Description of the vulnerability
- Steps to reproduce
- Impact assessment (what data is at risk)
- Suggested fix (if you have one)

**Response timeline:**
- Acknowledgment within 48 hours
- Assessment within 5 business days
- Fix or mitigation within 14 days for critical issues
- Credit in release notes (unless you prefer anonymity)

## Scope

Security issues we care about:
- Exposure of secrets (API keys, tokens, passwords) from scanned configs
- Anonymization bypass (PII sent to AI APIs — Anthropic, OpenAI, Google, xAI, Ollama, OpenRouter)
- Unauthorized filesystem access beyond declared Tauri capabilities
- Config file corruption during cleanup operations
- Path traversal or injection in scanner/parser logic
- Backup files with insufficient permissions
- Arbitrary file write/delete outside intended paths

## Out of Scope

- Vulnerabilities in upstream dependencies (report to the dependency maintainer)
- Issues requiring physical access to the machine
- Social engineering attacks
- Denial of service against local-only application

## Design Principles

Naqi is built with security-first principles:
- **Never store secrets in plain text** — API keys and tokens stored in the OS keychain (macOS Keychain, Windows Credential Manager, Linux Secret Service) via the `keyring` crate. Scanned env var values are masked in memory and never written to disk.
- **Workspace anonymization** — before any AI API call, workspace data is stripped of file paths, secret values, and memory content. Only metadata (names, types, ages, counts) is sent.
- **Backup-first cleanup** — every modification creates a backup, auto-rollback on validation failure
- **Read-only scanning** — the scanner never modifies source config files
- **Capability scoping** — Tauri filesystem access restricted to known config paths
- **No network by default** — core features work entirely offline. AI features are opt-in and require explicit API key configuration.
- **AI recs are advisory only** — AI-sourced recommendations cannot be auto-applied; they require manual review
- **Sanitized error responses** — raw API error bodies (which may contain org IDs, key fragments, or internal structures) are never forwarded to the frontend; only safe, user-friendly messages are shown

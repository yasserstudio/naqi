# Security Policy

## Supported Versions

| Version | Supported |
|---------|-----------|
| 0.1.x   | Yes       |

## Reporting a Vulnerability

If you discover a security vulnerability in Naqi, please report it responsibly.

**Email:** security@naqi.dev

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
- Anonymization bypass (PII sent to Claude API)
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
- **Never store secrets** — API keys go in OS keychain, env vars are masked in memory
- **Backup-first cleanup** — every modification creates a backup, auto-rollback on validation failure
- **Read-only scanning** — the scanner never modifies source config files
- **Capability scoping** — Tauri filesystem access restricted to known config paths
- **No network by default** — core features work entirely offline

# Security Policy

## Reporting Vulnerabilities

If you find a security vulnerability in Naqi, please report it responsibly:

**Email:** security@naqi.dev

Do NOT open a public GitHub issue for security vulnerabilities.

We will:
- Acknowledge within 48 hours
- Provide a fix timeline within 7 days
- Credit you in the release notes (unless you prefer anonymity)

## Scope

Security issues we care about:
- Config file data leaking to unintended destinations
- Anonymization bypass (PII sent to Claude API)
- Backup files with insufficient permissions
- API key exposure
- Arbitrary file write/delete outside intended paths

## Non-Scope

- Denial of service against the local app
- Issues requiring physical access to the machine
- Social engineering

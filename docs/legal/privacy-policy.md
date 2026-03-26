# Privacy Policy

> **Naqi** (naqi.dev) — Privacy Policy
>
> Last updated: March 26, 2026

---

## Overview

Naqi is a desktop application that scans and cleans up AI agent configurations on your machine. Privacy is central to how Naqi works: your data stays local by default, and nothing leaves your machine unless you explicitly trigger it.

This policy explains what data Naqi collects, where it goes, and what control you have over it.

---

## 1. What Naqi Scans (Local Only)

When you run a scan, Naqi reads configuration files from AI clients installed on your machine:

- **Claude Desktop** config (`claude_desktop_config.json`)
- **Claude Code** config and memories (`~/.claude/`)
- **Cursor** MCP configs
- **VS Code** MCP/AI settings
- **Windsurf** configs

Naqi reads:
- MCP server names, transport types, and connection commands
- Environment variable **names** (never values)
- Memory file contents (for contradiction detection)
- Skill directory names, sizes, and modification dates
- Config file modification timestamps

**This data never leaves your machine during a scan.** Scanning is entirely local, performed by Naqi's Rust backend. No network requests are made during scanning.

---

## 2. What Data Is Sent to Anthropic (Pro Tier Only)

If you are a Pro user and you choose to run AI-powered analysis, Naqi sends an **anonymized summary** of your workspace to the Anthropic Claude API. This only happens when you explicitly click "Analyze" — it never happens automatically.

### What is sent

- MCP server names and transport types
- Environment variable **names only** (e.g., `GITHUB_TOKEN` — never the value)
- Anonymized server commands (package name only, flags and arguments stripped)
- Memory content with PII redacted (see anonymization rules below)
- Skill metadata (name, source type, file count, size, modification date — never file contents)
- Workspace summary statistics (number of servers, memories, skills, clients)

### What is never sent

- API key values, tokens, or secrets
- Environment variable values
- Full file paths (usernames are replaced with `[USER]`)
- Skill file contents
- Your Claude API key (used only as an authentication header, never in the message body)

### Anonymization rules

Before any data is sent to the Anthropic API, Naqi's anonymization engine applies these transformations:

| Data type | What happens |
|-----------|-------------|
| Environment variable values | Completely removed — only variable names are sent |
| File paths | Username replaced with `[USER]`, project paths shortened |
| Email addresses in memory content | Replaced with `[EMAIL]` |
| API keys in memory content | Replaced with `[API_KEY]` (matches `sk-`, `ghp_`, `gho_`, `xoxb-`, `xoxp-` patterns) |
| URLs with embedded credentials | Auth portion replaced with `[AUTH]` |
| IP addresses in memory content | Replaced with `[IP]` |
| Phone numbers in memory content | Replaced with `[PHONE]` |
| Server commands | Flags and arguments stripped; only the binary/package name is kept |

You can inspect the exact payload before it is sent using the "View Raw Payload" feature in the app. Nothing is sent until you confirm.

### Anthropic's data handling

Data sent to the Anthropic API is subject to [Anthropic's API Terms of Service](https://www.anthropic.com/api-terms). As of this writing, Anthropic states that API inputs and outputs are not used to train their models. Naqi has no control over Anthropic's policies — please review their terms directly.

---

## 3. What Data Goes to Lemon Squeezy (Pro Purchases)

Naqi uses **Lemon Squeezy** as its Merchant of Record for Pro license purchases. When you buy Naqi Pro, Lemon Squeezy collects:

- Your email address
- Payment information (credit card or other payment method)
- Billing address (for tax calculation)
- Country (for VAT/GST compliance)

Naqi itself never sees or stores your payment information. Lemon Squeezy handles all payment processing, tax compliance, and receipt generation.

When you activate your license key in the app, Naqi sends:
- Your license key
- A hashed machine identifier (SHA-256 hash of your hardware ID — the raw hardware ID never leaves your machine)

This data is sent to Lemon Squeezy's license validation API to verify your purchase and manage device activations.

Lemon Squeezy's privacy practices are governed by their own [Privacy Policy](https://www.lemonsqueezy.com/privacy).

---

## 4. What Naqi Stores Locally

Naqi stores its own data in `~/.naqi/` on your machine:

| File/Directory | Contents |
|---------------|----------|
| `config.json` | App preferences, license status, activation metadata, hashed machine ID |
| `state.json` | Cached scan results, health score |
| `backups/` | Timestamped copies of config files created before any cleanup action |
| `undo/` | Undo history entries for reversing cleanup actions |
| `logs/` | Application logs (local only, never transmitted) |

**License key storage:** Your license key is encrypted at rest using the OS keychain (macOS Keychain, Windows DPAPI, Linux Secret Service). It is not stored as plaintext.

**Claude API key storage:** Your API key (if you provide one) is stored in the OS keychain, never in plain JSON files. It is only loaded into memory when making an API call.

**Backup files:** Backups in `~/.naqi/backups/` contain original config file content, which may include API keys and tokens. These files are protected with owner-only filesystem permissions (`chmod 700`).

---

## 5. Analytics, Tracking, and Telemetry

**Naqi does not include any analytics, tracking, or telemetry.** There is no usage tracking, no crash reporting, no phone-home behavior, and no anonymous usage statistics.

The only network requests Naqi makes are:
1. **Claude API calls** — only when you explicitly trigger AI analysis (Pro tier)
2. **Lemon Squeezy license validation** — on activation and periodically (every 7 days) to check license status
3. **Update checks** — to check for new versions of Naqi (standard Tauri updater)

That's it. No other outbound connections.

---

## 6. Cookie Policy (naqi.dev Website)

The naqi.dev website uses only essential cookies required for basic site functionality. We do not use:

- Analytics cookies (no Google Analytics, no Plausible, no Fathom)
- Advertising cookies
- Third-party tracking cookies
- Social media tracking pixels

If you visit the Lemon Squeezy checkout page (hosted on lemonsqueezy.com), that page is subject to Lemon Squeezy's own cookie policy.

---

## 7. Your Rights (GDPR / CCPA)

Regardless of where you live, we believe you should have control over your data.

### Right to access

You can see everything Naqi stores locally by reading the files in `~/.naqi/`. For data held by Lemon Squeezy (purchase records, email), contact them directly or email us and we will assist.

### Right to deletion

- **Local data:** Delete the `~/.naqi/` directory to remove all Naqi data from your machine. Uninstalling the app also removes this data.
- **Lemon Squeezy data:** Email privacy@naqi.dev and we will request deletion of your records from Lemon Squeezy on your behalf.
- **Anthropic API data:** Anonymized summaries sent to the Claude API are subject to Anthropic's data retention policies. Since the data is anonymized before transmission, it cannot be linked back to you.

### Right to portability

Your scan data is stored as JSON files in `~/.naqi/`. You can copy, export, or move them freely. Naqi also includes a scan report export feature (JSON and Markdown formats).

### Right to opt out

- You can use Naqi's Free tier without any data leaving your machine — ever.
- AI analysis is opt-in and requires a deliberate action each time.
- You can delete your license data and revert to the Free tier at any time.

### For California residents (CCPA)

Naqi does not sell personal information. We do not share personal information with third parties for their marketing purposes. The categories of personal information we handle are limited to what is described in this policy.

### For EU/EEA residents (GDPR)

The legal basis for processing your data is:
- **Contract performance** — license validation is necessary to provide Pro features you purchased
- **Legitimate interest** — anonymized API calls are necessary to deliver the AI analysis feature you requested
- **Consent** — AI analysis only runs when you explicitly trigger it

---

## 8. Data Security

- All API communication uses HTTPS with certificate verification
- License keys are encrypted at rest using OS-level keychain services
- Claude API keys are stored in the OS keychain, never in plain files
- Backup files are protected with owner-only filesystem permissions
- Naqi requires no elevated privileges — it runs as your user account
- Machine identifiers are SHA-256 hashed before leaving your device

---

## 9. Children's Privacy

Naqi is a developer tool and is not directed at children under 13. We do not knowingly collect data from children.

---

## 10. Changes to This Policy

If we make material changes to this policy, we will:
- Update the "Last updated" date at the top
- Post a notice on naqi.dev
- For significant changes, notify Pro users via the email address associated with their Lemon Squeezy purchase

---

## 11. Contact

For privacy questions or data requests:

**Email:** privacy@naqi.dev

We aim to respond to all privacy inquiries within 7 business days.

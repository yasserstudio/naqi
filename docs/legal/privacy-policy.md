# Privacy Policy

> **Naqi** (getnaqi.com) — Privacy Policy
>
> Last updated: April 15, 2026
>
> Canonical version: [getnaqi.com/privacy](https://getnaqi.com/privacy). This file mirrors the live page for transparency and version history.

---

## Overview

Naqi is a desktop application that scans and cleans up AI agent configurations on your machine. Privacy is central to how Naqi works: your data stays local by default, and nothing leaves your machine unless you explicitly trigger it.

This policy explains what data Naqi collects, where it goes, and what control you have over it.

**Data controller:** Yasser's studio (publisher of Naqi). Yasser's studio is established outside the European Economic Area. See [Section 9 — EU Residents](#9-eu-residents-gdpr) for the implications.

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

## 2. AI Analysis (Pro Tier Only)

If you are a Pro user and you choose to run AI-powered analysis, Naqi sends an **anonymized summary** of your workspace to your chosen AI provider. This only happens when you explicitly click "Analyze" — never automatically.

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
- Your provider API key (used only as an authentication header, never in the message body)

### Anonymization rules

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

### AI providers

Supported providers (you bring your own API key; the call goes from your machine to the provider directly): Anthropic, OpenAI, Google, xAI, Ollama (local), and OpenRouter. Each provider's own privacy policy governs what they do with the request you initiate. Naqi has no control over any provider's data handling — please review their terms directly.

---

## 3. Payments (Pro Purchases)

Naqi uses **Paddle** as its Merchant of Record for Pro license purchases. When you buy Naqi Pro, Paddle collects:

- Your email address
- Payment information (handled entirely by Paddle)
- Billing address (for tax calculation)
- Country (for VAT/GST compliance)

Naqi itself never sees or stores your payment information. Paddle handles all payment processing, tax compliance, and receipt generation.

When you activate your license key in the app, Naqi sends:

- Your license key
- A SHA-256 hash of your hardware ID (the raw hardware ID never leaves your machine)

This data is sent to Paddle's license validation API to verify your purchase and manage device activations.

Paddle's privacy practices are governed by their own [Privacy Policy](https://www.paddle.com/legal/privacy).

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

**AI provider API key storage:** Your provider API key (if you provide one) is stored in the OS keychain, never in plain JSON files. It is only loaded into memory when making an API call.

**Backup files:** Backups in `~/.naqi/backups/` contain original config file content, which may include API keys and tokens. These files are protected with owner-only filesystem permissions.

---

## 5. Analytics, Tracking, and Telemetry

**Naqi does not include any analytics, tracking, or telemetry.** There is no usage tracking, no crash reporting, no phone-home behavior, and no anonymous usage statistics.

The only network requests Naqi makes are:

1. **AI API calls** — only when you explicitly trigger AI analysis (Pro tier)
2. **Paddle license validation** — on activation and periodically (every 7 days) to check license status
3. **Update checks** — to check for new versions of Naqi (standard Tauri updater)

That's it. No other outbound connections.

---

## 6. Cookie Policy (getnaqi.com Website)

The getnaqi.com website uses only essential cookies required for basic site functionality. We do not use:

- Analytics cookies (no Google Analytics, no Plausible, no Fathom)
- Advertising cookies
- Third-party tracking cookies
- Social media tracking pixels

If you are routed to a Paddle-hosted checkout, that page is subject to Paddle's own cookie policy.

---

## 7. Sub-processors

Where data is processed by third parties on our behalf:

| Sub-processor | Purpose | Region |
|---|---|---|
| **Paddle.com Market Limited** (UK) | Payment processing, license API, Merchant of Record | UK / global |
| **Resend** (US) | Transactional email delivery | US (with EU regions) |
| **Vercel Inc.** (US) | Hosting of getnaqi.com and the license validation API | US (with EU regions) |
| **Upstash Inc.** (US) | Redis cache for license validation rate limiting | US (with EU regions) |
| **GitHub Inc.** (US) | Release artifact hosting and download delivery | US |

AI providers you bring your own key for (Anthropic, OpenAI, Google, xAI, OpenRouter) are not Naqi sub-processors — you contract with them directly.

---

## 8. Your Rights

### Right to access

You can see everything Naqi stores locally by reading the files in `~/.naqi/`. For data held by Paddle (purchase records, email), contact them directly or email us and we will assist.

### Right to deletion

- **Local data:** Delete the `~/.naqi/` directory to remove all Naqi data from your machine. Uninstalling the app also removes this data.
- **Paddle data:** Email `hello@getnaqi.com` and we will request deletion of your records from Paddle on your behalf.

### Right to portability

Your scan data is stored as JSON files in `~/.naqi/`. You can copy, export, or move them freely. Naqi also includes a scan report export feature (JSON and Markdown formats).

### Right to opt out

- You can use Naqi's Free tier without any data leaving your machine — ever.
- AI analysis is opt-in and requires a deliberate action each time.
- You can delete your license data and revert to the Free tier at any time.

### For California residents (CCPA)

Naqi does not sell personal information. We do not share personal information with third parties for their marketing purposes. The categories of personal information we handle are limited to what is described in this policy.

---

## 9. EU Residents (GDPR)

If you are in the European Economic Area, Naqi processes the following limited personal data:

- **Pro purchasers:** email address (collected by Paddle as Merchant of Record), license key and hashed machine ID (held by us via Paddle's license API)
- **Free-tier users:** no personal data — Naqi runs entirely locally with no contact to our servers
- **Website visitors:** standard server logs (IP at request time, retained ≤ 30 days)

### Legal bases

- **Contract performance** (Article 6(1)(b)) — license validation is necessary to provide Pro features you purchased
- **Legitimate interest** (Article 6(1)(f)) — security and integrity of the license validation service
- **Consent** (Article 6(1)(a)) — AI analysis only runs when you explicitly trigger it

### EU representative (Article 27)

Yasser's studio is established outside the EU and currently does not have a designated Article 27 representative. We process minimal personal data (Pro purchaser email + hashed hardware ID; no telemetry, no profiling). We will appoint a representative as the user base grows.

EU data subjects can exercise their rights directly via `hello@getnaqi.com` — we respond within 30 days.

You have the right to access, rectify, erase, restrict, port, and object to processing of your personal data, and to lodge a complaint with your local supervisory authority.

---

## 10. Data Security

- All API communication uses HTTPS with certificate verification
- License keys are encrypted at rest using OS-level keychain services
- AI provider API keys are stored in the OS keychain, never in plain files
- Backup files are protected with owner-only filesystem permissions
- Naqi requires no elevated privileges — it runs as your user account
- Machine identifiers are SHA-256 hashed before leaving your device

---

## 11. Children's Privacy

Naqi is a developer tool intended for adults. It is not directed at children under 16. We do not knowingly collect data from children.

---

## 12. Changes to This Policy

If we make material changes to this policy, we will:

- Update the "Last updated" date at the top
- Post a notice on getnaqi.com
- For significant changes, notify Pro users via the email address associated with their Paddle purchase

---

## 13. Contact

For privacy questions or data requests:

**Email:** `hello@getnaqi.com`

We aim to respond within 7 business days for general inquiries and 30 days for formal GDPR data subject requests.

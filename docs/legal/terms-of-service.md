# Terms of Service

> **Naqi** (getnaqi.com) — Terms of Service
>
> Last updated: April 15, 2026
>
> Canonical version: [getnaqi.com/terms](https://getnaqi.com/terms). This file mirrors the live page for transparency and version history.

---

## Overview

These terms govern your use of Naqi, a desktop application for scanning, auditing, and cleaning up AI agent configurations (MCP servers, memories, skills, and config files). Naqi is developed and published by **Yasser's studio**. By installing or using Naqi, you agree to these terms.

We wrote these in plain English because our users are developers who value clarity over legalese.

---

## 1. What Naqi Is

Naqi is a desktop app (macOS, Windows, Linux) built with Tauri and Rust that:

- Scans your machine for AI client configurations (Claude Desktop, Claude Code, Cursor, VS Code, and others)
- Inventories MCP servers, memories, and skills across those clients
- Provides a health score and cleanup recommendations
- Optionally sends anonymized workspace summaries to an AI provider for AI-powered analysis (Pro tier)
- Helps you remove stale, broken, or duplicate configurations with backup and undo support

Naqi runs locally on your machine. It reads and (with your permission) modifies configuration files.

---

## 2. Tiers and Pricing

### Free Tier

Available to everyone, forever. Includes:

- Full config scanning across all supported AI clients
- Dashboard and inventory display
- Health score
- Local recommendations (staleness, broken servers, duplicates)
- Scan report export (JSON and Markdown)
- Manual config backup

### Pro Tier (Subscription)

- **Annual plan:** $59 per year
- **Monthly plan:** $7.99 per month
- **30-day unconditional refund** on your first subscription charge (annual or monthly). No trial at launch.

Unlocks:

- Live Token Hygiene (9 waste-pattern detectors, session threshold watcher, weekly TokenDiet digest)
- 30-day historical token analysis with per-session drill-down
- `naqi lint-prompt` CLI and Claude Code skill
- AI-powered cleanup recommendations via 6 providers (bring your own key)
- Memory contradiction detection
- Batch cleanup and cross-client diff/sync
- Diff preview (before/after) and undo/restore for all actions
- Automatic pre-cleanup backups
- Config profiles (capture, apply, export/import)
- Scheduled scans and full notification system
- Priority support and early access to new detectors

The Pro tier is a **recurring subscription**. Access to Pro features continues for as long as your subscription is active. You may cancel at any time; Pro features remain active until the end of the current billing cycle. After the cycle ends, you will be downgraded to the Free tier with all your data and history preserved.

**Price changes:** We may update the price of future renewals. If we do, we will notify active subscribers via email at least 60 days before the change takes effect. You may cancel before the new rate applies if you do not wish to continue.

---

## 3. License Grant (Proprietary Software)

Naqi is **proprietary, closed-source software**. All rights reserved by Yasser's studio. The source code is not publicly available.

### Free Tier license

You receive a personal, non-exclusive, non-transferable license to install and use the Free version of Naqi on your own devices. You may not redistribute the binary, decompile, reverse-engineer, or create derivative works.

### Pro license

When you purchase Naqi Pro, you receive a personal, non-exclusive, non-transferable license to use Pro features in the distributed binary on up to 3 devices simultaneously.

You may not redistribute, resell, lend, or share your license key.

---

## 4. Payment and Merchant of Record

All payments for Naqi Pro are processed by **Paddle**, which acts as the Merchant of Record. This means:

- Paddle handles payment processing, receipts, and invoicing
- Paddle handles global tax compliance (VAT, GST, sales tax)
- Naqi never sees or stores your payment information
- Refund requests are processed through Paddle

Your purchase is subject to [Paddle's Terms of Service](https://www.paddle.com/legal/terms) in addition to these terms.

---

## 5. Refund Policy

**30-day unconditional refund.** If you are not satisfied with Naqi Pro for any reason, you can request a full refund within 30 days of purchase. No questions asked.

To request a refund:

- Use the refund link in your Paddle purchase confirmation email, or
- Email `hello@getnaqi.com` with your order number

On refund, your license key will be revoked and Pro features will be disabled on your next license validation check (within 7 days).

---

## 6. Your Responsibilities

When using Naqi, you agree to:

- **Use it for personal or professional purposes.** Naqi is a productivity tool for managing your own AI workspace.
- **Keep your license key private.** Do not share, publish, or redistribute your Pro license key.
- **Provide your own AI provider API key for AI analysis.** Naqi does not include API credits. You are responsible for your own provider usage and costs.
- **Review cleanup actions before confirming.** Naqi shows you a diff preview before modifying any file. You are responsible for reviewing and approving changes.

You agree **not** to:

- Redistribute, resell, lend, or share Naqi Pro license keys
- Use Naqi to intentionally damage or corrupt configuration files on machines you do not own
- Decompile, reverse-engineer, or attempt to circumvent the license validation mechanism in the distributed binary

---

## 7. Config File Modifications and Risk

Naqi modifies AI client configuration files when you approve cleanup actions. This is the core function of the app. You should understand the following:

### What Naqi does to protect you

- **Backup before every change.** Before modifying any config file, Naqi creates a timestamped backup in `~/.naqi/backups/`.
- **Diff preview.** You see exactly what will change before confirming.
- **Undo support.** Every cleanup action can be reversed from the undo history.
- **Validation after changes.** Naqi re-parses modified files to verify they are valid. If validation fails, the change is automatically rolled back.
- **No auto-delete.** Nothing is removed without your explicit approval.

### What you accept

- Config file modifications can affect how your AI tools (Claude Desktop, Cursor, VS Code, etc.) behave. Removing an MCP server config, for example, means that AI client will no longer have access to that server.
- While Naqi backs up every file before modification, you are responsible for verifying that your AI tools work as expected after cleanup.
- Naqi's AI-powered recommendations are suggestions, not guarantees. The provider you choose may occasionally suggest removing something you still need. Always review recommendations before approving.

---

## 8. No Warranty

Naqi is provided **"AS IS"** and **"AS AVAILABLE"** without warranty of any kind, express or implied, including but not limited to warranties of merchantability, fitness for a particular purpose, and non-infringement.

Specifically:

- We do not guarantee that Naqi will correctly identify all stale, broken, or redundant configurations
- We do not guarantee that AI-powered recommendations will be accurate or appropriate for your setup
- We do not guarantee uninterrupted or error-free operation
- We do not guarantee compatibility with all AI client versions or config formats

---

## 9. Limitation of Liability

To the maximum extent permitted by law, Yasser's studio shall not be liable for any indirect, incidental, special, consequential, or punitive damages, including but not limited to:

- Loss of data or configuration
- Disruption of AI tool workflows
- Costs of re-configuring AI clients
- Loss of profits or business opportunity

Our total liability for any claim arising from your use of Naqi is limited to the amount you paid to us for your Naqi Pro subscription in the twelve months preceding the claim, or $0 if you are using the Free tier.

**In practical terms:** Naqi modifies config files, and despite our backup and undo safeguards, things can go wrong. By using Naqi's cleanup features, you accept this risk. We strongly recommend verifying your AI tools work correctly after any cleanup action.

---

## 10. License Key and Account Termination

### By you

You can stop using Naqi at any time. Delete the `~/.naqi/` directory and uninstall the app. If you want to deactivate your Pro license on a specific device, use Settings → License → Deactivate.

### By us

We may revoke your Pro license if:

- You receive a refund (license is automatically revoked)
- A chargeback is filed against your purchase
- You are found to be distributing or reselling license keys at scale

We will not revoke a license without cause. If a revocation is disputed, email `hello@getnaqi.com` and we will work with you to resolve it.

---

## 11. Third-Party Services

Naqi integrates with the following providers for optional, user-initiated AI analysis: **Anthropic, OpenAI, Google, xAI, Ollama, and OpenRouter**. You bring your own API key for each; the call goes from your machine to the provider directly, subject to that provider's terms.

Payment processing uses **Paddle** (Merchant of Record).

Naqi is not affiliated with, endorsed by, or sponsored by any AI client vendor (Anthropic, Cursor, Microsoft, etc.). All product names are trademarks of their respective owners.

---

## 12. Intellectual Property

- Naqi (including its source code, binary, name, logo, brand assets, and documentation) is the intellectual property of Yasser's studio. **All rights reserved.**
- Naqi is proprietary, closed-source software. The Naqi name, logo, and brand assets are proprietary.
- User-generated data (your config files, scan results, backups) remains yours. Naqi claims no ownership over your data.

---

## 13. Changes to These Terms

We may update these terms from time to time. When we do:

- We will update the "Last updated" date at the top
- For material changes, we will provide **30 days' notice** before the new terms take effect
- Notice will be posted on getnaqi.com and, for significant changes, emailed to Pro users via their Paddle email address
- Your continued use of Naqi after the notice period constitutes acceptance of the updated terms

---

## 14. Governing Law

These terms are governed by the laws of **France**. Any disputes will be resolved in the courts of France, without prejudice to any mandatory consumer protections in your country of residence.

---

## 15. Severability

If any part of these terms is found to be unenforceable, the remaining parts stay in effect.

---

## 16. Contact

**Yasser's studio** — publisher of Naqi.

- Legal and general inquiries: `hello@getnaqi.com`
- Privacy questions: see the [Privacy Policy](privacy-policy.md) or email `hello@getnaqi.com`
- Security vulnerabilities: `security@getnaqi.com` (do not open public GitHub issues for security reports)

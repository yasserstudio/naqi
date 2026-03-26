# Terms of Service

> **Naqi** (naqi.dev) — Terms of Service
>
> Last updated: March 26, 2026

---

## Overview

These terms govern your use of Naqi, a desktop application for scanning, auditing, and cleaning up AI agent configurations (MCP servers, memories, skills, and related config files). By using Naqi, you agree to these terms.

We wrote these in plain English because our users are developers who value clarity over legalese.

---

## 1. What Naqi Is

Naqi is a desktop app built with Tauri and Rust that:

- Scans your machine for AI client configurations (Claude Desktop, Claude Code, Cursor, VS Code, and others)
- Inventories MCP servers, memories, and skills across those clients
- Provides a health score and cleanup recommendations
- Optionally sends anonymized workspace summaries to the Anthropic Claude API for AI-powered analysis (Pro tier)
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

### Pro Tier ($14.99 one-time)

A one-time purchase that unlocks:
- AI-powered cleanup recommendations via the Claude API
- Memory contradiction detection
- One-click and batch cleanup actions
- Diff preview (before/after)
- Undo and restore
- Automatic pre-cleanup backups

The Pro tier is a **perpetual license**. You pay once and keep access to all current and future Pro features, including updates. There is no subscription, no annual renewal, and no expiration.

### Future Team Tier

A per-seat annual subscription for teams may be offered in the future. Existing Pro license holders will not be affected — individual Pro licenses remain perpetual regardless of any future plan changes.

---

## 3. License Grant

### Source code (MIT License)

Naqi's source code is released under the MIT License. You are free to use, copy, modify, merge, publish, distribute, sublicense, and sell copies of the source code, subject to the MIT License terms included in the repository.

### Pro features in the distributed binary

The pre-built, signed Naqi binary includes Pro features that are gated by a license key. When you purchase Naqi Pro, you receive a personal, non-exclusive, non-transferable license to use Pro features in the distributed binary on up to 3 devices simultaneously.

**What this means in practice:**
- The source code is always open and free (MIT)
- If you can build from source and configure your own API key, you can access equivalent functionality without a license
- The Pro license pays for the convenience of a packaged, signed, auto-updating binary with Pro features enabled out of the box
- You may not redistribute, resell, or share your license key

---

## 4. Payment and Merchant of Record

All payments for Naqi Pro are processed by **Lemon Squeezy**, which acts as the Merchant of Record. This means:

- Lemon Squeezy handles payment processing, receipts, and invoicing
- Lemon Squeezy handles global tax compliance (VAT, GST, sales tax)
- Naqi never sees or stores your payment information
- Refund requests are processed through Lemon Squeezy

Your purchase is subject to Lemon Squeezy's [Terms of Service](https://www.lemonsqueezy.com/terms) in addition to these terms.

---

## 5. Refund Policy

**30-day unconditional refund.** If you are not satisfied with Naqi Pro for any reason, you can request a full refund within 30 days of purchase. No questions asked.

To request a refund:
- Use the refund link in your Lemon Squeezy purchase confirmation email, or
- Email legal@naqi.dev with your order number

On refund, your license key will be revoked and Pro features will be disabled on your next license validation check (within 7 days).

---

## 6. Your Responsibilities

When using Naqi, you agree to:

- **Use it for personal or professional purposes.** Naqi is a productivity tool for managing your own AI workspace.
- **Keep your license key private.** Do not share, publish, or redistribute your Pro license key.
- **Provide your own Claude API key for AI analysis.** Naqi does not include API credits. You are responsible for your own Anthropic API usage and costs.
- **Review cleanup actions before confirming.** Naqi shows you a diff preview before modifying any file. You are responsible for reviewing and approving changes.

You agree **not** to:
- Redistribute or resell Naqi Pro license keys
- Use Naqi to intentionally damage or corrupt configuration files on machines you do not own
- Reverse engineer the license validation mechanism for the purpose of circumventing it in the distributed binary (note: the source code is MIT-licensed, so reading and modifying the source is always permitted)

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
- Naqi's AI-powered recommendations are suggestions, not guarantees. The Claude API may occasionally suggest removing something you still need. Always review recommendations before approving.

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

To the maximum extent permitted by law, Naqi and its author shall not be liable for any indirect, incidental, special, consequential, or punitive damages, including but not limited to:

- Loss of data or configuration
- Disruption of AI tool workflows
- Costs of re-configuring AI clients
- Loss of profits or business opportunity

Our total liability for any claim arising from your use of Naqi is limited to the amount you paid for Naqi Pro ($14.99), or $0 if you are using the Free tier.

**In practical terms:** Naqi modifies config files, and despite our backup and undo safeguards, things can go wrong. By using Naqi's cleanup features, you accept this risk. We strongly recommend verifying your AI tools work correctly after any cleanup action.

---

## 10. License Key and Account Termination

### By you

You can stop using Naqi at any time. Delete the `~/.naqi/` directory and uninstall the app. If you want to deactivate your Pro license on a device, use Settings > License > Deactivate, or visit naqi.dev/manage.

### By us

We may revoke your Pro license if:
- You receive a refund (license is automatically revoked)
- A chargeback is filed against your purchase
- You are found to be distributing or reselling license keys at scale

We will not revoke a license without cause. If a revocation is disputed, email legal@naqi.dev and we will work with you to resolve it.

---

## 11. Third-Party Services

Naqi integrates with:

| Service | Purpose | Their terms apply |
|---------|---------|-------------------|
| **Anthropic Claude API** | AI-powered analysis (Pro tier, user-initiated) | [Anthropic API Terms](https://www.anthropic.com/api-terms) |
| **Lemon Squeezy** | Payment processing and license management | [Lemon Squeezy Terms](https://www.lemonsqueezy.com/terms) |

Naqi is not affiliated with, endorsed by, or sponsored by Anthropic or any AI client vendor (Claude, Cursor, VS Code, etc.). All product names are trademarks of their respective owners.

---

## 12. Intellectual Property

- The Naqi source code is MIT-licensed. See the LICENSE file in the repository.
- The Naqi name, logo, and brand assets are proprietary. The MIT license applies to the code, not the brand.
- User-generated data (your config files, scan results, backups) remains yours. Naqi claims no ownership over your data.

---

## 13. Changes to These Terms

We may update these terms from time to time. When we do:

- We will update the "Last updated" date at the top
- For material changes, we will provide **30 days' notice** before the new terms take effect
- Notice will be posted on naqi.dev and, for significant changes, emailed to Pro users via their Lemon Squeezy email address
- Your continued use of Naqi after the notice period constitutes acceptance of the updated terms

---

## 14. Governing Law

These terms are governed by the laws of [Jurisdiction TBD]. Any disputes will be resolved in the courts of [Jurisdiction TBD].

---

## 15. Severability

If any part of these terms is found to be unenforceable, the remaining parts stay in effect.

---

## 16. Contact

For questions about these terms:

**Email:** legal@naqi.dev

For privacy-related questions, see our [Privacy Policy](privacy-policy.md) or email privacy@naqi.dev.

For security vulnerabilities, email security@naqi.dev (do not open public GitHub issues for security reports).

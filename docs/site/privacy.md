---
title: "Privacy"
slug: privacy
description: "Naqi's privacy policy in plain English. What the website collects, what the app collects, what we never send anywhere — written by a developer, not a lawyer."
url: /privacy
type: page
last_updated: 2026-04-07
---

> **Page intent (per [site-architecture.md](../business/site-architecture.md)):** required Phase 1 trust page. Must read as a brand-strengthening asset, not a legal CYA. Plain English. Verifiable technical claims with actual file paths and code references. No "we may share with third-party affiliates" boilerplate.

# Privacy

This page is short on purpose. There isn't much to write about, and that's the point.

## TL;DR

| Where you are | What we collect |
|---|---|
| **getnaqi.com** (this website) | Anonymous, cookieless visit counts via Plausible. No personal data, no fingerprinting, no cross-site tracking. The dashboard is **public** at [plausible.io/getnaqi.com](https://plausible.io/getnaqi.com). |
| **The Naqi desktop app** | **Nothing.** No telemetry, no analytics, no crash reports, no usage tracking, no phone-home. The app makes zero network requests by default. |
| **Pro AI recommendations** (optional) | When you explicitly run an AI analysis, an anonymized summary of your scan goes to whichever AI provider you picked — and only that provider. Naqi never sees it. |
| **The waitlist or newsletter** (optional) | Your email address, sent only to Buttondown. We unsubscribe you with one click and never share the list. |

That's the whole policy. The rest of this page explains each row in detail.

---

## The website (getnaqi.com)

The marketing site uses [Plausible Analytics](https://plausible.io) — a privacy-first analytics tool that runs on EU servers, uses no cookies, and never collects personal data.

What Plausible measures:
- How many people visited each page
- Which page they came from (referrer)
- What country they're in (resolved from IP, then the IP is discarded)
- Whether they clicked a download button

What Plausible **does not** measure:
- Your IP address (used for geolocation, then discarded — never stored)
- Cookies (none are set)
- Fingerprints (no canvas, no font, no hardware fingerprinting)
- Mouse movements, scroll depth, or heatmaps
- Anything that could identify you across sessions or devices

Because Plausible uses no cookies and stores no personal data, **there is no cookie consent banner on this site**. There is nothing to consent to. (This is also why the site is faster than 99% of competitor sites — no GDPR banner script, no third-party tag manager, no Facebook Pixel.)

**The Plausible dashboard is public.** Anyone can see exactly what we see. It's at [plausible.io/getnaqi.com](https://plausible.io/getnaqi.com). If we ever start collecting more than what's listed here, you'll see it on that dashboard before you read about it on this page.

---

## The desktop app

The Naqi desktop app does not have analytics, telemetry, crash reporting, error logging-to-server, or any other phone-home behavior. **By default, it does not make any network requests.**

You can verify this in three ways:

1. **Inspect the network panel.** Open Naqi, run a scan, browse the dashboard, run cleanups — do everything. Then check Little Snitch, LuLu, or any network monitor. Naqi will show zero outbound requests.
2. **Read the source.** The Tauri capability config in `src-tauri/tauri.conf.json` restricts the app's network permissions. The HTTP plugin is only loaded when Pro AI features are explicitly invoked.
3. **Run with the network disabled.** Naqi works fully offline. Scans, cleanups, backups, undo, dashboard, and all free-tier features run without an internet connection. Test it.

The only files Naqi reads are the AI client config files listed on the [How it works](/how-it-works) page. The only files Naqi writes are:

- `~/.naqi/` — your settings, scan history, backups, undo stack, and license key
- The specific config files you explicitly cleaned (in-place edits, with backups in `~/.naqi/backups/` first)

That's it. Naqi has no awareness of your project files, source code, browser data, or anything outside that list.

---

## Pro AI recommendations (the one network request)

If you upgrade to Pro and enable AI recommendations, Naqi sends an anonymized summary of your workspace inventory to whichever AI provider you chose:

| Provider | Where the data goes |
|---|---|
| **Anthropic** | api.anthropic.com |
| **OpenAI** | api.openai.com |
| **Google Gemini** | generativelanguage.googleapis.com |
| **xAI Grok** | api.x.ai |
| **Ollama** | `localhost` (your Mac, fully offline — never leaves the device) |
| **OpenRouter** | openrouter.ai |

**Naqi is not in the loop.** Your data goes from the desktop app directly to the provider you picked, using your own API key. We do not proxy, log, store, or see any of it.

Before sending, the summary is anonymized:
- Command paths are reduced to basenames (`/Users/yasser/work/secret-project/server.js` → `server.js`)
- Project names are stripped from memory content
- API keys, tokens, and credentials are removed entirely (never sent under any circumstance)
- File paths in skill descriptions are redacted to client-relative paths
- The full source for the anonymizer is in `src-tauri/src/analyzer/anonymize.rs` — read it

If you want zero network exposure even for AI recommendations, **pick Ollama as your provider.** It runs locally on your Mac, never makes an outbound request, and is free.

---

## API keys and credentials

When you configure an AI provider key (or a GitHub PAT for skill update checks), Naqi stores it in your **macOS Keychain** via the system `keyring` API. It is never:

- Written to a plain-text file
- Logged (not in stdout, not in the rotating log file at `~/Library/Logs/app.naqi.desktop/naqi.log`)
- Sent anywhere except to the provider you authenticated against, when you explicitly invoke an AI feature
- Visible to other apps (Keychain enforces per-app access)

Naqi only displays a masked preview of the key in Settings (first 6 + `••••••` + last 4 characters). The full key only exists in memory during an AI request.

---

## Backups

Backups stay on your Mac. Naqi creates timestamped copies of your config files in `~/.naqi/backups/` before any modification. They are never uploaded, synced, or sent anywhere. You can export them as a ZIP if you want to back them up off-machine — that's a manual action you trigger.

Default retention is 90 days. Configurable in Settings.

---

## Crash reports

There are none. Naqi does not collect crash reports or send error logs to any server. If the app crashes, the crash data stays on your Mac in the standard macOS crash log location (`~/Library/Logs/DiagnosticReports/`). You can read it; we cannot.

If you want to send us a crash log to help diagnose a bug, you can email it to [yasser@getnaqi.com](mailto:yasser@getnaqi.com). We will only ever look at logs you explicitly send.

---

## The waitlist and newsletter (optional)

If you sign up for the pre-launch waitlist or the post-install newsletter, your email address is stored in [Buttondown](https://buttondown.email). Buttondown is a privacy-respecting email provider — they don't track opens with pixel beacons by default and they don't sell or share lists.

You can unsubscribe with one click from any email. Unsubscribing removes you from the list immediately.

We will never:
- Sell your email address
- Share the list with anyone
- Subscribe you to anything else
- Email you more than the cadence promised in the welcome email

---

## Lemon Squeezy (Pro purchases)

If you buy Naqi Pro, the checkout runs through [Lemon Squeezy](https://lemonsqueezy.com), which acts as the merchant of record. Lemon Squeezy handles the payment, the receipt, the VAT/sales tax, and the refund flow. Their privacy policy applies to that transaction.

Naqi receives only what's needed to issue your license key:
- Your email address (to deliver the license)
- The order ID (to validate the license)
- The country code (for tax compliance)

Naqi does not receive or store your credit card number, billing address, or any payment data.

---

## Your rights

You have the right to:

- **Know what we have** — for the website, it's nothing personal. For the app, it's nothing at all. For email subscribers, it's your email address.
- **Delete it** — unsubscribe removes you from any list. Uninstalling Naqi removes everything in `~/.naqi/`.
- **Export it** — backups can be exported as ZIP from the Backups page. Email lists are exportable on request.
- **Object** — email [yasser@getnaqi.com](mailto:yasser@getnaqi.com) and we'll talk like humans.

These rights apply regardless of where you live. We extend them globally because they're the right defaults, not because a regulation forces us to.

---

## Changes to this policy

If this policy ever changes, the change will be announced in the [changelog](/changelog) and at the top of the [next monthly newsletter](mailto:hi@getnaqi.com). The previous version of this page will always be available in the [git history](https://github.com/yasserstudio/naqi/commits/main/docs/site/privacy.md).

This is unusual for a privacy policy. Most companies update privacy policies silently. We won't, because the whole point of this page is that you can trust what's on it.

---

## Contact

Email [yasser@getnaqi.com](mailto:yasser@getnaqi.com). One person reads it. Reply expected within 48 hours.

For security disclosures, see [SECURITY.md](https://github.com/yasserstudio/naqi/blob/main/SECURITY.md).

---

*This policy was last updated on April 7, 2026. It is intentionally short. If a future version is longer, something has gone wrong.*

<div align="center">

<img src="brand-assets/naqi-icon-transparent.png" alt="Naqi" width="140" />

# naqi

**Cleaning up your AI workspace saves you tokens.**

Every MCP server, memory, and skill you're not using burns context on every request. Naqi scans every AI client, finds what's dead, stale, or contradictory — then cleans it up safely.

[![Public beta](https://img.shields.io/badge/status-public%20beta-f5a623.svg)](VERSIONING.md)
[![Proprietary](https://img.shields.io/badge/license-proprietary-red.svg)](LICENSE)
[![macOS](https://img.shields.io/badge/macOS-12%2B-40bfa0.svg)]()
[![Windows](https://img.shields.io/badge/Windows-10%2B-40bfa0.svg)]()
[![Linux](https://img.shields.io/badge/Linux-40bfa0.svg)]()
[![Built with Tauri](https://img.shields.io/badge/built%20with-Tauri%20v2-40bfa0.svg)]()
[![Binary Size](https://img.shields.io/badge/binary-~10MB-40bfa0.svg)]()

[Download](#install) · [Changelog](CHANGELOG.md) · [Versioning](VERSIONING.md) · [getnaqi.com](https://getnaqi.com)

> **Heads up:** Naqi is in public beta (`1.0.0-beta.N`). The app is stable enough for daily use — Safe Mode, versioned backups, and the full undo stack have your back — but expect UI refinements and occasional breaking changes between beta bumps. Read the [CHANGELOG](CHANGELOG.md) before upgrading.

</div>

<br />

<!-- TODO: Replace with actual dashboard screenshot before public launch -->
<!-- <p align="center">
  <img src="docs/assets/screenshot-dashboard.png" alt="Naqi Dashboard" width="800" />
</p> -->

## The problem

Every MCP server you connected for a one-off task? Still there. Still authenticated.
Every memory Claude saved about a project you shipped six months ago? Still shaping responses.
Every skill you installed from a blog post? Still loaded. Never triggered.

There is no "Manage Connected Apps" for your AI workspace. No usage tracking. No expiry. No cleanup.

You just accumulate.

<br />

## See everything. Clean what's broken. Undo anything.

<table>
<tr>
<td width="33%" valign="top">

### One scan, every client

Finds MCP servers, memories, skills, and config files across **10 AI clients** — Claude Desktop, Claude Code, Cursor, VS Code, Windsurf, GitHub Copilot, JetBrains, Zed, Amp, and Kiro. 3 seconds. 100% local.

</td>
<td width="33%" valign="top">

### Smart recommendations

AI-powered analysis surfaces what's stale, duplicated, contradictory, or unused — with **explanations, not just warnings**. Ranked by impact.

</td>
<td width="33%" valign="top">

### Safe cleanup

Every action backed up before changes. **Diff preview. Full undo stack.** Nothing auto-deleted. Ever.

</td>
</tr>
</table>

<br />

## How it works

```
1. Scan       Naqi reads config files locally. No accounts, no cloud, no permissions.
              Takes 3 seconds.

2. Review     See your full AI inventory in one dashboard. Health score shows
              where you stand. Recommendations show what needs attention and why.

3. Clean      Apply recommended cleanups with one click. Preview every change
              before confirming. Everything backed up. Full undo anytime.
```

> Scanning is 100% local. The Pro tier sends an anonymized summary to your chosen AI provider — Anthropic, OpenAI, Gemini, Grok, Ollama (fully offline), or OpenRouter. API keys stripped, paths redacted, no PII. You bring your own key.

<br />

## Install

### Direct download

[Latest release](https://github.com/yasserstudio/naqi/releases/latest):

- **macOS** — `.dmg` (Apple Silicon + Intel universal, signed + notarized)
- **Windows** — `.msi` (x64, unsigned during beta — see first-run guide)
- **Linux** — `.deb` (Debian/Ubuntu) or `.AppImage` (any distro)

Each release is pinned to a SHA-256 checksum on the release page.

### Homebrew (macOS)

```bash
brew install yasserstudio/naqi/naqi
```

### First-run warnings?

macOS may show a Gatekeeper prompt on first launch; Windows will show a SmartScreen warning during the beta (code signing deferred until stable). **Walkthrough at [getnaqi.com/install](https://getnaqi.com/install)** — takes 30 seconds.

<br />

## Free vs Pro

| | Free | Pro · $59/yr or $7.99/mo |
|:---|:---:|:---:|
| Scan 10 AI clients | ✓ | ✓ |
| Health score and dashboard | ✓ | ✓ |
| Memory and skill audit | ✓ | ✓ |
| Manual cleanup + undo | ✓ | ✓ |
| Versioned backups (browse, preview, compare, export ZIP) | ✓ | ✓ |
| Safe Mode and change log | ✓ | ✓ |
| Exportable reports | ✓ | ✓ |
| 7-day Token Hygiene snapshot | ✓ | ✓ |
| **One-click backup restore + ZIP import** | — | ✓ |
| **Live Token Hygiene** (9 detectors + session watcher + weekly digest) | — | ✓ |
| **30-day historical token analysis** | — | ✓ |
| **Prompt waste detection** | — | ✓ |
| **Batch cleanup + cross-client sync** | — | ✓ |
| **AI-powered recommendations** (6 providers, BYO key) | — | ✓ |
| **Contradiction detection** (negation-aware) | — | ✓ |
| **Config profiles + scheduled scans** | — | ✓ |

<p align="center"><sub>30-day unconditional refund · cancel anytime · 3 devices per subscription</sub></p>

<br />

## Features

<table>
<tr>
<td width="50%" valign="top">

- **Dashboard** — health score, trend chart, daily brief, attention cards, FAB scan trigger
- **Per-request token cost** — see exactly how many tokens every MCP server, memory, and skill costs on every AI turn. Dashboard widget + per-artifact chip on every list (estimated / active badge). Cleanup recs quote the savings ("Remove to save ~200 tokens / request")
- **Security audit** — dedicated `/security` view with Critical → Warning → Info bands, capability detection (filesystem / network / shell), shape-aware secret detection (Anthropic, OpenAI, Stripe, GitHub, Slack, HuggingFace, AWS, JWT, etc.), cross-server duplicate-secret detection, inline actions (Open config, Copy rotation hint, Suppress). Markdown export
- **System tray** — menu bar icon with liquid-glass rounded panel, 2×3 stat grid incl. per-request token cost, quick scan, health at a glance
- **Servers** — full MCP inventory, health checks, transport badges, cross-client diff, sort-by-tokens
- **Memories** — browse by project, enhanced contradiction detection (negation-aware), bulk actions, archive
- **Profiles** — capture, apply, export/import configs across clients
- **Backups** — versioned with SHA-256 dedup, auto-backup on scan, browser page with diff viewer, ZIP export (free) · one-click restore + ZIP import (Pro)

</td>
<td width="50%" valign="top">

- **Visual config editor** — inline JSON editing with line numbers, validation, format, `Cmd+S` save
- **Skill inventory** — enriched tiles with description preview, source repo slug, absolute last-modified date, commit SHA, installed/updated dates. Per-skill context menu: Open in Editor, Show in Finder, Copy Name/Install Path/Source Repo, Check for Update, Set Source Repo, Remove
- **GitHub PAT for skill updates** — optional personal access token (OS keychain) raises rate limit from 60 to 5,000 req/hr for skill update checks. Configure in Settings > Skills
- **Prompt waste detection** — flags expensive prompt patterns (dive-deep openers, log pastes, short prompts) before they burn tokens
- **Workspace share card** — one-click social share of your health score + stats. Copy summary, share on X, share on Reddit. Anonymize-by-default
- **Safe mode** — block all modifications, per-client locking, `Cmd+Shift+M` toggle
- **Global search** — `Cmd+K` across servers, memories, skills, configs
- **Keyboard-first** — `Cmd+R` rescan, `Cmd+Z` undo, `Cmd+1-5` nav, `?` shortcuts
- **OS keychain** — API keys and GitHub PAT stored in OS keychain (macOS Keychain, Windows Credential Manager, or Linux Secret Service), never plaintext files
- **Native desktop (macOS-first)** — overlay titlebar, resizable sidebar, tray on close, WCAG AA accessible

</td>
</tr>
</table>

<br />

## Tech stack

| Layer | Stack |
|:------|:------|
| **Frontend** | React 19 · TypeScript · Tailwind CSS v4 · Vite · shadcn/ui |
| **Backend** | Rust · Tauri v2 · serde · chrono · reqwest · thiserror |
| **AI** | 6 providers: Anthropic (Sonnet 4.6) · OpenAI (GPT-5.4 Nano) · Google Gemini (2.5 Flash) · xAI Grok · Ollama (local) · OpenRouter (any model). Per-provider model selector. Optional — local analysis works without API key |
| **Design** | Dark-mode-first · Inter + JetBrains Mono · macOS HIG-aligned |
| **Binary** | ~10MB (Tauri uses system webview — no bundled Chromium) |

<br />

## Development

<details>
<summary><b>Prerequisites</b></summary>

- Rust 1.75+ (`rustup`)
- Node.js 20+ (`nvm` or `brew install node@20`)
- pnpm 10+ (`npm install -g pnpm`)
- Xcode Command Line Tools (`xcode-select --install`)

</details>

```bash
git clone https://github.com/yasserstudio/naqi.git
cd naqi
pnpm install
pnpm tauri dev
```

```bash
pnpm check                    # lint + typecheck + frontend tests
pnpm test:e2e                 # playwright e2e tests (27 tests)
cd src-tauri && cargo test    # rust tests
```

<br />

## Feedback & Issues

Bug reports and feature requests are welcome via [GitHub Issues](https://github.com/yasserstudio/naqi/issues). Naqi is proprietary software — see [CONTRIBUTING.md](CONTRIBUTING.md).

## Security

See [SECURITY.md](SECURITY.md). Report vulnerabilities via email, not public issues.

<br />

<div align="center">

© 2026 Yasser's studio · Proprietary · [getnaqi.com](https://getnaqi.com)

<br />

Built by

<a href="https://yasser.studio">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="brand-assets/yasser-studio-logo-white.svg" />
    <source media="(prefers-color-scheme: light)" srcset="brand-assets/yasser-studio-logo-official.png" />
    <img src="brand-assets/yasser-studio-logo-official.png" alt="Yasser's Studio" height="30" />
  </picture>
</a>

</div>

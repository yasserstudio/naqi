<div align="center">

<img src="brand-assets/naqi-icon-transparent.png" alt="Naqi" width="140" />

# naqi

**The AI workspace cleaner.**

Scan, audit, and clean up MCP servers, memories, and skills across every AI client.

[![License: MIT](https://img.shields.io/badge/license-MIT-40bfa0.svg)](LICENSE)
[![macOS](https://img.shields.io/badge/macOS-12%2B-40bfa0.svg)]()
[![Built with Tauri](https://img.shields.io/badge/built%20with-Tauri%20v2-40bfa0.svg)]()
[![Binary Size](https://img.shields.io/badge/binary-~12MB-40bfa0.svg)]()

[Download](https://naqi.app) · [Changelog](CHANGELOG.md) · [Contributing](CONTRIBUTING.md)

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

You just accumulate. **Naqi fixes that.**

<br />

## What Naqi does

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

> Scanning is 100% local. The Pro tier sends an anonymized summary to Claude's API for analysis — API keys stripped, paths redacted, no PII. You supply your own API key.

<br />

## Install

### macOS (Homebrew)

```bash
brew tap yasserstudio/naqi
brew install --cask naqi
```

### Direct download

[macOS (Apple Silicon)](https://github.com/yasserstudio/naqi/releases/latest) · [macOS (Intel)](https://github.com/yasserstudio/naqi/releases/latest)

Windows and Linux coming soon.

<br />

## Free vs Pro

| | Free | Pro · $14.99 one-time |
|:---|:---:|:---:|
| Config scanning (10 AI clients) | **Yes** | **Yes** |
| Dashboard and health score | **Yes** | **Yes** |
| Memory and skill audit | **Yes** | **Yes** |
| Exportable reports | **Yes** | **Yes** |
| AI-powered recommendations | — | **Yes** |
| Contradiction detection | — | **Yes** |
| One-click cleanup with batch mode | — | **Yes** |
| Diff preview and undo | — | **Yes** |

<p align="center"><sub>One-time purchase · No subscription · Works on 3 devices</sub></p>

<br />

## Features

<table>
<tr>
<td width="50%" valign="top">

- **Dashboard** — health score, trend chart, daily brief, attention cards, FAB scan trigger
- **System tray** — menu bar icon with stat grid panel, quick scan, health at a glance
- **Servers** — full MCP inventory, health checks, transport badges, cross-client diff
- **Memories** — browse by project, enhanced contradiction detection (negation-aware), bulk actions, archive
- **Profiles** — capture, apply, export/import configs across clients
- **Backups** — versioned with SHA-256 dedup, auto-backup on scan, ZIP export/import, one-click restore, dedicated browser page with diff viewer

</td>
<td width="50%" valign="top">

- **Visual config editor** — inline JSON editing with line numbers, validation, format, `Cmd+S` save
- **Security audit** — detects insecure HTTP, secrets in args/URLs, credential sprawl
- **CLI companion** — `naqi scan`, `naqi score`, `naqi clean` for terminal workflows
- **Safe mode** — block all modifications, per-client locking, `Cmd+Shift+M` toggle
- **Global search** — `Cmd+K` across servers, memories, skills, configs
- **Keyboard-first** — `Cmd+R` rescan, `Cmd+Z` undo, `Cmd+1-8` nav, `?` shortcuts
- **OS keychain** — API keys stored in macOS Keychain, not plaintext files
- **Native macOS** — overlay titlebar, resizable sidebar, tray on close, WCAG AA accessible

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
| **Binary** | ~12MB (Tauri uses system webview — no bundled Chromium) |

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
pnpm test:e2e                 # playwright e2e tests (12 tests)
cd src-tauri && cargo test    # rust tests
```

<br />

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). New AI client parsers are the most impactful contribution — check [good first issues](https://github.com/yasserstudio/naqi/labels/good%20first%20issue).

## Security

See [SECURITY.md](SECURITY.md). Report vulnerabilities via email, not public issues.

<br />

<div align="center">

[MIT License](LICENSE) · [naqi.app](https://naqi.app)

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

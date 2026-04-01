# naqi

The AI workspace cleaner.

<!-- TODO: Add hero screenshot before public launch -->
<!-- ![Naqi Dashboard](docs/assets/screenshot-dashboard.png) -->

## The problem

Every MCP server you connected for a one-off task? Still there. Still authenticated.
Every memory Claude saved about a project you shipped six months ago? Still shaping responses.
Every skill you installed from a blog post? Still loaded. Never triggered.

There is no "Manage Connected Apps" for your AI workspace. No usage tracking. No expiry. No cleanup.

You just accumulate. Naqi fixes that.

## What Naqi does

- **One scan, every AI client** — finds MCP servers, memories, skills, and config files across Claude Desktop, Claude Code, Cursor, VS Code, and Windsurf. 3 seconds. 100% local.
- **Smart recommendations** — AI-powered analysis surfaces what's stale, duplicated, contradictory, or unused. With explanations, not just warnings.
- **Safe cleanup** — every action backed up before changes. Diff preview. Full undo stack. Nothing auto-deleted. Ever.

## Install

### macOS (Homebrew)

```bash
brew tap yasserstudio/naqi
brew install --cask naqi
```

### Download

[macOS (Apple Silicon)](https://github.com/yasserstudio/naqi/releases/latest) · [macOS (Intel)](https://github.com/yasserstudio/naqi/releases/latest)

Windows and Linux coming soon.

## How it works

1. **Scan** — Naqi reads config files locally. No accounts, no cloud, no permissions to grant. Takes 3 seconds.
2. **Review** — see your full AI inventory in one dashboard. Health score shows where you stand. Recommendations show what needs attention and why.
3. **Clean** — apply recommended cleanups with one click. Preview every change before confirming. Everything backed up. Full undo anytime.

Scanning is 100% local. The Pro tier sends an anonymized summary to Claude's API for analysis — API keys stripped, paths redacted, no PII. You supply your own API key.

## Free vs Pro

| | Free | Pro ($14.99 one-time) |
|---|---|---|
| Config scanning (5 AI clients) | Yes | Yes |
| Dashboard and health score | Yes | Yes |
| Memory and skill audit | Yes | Yes |
| Exportable reports | Yes | Yes |
| AI-powered recommendations | — | Yes |
| Contradiction detection | — | Yes |
| One-click cleanup with batch mode | — | Yes |
| Diff preview and undo | — | Yes |

One-time purchase. No subscription. Works on 3 devices.

## Features

- **Dashboard** — health score with category-based stacked bar, 7-day trend chart, daily brief, attention cards
- **System tray** — persistent menu bar icon with glass-styled status panel, quick scan, health score at a glance
- **Servers page** — full MCP server inventory, health checks (binary/HTTP), transport badges, cross-client comparison
- **Memories page** — browse Claude Code memories by project, contradiction detection, bulk actions, archive
- **Config profiles** — capture, apply, export/import server configurations across clients
- **Config history** — monitors config file changes over time with line-level diff stats
- **Global search** (`Cmd+K`) — search across servers, memories, skills, and configs
- **Keyboard-first** — `Cmd+R` rescan, `Cmd+Z` undo, `Cmd+Shift+S` toggle sidebar, `Cmd+1-8` page nav, `?` shortcuts
- **Native macOS** — overlay titlebar, resizable sidebar, hides to tray on close, state restoration
- **Accessible** — WCAG AA contrast, focus rings, status icons (not color-only), reduced motion support

## Tech stack

- **Frontend:** React 19, TypeScript, Tailwind CSS v4, Vite, shadcn/ui
- **Backend:** Rust (Tauri v2), serde, chrono, reqwest, thiserror
- **AI:** Multi-provider — Claude Sonnet 4.6 + OpenAI GPT-4.1 Mini (optional, local analysis works without API key)
- **Design:** Dark-mode-first, Inter + JetBrains Mono, macOS HIG-aligned
- **Binary:** ~12MB (Tauri uses system webview, no bundled Chromium)

## Development

### Prerequisites

- Rust 1.75+ (`rustup`)
- Node.js 20+ (`nvm` or `brew install node@20`)
- pnpm 9+ (`npm install -g pnpm`)
- Xcode Command Line Tools (`xcode-select --install`)

### Setup

```bash
git clone https://github.com/yasserstudio/naqi.git
cd naqi
pnpm install
pnpm tauri dev
```

### Run checks

```bash
pnpm check                    # lint + typecheck + frontend tests
cd src-tauri && cargo test    # Rust tests
```

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). New AI client parsers are the most impactful contribution — check [good first issues](https://github.com/yasserstudio/naqi/labels/good%20first%20issue).

## Security

See [SECURITY.md](SECURITY.md). Report vulnerabilities via email, not public issues.

## License

[MIT](LICENSE)

## Links

- [naqi.dev](https://naqi.dev) — download and docs
- [Changelog](CHANGELOG.md)

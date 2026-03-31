# naqi نقي

> The AI workspace cleaner — scan, audit, and clean up your MCP servers, memories, and skills.

<!-- TODO: Replace with actual screenshot before public launch -->
<!-- ![Naqi Dashboard](docs/assets/screenshot-dashboard.png) -->

## The Problem

AI tools encourage you to connect MCP servers, save memories, and install skills — but provide no way to clean up afterward. Over time, you accumulate stale servers, contradictory memories, and unused skills that silently degrade your AI experience.

There is no tool to answer: *"What AI stuff do I have, what's actually used, and what should I remove?"*

Naqi answers that.

## What Naqi Does

- **Scans** Claude Desktop, Claude Code, Cursor, VS Code, and Windsurf configs in one view
- **Detects** stale servers, contradictory memories, unused skills, and config inconsistencies
- **Cleans up** with AI-powered recommendations (Claude + OpenAI), safe one-click removal, and full undo support

## Install

### macOS (Homebrew)

```bash
brew tap yasserstudio/naqi
brew install --cask naqi
```

### Download

[macOS (Apple Silicon)](https://github.com/yasserstudio/naqi/releases/latest) · [macOS (Intel)](https://github.com/yasserstudio/naqi/releases/latest)

> Windows and Linux coming in v0.3.0.

## Features

- **Dashboard** with category-based health bar, 7-day trend chart, and editorial card grid
- **Menu bar tray** — persistent icon with glass-styled status panel (health score, quick scan, fix issues)
- **Resizable sidebar** — drag to resize (180–400px), double-click to reset, width persisted
- **Global search** (`Cmd+K`) across servers, memories, skills, and configs with recent queries
- **Keyboard-first** — `Cmd+Z` undo, `Cmd+R` rescan, `Cmd+Shift+S` toggle sidebar, `Cmd+1-8` page nav
- **macOS native** — overlay titlebar, collapsible sidebar, hides to tray on close, state restoration
- **Accessible** — WCAG AA contrast, focus rings, status icons, reduced motion, full keyboard nav
- **Server health checks** — test MCP servers with one click (binary check for Stdio, HTTP ping for Http/Sse)
- **Config profiles** — capture, apply, export/import server configurations across clients
- **Config diff tracking** — monitors config file changes over time with line-level stats
- **Multi-workspace** — filter memories by Claude Code project directory
- **AI-powered** — Claude Sonnet 4.6 or OpenAI GPT-4.1 Mini for intelligent cleanup recommendations (optional, local analysis works without API key)

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Cmd+K` | Search workspace |
| `Cmd+R` | Rescan workspace |
| `Cmd+Z` | Undo last cleanup action |
| `Cmd+Shift+S` | Toggle sidebar |
| `Cmd+,` | Open Settings |
| `Cmd+1-8` | Navigate to page |
| `?` | Show all shortcuts |

## Tech Stack

- **Frontend:** React 19, TypeScript 5.8, Tailwind CSS v4, Vite 7
- **Backend:** Rust (Tauri v2), serde, chrono, reqwest, thiserror
- **AI:** Multi-provider (Claude Sonnet 4.6 + OpenAI GPT-4.1 Mini)
- **Design:** Dark-mode-first glass morphism, Inter + JetBrains Mono
- **Tests:** 243 Rust + 86 frontend (329 total)

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

### Checks

```bash
pnpm check              # lint + typecheck + test
cd src-tauri && cargo test  # Rust tests
```

See [Development Guide](docs/technical/development-guide.md) for full details.

## Documentation

| Area | Contents |
|------|----------|
| [Product](docs/product/) | Vision, personas, features, user flows, roadmap |
| [Technical](docs/technical/) | Architecture, data models, API design, design system, testing |
| [Business](docs/business/) | Pricing, GTM, competitive analysis, GitHub strategy |
| [Distribution](docs/distribution.md) | Code signing, Homebrew, release process |
| [CLI](docs/cli.md) | Planned CLI commands (scan, score, clean) |
| [Legal](docs/legal/) | Privacy policy, terms of service |

## Roadmap

See [ROADMAP.md](ROADMAP.md) for the full roadmap. Currently preparing v0.1.0 public release.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for how to get started.

## License

[MIT](LICENSE)

# naqi نقي

> The AI workspace cleaner — scan, audit, and clean up your MCP servers, memories, and skills.

<!-- Screenshot will go here once the dashboard is built -->

## The Problem

AI tools encourage you to connect MCP servers, save memories, and install skills — but provide no way to clean up afterward. Over time, you accumulate stale servers, contradictory memories, and unused skills that silently degrade your AI experience.

There is no tool to answer: *"What AI stuff do I have, what's actually used, and what should I remove?"*

Naqi answers that.

## What Naqi Does

- **Scans** Claude Desktop, Claude Code, Cursor, VS Code, and Windsurf configs in one view
- **Detects** stale servers, contradictory memories, unused skills, and config inconsistencies
- **Cleans up** with AI-powered recommendations (Claude + OpenAI), safe one-click removal, and full undo support

## Features

- **Dashboard** with category-based health bar, 7-day trend chart, and editorial card grid
- **Resizable sidebar** — drag to resize (180–400px), double-click to reset, width persisted
- **Global search** (`Cmd+K`) across servers, memories, skills, and configs with recent queries
- **Keyboard-first** — `Cmd+Z` undo, `Cmd+R` rescan, `Cmd+Shift+S` toggle sidebar, `Cmd+1-8` page nav
- **Menu bar tray** — persistent icon with glass-styled status panel (health score, category bar, quick scan, fix issues)
- **macOS native** — overlay titlebar with traffic lights, collapsible sidebar, hides to tray on close, state restoration
- **Accessible** — WCAG AA contrast, focus rings, status icons (not color-only), reduced motion support, full keyboard navigation
- **Config profiles** — capture, apply, export/import server configurations across clients
- **Inline onboarding** — dismissible welcome card on first launch
- **Server health checks** — test MCP servers with one click (binary check for Stdio, HTTP ping for Http/Sse)
- **Config diff tracking** — monitors config file changes over time with line-level stats
- **Multi-workspace** — filter memories by Claude Code project directory
- **Micro-interactions** — spring hover/tap on cards, slide-down expand, cleanup dissolve animations

## Status

**In active development.** Feature-rich with 243 Rust + 86 frontend tests. Scanner, dashboard, cleanup engine, AI recommendations, server health checks, config diff tracking, multi-workspace support, resizable sidebar, and global search are built. HIG-audited for macOS. Preparing for first public release.

## Tech Stack

- **Frontend:** React 19 + TypeScript + Tailwind CSS v4 + Vite
- **Backend:** Rust (Tauri v2)
- **AI:** Multi-provider (Claude Sonnet 4.6 + OpenAI GPT-4.1 Mini) for smart cleanup recommendations
- **Architecture:** Local-first — all scanning happens on your machine
- **Design:** Dark-mode-first glass morphism, Inter + JetBrains Mono, responsive root font-size

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

### Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Cmd+K` | Search workspace |
| `Cmd+R` | Rescan workspace |
| `Cmd+Z` | Undo last cleanup action |
| `Cmd+Shift+S` | Toggle sidebar |
| `Cmd+,` | Open Settings |
| `Cmd+1-8` | Navigate to page |
| `?` | Show all shortcuts |

See [Development Guide](docs/technical/development-guide.md) for full details.

## Documentation

Comprehensive docs live in [`docs/`](docs/README.md):

- **Product:** Vision, personas, features, user flows, implementation roadmap
- **Business:** Pricing, GTM, competitive analysis, licensing
- **Technical:** Architecture, data models, API design, design system, testing plan
- **Legal:** Privacy policy, terms of service

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for how to get started.

## License

[MIT](LICENSE)

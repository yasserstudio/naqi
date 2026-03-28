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

## Status

**In active development.** MVP feature-complete with 240 tests. Scanner, dashboard, cleanup engine, and AI-powered recommendations are built. Preparing for first public release.

## Tech Stack

- **Frontend:** React 19 + TypeScript + Tailwind CSS v4 + Vite
- **Backend:** Rust (Tauri v2)
- **AI:** Multi-provider (Claude Sonnet 4.6 + OpenAI GPT-4.1 Mini) for smart cleanup recommendations
- **Architecture:** Local-first — all scanning happens on your machine

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

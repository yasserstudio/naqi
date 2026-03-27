# Changelog

All notable changes to Naqi are documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).
Versioning follows [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- Claude API integration for AI-powered recommendations (Pro tier)
- License activation via Lemon Squeezy
- Code signing and notarization for macOS distribution
- Frontend test suite with Vitest
- Empty and loading states for all list pages
- Error boundaries on all pages

---

## [0.1.0-alpha.1] — 2026-03-27

### Added
- **Config scanner** for 5 AI clients: Claude Desktop, Claude Code, Cursor, VS Code, Windsurf
- **Memory scanner** for Claude Code (global + per-project memories with frontmatter parsing)
- **Skill scanner** across ~/.claude/skills, agent-skills, .cursor/rules
- **Health score** (0-100) with 5 bands: Pristine, Healthy, Cluttered, Bloated, Critical
- **Local recommendations engine** detecting stale servers, broken servers, duplicate configs, stale memories
- **Cleanup engine** with backup-first protocol: backup > preview diff > confirm > apply > validate > undo
- **Persistent undo stack** that survives app restart and hot-reload
- **Confirmation dialogs** on all destructive actions
- **8-page UI**: Dashboard, Servers, Memories, Skills, Configs, Cleanup, History, Settings
- **Export reports** in JSON and Markdown formats
- **Design system**: dark-mode-first glassmorphic UI with Tailwind v4, shadcn/ui, Inter + JetBrains Mono
- **JSONC support**: strips // and /* */ comments, handles trailing commas
- **Secret masking**: env vars with TOKEN/KEY/SECRET/PASSWORD/CREDENTIAL/AUTH are masked
- **Server resolution**: validates stdio commands exist via `which`
- **145 Rust tests** covering parsers, memory, recommendations, cleanup, and edge cases
- **9 test fixture files** for all client config formats
- Full documentation suite (26 documents: architecture, API, testing, roadmap, GTM, legal)

### Fixed
- Undo stack now persists to disk (was in-memory only)
- Settings IPC param name mismatch between Rust and JS
- Background blobs visibility and text contrast on glass cards
- Stat card visibility using proper div instead of shadcn Card

---

## Release History

| Version | Date | Highlights |
|---------|------|------------|
| 0.1.0-alpha.1 | 2026-03-27 | MVP feature-complete, 145 tests |

[Unreleased]: https://github.com/yasserstudio/naqi/compare/v0.1.0-alpha.1...HEAD
[0.1.0-alpha.1]: https://github.com/yasserstudio/naqi/releases/tag/v0.1.0-alpha.1

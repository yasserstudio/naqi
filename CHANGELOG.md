# Changelog

All notable changes to Naqi are documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).
Versioning follows [Semantic Versioning](https://semver.org/spec/v2.0.0.html) — see [VERSIONING.md](VERSIONING.md) for full strategy.

## [Unreleased]

### Added
- **Multi-provider AI recommendations** — Anthropic (Claude Sonnet 4) and OpenAI (GPT-4o Mini) support for intelligent workspace analysis
- **Workspace anonymizer** — strips secrets, file paths, and memory content before sending to AI APIs
- **API key management** — secure storage in `~/.naqi/credentials.json` with provider selection, key validation, and 600 file permissions
- **Provider picker** in Settings — choose between Anthropic and OpenAI with dynamic key format hints
- **AI badge on recommendations** — visual indicator distinguishing local vs AI-sourced recommendations
- **Loading skeletons** on all 8 pages — animated placeholder UI during data fetch
- **Error states** with retry on all pages — consistent error handling with AlertTriangle icon
- **Empty states** with icons and descriptions on all pages — contextual messages when no data
- **Reusable UI components** — Skeleton, EmptyState, ErrorState, PageSkeleton variants
- **Frontend test suite** — 63 Vitest tests covering API wrappers, hooks, and components
- **CI/CD pipeline** — GitHub Actions for Rust (fmt, clippy, test) + frontend (typecheck, lint)
- **Release workflow** — tag-triggered Tauri builds with GitHub Release draft
- **Versioning strategy** — SemVer with alpha/beta/rc pre-release tags
- New IPC commands: `save_api_key`, `remove_api_key`, `check_api_key`, `get_ai_provider`, `get_ai_recommendations`

### Changed
- **Recommendations engine** — merges local + AI recommendations with deduplication by affected item
- **RecommendationCard** — AI recs show "Review manually" instead of actionable Remove button
- **Settings page** — real API key save/remove (was placeholder), provider picker UI
- **CleanupPage** — shows AI analyzing badge, AI unavailable badge on error, deduped merged recs

### Fixed
- AI recommendations no longer have clickable Remove button (empty placeholder actions)
- HTTP client now has 30s timeout (was unbounded, could hang)
- API key removal now invalidates AI recommendation cache
- OpenAI prompt explicitly requests JSON array response
- Empty workspace no longer triggers unnecessary API call
- AI errors surfaced to frontend instead of silently swallowed
- Unsafe memory source type casting replaced with safe type guard
- Error boundary catches unhandled exceptions and shows recovery UI
- ConfirmDialog now has keyboard Escape handling, focus management, and ARIA attributes
- Toast messages use safe error formatting instead of raw string interpolation
- Null check on affected_items in cleanup toast (prevents "Removed undefined")

### Security
- Workspace data anonymized before any AI API call (no paths, secrets, or memory content)
- API keys stored with restricted file permissions (chmod 600 on Unix)
- AI recommendation confidence capped at 0.95 to prevent overconfidence
- API error responses sanitized — raw bodies (containing org IDs, key fragments, internal JSON) never reach the frontend; known HTTP codes mapped to user-friendly messages; full details logged server-side only

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
- **177 Rust tests** covering parsers, memory, recommendations, cleanup, anonymizer, AI analysis, error sanitization, and edge cases
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
| 0.1.0-alpha.1 | 2026-03-27 | MVP feature-complete, 177 Rust + 63 frontend tests |

[Unreleased]: https://github.com/yasserstudio/naqi/compare/v0.1.0-alpha.1...HEAD
[0.1.0-alpha.1]: https://github.com/yasserstudio/naqi/releases/tag/v0.1.0-alpha.1

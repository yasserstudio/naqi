# Changelog

All notable changes to Naqi are documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).
Versioning follows [Semantic Versioning](https://semver.org/spec/v2.0.0.html) — see [VERSIONING.md](VERSIONING.md) for full strategy.

## [Unreleased]

### Added
- **Multi-provider AI recommendations** — Claude (Sonnet 4.6) and OpenAI (GPT-4.1 Mini) support for intelligent workspace analysis
- **Workspace anonymizer** — strips secrets, file paths, and memory content before sending to AI APIs
- **API key management** — secure storage in `~/.naqi/credentials.json` with provider selection, key validation, and 600 file permissions
- **Diff preview** in cleanup confirm dialog — shows before/after JSON with red/green line highlighting before applying any change
- **Server detail panel** — click any ServerCard to expand and see full command with all args, env vars with masked values and secret badges, config file path, and duplicate info. Copy command button included.
- **Batch cleanup** — select multiple recommendations with checkboxes, apply all at once with "Apply N selected" button. Sequential execution with backup per action. AI-only recs excluded from selection.
- **Health score trend chart** — area chart on dashboard showing score history over time (up to 90 entries). Auto-records each scan. Shows trend indicator (+/-) comparing latest vs previous. Uses recharts with gradient fill colored by health band.
- **Global search** (Cmd+K) — search overlay across servers, memories, skills, and configs. Arrow key navigation, Enter to jump to page. Results show type-colored icons and detail.
- **Keyboard shortcuts** — Cmd+K search, Cmd+R rescan, Cmd+1-8 page navigation. Search hint button in sidebar.
- **Config file watcher** — polls config file mtimes every 30s, auto-rescans when external changes detected, shows toast notification
- **First-launch onboarding** — 4-step walkthrough on first launch explaining scan, recommendations, safety, and undo. Skip button available. Sets `first_launch: false` on completion and triggers first scan.
- **Auto-updater** — Tauri v2 updater plugin checks for updates on startup, shows "Update to vX.Y.Z" button in sidebar with download progress bar. Release workflow signs builds and generates `latest.json` manifest for GitHub Releases.
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
- **Settings page** — redesigned AI provider picker with official brand SVG logos (Claude sparkle, OpenAI hexagon), per-provider color glows (#C15F3C Claude, #EEE OpenAI), model labels, and connected state badge
- **CleanupPage** — shows AI analyzing badge, AI unavailable badge on error, deduped merged recs
- **Claude API** — upgraded from claude-sonnet-4-20250514 to claude-sonnet-4-6 per official docs audit; response parsing now filters by content block type and checks `stop_reason` for truncation
- **OpenAI API** — upgraded from gpt-4o-mini to gpt-4.1-mini per official docs audit; request param renamed `max_tokens` to `max_completion_tokens`; added `finish_reason` check for truncation and content filtering

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
- Responsive stat cards grid (2-col on narrow, 4-col on wide)
- Content area max-width increased from 960px to 1024px for better full-screen use
- Page headers wrap gracefully on narrow windows
- Recommendation badges wrap instead of overflowing
- Confirm dialog constrained to 90vw with responsive padding
- Horizontal scroll prevented on main content area
- Light mode: replaced all hardcoded `text-white` with `text-[hsl(var(--foreground))]` (7 instances), added light-mode glass variables (semi-transparent white bg, dark borders, softer shadows)

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
| 0.1.0-alpha.1 | 2026-03-27 | MVP feature-complete, 243 tests (180 Rust + 63 frontend) |

[Unreleased]: https://github.com/yasserstudio/naqi/compare/v0.1.0-alpha.1...HEAD
[0.1.0-alpha.1]: https://github.com/yasserstudio/naqi/releases/tag/v0.1.0-alpha.1

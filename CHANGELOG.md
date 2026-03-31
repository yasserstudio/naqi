# Changelog

All notable changes to Naqi are documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).
Versioning follows [Semantic Versioning](https://semver.org/spec/v2.0.0.html) — see [VERSIONING.md](VERSIONING.md) for full strategy.

## [Unreleased]

### Added
- **Resizable sidebar** — drag the right edge to resize (180–400px), double-click to reset to 240px, width persisted to localStorage across sessions
- **Circular usage ring** — SVG-based ring gauge for AI usage stats with animated arc fill, spring counter, and warning color shift at 80% threshold. Replaces flat stat grid.
- **Micro-interaction polish** — shared motion presets (`cardHover`, `buttonPress`, `expandContent`) applied across all cards, consistent scale hover/tap feedback, expand panels slide-down with y offset
- **Category-based health bar** — stacked bar where each category (Servers 50pts, Memories 30pts, Configs 20pts) owns a segment colored by its own health status. Per-category scores computed in Rust backend.
- **Panel size persistence** — `useLocalStorage` hook for generic localStorage state with cross-window sync
- **Inline onboarding** — first-launch onboarding moved from full-screen overlay to dismissible card on dashboard
- **Cleanup loading states** — Remove button shows spinner during action, batch apply shows "Cleaning 2/5..." progress, confirmation dialog lists affected items
- **Unified page headers** — `PageHeader` component with icon badge + title + count across all 8 pages
- **Section headers** — `SectionHeader` component with uppercase label + count + horizontal rule for grouped lists (Skills by client, Configs by client)
- **Stagger entrance animations** — list items fade-in with 30ms staggered delay on Servers, Skills, Configs, Memories, History, and Profiles pages
- **App icon and branding** — new icon set (macOS icns, iOS, Android, Windows), sidebar logo, tray icon
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
- **First-launch onboarding** — 3-step walkthrough on first launch (scan, recommendations, safe cleanup with undo). Skip button available. Sets `first_launch: false` on completion and triggers first scan.
- **Auto-updater** — Tauri v2 updater plugin checks for updates on startup, shows "Update to vX.Y.Z" button in sidebar with download progress bar. Release workflow signs builds and generates `latest.json` manifest for GitHub Releases.
- **Memory content viewer** — click any MemoryItem to expand and see full content in a scrollable monospace view, description, file path, contradiction badges, and copy-to-clipboard button
- **Skill detail panel** — click any SkillItem to expand and see description, install path, file count, size, staleness badge, and source type badge with color coding
- **AI rate limiting** — 10 calls/hour and 50 calls/day limits enforced before API calls. Rate limit errors surfaced to frontend with clear messaging.
- **AI usage tracking** — tracks calls, estimated tokens (input/output), and estimated cost per provider (Anthropic $3/$15 MTok, OpenAI $0.40/$1.60 MTok). Daily and all-time totals persisted to `~/.naqi/usage.json`.
- **AI usage dashboard** in Settings — shows today's calls, tokens, cost, total stats, and hourly rate limit bar with warning state. Only visible when API key is configured.
- **Memory contradiction detection** — local Jaccard similarity analysis finds memories with >40% word overlap across different types/sources. AI analysis enhanced with content snippets (first 300 chars) for semantic contradiction detection. 5 new Rust tests.
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

- **Apple HIG audit** — 21 findings addressed across accessibility, window chrome, keyboard access, motion, and layout:
  - macOS overlay titlebar with drag regions and traffic light integration
  - `Cmd+Z` global undo, `Cmd+Shift+S` sidebar toggle shortcuts
  - Status indicators use shape icons alongside color (CheckCircle2, XCircle, Clock, AlertCircle)
  - Focus ring opacity increased to 70% across all interactive components
  - Responsive root font-size `clamp(13px, 0.875rem, 18px)` for system text size support
  - Full keyboard access audit — sidebar search, HealthGauge link, all cards Tab-reachable
  - ARIA labels added to icon-only buttons and expandable cards
  - Muted-foreground contrast ratio fixed to 5.0:1 (WCAG AA)
  - Sidebar widened to 240px with source-list section headers (Workspace, Tools)
  - ShortcutsDialog converted from full-screen modal to lightweight popover
  - Onboarding consolidated from 4 steps to 3
  - Search overlay shows recent queries when empty
  - Spring animations tuned to critically damped (bounce ≤ 0.15)
  - Onboarding wrapped in MotionConfig for reduced motion support
  - Consistent 72px min-height on collapsed list items
  - Last active route persisted and restored on launch
  - Normalized gap spacing across all pages

- **Settings tabbed layout** — four tabs with icons: Appearance (theme + schedule), AI (provider + key + usage), Export, About. Active tab persisted to localStorage.
- **ServerPanel inline inspector** — converted from modal overlay to inline split-view that opens alongside the server list. Width animates 0→420px. Server list shrinks to accommodate.
- **Performance optimizations** — will-change:transform on blobs, contain:layout on glass-surface, glass-blur reduced from 20px to 12px
- **Frontend tests** — useLocalStorage (6 tests), RecommendationCard (9 tests), Sidebar (7 tests). Total: 86 tests (was 64).
- **Light mode audit** — fixed hardcoded dark-only colors across ProviderCard (border-white → border-primary), ScheduleSettings toggle thumb (added shadow), BackgroundBlobs (5x lower opacity in light mode), card-elevated (uses theme-aware glass vars)

### Changed
- **Recommendations engine** — merges local + AI recommendations with deduplication by affected item
- **RecommendationCard** — AI recs show "Review manually" instead of actionable Remove button
- **Settings page** — redesigned AI provider picker with official brand SVG logos (Claude sparkle, OpenAI hexagon), per-provider color glows (#C15F3C Claude, #EEE OpenAI), model labels, and connected state badge
- **CleanupPage** — shows AI analyzing badge, AI unavailable badge on error, deduped merged recs
- **Claude API** — upgraded from claude-sonnet-4-20250514 to claude-sonnet-4-6 per official docs audit; response parsing now filters by content block type and checks `stop_reason` for truncation
- **OpenAI API** — upgraded from gpt-4o-mini to gpt-4.1-mini per official docs audit; request param renamed `max_tokens` to `max_completion_tokens`; added `finish_reason` check for truncation and content filtering

### Fixed
- SkillsPage and ConfigsPage `useMemo` moved before early returns (fixed "Rendered more hooks" crash)
- Shell undo toast: access `entry.action_description` instead of passing raw UndoEntry object
- HealthGauge test updated for category-based redesign (message only shown when issues exist)
- Removed unused `formatDate` from HealthTrend (replaced by 7-day grid builder)
- ServerPanel setTimeout cleared on unmount (memory leak fix)
- All checks pass: typecheck 0 errors, lint 0 errors, 64/64 frontend tests, 237/237 Rust tests
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
- Batch cleanup now reports failed actions individually instead of swallowing errors silently
- `build_prompt` returns Result instead of silently defaulting to empty string on serialization failure
- DiffPreview query uses safe null check instead of non-null assertion
- SkillItem badge styling fixed (broken ternary always returned empty string)
- Theme persisted across app restarts (applied on startup from saved settings)
- ConfirmDialog: focus trap (Tab/Shift+Tab stays within dialog), cancel button auto-focused
- Text contrast: removed /50 opacity on visible muted text across Sidebar, Settings, Onboarding, SearchOverlay
- Added `@media (prefers-reduced-motion: reduce)` — disables all animations
- Added `@media (prefers-reduced-transparency: reduce)` — replaces glass with solid backgrounds
- Accessible labels: API key input, onboarding dots, search overlay dialog
- ServerCard: fixed nested interactive elements (header is now the toggle button, card is non-interactive container)
- SearchOverlay: added role="dialog", aria-modal, aria-label
- Implemented Cmd+, shortcut to open Settings (macOS standard)
- Added Motion animation library (`motion/react`) for premium UI transitions:
  page route transitions (fade + slide), search overlay spring enter/exit,
  confirm dialog spring scale, stat cards staggered entrance on dashboard,
  onboarding step slide transitions. MotionConfig sets global defaults.
  Health score animated counter (spring physics count-up from 0), health bar
  spring fill, sidebar active indicator with layoutId sliding highlight,
  stat card whileHover/whileTap spring micro-interactions, card expand/collapse
  height animations (ServerCard, MemoryItem, SkillItem), recommendation card
  spring entrance + slide-out dismiss via AnimatePresence.

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

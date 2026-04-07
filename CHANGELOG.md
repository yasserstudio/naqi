# Changelog

All notable changes to Naqi are documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).
Versioning follows [Semantic Versioning](https://semver.org/spec/v2.0.0.html) — see [VERSIONING.md](VERSIONING.md) for full strategy.

## [Unreleased] — 2026-04-07

### Added
- **Weekly workspace digest** — new `WeeklyDigest` notification type that summarises the past 7 days of health data (trend direction, average score, scan count). Generated after each scheduled scan but deduplicated to at most once per 7-day window. Shows in the in-app notification center as Info severity (no native notification). Toggled via Settings > Notifications > Weekly digest.
- **Miscategorized memory detection** — new `detect_miscategorized_memories` analyzer rule flags memories whose filename prefix (`user_`/`feedback_`/`project_`/`reference_`/`ref_`) disagrees with the `type:` field in their frontmatter. Emits `ConfigInconsistency/Info` with `ActionType::Advisory` suggesting the user fix the type or rename the file. 6 new Rust tests.
- **Natural language workspace query** — new "Ask AI" widget on the dashboard (5th draggable widget, visible when an AI provider key is configured). Type free-form questions about your workspace ("Which servers haven't been used recently?") and get concise answers from the configured AI provider. Supports follow-up questions; conversation history shown inline with a Clear button. Backed by new `query_workspace` Tauri command using the same multi-provider LLM infrastructure with workspace anonymization.
- **Predictive health trend** — `HealthTrend` chart now projects health 3 days forward using linear regression over the last 7 days of scan history (requires ≥3 data points). Displayed as a dashed line in the same color as actual data at 40% opacity. Header shows "→ N in 3d" label alongside the existing trend delta.
- **Cross-client config drift detection** — new `detect_config_drift` analyzer rule identifies servers with the same name configured differently across clients (differing args, env var names, or transport). Emits `ConfigInconsistency/Warning` with `ActionType::Advisory`. Separated from duplicate detection: `detect_duplicates` now only flags identical configs; `detect_config_drift` handles semantic divergence.
- **Persistent recommendation dismissals** — dismissed recommendations in CleanupPage now survive app restarts (stored in `localStorage`). Category detail view shows a "Show hidden (N)" toggle that reveals dismissed items as compact rows with individual Restore buttons. Page header shows total hidden count as a badge that navigates to the relevant category with dismissed items pre-expanded.
- **Draggable dashboard widgets** — each dashboard section (health gauge, daily brief, attention, clients) can be reordered by dragging the grip handle that appears on hover. Order is persisted to `localStorage` and survives app restarts. A "Restore default order" button appears below the widgets whenever the order has been customised. Conditional widgets (brief, attention) are excluded from the drag group when they have no content.
- **Skill inventory enriched tiles** — collapsed tile shows description preview, source repo slug (detected from `.git/config` remote URL), Clock icon + absolute last-modified date (no more "X days ago" relative format). Expanded panel shows commit SHA + date, installed/updated dates from lock file, source repo with link.
- **Per-skill context menu** — right-click any skill tile for: Open in Editor, Show in Finder, Copy Name, Copy Install Path, Copy Source Repo, Check for Update, Set Source Repo, Remove.
- **Set Source Repo dialog** — uses `createPortal(dialog, document.body)` to escape `contain: layout style` on parent glass-surface tiles.
- **GitHub PAT for skill updates** — optional personal access token stored in OS keychain (`storage::api_key`, account `github-token`). Raises GitHub API rate limit from 60 to 5,000 req/hr for skill update checks. Token takes priority over `GITHUB_TOKEN` env var.
- **Settings > Skills section** — new 7th section in Settings for GitHub PAT configuration. Includes Test button (hits `/rate_limit` endpoint, returns authenticated limit as u32), masked placeholder when token is already set, and "Create a token on GitHub →" link button.
- **`storage/skill_sources.rs`** — new module persisting `~/.naqi/skill-sources.json`. Exposes `load() -> HashMap<String, String>`, `set(name, source_repo)`, `remove(name)`. Uses `write_atomic()` for crash-safe writes. Stores user-managed `source_repo` overrides per skill name (highest priority over git remote detection).
- **Skill source priority** — `source_repo` resolved in order: (1) user override in `skill-sources.json`, (2) git remote URL parsed from `<install_path>/.git/config`, (3) lock file field.
- **Non-2xx GitHub error surfacing** — skill update checks surface the error message from the response body instead of crashing on a JSON parse error.
- **New Tauri commands** — `check_single_skill_update`, `set_skill_source`, `remove_skill`, `save_github_token`, `remove_github_token`, `github_token_configured`, `test_github_token`, `ping_ai_provider`, `get_masked_api_key`, `get_masked_github_token`.

### Changed
- **Settings page** — now has 7 sections: Appearance, AI Provider, Skills (new), Export, About, Safe Mode, Danger Zone. Display name saves on blur/Enter. Test Connection uses lightweight `ping_provider()` (hits models endpoint, no scan triggered). Masked placeholder shown when API key or GitHub token is already configured. App version loaded dynamically via `getVersion()`. All links open via `openUrl()` from `@tauri-apps/plugin-opener`.
- **Button design** — all buttons now use `rounded-full` (pill shape). New `glass` variant with backdrop blur + inset highlight; `outline` variant aliased to `glass`.
- **Updater: graceful "no release" handling** — when no GitHub release has been published yet, the auto-updater no longer shows an error toast. "Could not fetch a valid release JSON" errors are silently swallowed on auto-check; on manual check they surface a friendly "Naqi is up to date" message instead of an error.

### Fixed
- **`release.yml` Intel build** — ARM build job was incorrectly using `macos-latest` (ARM runner) for the Intel target; corrected to `macos-13`.

### Added
- **`release.yml` `prepare` job** — extracts the matching `## [X.Y.Z]` section from `CHANGELOG.md` and passes the notes as the GitHub Release body, replacing the previous generic link.
- **`release.yml` build caches** — Rust cache (`Swatinem/rust-cache@v2`) and Node cache added to all build jobs for faster CI runs. Removed the redundant "Run checks" step from release builds (CI workflow handles that separately).
- **`publish-homebrew.yml` workflow** — new workflow triggered on `release: published`. Downloads both ARM and Intel DMGs, computes SHA256 for each, and pushes an updated dual-arch cask to `yasserstudio/homebrew-naqi`. Requires a new repository secret `HOMEBREW_TAP_TOKEN` (GitHub PAT with `Contents: write` permission on the homebrew-naqi repo).
- **Dual-arch Homebrew cask** — `homebrew/naqi.rb` updated from single-arch (ARM only) to dual-arch format using `on_arm do` / `on_intel do` blocks. Version placeholder is `0.1.0` with `sha256 :no_check` until the first signed release is published.
- **Rust test coverage +20 tests** — total Rust tests: 326 → 347.
  - `scanner/parsers/copilot.rs`: 1 → 5 tests (empty paths, broken JSON, multiple paths, no-servers contract)
  - `scanner/parsers/jetbrains.rs`: 2 → 7 tests (broken JSON, multiple servers, SSE transport, env secrets, multiple configs)
  - `scanner/parsers/zed.rs`: 2 → 8 tests (broken JSON, multiple servers, env secrets, SSE, unrelated keys, metadata)
  - `analyzer/anonymize.rs`: 14 → 19 tests (command path stripping, all Staleness variants, byte content length, stale skill path suppression)
  - Fixed compile error in `anonymize.rs` test fixture: added missing `installed_at`/`updated_at` fields

---

## [0.2.0] — 2026-04-02

### Added
- **3 new AI client scanners** — GitHub Copilot, JetBrains (dynamic IDE discovery), and Zed (`context_servers`). Total: 10 clients scanned (Amp and Kiro added in [Unreleased]).
- **Backup system** — versioned backups with SHA-256 dedup, max 10 per file. Auto-backup on every scan. Export as ZIP with manifest, one-click restore. Configurable retention (7/30/90 days).
- **Safe mode** — `safe_mode` toggle blocks all modify/delete actions. Per-client locking (`locked_clients`). Guards in cleanup, profiles, danger zone. Change log records every modification.
- **Danger zone** — Reset client configs, Delete memories, Remove skills, Clear Naqi data, Factory reset. Two-step confirmation (dialog + type DELETE). Backup before every destructive action. Respects safe mode and locked clients.
- **3 new AI providers** — Google Gemini (2.5 Flash, $0.10/$0.40 MTok), xAI Grok (grok-4-1-fast, $0.20/$0.50 MTok), Ollama (local, free). OpenAI updated to GPT-5.4 Nano ($0.20/$1.25 MTok). Total: 6 providers (OpenRouter added in [Unreleased]).
- **Gemini custom API** — `system_instruction` + `responseMimeType` for structured JSON output
- **OpenAI-compatible transport** — shared by OpenAI, Grok, and Ollama providers
- **Per-provider cost estimation** in usage stats
- **AI settings tab** — provider-specific placeholders, test connection button, Ollama health check
- **Floating Action Button (FAB)** — bottom-anchored scan trigger with multi-layer glow, progress arc, spinning ring, idle float animation, progressive blur dock
- **Pre-scan welcome state** — hero icon + greeting + single Scan CTA (replaces empty dashboard)
- **Scan progress category grid** — 5 tiles with ambient glow theater, 4-second minimum display
- **Tray panel redesign** — 2x3 stat grid with colored gradient tiles + recommendation card + footer
- **Dynamic background blob** — color tied to health band (green/yellow/orange/red)
- **Enhanced color system** — primary boosted to `hsl(170, 70%, 46%)`, all colors +10-15% saturation
- **Increased button sizes** — xs=28px, sm=32px, default=36px, lg=40px for desktop ergonomics
- **Window opens centered** — 1200x800, no state persistence, double-click titlebar toggles fullscreen

### HIG Compliance (13 fixes)
- **System font stack** — font-family now uses `-apple-system, BlinkMacSystemFont` first so SF Pro renders on macOS, with Inter as fallback for other platforms
- **Light mode contrast** — secondary bg darkened from 95% to 91%, glass surface opacity increased to 0.92 for better readability
- **Animation durations** — reduced across app: emoji wave 1.5s to 0.6s, FAB idle 2.5s to 1.8s, scan progress breathing 1.2s to 0.8s (macOS standard 0.3-0.8s range)
- **ARIA labels** — added to FAB (aria-busy), HealthGauge (role="button" with descriptive label), memory checkboxes (aria-label per item), sidebar logo alt text; removed duplicate aria-label on memory group checkbox
- **8pt grid alignment** — select-control now uses 36px min-height, 8px/12px padding, 6px border-radius
- **Toggle switches** — increased from 40x24px to 44x26px for macOS touch target compliance
- **Reduced motion** — complete support: animation-duration 0s, keeps 150ms transitions for non-jarring feedback, disables blob float animation
- **Scrollbar** — added scrollbar-width: thin (Firefox/standards) + increased webkit scrollbar from 6px to 8px
- **Line height** — body increased from 1.55 to 1.6 per macOS type guidelines
- **Background blob** — pauses animation on document.hidden (battery saving), added will-change hint for GPU acceleration
- **Dark mode glass** — glass-bg lightened from rgba(14,17,26) to rgba(22,25,38) for better surface elevation visibility
- **Sidebar spacing** — increased nav gap from 0.5 to 1 (4px), section margin from 2 to 4 (16px), divider margin from 3 to 4 (16px)

### Changed
- **AI system prompt rewritten** — clarifies available data fields, caps recommendations at 5
- **Robust JSON extraction** — bracket-depth counting parser replaces regex-based extraction
- **Stable recommendation IDs** — content hash instead of random generation
- **Contradiction threshold** raised from 0.4 to 0.6 (reduces false positives)
- **Staleness confidence** rebalanced for more accurate scoring
- **Command paths anonymized** to basename only in AI submissions
- **Project names stripped** from anonymized data
- **Rate limits checked** before initiating API calls (fail-fast)
- **Notification dedup window** reduced from 1 hour to 10 minutes

### Refactored
- **Atomic file writes** across all 8 storage modules (write to temp + rename)
- **Undo stack Mutex** for safe concurrent access
- **LLM retry logic** — 2x exponential backoff on HTTP 429/5xx responses
- **18 raw `<button>` elements** replaced with shadcn `<Button>` for consistency
- **ESLint react-hooks plugin** added — enforces `rules-of-hooks` and `exhaustive-deps`
- **`useThemeToggle` hook** extracted from inline toggle logic
- **Hooks-after-early-returns** fixed in 3 pages (prevents "Rendered more hooks" crash)
- **`formatError` used consistently** across all toast notifications

### Fixed
- Tray: removed `expect()` crash, fixed scale factor defaulting to 1.0 on missing value
- All checks pass: 141 frontend tests, 326 Rust tests

### Security
- Safe mode prevents accidental modifications across cleanup, profiles, and danger zone
- Danger zone requires two-step confirmation with typed DELETE keyword
- Backup created before every destructive action in danger zone
- Per-client locking prevents modifications to specific client configs

---

## [Unreleased]

### Added
- **Backup Browser page** — new `/backups` route with file list grouped by client, version timeline, View/Compare/Restore buttons, side-by-side diff viewer for comparing backup versions, export ZIP button. Added to sidebar nav (Archive icon).
- **Onboarding Safe Mode** — Step 3 of onboarding now shows Safe Mode toggle (defaults ON). New users start in read-only mode. Saved to `settings.safe_mode` during `handleComplete`.
- **Model selector** — `available_models()` per provider returns 3-5 models. `ai_model` field in AppSettings. ModelSelector dropdown in AI settings. LLM client reads `ai_model` as override (falls back to provider default). Models: Claude Sonnet/Opus/Haiku, GPT-5.4 Nano/Mini/Full, Gemini Flash/Pro, Grok Fast/4.20/Reasoning, Ollama Llama/Gemma/Qwen/Mistral, OpenRouter 5 models.
- **OpenRouter provider** — 6th AI provider. OpenAI-compatible at `openrouter.ai/api/v1/chat/completions`. Default model: `anthropic/claude-sonnet-4-6`. Key prefix: `sk-or-`. Any OpenRouter model ID works via model selector.
- **E2E test suite** — Playwright: 12 tests across 3 files (`e2e/navigation.spec.ts` 4 tests, `e2e/settings.spec.ts` 6 tests, `e2e/dashboard.spec.ts` 2 tests). `playwright.config.ts` targets `localhost:1420`. `pnpm test:e2e` script.
- **Homebrew + Release** — `homebrew/naqi.rb` cask formula for macOS. Release workflow: Apple code signing enabled, aarch64 + x86_64 targets. Auto-updater via `tauri-plugin-updater` already configured.
- **Ping updates server status** — Check All and individual Test buttons now update `server.status` in workspace cache. Broken servers always sort to top. Health score recalculates after Check All.
- **23 new frontend tests** — useSafeMode (6), useThemeToggle (6), useSkillUpdates (5), server-templates (6). Total: 141 frontend tests.
- **Shared test utilities** — `src/__tests__/test-utils.ts` with `createWrapper()` and `createBaseSettings()` factory, deduplicated from 6 test files.
- **Safe Mode keyboard shortcut** — `⌘⇧M` toggles Safe Mode from anywhere with toast confirmation
- **Field-level validation** — ServerForm shows per-field error hints on blur (name, command, URL)
- **SkillsPage empty state** — shows helpful message when no skills are installed
- **CLI companion** — `naqi scan` (show clients/servers/broken), `naqi score` (health 0-100 with band), `naqi clean` (show recommendations). Reuses existing scanner/analyzer modules.
- **Import configs from ZIP** — restore configs from Naqi-exported ZIP archives. Reads manifest, backs up existing files before overwriting.
- **Config snapshot tracking** — auto-snapshot on every scan (already wired, confirmed working).
- **Security audit** — 5 local detection rules: insecure HTTP URLs (non-localhost), secrets in command args, secrets in URL query params, unused secrets on broken servers, credential sprawl across clients. SecurityConcern recommendations now generated locally (previously AI-only).
- **Amp parser** — scans `~/.config/amp/settings.json` with `amp.mcpServers` key (Sourcegraph). Supports `disabled` field. Stdio transport only.
- **Kiro parser** — scans `~/.kiro/settings/mcp.json` (AWS). Supports `disabled`/`disabledTools`, HTTP headers as env vars. Stdio + HTTP transport.
- **10 AI clients** — up from 8. Naqi now scans: Claude Desktop, Claude Code, Cursor, VS Code, Windsurf, GitHub Copilot, JetBrains, Zed, Amp, Kiro.
- **OS keychain for API keys** — API keys now stored in macOS Keychain (via `keyring` crate) instead of plaintext `credentials.json`. Existing keys auto-migrated on first load. Cross-platform ready (Windows Credential Manager, Linux Secret Service).
- **Enhanced contradiction detection** — case-insensitive matching, stop word filtering (60+ words), negation detection (`not`, `dont`, `never`, `avoid`), three labels (Contradictory/Near-duplicate/Redundant), same-source near-duplicate detection, fixed `contradiction_ids` pipeline for notifications.
- **Visual config editor** — click any config file to expand inline JSON editor with line numbers, real-time validation, Format button, ⌘S save, Revert, and Safe Mode guard. Backup created before every save.
- **ZIP import UI** — Import button on BackupsPage with native file dialog (`tauri-plugin-dialog`). Restores configs from Naqi-exported ZIP archives.
- **Security section on CleanupPage** — dedicated "Security" group with shield icon separates security findings from regular cleanup recommendations.
- **Bulk server delete** — Select mode toggle on ServersPage with per-server checkboxes, bulk delete bar, and confirm dialog. Respects Safe Mode.
- **CLI reference in Settings** — About section shows `naqi scan`, `naqi score`, `naqi clean` commands in styled code block.
- **Config file creation notice** — ServerPanel shows "This will create a new config file" banner when adding a server to a client with no existing config.
- **Code signing + notarization** — Developer ID Application certificate, entitlements, and release workflow configured for aarch64 + x86_64

### Fixed
- 15 failing ServerCard tests fixed (missing QueryClientProvider wrapper)
- 9 ESLint warnings fixed (hook deps, console.debug → console.warn, TrayPanel timer useState → useRef)
- 7 cargo clippy warnings fixed (rsplit, is_some_and, is_none_or, saturating_sub)
- Rust anonymize test assertion corrected (project name correctly stripped during anonymization)
- Removed unused `make_health` test helper in notifications.rs
- Replaced raw `<button>` elements with shadcn `<Button>` in MemoriesPage, DashboardPage, SearchOverlay
- Added aria-label to DashboardPage attention heading

### Refactored
- Removed dead `canGoBack` code and unreachable back-button JSX from OnboardingOverlay
- Removed redundant `scanning` useState from TrayPanel (replaced with `rescan.isPending`)
- Extracted shared test boilerplate to `test-utils.ts` (net -81 lines)

### Previously added
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
- **Server health checks** — Test button on ServerCard expanded view. Checks binary existence (Stdio) or HTTP HEAD (Http/Sse) with 5s timeout. Shows reachable/latency or error.
- **Config diff history** — tracks config file hash changes across scans, shows change history on History page with path, client, lines added/removed, and relative time
- **Multi-workspace support** — detects Claude Code project directories, adds workspace filter dropdown to Memories page (Global / per-project filtering)
- **Distribution prep** — macOS entitlements.plist, Homebrew cask formula (ARM/Intel), distribution guide, CLI companion docs
- **System tray with liquid glass panel** — persistent menu bar icon (template icon via `icon_as_template`) with custom webview panel using macOS vibrancy (`NSVisualEffectMaterial::Popover` via `window-vibrancy` crate). Panel positioned under the tray icon using click rect coordinates with Retina scale factor correction. Shows health score, category bar, workspace counts, Scan Now, Fix Issues (opens app on Cleanup page). Panel auto-hides on blur/Escape. Main window hides to tray on close (app keeps running for scheduled scans).
- **Cleanup dissolve animation** — applying a recommendation shows green checkmark overlay with backdrop blur, then card scales down + slides left + dissolves on exit
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

### Refactored (simplify code review)
- **Stagger animation preset** — extracted `staggerItem(index, groupOffset)` in `motion-presets.ts`, replaced 7 inline copies across pages
- **PageHeader built-in spacing** — baked `mb-4` into component, removed 7 redundant spacer divs
- **SectionHeader icon prop** — added optional `icon` prop, used in HistoryPage instead of manual markup
- **Shared HTTP client** — `scanner/health.rs` uses `OnceLock<reqwest::Client>` matching `llm_client` pattern
- **Spawn blocking** — `check_stdio_server` wrapped in `spawn_blocking` to avoid blocking async runtime
- **No-op write guard** — `useLocalStorage` skips localStorage write when value unchanged
- **Config history bug** — stored `line_count` in snapshot to fix diff stats (was re-reading current file as "previous")

### Fixed
- Config diff stats always showing 0 lines changed (double file read bug in `config_history.rs`)
- SkillsPage and ConfigsPage `useMemo` moved before early returns (fixed "Rendered more hooks" crash)
- Shell undo toast: access `entry.action_description` instead of passing raw UndoEntry object
- HealthGauge test updated for category-based redesign (message only shown when issues exist)
- Removed unused `formatDate` from HealthTrend (replaced by 7-day grid builder)
- ServerPanel setTimeout cleared on unmount (memory leak fix)
- All checks pass: typecheck 0 errors, lint 0 errors, 86/86 frontend tests, 243/243 Rust tests
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
| 0.2.0 | 2026-04-02 | 10 clients, 6 AI providers, backup system, safe mode, danger zone, CleanMyMac-inspired UI |
| 0.1.0-alpha.1 | 2026-03-27 | MVP feature-complete, 243 tests (180 Rust + 63 frontend) |

[Unreleased]: https://github.com/yasserstudio/naqi/compare/v0.2.0...HEAD
[0.2.0]: https://github.com/yasserstudio/naqi/compare/v0.1.0-alpha.1...v0.2.0
[0.1.0-alpha.1]: https://github.com/yasserstudio/naqi/releases/tag/v0.1.0-alpha.1

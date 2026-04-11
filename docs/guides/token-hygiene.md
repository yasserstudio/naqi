# Token Hygiene Guide

Naqi's second product pillar: **make token waste visible so you can stop paying for it.** This guide covers the full Token Hygiene feature set that shipped in v0.6.0 — the scanner, dashboard, waste-pattern detectors, proactive nudges, CLI, and the Claude Code skill wrapper — plus a short playbook for writing token-efficient prompts distilled from what the detectors encode.

## Why this exists

A real audit of 294 Claude Code sessions over two weeks showed **17.8 billion tokens** consumed — 75 % of them concentrated in 3 projects, nearly all of it driven by a small set of repeatable anti-patterns. The audit turned into a spec. Every waste-pattern detector in Naqi maps to a concrete failure mode observed in that audit:

- Broad "dive deep" openers that fan out into subagent swarms
- Log pastes that re-bill on every cache read
- Screenshot-review loops in UI-iteration sessions
- 1,400-skill auto-load listings that almost nobody actually invokes
- Auto-compactions from forgetting to `/clear`
- Short prompts re-billing bloated cached context

If you've ever wondered "where did my tokens go?" — this is the answer engine.

## Quick start

```bash
# Scan your Claude Code session logs
naqi tokens

# Top 10 costliest sessions
naqi tokens top --n 10

# Waste-pattern recommendations
naqi tokens waste

# Before sending a draft prompt, check it for waste
echo "Dive deep into the codebase and explain everything" | naqi lint-prompt
```

Or open the Naqi desktop app and click **Tokens** in the sidebar.

## Scope

Token Hygiene only analyzes **Claude Code** session logs (`~/.claude/projects/<project>/*.jsonl`). Cursor, Windsurf, and other clients write their own formats and aren't covered yet. Scanning is strictly read-only — Naqi never modifies session files.

---

## The 9 waste-pattern detectors

Every detector produces an advisory `Recommendation` with `rec_type: TokenWaste` that shows up on the Tokens page, in the tray panel, and in `naqi tokens waste`. None of them apply automatic fixes — tokens are already spent by the time the detector fires. The value is making the pattern visible so you can avoid it next time.

| Detector | Fires when | Fix |
|---|---|---|
| **Mega-session** | ≥50 turns AND ≥100 M tokens | Break work into smaller sessions; use `/clear` at milestones |
| **Background-bash overhead** | ≥20 `queue-operation` `enqueue` events (one per background `Bash`) | Batch commands or run them outside the AI session |
| **Dive-deep opener** | First prompt matches `(dive deep\|understand the project\|check everything\|explore the codebase\|read all\|scan everything\|analyze the entire)` | Name the exact files or areas to examine |
| **Auto-compact waste** | ≥1 `"This session is being continued from a previous conversation"` summary | Use `/clear` before the context window fills |
| **Subagent swarm** | ≥8 subagent fan-outs | Use fewer, more targeted agents |
| **Short-prompt thrashing** | ≥80 turns AND avg >5 M tokens/turn AND last prompt <20 chars | Batch instructions into complete prompts |
| **Log paste** | ≥5 consecutive log-like lines totaling ≥2 KB in a user prompt | Save output to a file and reference the path |
| **Image paste (screenshot loop)** | ≥3 pasted images in a ≥20-turn session, OR ≥5 images anywhere | Skip screenshot-review loops for code-only changes |
| **Skill auto-load overhead** | `skill_listing` attachment ≥4 KB AND fewer than 5 distinct skills actually invoked | Trim `~/.claude/agent-skills/skills/` to what you actually use |

Each detector has a severity band (Info / Warning / Critical) that escalates with how far over the threshold the session is — e.g., a 10 KB log paste is Info, a 100 KB one is Warning.

### Why these thresholds

Every threshold is tuned to avoid false positives on legitimate usage. A single `[ERROR]` in prose shouldn't trip the log-paste detector, so we require ≥5 consecutive log-like lines before any bytes are counted. A short prompt on a cheap session isn't expensive, so short-prompt thrashing additionally requires ≥5 M tokens/turn average. Auto-compacts only fire when the count is ≥1 (it's always waste). And so on.

The thresholds live as `const` values at the top of `src-tauri/src/analyzer/token_waste.rs` — easy to grep, easy to PR.

---

## Dashboard visuals

Open the **Tokens** page in the Naqi desktop app to see three visual summaries of token cost.

### 7-day token trend chart

Area chart at the top of the page showing the total daily token consumption across all projects for the last 7 days. Mirrors the shape of the main `HealthTrend` chart so the visual language stays consistent. Tooltip shows full date, total tokens, and the session/project counts for that day. Delta indicator top-right is inverted from the health trend — **upward is red** (more usage = worse), **downward is green** (less usage = better).

Data source: `~/.naqi/token-history.json`, appended on every token scan. Capped at 90 entries.

### Per-session source breakdown pie

Inside each expanded session row, a small 120×120 donut decomposes the session's total billed tokens into five buckets:

- **Base context** — residual, neutral session content
- **Subagents** — exact billed subagent usage (pulled directly from `subagent_usage`)
- **Skill listing** — auto-loaded skill registry, estimated from raw bytes × cache-replay multiplier
- **Log pastes** — estimated from `log_paste_bytes` × turn multiplier
- **Images** — `image_paste_count` × ~1500 tokens/image × turn multiplier

The non-subagent buckets are **estimated** — the header caption says so — because pasted content sits in the context window for the rest of the session and gets re-billed on every cache read, and that compounded cost isn't directly tracked by the Anthropic API. Estimates are clamped so extras never exceed `(total − subagents)`. The leftover becomes base context.

### 30-day per-project waste banner

Inside each expanded project row, a compact warning-colored card showing **~X tokens spent last month on preventable patterns, here are the top 3**. Self-hides when the total estimated waste is under 500 K tokens — clean projects show nothing at all.

The top 3 rows each show icon + label + token count + actionable hint. Uses the same formulas as the per-session pie but aggregated over a 30-day lookback window filtered on `started_at`. Subagents are intentionally excluded from "waste" — legitimate use is too common to call the bucket preventable.

---

## Proactive nudges (notifications)

Three notification types related to Token Hygiene, all toggleable in **Settings → Notifications**:

### Weekly token diet

Runs after every scheduled scan, dedup'd once per 7 days. Reports this week's session count, total tokens, top project, and waste-pattern count. Severity escalates to Warning when ≥10 waste patterns are detected in the week. Actionable — click to open the Tokens page.

### Project hotspot

Fires when a single project consumes ≥60 % of the tokens spent in the last 7 days. Severity: Info ≥60 %, Warning ≥75 %, Critical ≥90 %. Dedup key embeds the project name so different hotspots across weeks still fire. Requires ≥5 M weekly total to avoid firing on low-volume users where distribution is meaningless.

### Live session threshold alert

A background watcher thread polls token usage every 60 seconds and fires a `SessionThresholdAlert` the moment a live session crosses **50 turns** or **200 M tokens**. "Live" means `ended_at` is within the last 15 minutes — historical sessions that long ago crossed thresholds are ignored. Each session alerts at most once per app lifetime (in-memory seen-set; restart resets).

The watcher is a separate thread from the main scheduler and uses the existing token-cache mtime invalidation, so each poll only re-parses session files that have actually changed. Cheap enough to run every minute even on 300+ session histories.

Severity escalates by "how over threshold": Info at ≥1×, Warning at ≥2×, Critical at ≥4× (using the max ratio of turns/50 and tokens/200 M).

Skip the 30-second startup grace period and the live alert when you just launched the app — that's intentional, to avoid popping alerts while the main window is still painting.

---

## CLI reference

The `naqi-cli` binary ships with the app and exposes the full Token Hygiene analysis surface for terminal and CI use.

### `naqi tokens`

Per-project token breakdown across all Claude Code sessions.

```bash
naqi tokens                    # Table output
naqi tokens --json             # JSON for scripting
naqi tokens --project naqi     # Filter to one project
```

### `naqi tokens top`

Top N costliest sessions by total token cost.

```bash
naqi tokens top                # Default: top 10
naqi tokens top --n 25         # Top 25
naqi tokens top --json
```

Each row shows session ID, project, turn count, total tokens, and first-prompt preview.

### `naqi tokens waste`

All waste-pattern recommendations across cached sessions, grouped by severity. This is the same list that powers the Tokens-page waste banner.

```bash
naqi tokens waste              # Human-readable
naqi tokens waste --json       # JSON
naqi tokens waste --project naqi  # Filter by project
```

### `naqi tokens health <session-id>`

Per-session health score breakdown showing the contributing factors (turn count, cache bloat, auto-compacts, subagent swarm, dive-deep opener).

```bash
naqi tokens health 084d1abb-85f2-4f95-8c36-a1ebc3d5abb7
naqi tokens health <id> --json
```

### `naqi lint-prompt`

Pre-send linter that runs four checks against a draft prompt and exits 1 on any issue. Designed to compose as a pre-commit hook, shell pipe, or Claude Code skill.

```bash
# Read from a file
naqi lint-prompt draft.md

# Read from stdin
echo "continue" | naqi lint-prompt

# Heredoc for multi-line drafts
cat <<'EOF' | naqi lint-prompt
Dive deep into the project and figure out why tests are failing.
EOF

# JSON output for tooling
naqi lint-prompt draft.md --json
```

Four rules:

| Rule | Severity | Fires when | Fix |
|---|---|---|---|
| `ShortPrompt` | Warning | Trimmed input <20 chars or <3 words | Batch instructions; `/clear` first |
| `DiveDeep` | Warning | Dive-deep-pattern regex match on the trimmed opener | Name the exact files/areas |
| `LogPaste` | Warning | Pasted log run ≥2 KB (same detector as the waste recommendation) | Save to a file, `Read` the path |
| `ImperativeNoAnchor` | Info | First word is imperative (fix/add/update/…) AND no path or `*.ext` token present | Name the target file(s) |

Exit codes: `0` when clean, `1` on any issue. Empty input is treated as clean (no work to lint).

---

## Claude Code skill: `lint-prompt`

An optional Claude Code skill wrapper ships in [`skills/lint-prompt/`](../../skills/lint-prompt/SKILL.md). Once installed, Claude Code will invoke `naqi lint-prompt` automatically when you draft a prompt and ask for feedback, paste a large log block, or say "does this look good to send?".

### Install

```bash
cp -r skills/lint-prompt ~/.claude/agent-skills/skills/
```

Requires `naqi-cli` on `PATH`. Claude Code picks up new skills on the next session start — no restart or rebuild.

### How it works

The skill is a thin wrapper around the CLI. Claude reads the draft (usually from the current conversation), invokes `naqi-cli lint-prompt` via the Bash tool, and presents the findings using the linter's own `title` + `hint` strings. The hints are designed to be actionable as-written — Claude doesn't paraphrase them.

If a draft fails the linter, Claude will suggest a concrete rewrite when the draft is short enough to edit inline, then ask whether to use the revised version. If you disagree with a specific finding (e.g., you insist on pasting the full log), just say so — the linter is advisory, not a hard gate.

See the [skill definition](../../skills/lint-prompt/SKILL.md) for the full trigger rules and presentation protocol.

---

## A token-efficient prompting playbook

The detectors encode nine concrete rules. Here they are as positive advice:

1. **Name your target.** Replace "fix the bug" with "fix the retry loop in `src/http/client.rs:42`". Every imperative opener should include a file path, a function name, or at minimum a directory.
2. **Reference files, don't paste them.** If the output of a command matters, save it to `/tmp/something.log` and tell Claude to `Read` the path. A pasted 10 KB log on a 60-turn session costs ~300 KB of billing; the same log `Read` from a file costs ~10 KB total.
3. **`/clear` at milestones.** Every time you finish one unit of work and start another, `/clear`. A 50-turn session that should have been three 15-turn sessions pays 3× the context cost on every cache read.
4. **Skip the dive-deep opener.** "Dive deep into the project" reliably triggers massive subagent fan-outs and broad file-system walks. Instead, ask one targeted question about one file or function, then follow up.
5. **Trim your skill registry.** Every skill in `~/.claude/agent-skills/skills/` gets auto-loaded on session start. If you have 1,400 skills and only invoke 3, you're paying for the other 1,397 on every cache read of every session.
6. **Avoid screenshot-review loops.** Pasting 5 screenshots during a UI iteration costs 5 × image_tokens × (session_length / 2). If the change is code-only, read the rendered markup or describe the issue in text.
7. **Batch short prompts.** "continue", "ok", "go" each re-bill the entire cached context. One long instruction beats ten short acknowledgements.
8. **Minimize background bash.** Every background `Bash` injects 2–3 turns of status messages. Foreground the commands you actually need results from; skip the ones you don't.
9. **Target subagents.** A subagent inherits a copy of the current context. Eight concurrent subagents means 8× context billing. Use one or two with narrow scopes instead of fan-outs.

All nine rules compile down to one heuristic: **treat your context window like RAM, not a disk.** Anything that sits in it gets paid for on every turn.

---

## Data storage

Everything Token Hygiene persists lives under `~/.naqi/`:

| File | Purpose | Capped at |
|---|---|---|
| `token-cache/manifest.json` | Per-session parsed stats with mtime-based invalidation | 1 entry per session file |
| `token-history.json` | Timestamped snapshots of aggregate usage for the trend chart | 90 entries |

Both files are atomic writes (tmp + rename). Deleting them just means re-parsing on the next scan — no data loss, and no configuration is kept in them.

## Architecture notes

- **Parser is strictly read-only.** `src-tauri/src/scanner/parsers/claude_sessions.rs` opens session JSONL files for read, never writes.
- **Cached session stats with mtime invalidation** — unchanged sessions are not re-parsed on subsequent scans, so polling every 60 s (the session watcher) is cheap.
- **All waste patterns surface via existing `RecommendationCard`** with `rec_type: TokenWaste`. No bespoke UI per detector.
- **Anonymizer handles prompt previews.** Paths, API keys, and URLs are stripped before any preview is displayed or submitted to an AI provider.
- **Coverage targets:** 90 %+ on parser, 85 %+ on detectors, 90 %+ on aggregation. Current counts live in `src-tauri/` — run `cargo test --lib` to see them.

## FAQ

**Does Naqi send my session data anywhere?**
No. All Token Hygiene analysis runs locally in Rust. The only time session content leaves your machine is if you manually use the AI-powered workspace recommendation feature in Settings, which anonymizes previews before submission.

**Can I turn off the live session watcher?**
Yes. **Settings → Notifications → Live session alerts**. You can also disable all notifications via the master toggle, or set Do Not Disturb for a few hours. The watcher short-circuits before it even calls `scan_tokens` when notifications are off.

**Can I tune the thresholds?**
Not at runtime — they're `const` values in `src-tauri/src/analyzer/token_waste.rs` and `src-tauri/src/notifications.rs`. PR-ready.

**Does this work with Claude Pro or API keys?**
Both. Token Hygiene reads session logs from `~/.claude/projects/`, which Claude Code writes regardless of how you auth. The cost math is the same — cache reads bill the same tokens whether they come out of a Pro subscription or a direct API bill.

**Why only Claude Code?**
Because Claude Code writes structured JSONL session logs with per-turn usage. Cursor, Windsurf, and most other clients either don't persist session data, persist it in less-structured formats, or don't expose per-turn tokens. Adding more client parsers is on the v1.0.0+ roadmap.

## See also

- [ROADMAP.md](../../ROADMAP.md) — full roadmap, including the v0.6.0 Token Hygiene milestone
- [CLI docs](../cli.md) — complete CLI reference for all Naqi commands
- [`skills/lint-prompt/SKILL.md`](../../skills/lint-prompt/SKILL.md) — Claude Code skill definition
- [CLAUDE.md](../../CLAUDE.md) — architectural notes for contributors

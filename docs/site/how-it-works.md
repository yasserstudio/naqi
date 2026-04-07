---
title: "How Naqi Works"
slug: how-it-works
description: "What Naqi reads, what it never touches, how cleanup is reversible, and why a tool that modifies your AI configs is safe to install."
url: /how-it-works
type: page
last_updated: 2026-04-07
---

> **Page intent (per [site-architecture.md](../business/site-architecture.md)):** trust intermediary in Phase 1. Ships day one. Single page, ~700 words. Job: convert the cautious developer who almost downloaded but wanted one more reassurance about installing software that touches config files. Plain text, no marketing voice, technical concrete details.

# How Naqi works

Naqi is a 10MB native macOS app that scans every AI client on your Mac, finds the dead MCP servers, stale memories, and contradictory skills dragging your AI down, and lets you clean them up with versioned backups and one-click undo.

Here's exactly what that means.

---

## The flow

**Scan → Review → Clean → Undo (if you change your mind).**

Three buttons, in that order. Nothing happens to any file on your Mac until you explicitly click Clean — and even then, every change is backed up before it runs.

### 1. Scan

When you click Scan (or hit `⌘R`), Naqi walks a known list of config file paths for 10 AI clients and reads them into memory. Reading takes about 3 seconds. The scan is **strictly read-only** — Naqi cannot write to anything during a scan, and the scanner code path is in the open source repo so you can verify this yourself.

What gets read:

| Client | Files |
|---|---|
| Claude Desktop | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Claude Code | `~/.claude.json`, `~/.claude/CLAUDE.md`, `~/.claude/projects/**/memory/` |
| Cursor | `~/Library/Application Support/Cursor/User/settings.json`, `~/.cursor/mcp.json` |
| VS Code | `~/Library/Application Support/Code/User/settings.json`, `~/.vscode/mcp.json` |
| Windsurf | `~/.codeium/windsurf/mcp_config.json` |
| GitHub Copilot | `~/Library/Application Support/GitHub Copilot/config.json` |
| JetBrains IDEs | `~/Library/Application Support/JetBrains/<product>/options/mcp.xml` |
| Zed | `~/.config/zed/settings.json` |
| Amp | `~/.config/amp/config.json` |
| Kiro | `~/.kiro/config.json` |

If a client isn't installed, its files don't exist, and Naqi skips it silently. If a file is malformed, Naqi reports it as a parse error and doesn't try to recover anything from it — better to surface the problem than guess.

### 2. Review

After the scan, Naqi shows you a dashboard with:

- A 0-100 health score
- Counts per artifact type (MCP servers, memories, skills, configs)
- A list of issues sorted by severity
- A trend line if you've scanned before

Reviewing is also strictly read-only. You can browse, search, sort, and filter forever without anything on disk changing.

### 3. Clean

When you decide to remove something, Naqi follows a strict 6-step protocol for every modification:

1. **Backup** — Naqi writes a timestamped copy of the original file to `~/.naqi/backups/` before touching anything. The backup is SHA-256 deduplicated so 10 backups of an unchanged file take the space of 1.
2. **Diff preview** — A side-by-side diff shows you exactly what's about to change. You can scroll, expand, and cancel.
3. **Confirm** — Cleanup never happens without an explicit click. There is no auto-clean.
4. **Apply** — Naqi performs a *targeted* edit (delete a single key from a JSON object), not a full file rewrite. Comments, formatting, and ordering are preserved. The change is wrapped in an atomic file write — if anything goes wrong mid-write, the original file is restored from the in-memory snapshot.
5. **Validate** — Naqi re-reads the file and verifies the JSON parses. If validation fails, Naqi automatically rolls back to the backup.
6. **Undo stack** — Every cleanup is pushed onto an undo stack. `⌘Z` reverses the last action. There is no time limit on undo.

### Backups

Every backup is browsable in the Backups page. You can View, Compare, Restore, or Export as ZIP at any point. Retention defaults to 90 days; configurable in Settings.

### Safe Mode (on by default for new users)

Safe Mode blocks all destructive actions until you explicitly turn it off in Settings. When Safe Mode is on, the cleanup buttons are greyed out and a shield icon appears in the sidebar. The only way to disable Safe Mode is from Settings — there's no nag dialog, no upsell, no countdown.

You can also lock individual clients (Settings → Locked Clients) so Naqi will never touch their files even when Safe Mode is off.

---

## What Naqi never touches

| | Why |
|---|---|
| **Files outside the known config path list** | The scanner is hard-coded to specific paths. It does not crawl your filesystem. |
| **Network requests during a scan** | Scanning is 100% local. No requests of any kind. |
| **Your AI provider API keys** | Stored in your macOS Keychain, never logged, never sent anywhere. Naqi only displays a masked version (first 6 + last 4 characters). |
| **Your project files, source code, or git history** | Naqi has no awareness these exist. |
| **System files, browser data, or anything not in the config list above** | Out of scope. |
| **Your data, ever, by default** | The only opt-in network call is the AI recommendations feature in Pro, and that sends an anonymized summary (paths reduced to basenames, project names stripped) to whichever AI provider you chose — Anthropic, OpenAI, Gemini, Grok, Ollama (fully offline), or OpenRouter. Skip Pro and Naqi never makes a network request. |

---

## What's open source

Everything. The scanner, the cleanup engine, the backup system, the AI integration, and the UI are all MIT-licensed on [GitHub](https://github.com/yasserstudio/naqi). The Pro tier gates AI recommendations and batch cleanup behind a license check, not behind closed source — if you want to fork the repo and bypass the check, you can. The packaged, signed, notarized, auto-updating app is the real product.

The scanner code path is `src-tauri/src/scanner/`. The cleanup protocol is in `src-tauri/src/cleanup/`. Read either one and verify the claims on this page yourself.

---

## TL;DR

| Question | Answer |
|---|---|
| Will Naqi delete something I need? | Not unless you click Clean, see the diff preview, and confirm. Even then, every change is backed up and can be undone. |
| Does Naqi send my data anywhere? | No, by default. The optional Pro AI recommendations send an anonymized summary to your chosen provider (Ollama runs fully offline). |
| What if Naqi breaks something? | The backup-first protocol creates a timestamped copy before every modification. Rolling back is one click. |
| Can I audit the code? | Yes — MIT licensed on GitHub, scanner and cleanup paths are clearly named. |
| What if I just want to look without touching anything? | Safe Mode is on by default. The scan, dashboard, and inventory are 100% read-only and free forever. |

---

[**Download Naqi for macOS →**](/download)

Free, 10MB, no signup, no telemetry. About 30 seconds to your first scan.

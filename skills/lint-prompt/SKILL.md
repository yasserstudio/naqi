---
name: lint-prompt
description: Run `naqi lint-prompt` on a draft Claude Code prompt to catch token-wasting patterns before sending. Use when the user is drafting a long prompt, asks "is this a good prompt?", mentions pasting logs/screenshots into a prompt, or is about to start a new expensive-looking session. The linter flags four issues — dive-deep language, short prompts that re-bill the cache, pasted log blocks over 2 KB, and imperative openers with no file anchor — and recommends a fix for each.
version: 1.0.0
license: MIT
---

# Lint a draft prompt with `naqi lint-prompt`

This skill wraps the `naqi lint-prompt` CLI, which runs four token-waste checks against any draft prompt you pipe in. Use it **before** sending an expensive prompt — once it's sent, the waste is already billed.

## When to use this skill

Trigger automatically when any of these are true:

- The user is drafting or pasting a long prompt (>3 lines) and asks whether it's OK to send, "does this look good?", "anything I should change?", etc.
- The user pastes a large log block or stack trace into a prompt they're about to send.
- The user asks for prompt feedback, prompt review, or "make this prompt cheaper/shorter/better."
- The user is starting a fresh Claude Code session and asks about opener best practices.
- The user explicitly asks for `naqi lint-prompt`, `/lint-prompt`, or "the naqi linter."

Do NOT use this for prompts you yourself are about to send to the user — this is for **user-authored drafts**, not for AI-generated content.

## How to run it

The `naqi-cli` binary (installed with Naqi) reads the draft from stdin or from a file argument and prints a report. Exit code is `0` when the prompt is clean, `1` when any issue is found.

Two invocation modes:

### 1. Draft in a file

```bash
naqi-cli lint-prompt /path/to/draft.md
```

### 2. Draft piped from stdin

```bash
echo "<the draft prompt>" | naqi-cli lint-prompt
```

Use a heredoc for multi-line drafts to avoid shell-quoting issues:

```bash
cat <<'EOF' | naqi-cli lint-prompt
<paste the full draft here>
EOF
```

Add `--json` when you want to parse results programmatically:

```bash
echo "<draft>" | naqi-cli lint-prompt --json
```

## What the linter checks

Every rule is conservative — it only fires when the cost is defensible. You can trust a green result.

| Rule | Severity | Fires when | Fix |
|---|---|---|---|
| **ShortPrompt** | Warning | Trimmed input is <20 chars or <3 words | Batch instructions into one full prompt, or `/clear` first to reset the cached context |
| **DiveDeep** | Warning | Opener matches "dive deep", "understand the project", "check everything", etc. | Name the exact files/areas to examine instead |
| **LogPaste** | Warning | Pasted log block ≥2 KB (≥5 consecutive log-like lines) | Save the output to a file and reference the path (`Read /tmp/build.log`) — billed once, not every turn |
| **ImperativeNoAnchor** | Info | First word is imperative (fix/add/update/…) AND no file path or `*.ext` token is present | Name the target file(s) or directory so the work stays bounded |

## How to present results to the user

1. **Run the CLI** with the full draft. Use a heredoc if the draft has multiple lines, backticks, or quote marks.
2. **Read the output.** If exit code 0 and output is "Prompt looks clean — no lint issues", tell the user "Looks clean, safe to send" and stop.
3. **Summarize each flagged issue** using the linter's own `title` and `hint` lines. Don't paraphrase — the hints are designed to be actionable as-written.
4. **Suggest a concrete rewrite** if the draft is short enough to edit inline. Show the user a revised version that addresses every finding, then let them approve or edit further.
5. **If the user disagrees** with a specific finding (e.g., they insist on pasting the full log), respect it — the linter is advisory. Just note the cost and move on.

## Example session

User: "I'm about to send this — does it look okay?"

```
Dive deep into the project and figure out why builds are slow.

[ERROR] bundler exited 1 at turn 847
[ERROR] module not found 'foo'
[ERROR] syntax error at line 12
[ERROR] type mismatch
[ERROR] undefined is not a function
[ERROR] heap out of memory
[ERROR] EACCES permission denied
[ERROR] cannot find module 'bar'
```

You (the assistant):

1. Save the draft to a temp file (heredoc into `mktemp` works) and run `naqi-cli lint-prompt /tmp/draft.txt`.
2. The linter will flag DiveDeep (Warning) and — if the log block is >2 KB — LogPaste (Warning).
3. Summarize:
   > Two issues: the "dive deep" opener will trigger a broad context-bloating search, and the pasted error block will be re-billed on every subsequent turn. Here's a tighter draft:
   >
   > ```
   > Builds got slow around commit abc123. Read /tmp/build-errors.log for the full trace,
   > then look at vite.config.ts and package.json to see what changed.
   > ```
4. Ask whether to use the revised version.

## Installation

This skill ships with the Naqi desktop app. To enable it in Claude Code:

```bash
cp -r <path-to-naqi-repo>/skills/lint-prompt ~/.claude/agent-skills/skills/
```

The `naqi-cli` binary must already be on `PATH` — installed automatically by `brew install naqi` or bundled with the desktop app.

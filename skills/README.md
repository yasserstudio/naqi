# Naqi Claude Code skills

Optional skills that ship with Naqi and plug into Claude Code's agent-skill system. Each subdirectory is a self-contained skill — a `SKILL.md` with YAML frontmatter plus any supporting references.

## Installing

Copy the skill directory into your Claude Code agent-skills folder:

```bash
cp -r skills/lint-prompt ~/.claude/agent-skills/skills/
```

Claude Code will pick it up on the next session start. No restart or rebuild required.

## Available skills

| Skill | Purpose | Requires |
|---|---|---|
| [`lint-prompt`](./lint-prompt/SKILL.md) | Run `naqi lint-prompt` against a draft prompt before sending. Flags dive-deep openers, short prompts that re-bill the cache, pasted log blocks over 2 KB, and imperative openers without a file anchor. | `naqi-cli` on `PATH` |

## Why as a skill

These wrappers are *advisory* — they only fire when the user is already drafting something that looks expensive. Delivering the behavior as an opt-in skill keeps the trigger contextual (Claude decides to use it when relevant) instead of forcing you to remember to run a CLI every time.

If you prefer explicit control, you can always invoke the underlying CLI directly — every skill here is a thin wrapper around a `naqi-cli` subcommand that you can run yourself.

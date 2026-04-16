---
title: "I Audited My MCP Servers — Here's the Bloat I Found"
slug: i-audited-my-mcp-servers
description: "A first-person audit of my AI workspace across Claude Desktop, Claude Code, Cursor, and 7 other clients. The numbers were worse than I expected."
date: 2026-04-07
author: Yasser
canonical: https://getnaqi.com/blog/i-audited-my-mcp-servers
tags:
  - mcp
  - claude
  - cursor
  - workspace-hygiene
  - build-in-public
---

> **Author note:** This post is a template — replace the numbers in `[brackets]` with your actual scan results before publishing. The story works best when the numbers are real and surprising, even (especially) if they're embarrassing.
>
> **CTA pattern (per [site-architecture.md](../business/site-architecture.md)):** Render this post with a sticky inline CTA at 30% scroll depth and an inline CTA block at the natural pivot point (between "Why this happens" and "The pattern"). Both link to `/download`. Footer-only CTAs convert at ~10% of inline CTAs — do not rely on the closing paragraph alone.

# I Audited My MCP Servers — Here's the Bloat I Found

A few weeks ago I added an MCP server to Claude Code for a client project. I needed it for about 40 minutes. I never disconnected it.

Last week I sat down and counted everything connected to my AI tools across every client I use. I'm a developer who builds an AI workspace cleanup tool, so I went in expecting to find nothing too bad — maybe a couple of stale entries, nothing dramatic.

Here's what I actually found.

## The numbers

Across **[10]** AI clients on my Mac:

| Artifact | Count | Last actually used |
|---|---|---|
| MCP servers | **[14]** | **[6 used in the last 30 days. 8 untouched for 60+ days.]** |
| Memories | **[47]** | **[3 referenced in the last week. 12 contradicted each other.]** |
| Skills | **[23]** | **[5 triggered in the last month. The rest sit in the context window doing nothing.]** |
| Config files | **[19]** | **[4 still pointed at projects I shipped or killed.]** |

Total weight in my AI's context window: **[somewhere between "noticeable" and "this is why Claude has felt slower lately"]**.

I'd been blaming the model.

## What was actually in there

Some of the worst offenders:

- **A Postgres MCP server** for a database that no longer exists. The credentials were still in `~/Library/Application Support/Claude/claude_desktop_config.json`. The credentials still worked against an old staging environment I forgot to tear down.
- **A skill called "react-component-generator"** I installed from a blog post in **[January]**. Looked at it once. Never triggered. Has been injecting **[~800 tokens]** into every single Claude Code session for **[3 months]**.
- **A memory that said "user prefers tabs"** and another memory, two months later, that said "user prefers spaces." Both were technically true at different points in different projects. Claude would occasionally apply one when it should have applied the other.
- **An MCP server for a project I shipped in February**. The project's been live for 6 weeks. The MCP server was still authenticated, still listed in 3 different clients (Claude Desktop, Cursor, and VS Code), all pointing at the same dead repo.
- **Two separate "github" MCP servers** in two different clients, configured slightly differently. I have no memory of why.

The thing that bothered me most wasn't any single entry. It was how *invisible* the accumulation had been. Every single one of these made sense the day I added it. None of them ever triggered a "you should remove this" prompt because **there is no such prompt**.

## Why this happens

There's an install flow for everything in the AI ecosystem. There's a removal flow for nothing.

When you add an MCP server, every AI client makes it easy: paste the JSON, restart, done. When you install a skill, you run a CLI command and it shows up. When Claude saves a memory about you, it doesn't ask permission — it just remembers.

But none of these surfaces asks the obvious follow-up question: *do you still need this in 60 days?*

Even worse, [the situation is broken on purpose in some cases](https://github.com/anthropics/claude-code/issues/30138). Claude Code's `/mcp` UI lets you "disable" a server but doesn't actually remove it — and disabled MCP servers still inject their tool definitions into your context window. The only way to actually remove one is to edit `~/.claude.json` by hand.

So you don't. Nobody does. You add things, you forget about them, and your AI gets quietly worse.

## The pattern

I talked to **[a few]** developer friends about this and asked them to count their MCP servers and skills before answering anything else. The pattern was identical:

- Everyone underestimated their inventory by **[at least half]**
- Nobody could name what every server did
- Every single person had at least one connection authenticated to a project they no longer worked on
- The most experienced developers had the *most* bloat — because they've been using AI tools the longest

This isn't a "you're doing it wrong" thing. It's a **structural gap in the AI tooling stack**. The tools all assume you'll manage cleanup yourself, and nobody does.

## The boring fix

The boring fix is: every **[60 / 90 / 30 — pick whatever makes sense for you]** days, sit down and audit your AI workspace. Open every config file. Check every MCP server. Read every memory. Decide what's still useful. Edit the JSON. Restart the clients.

Almost nobody will do this.

## What I built

I'm building a desktop app called Naqi that does the boring fix for me. It scans every AI client on my Mac, shows me everything in one dashboard with a health score, and lets me clean things up with versioned backups and one-click undo. I built the free version for myself first; I'm shipping it because **[a few]** of the people I showed it to said they wanted it.

It's not the only tool in this space. [Golf Scanner](https://github.com/) is an open-source CLI that does multi-client MCP discovery. [mcp-sec-audit](https://github.com/) is the best thing I've seen for hunting malicious MCP servers. I wrote a [comparison post](./mcp-cleanup-tools-compared.md) explaining when to pick each. If you're a CLI person who only cares about MCP, those might be a better fit than Naqi.

But if you want a desktop app that also covers memories and skills, with cleanup and undo, and you're on macOS, Naqi is at [getnaqi.com](https://getnaqi.com). The scan is free, no signup, and it'll take about 30 seconds to find out how bad your own bloat is.

## Try this even if you don't use Naqi

Here's a free, no-tool exercise. Open your `claude_desktop_config.json` (or whichever client you use most) right now and answer three questions about each MCP server in the list:

1. **What does this server do?** If you can't answer in one sentence, you don't need it.
2. **When did I last actually use a tool from this server?** If "I don't know," you probably don't need it.
3. **What does this server still have access to?** Check the API keys, the database connection strings, the GitHub tokens. Are any of them for projects that ended?

If you go through this exercise honestly, you'll remove **[at least a third]** of what's in there. You'll also feel slightly better about how Claude responds afterwards — not because the model changed, but because you stopped feeding it noise.

Then write the cleanup down somewhere so you remember to do it again in two months. Or use a tool that does it for you.

---

*I'm Yasser. I build [Naqi](https://getnaqi.com), the token watchdog for Claude Code — a 10MB desktop app with a live session watcher, 9 waste-pattern detectors, and a weekly TokenDiet digest, plus cleanup across 10 AI clients. The scanner is free forever (including a 7-day Token Hygiene snapshot with $ waste figure); Pro is $59/yr or $7.99/mo with a 14-day unconditional refund.*

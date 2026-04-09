---
title: "Download Naqi"
slug: download
description: "Download Naqi for macOS — free, 10MB, no signup, no telemetry. Universal binary for Apple Silicon and Intel. Or install with Homebrew."
url: /download
type: page
last_updated: 2026-04-07
---

> **Page intent (per [site-architecture.md](../business/site-architecture.md)):** single-purpose conversion page. Bottom of the funnel. Fewest words on the site. No marketing — the user is here to download, not to be convinced. Two install paths (direct + Homebrew), system requirements, verification info, "what happens next" in 3 lines, help link. That's it.

# Download Naqi

[**Download Naqi for macOS →**](https://github.com/yasserstudio/naqi/releases/latest/download/Naqi.dmg)

Free · 10MB · Universal binary (Apple Silicon + Intel) · No signup, no telemetry

---

## Or install with Homebrew

```bash
brew tap yasserstudio/naqi
brew install --cask naqi
```

The cask installs the same `.dmg` from the GitHub release and is updated within an hour of every new version.

---

## System requirements

- **macOS 12 (Monterey) or later**
- **Apple Silicon or Intel** — single universal binary, no separate downloads
- About 30 MB of disk space (the binary plus a small data directory at `~/.naqi/`)
- Internet only required if you opt into Pro AI recommendations — the free tier runs fully offline

Windows and Linux are on the roadmap but not shipping at launch.

---

## Verification

Naqi is signed and notarized by Apple, so you can install it without disabling Gatekeeper.

| Detail | Value |
|---|---|
| Developer ID | Yasser Studio (Team ID published in the release notes) |
| Signing | Apple Developer ID Application certificate |
| Notarization | Notarized by Apple, ticket stapled |
| SHA-256 hash | Published in every [GitHub release](https://github.com/yasserstudio/naqi/releases/latest) under "Assets" |

If macOS still warns you about an unidentified developer, the download is corrupt — re-download and try again. Naqi will never ship a build that fails Gatekeeper.

---

## After you install

1. Open Naqi from Applications. The first launch takes about 2 seconds.
2. Click **Scan**. The first scan walks all 10 supported AI clients and finishes in about 3 seconds.
3. Browse the dashboard. Nothing is touched on disk until you explicitly click Clean — see [How it works](/how-it-works) for the full safety protocol.

---

## Other ways to install

- [GitHub release page](https://github.com/yasserstudio/naqi/releases/latest) — full release notes, asset list, hashes
- Direct `.dmg` download — the button at the top of this page
- Homebrew cask — the snippet above

---

## If something goes wrong

Email [yasser@getnaqi.com](mailto:yasser@getnaqi.com) or [open a GitHub issue](https://github.com/yasserstudio/naqi/issues). One person reads both. Reply expected within 48 hours.

For security concerns, see [SECURITY.md](https://github.com/yasserstudio/naqi/blob/main/SECURITY.md).

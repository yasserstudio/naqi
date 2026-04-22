# Language & Localization

Naqi ships in **5 languages** across both the desktop app and the marketing website.

## Supported languages

| Language | Code | Desktop app | Website |
|---|---|---|---|
| English | en | ✓ (source) | ✓ (source) |
| Español (Spanish) | es | ✓ | ✓ |
| Français (French) | fr | ✓ | ✓ |
| Deutsch (German) | de | ✓ | ✓ |
| Português do Brasil | pt-BR | ✓ | ✓ |

## Changing the language

### Desktop app

**Settings → General → Appearance → Language.** Pick a language from the dropdown or leave it on "System" to follow your OS locale. The change takes effect immediately — no restart required.

The Rust backend and the React frontend both swap their catalogs on the fly. UI labels, notifications, toasts, error messages, recommendations, the macOS menu bar, and the system tray all update in place. The boot splash screen also displays status messages in the chosen language on next launch.

### Website

Click the globe icon in the site header (or the language list in the mobile menu). Your choice is saved in a `NEXT_LOCALE` cookie and persists across visits.

### Native macOS notifications

Native system notifications (the banners macOS shows outside the app) display fully localized titles and bodies for all 12 notification types: health drops, broken servers, stale servers, duplicates, contradictory memories, cleanup follow-ups, scan reminders, weekly digests, token diets, project hotspots, and session threshold alerts. Plural-dependent strings (e.g. "3 stale servers") use pre-split `_one`/`_other` key variants so the Rust backend can resolve them without an ICU parser.

## What stays in English

The following terms are never translated, regardless of locale.

### Product & brand names

| Term | Notes |
|---|---|
| Naqi | Product name |
| Naqi Pro | Paid tier |
| Naqi Free | Free tier |
| Yasser's Studio | Publisher name |

### Feature names

| Term | Notes |
|---|---|
| TokenDiet | Token-optimization feature |
| Smart Scan | Automated workspace scan |
| Safe Mode | Read-only protection mode |
| Config Profiles | Server configuration snapshots |

### Technical terms

| Term | Notes |
|---|---|
| MCP | Model Context Protocol |
| API | Application Programming Interface |
| CLI | Command Line Interface |
| JSON | Data format |
| URL | Web address |
| DELETE | Confirmation keyword (typed by user) |

### AI providers & tools

| Term | Notes |
|---|---|
| Claude Code | Anthropic's AI coding tool |
| Cursor | AI code editor |
| Windsurf | AI code editor |
| Ollama | Local AI runtime |
| GitHub Copilot | AI coding assistant |

### Pricing

Prices appear in USD only: $59/yr, $7.99/mo. Currency symbols and amounts are never localized.

## Scope

- **Desktop app:** 1155 translated keys per locale covering every page, dialog, notification, toast, error message, menu bar, boot splash, native notification bodies, dashboard widgets, settings, server forms, and onboarding
- **Website:** 993 translated keys per locale covering all pages, SEO metadata, emails, and legal copy
- **Documentation:** English only

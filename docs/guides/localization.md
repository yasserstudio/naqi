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

Native system notifications (the banners macOS shows outside the app) display localized titles for health drops, broken servers, scan reminders, weekly digests, token diets, project hotspots, and session threshold alerts. Notification bodies remain English in the native banner; the full localized body is always shown in the in-app notification center.

## What stays in English

Product names (Naqi, Naqi Pro, Naqi Free), feature names (TokenDiet, Smart Scan, Safe Mode), technical terms (MCP, API, CLI), AI provider names, and prices ($59/yr, $7.99/mo) are never translated. See the full list in the project glossary.

## Scope

- **Desktop app:** 919 translated keys per locale covering every page, dialog, notification, toast, error message, menu bar, and boot splash
- **Website:** 993 translated keys per locale covering all pages, SEO metadata, emails, and legal copy
- **Documentation:** English only

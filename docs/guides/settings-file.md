# Settings File

Naqi stores your preferences in a human-readable TOML file that you can edit, sync, and version-control.

## Location

| Platform | Path |
|---|---|
| macOS | `~/.naqi/settings.toml` |
| Windows | `%APPDATA%\naqi\settings.toml` |
| Linux | `~/.naqi/settings.toml` |

Open it from Naqi: **Settings > General > Edit settings file**, or copy the path with **Copy path**.

## Format

```toml
# Naqi settings — portable, human-editable, git-friendly.
# Changes take effect immediately.
schema_version = 1

[appearance]
theme = "dark"           # "dark" | "light" | "system"
font_family = "inter"    # "inter" | "system"
locale = "en"            # BCP-47 tag (en, es, fr, de, pt-BR)

[schedule]
enabled = false
interval_minutes = 240   # 4 hours

[notifications]
enabled = true
threshold = 70           # Health score alert threshold (0-100)
quiet_hours_enabled = false
quiet_hours_start = "22:00"
quiet_hours_end = "07:00"

[notifications.types]
scan_reminder = true
health_drop = true
stale_server = true
broken_server = true
new_duplicates = true
contradictory_memory = true
cleanup_follow_up = true
weekly_digest = true
token_diet = true
project_hotspot = true
session_threshold_alert = true

[safety]
safe_mode = true

[data]
backup_retention_days = 90
display_name = "Your Name"
```

## Syncing across machines

`settings.toml` is designed to be portable. Sync it via:

- **Dotfiles repo** — symlink `~/.naqi/settings.toml` to your dotfiles
- **iCloud Drive** — symlink to `~/Library/Mobile Documents/com~apple~CloudDocs/`
- **Dropbox / OneDrive** — same symlink approach

Machine-specific state (API keys, scan timestamps, onboarding progress) is stored separately in `state.json` and never leaves your device.

## What stays local

These are **not** included in `settings.toml` and cannot be synced:

- API key configuration (stored in OS keychain)
- AI provider and model selection
- Last scan timestamp
- Do Not Disturb schedule
- Locked clients list
- Onboarding state

## System policy (IT/MDM)

Organizations can enforce settings via a read-only policy file:

| Platform | Path |
|---|---|
| macOS | `/Library/Application Support/com.naqi.app/policy.toml` |
| Windows | `C:\ProgramData\naqi\policy.toml` |
| Linux | `/etc/naqi/policy.toml` |

Policy files use the same TOML format as `settings.toml`, plus a `[policy]` section:

```toml
[policy]
locked_keys = ["safety.safe_mode", "schedule.enabled"]

[safety]
safe_mode = true
```

Locked keys appear disabled in the Settings UI with a lock icon.

## Migration from config.json

If you're upgrading from a version that used `config.json`, Naqi automatically splits it into `settings.toml` + `state.json` on first launch. The original file is preserved as `config.json.migrated`.

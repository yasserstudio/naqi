# Config Profiles

**Pro feature.** Save your current MCP server configuration as a reusable profile, then apply it to any supported AI client in one click.

## What a profile captures

A profile snapshots every MCP server from your most recent scan — server name, command, args, env vars, and which client it came from. Profiles don't capture memories, skills, or non-MCP settings.

## Create a profile

1. Run a Smart Scan (⌘R) so Naqi has a fresh workspace snapshot
2. Open **Profiles** in the sidebar
3. Click **Create Profile**
4. Name it (e.g., "Work setup", "Minimal dev")

Naqi captures all servers from the scan. The profile is stored locally in `~/.naqi/profiles/`.

## Apply a profile

Right-click a profile → **Apply to Client** → pick the target client (Claude Desktop, Cursor, VS Code, etc.). Naqi adds missing servers and updates changed ones. Existing servers not in the profile are left untouched.

Every apply follows the same backup-first safety protocol as cleanup: timestamped backup → diff preview → confirm → atomic write → validation.

## Export and import

- **Export:** right-click → Export. Saves a `.json` file you can share with teammates or version-control.
- **Import:** click the Import button and select a previously exported profile `.json`.

## Use cases

- **Team standardization:** export your team's MCP server config as a profile, share the JSON, and have everyone import + apply it
- **Client migration:** moving from Cursor to Claude Code? Apply your profile to the new client instead of manually copying servers
- **Environment switching:** keep separate profiles for work vs personal projects and apply the right one per context

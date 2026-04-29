# Uninstall Naqi

Complete removal instructions for every platform. After uninstalling, no Naqi files remain on your machine.

## macOS

### 1. Remove the app

```bash
# If installed via Homebrew
brew uninstall --cask naqi

# If installed via DMG
rm -rf /Applications/Naqi.app
```

### 2. Remove data and caches

```bash
rm -rf ~/.naqi
rm -rf ~/Library/Caches/com.yasserstudio.naqi
rm -rf ~/Library/Application\ Support/com.yasserstudio.naqi
rm -rf ~/Library/Logs/com.yasserstudio.naqi
rm -rf ~/Library/WebKit/com.yasserstudio.naqi
```

### 3. Remove keychain entry (if you used Pro)

```bash
security delete-generic-password -s "com.yasserstudio.naqi" 2>/dev/null
```

### 4. Remove launch agent (if scheduled scans were enabled)

```bash
launchctl unload ~/Library/LaunchAgents/com.yasserstudio.naqi.plist 2>/dev/null
rm -f ~/Library/LaunchAgents/com.yasserstudio.naqi.plist
```

## Windows

1. Uninstall via **Settings → Apps → Naqi → Uninstall**
2. Remove remaining data:

```powershell
Remove-Item -Recurse -Force "$env:USERPROFILE\.naqi"
Remove-Item -Recurse -Force "$env:APPDATA\com.yasserstudio.naqi"
Remove-Item -Recurse -Force "$env:LOCALAPPDATA\com.yasserstudio.naqi"
```

## Linux

### Debian/Ubuntu (.deb)

```bash
sudo dpkg -r naqi
rm -rf ~/.naqi
rm -rf ~/.config/com.yasserstudio.naqi
rm -rf ~/.local/share/com.yasserstudio.naqi
```

### AppImage

Delete the AppImage file, then remove data:

```bash
rm -rf ~/.naqi
rm -rf ~/.config/com.yasserstudio.naqi
rm -rf ~/.local/share/com.yasserstudio.naqi
```

## What gets removed

| Location | Contents |
|---|---|
| `~/.naqi/` | Backups, scan cache, token history, profiles, `settings.toml`, `state.json` |
| App support / config dirs | WebView cache, window state, logs |
| Keychain / credential store | Pro license key (if activated) |
| Launch agent / autostart | Scheduled scan entry (if enabled) |

Uninstalling Naqi does **not** modify your AI client config files. Any changes Naqi made via cleanup are permanent (though backed up in `~/.naqi/backups/` before removal — copy them first if you want to roll back).

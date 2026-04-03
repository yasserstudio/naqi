# Distribution Guide

This document covers code signing, notarization, Homebrew distribution, and the release process for the Naqi desktop app.

## macOS Code Signing

Tauri v2 handles code signing automatically when the correct environment variables are set.

### Required Environment Variables

| Variable | Description |
|----------|-------------|
| `APPLE_SIGNING_IDENTITY` | Name of the Developer ID Application certificate (e.g., `Developer ID Application: Your Name (TEAM_ID)`) |
| `APPLE_ID` | Apple ID email used for notarization |
| `APPLE_PASSWORD` | App-specific password for notarization (generate at [appleid.apple.com](https://appleid.apple.com/account/manage)) |
| `APPLE_TEAM_ID` | 10-character Apple Developer Team ID |

### Certificate Setup

1. Enroll in the [Apple Developer Program](https://developer.apple.com/programs/).
2. In Xcode or the Apple Developer portal, create a **Developer ID Application** certificate.
3. Install the certificate in your macOS Keychain.
4. Verify it is available:
   ```bash
   security find-identity -v -p codesigning
   ```
5. Set the `APPLE_SIGNING_IDENTITY` variable to the full certificate name shown in the output.

### Entitlements

The file `src-tauri/entitlements.plist` declares the app sandbox entitlements:

- `com.apple.security.app-sandbox` -- enables App Sandbox
- `com.apple.security.network.client` -- allows outbound network connections (needed for AI API calls and the auto-updater)
- `com.apple.security.files.user-selected.read-only` -- allows reading user-selected files

Both the entitlements and signing identity are configured in `tauri.conf.json`:

```json
{
  "bundle": {
    "macOS": {
      "signingIdentity": "-",
      "entitlements": "entitlements.plist"
    }
  }
}
```

The `signingIdentity: "-"` uses ad-hoc signing for local dev builds. In CI, the `APPLE_SIGNING_IDENTITY` environment variable overrides this with the full Developer ID certificate.

## Notarization with Tauri v2

Tauri v2 performs notarization automatically during `pnpm tauri build` when all four environment variables are set. No additional tooling or configuration is needed.

### How It Works

1. Tauri builds the app and creates the `.app` bundle.
2. The bundle is signed with the Developer ID certificate.
3. The signed bundle is submitted to Apple's notarization service.
4. Tauri polls for notarization status and staples the ticket to the bundle.
5. The DMG is created from the notarized `.app`.

### Running a Signed Build

```bash
export APPLE_SIGNING_IDENTITY="Developer ID Application: Your Name (TEAMID)"
export APPLE_ID="your@email.com"
export APPLE_PASSWORD="xxxx-xxxx-xxxx-xxxx"
export APPLE_TEAM_ID="TEAMID"

pnpm tauri build
```

The signed and notarized DMG will be at:
```
src-tauri/target/release/bundle/dmg/Naqi_0.1.0_aarch64.dmg
```

### Troubleshooting

- **"No identity found"** -- ensure the certificate is installed in the login keychain and is not expired.
- **Notarization rejected** -- check the report at [Apple's notarization log](https://developer.apple.com/account) for specific issues. Common causes: unsigned dylibs, hardened runtime violations.
- **App-specific password invalid** -- generate a new one at [appleid.apple.com](https://appleid.apple.com/account/manage). Two-factor authentication must be enabled.

## Homebrew Tap

Naqi is distributed via a Homebrew cask through a dedicated tap repository.

### Setting Up the Tap

1. Create a public GitHub repository named `homebrew-naqi` under the `yasserstudio` org.
2. Add the cask formula at `Casks/naqi.rb` (the source template is at `dist/homebrew/naqi.rb` in this repo).
3. Users install with:
   ```bash
   brew tap yasserstudio/naqi
   brew install --cask naqi
   ```

### Updating the Formula After a Release

After each release, update the cask with the new version and SHA256:

```bash
# Get SHA256 for the new DMG
shasum -a 256 Naqi_0.2.0_aarch64.dmg
shasum -a 256 Naqi_0.2.0_x64.dmg
```

Update `version` and replace `sha256 :no_check` with the actual checksums:

```ruby
cask "naqi" do
  version "0.2.0"

  on_arm do
    sha256 "abc123..."
    url "https://github.com/yasserstudio/naqi/releases/download/v#{version}/Naqi_#{version}_aarch64.dmg"
  end
  on_intel do
    sha256 "def456..."
    url "https://github.com/yasserstudio/naqi/releases/download/v#{version}/Naqi_#{version}_x64.dmg"
  end
  # ...
end
```

## Release Process

### Overview

```
Tag push --> CI builds --> Signed DMG --> GitHub Release --> Update Homebrew SHA
```

### Step-by-Step

1. **Bump version** in `src-tauri/tauri.conf.json` and `package.json`:
   ```bash
   # Update version in both files to the new version
   ```

2. **Commit and tag**:
   ```bash
   git add -A
   git commit -m "chore: release v0.2.0"
   git tag v0.2.0
   git push origin main --tags
   ```

3. **CI builds the release** (GitHub Actions):
   - Runs `pnpm tauri build` on macOS runners (both `macos-latest` for ARM and `macos-13` for Intel).
   - Code signing and notarization happen automatically via the environment variables stored as GitHub Actions secrets.
   - Produces: `Naqi_VERSION_aarch64.dmg`, `Naqi_VERSION_x64.dmg`, and `latest.json` (for the auto-updater).

4. **Create GitHub Release**:
   - The CI workflow creates a draft release attached to the tag.
   - Upload the DMGs and `latest.json` as release assets.
   - Write release notes and publish.

5. **Update Homebrew tap**:
   - Compute SHA256 for both DMGs.
   - Update `Casks/naqi.rb` in the `homebrew-naqi` repository with the new version and checksums.
   - Commit and push.

### GitHub Actions Secrets

Add these secrets to the `naqi` repository settings:

| Secret | Value |
|--------|-------|
| `APPLE_CERTIFICATE` | Base64-encoded `.p12` certificate (`base64 -i cert.p12`) |
| `APPLE_CERTIFICATE_PASSWORD` | Password for the `.p12` file |
| `APPLE_SIGNING_IDENTITY` | Full certificate name (e.g., `Developer ID Application: Name (TEAMID)`) |
| `APPLE_ID` | Apple ID email |
| `APPLE_PASSWORD` | App-specific password |
| `APPLE_TEAM_ID` | 10-character Team ID |
| `TAURI_SIGNING_PRIVATE_KEY` | Updater private key (for signing `latest.json`) |
| `TAURI_SIGNING_PRIVATE_KEY_PASSWORD` | Updater key password |

## Auto-Updater

The auto-updater is already configured in `tauri.conf.json`:

```json
{
  "plugins": {
    "updater": {
      "endpoints": [
        "https://github.com/yasserstudio/naqi/releases/latest/download/latest.json"
      ],
      "pubkey": "..."
    }
  },
  "bundle": {
    "createUpdaterArtifacts": true
  }
}
```

### How It Works

1. On app launch, Naqi checks the `latest.json` endpoint for a newer version.
2. If an update is available, the user is prompted to download and install it.
3. The update signature is verified against the public key before installation.
4. The app restarts with the new version.

### Updater Key Management

The public key is stored in `tauri.conf.json`. The private key must be set as the `TAURI_SIGNING_PRIVATE_KEY` environment variable during CI builds. Never commit the private key to the repository.

To generate a new key pair:

```bash
pnpm tauri signer generate -w ~/.tauri/naqi.key
```

This outputs the public key to stdout and writes the private key to the specified path.

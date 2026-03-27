# Versioning Strategy

Naqi follows [Semantic Versioning 2.0.0](https://semver.org/spec/v2.0.0.html).

## Format

```
MAJOR.MINOR.PATCH[-PRERELEASE]
```

| Segment | When to bump | Example |
|---------|-------------|---------|
| **MAJOR** | Breaking changes to user-facing behavior, config format, or CLI interface | `1.0.0` → `2.0.0` |
| **MINOR** | New features, new client parsers, new recommendation types | `0.1.0` → `0.2.0` |
| **PATCH** | Bug fixes, performance improvements, dependency updates | `0.1.0` → `0.1.1` |
| **PRERELEASE** | Testing builds before a stable release | `0.1.0-alpha.1`, `0.1.0-beta.1`, `0.1.0-rc.1` |

## Pre-release Tags

| Tag | Meaning | Audience |
|-----|---------|----------|
| `alpha` | Feature-incomplete, may have known bugs | Internal testing only |
| `beta` | Feature-complete, seeking feedback | Early adopters |
| `rc` | Release candidate, no known blockers | Final validation before stable |

**Progression:** `alpha.1` → `alpha.2` → ... → `beta.1` → ... → `rc.1` → stable

## Current Phase

Naqi is in **pre-stable** (`0.x.y`). During this phase:
- Minor versions (`0.1.0` → `0.2.0`) may include breaking changes
- Patch versions (`0.1.0` → `0.1.1`) are always backwards-compatible
- No stability guarantees until `1.0.0`

## Planned Milestones

| Version | Codename | Scope |
|---------|----------|-------|
| `0.1.0` | MVP | Scanner + cleanup + dashboard for macOS |
| `0.2.0` | Insight | AI-powered recommendations via Claude API |
| `0.3.0` | Everywhere | Windows + Linux builds |
| `0.4.0` | Watchful | Scheduled scans + health history |
| `1.0.0` | Stable | CLI companion, plugin system, full a11y audit |

## Version Sources

Version is defined in two places and they **must stay in sync**:

| File | Field | Example |
|------|-------|---------|
| `package.json` | `"version"` | `"0.1.0"` |
| `src-tauri/Cargo.toml` | `version` | `"0.1.0"` |

## Git Tags

Every release gets a git tag prefixed with `v`:

```bash
# Stable release
git tag -a v0.1.0 -m "v0.1.0 — First public release"

# Pre-release
git tag -a v0.1.0-alpha.1 -m "v0.1.0-alpha.1"

# Push tag (triggers release workflow)
git push origin v0.1.0
```

**Tag rules:**
- Tags are immutable — never delete or move a published tag
- Tags trigger the release workflow in `.github/workflows/release.yml`
- Pre-release tags create draft releases; stable tags create published releases

## Version Bump Process

1. **Decide the version** based on changes since last release
2. **Update both files:**
   ```bash
   # package.json
   pnpm version 0.2.0 --no-git-tag-version

   # Cargo.toml — edit manually
   ```
3. **Update CHANGELOG.md** — move [Unreleased] items under new version header with date
4. **Commit:**
   ```
   chore: bump version to 0.2.0
   ```
5. **Tag and push:**
   ```bash
   git tag -a v0.2.0 -m "v0.2.0 — AI-powered recommendations"
   git push origin main --follow-tags
   ```
6. **Verify** the release workflow creates the GitHub Release draft

## What Counts as Breaking

In the `0.x` phase, these are breaking (bump minor):
- Removing or renaming a Tauri IPC command
- Changing the structure of `Workspace`, `McpServer`, or other shared models
- Changing the health score algorithm in a way that shifts bands
- Removing support for an AI client parser

These are **not** breaking (bump patch):
- Adding new fields to existing models (with defaults)
- Adding new IPC commands
- Adding new recommendation types
- Improving detection accuracy
- UI layout changes

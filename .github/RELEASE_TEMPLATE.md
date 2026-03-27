# Release Title Format

```
v{MAJOR}.{MINOR}.{PATCH} — {One-line summary}
```

**Examples:**
- `v0.1.0 — First public release`
- `v0.1.1 — Fix Cursor parser crash on empty configs`
- `v0.2.0 — AI-powered recommendations with Claude API`
- `v1.0.0 — Stable release`

---

# Release Description Template

```markdown
## Highlights

<!-- 1-3 sentences describing the most important change. Lead with user value. -->

## What's New

### Added
<!-- New features or capabilities -->
-

### Changed
<!-- Changes to existing functionality -->
-

### Fixed
<!-- Bug fixes -->
-

### Security
<!-- Security-related changes, if any -->
-

## Upgrade Notes

<!-- Breaking changes, migration steps, or things users should know before upgrading -->

## Stats

- **Tests:** {X} passing (Rust) + {Y} passing (frontend)
- **Clients supported:** Claude Desktop, Claude Code, Cursor, VS Code, Windsurf
- **Platform:** macOS (Apple Silicon + Intel)

## Installation

### Homebrew (recommended)
\`\`\`bash
brew install --cask naqi
\`\`\`

### Direct download
Download the `.dmg` from the assets below.

---

**Full changelog:** [`v{PREV}...v{CURRENT}`](https://github.com/yasserstudio/naqi/compare/v{PREV}...v{CURRENT})
```

---

# Versioning Strategy

**Semantic Versioning (SemVer)**

| Version | Meaning |
|---------|---------|
| `0.x.y` | Pre-stable. Breaking changes allowed between minors. |
| `0.1.0` | MVP — first public release |
| `0.2.0` | AI recommendations + memory analysis |
| `0.3.0` | Cross-platform (Windows + Linux) |
| `1.0.0` | Stable — public API locked, breaking changes bump major |

**Patch releases** (`0.1.1`, `0.1.2`) for bug fixes and minor improvements.
**Minor releases** (`0.2.0`, `0.3.0`) for new features.
**Major releases** (`1.0.0`) for stability milestones.

---

# Release Checklist

- [ ] All CI checks pass (`pnpm check` + `cargo test` + `cargo clippy`)
- [ ] CHANGELOG.md updated with release date
- [ ] Version bumped in `package.json` and `Cargo.toml`
- [ ] Git tag created: `git tag -a v{VERSION} -m "v{VERSION}"`
- [ ] Tag pushed: `git push origin v{VERSION}`
- [ ] GitHub Release created (draft → review → publish)
- [ ] Release notes include highlights, changes, and upgrade notes
- [ ] .dmg tested on clean macOS install
- [ ] Homebrew cask updated (when applicable)

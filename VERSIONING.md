# Versioning

Naqi follows [Semantic Versioning 2.0.0](https://semver.org/spec/v2.0.0.html).

## Current phase: public beta

Naqi is currently in **public beta** at `1.0.0-beta.N`. Every release during this phase bumps the beta identifier — `1.0.0-beta.1` → `1.0.0-beta.2` → and so on. The `1.0.0` stays pinned until the product is stable enough for a stable release.

**What beta means for you:**

- The app is real software you can use every day. It ships signed, notarized, and with full auto-update support.
- Breaking changes to behavior or UI may happen between betas — read [CHANGELOG](CHANGELOG.md) before upgrading if you rely on a specific workflow.
- If something breaks, Safe Mode + versioned backups + the full undo stack protect your configs.
- Your Pro license carries across beta bumps — no reactivation needed.

## Format

```
MAJOR.MINOR.PATCH[-PRERELEASE]
```

| Segment | Meaning |
|---------|---------|
| **MAJOR** | Breaking changes to app behavior, config format, or CLI interface |
| **MINOR** | New features or new AI-client parsers |
| **PATCH** | Bug fixes, performance improvements, dependency bumps |
| **PRERELEASE** | Pre-stable releases during the beta phase (`1.0.0-beta.N`) |

## After `1.0.0` stable

Once Naqi cuts a stable `1.0.0`, normal semver rules apply — `1.0.1` for patches, `1.1.0` for new features, `2.0.0` for anything that breaks your workflow.

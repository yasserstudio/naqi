# Naqi Documentation

> **The AI Workspace Cleaner** — Keep your AI workspace pure.

---

## Documentation Map

### Product
Core product vision, features, and roadmap.

- [Project Overview](product/project-overview.md) — Vision, problem statement, tech stack
- [User Personas](product/user-personas.md) — Target segments, profiles, and jobs-to-be-done
- [Pain Points](product/pain-points.md) — 6 core problems with real-world scenarios
- [Features](product/features.md) — Full feature spec with acceptance criteria
- [Solutions](product/solutions.md) — How each feature solves each pain point
- [User Flows](product/user-flows.md) — Screen-by-screen UX journeys for all core flows
- [Implementation Roadmap](product/implementation-roadmap.md) — Build plan with dependencies

### Technical
Architecture, data models, and implementation guides.

- [System Architecture](technical/architecture.md) — Module design, IPC contract, security model
- [Config Formats Reference](technical/config-formats.md) — Config schemas for every AI client
- [Data Models](technical/data-models.md) — Rust structs + TypeScript types
- [API Design](technical/api-design.md) — Claude/OpenAI integration, prompts, anonymization
- [Design System](technical/design-system.md) — Color tokens, typography, spacing, components, motion, accessibility
- [Development Guide](technical/development-guide.md) — Setup, conventions, testing, workflow
- [Testing Plan](technical/testing-plan.md) — 500 tests (370 Rust + 181 frontend + 20 E2E), coverage targets, fixtures
- [Error States & Edge Cases](technical/error-states-edge-cases.md) — Every error state and recovery behavior
- [GitHub Strategy](technical/github-strategy.md) — Repo posture, branch strategy, workflows, required secrets, issue management
- [Update Strategy](technical/update-strategy.md) — Update delivery, telemetry, forced-update gate, release process, bad-update playbook

### Distribution
Release, signing, and distribution.

- [Distribution Guide](distribution.md) — Code signing, notarization, Homebrew tap, release process, auto-updater
- [CLI Companion](cli.md) — Full CLI reference: scan, score, clean --apply, export, update with --json and --fail-below flags
- [Deployment](technical/deployment.md) — CI/CD, build process, platform targets

### Guides
User-facing explainers for specific feature sets.

- [Token Hygiene Guide](guides/token-hygiene.md) — The v0.6.0 Token Hygiene pillar: 9 waste-pattern detectors, 3 dashboard visuals, 3 proactive nudges, CLI reference, Claude Code skill wrapper, and a token-efficient prompting playbook

### Business
Strategy, pricing, market analysis, and growth.

- [Business Model & Pricing](business/business-model.md) — Revenue model, pricing tiers
- [Pricing Research](business/pricing-research.md) — Comparable products, willingness to pay, anchor analysis
- [Go-To-Market Strategy](business/go-to-market.md) — Launch plan, channels, growth loops
- [Launch Playbook](business/launch-playbook.md) — Day-by-day launch sequence
- [Competitive Analysis](business/competitive-analysis.md) — Landscape, positioning, moat
- [Financial Projections](business/financial-projections.md) — Revenue forecasts, milestones
- [Pro Tier Licensing](business/licensing.md) — License keys, validation, feature gating
- [Paywall Design](business/paywall-design.md) — Upgrade flow, gating UX
- [Site Architecture](business/site-architecture.md) — Information architecture for getnaqi.com: pages, URL conventions, navigation, phasing
- [Landing Page Spec](business/landing-page.md) — getnaqi.com homepage content and design
- [Landing Page Copy](business/landing-page-copy.md) — Final hero/section copy for getnaqi.com
- [Content Strategy](business/content-strategy.md) — Blog topics, SEO clusters, editorial calendar
- [Social Media Strategy](business/social-media-strategy.md) — X/LinkedIn/Reddit playbooks

### Legal
Privacy, terms, and compliance.

- [Privacy Policy](legal/privacy-policy.md) — Data handling, anonymization, no telemetry
- [Terms of Service](legal/terms-of-service.md) — License grant, liability, acceptable use

### Project Root
- [ROADMAP.md](../ROADMAP.md) — Public roadmap with version milestones
- [CHANGELOG.md](../CHANGELOG.md) — Detailed change history
- [CONTRIBUTING.md](../CONTRIBUTING.md) — How to contribute
- [CLAUDE.md](../CLAUDE.md) — AI-assisted development conventions
- [VERSIONING.md](../VERSIONING.md) — SemVer strategy

---

*Last updated: April 11, 2026 — v0.6.0 Token Hygiene complete*

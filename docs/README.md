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
- [Testing Plan](technical/testing-plan.md) — 455 tests (302 Rust + 141 frontend + 12 E2E), coverage targets, fixtures
- [Error States & Edge Cases](technical/error-states-edge-cases.md) — Every error state and recovery behavior

### Distribution
Release, signing, and distribution.

- [Distribution Guide](distribution.md) — Code signing, notarization, Homebrew tap, release process, auto-updater
- [CLI Companion](cli.md) — Planned CLI commands (scan, score, clean) with output formats
- [Deployment](technical/deployment.md) — CI/CD, build process, platform targets

### Business
Strategy, pricing, market analysis, and growth.

- [Business Model & Pricing](business/business-model.md) — Revenue model, pricing tiers
- [Go-To-Market Strategy](business/go-to-market.md) — Launch plan, channels, growth loops
- [Competitive Analysis](business/competitive-analysis.md) — Landscape, positioning, moat
- [Financial Projections](business/financial-projections.md) — Revenue forecasts, milestones
- [Pro Tier Licensing](business/licensing.md) — License keys, validation, feature gating
- [Landing Page Spec](business/landing-page.md) — naqi.app content and design
- [GitHub Strategy](business/github-strategy.md) — Open-source strategy, community, GitHub as growth channel

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

*Last updated: March 31, 2026 — v0.1.0 feature-complete, preparing public release*

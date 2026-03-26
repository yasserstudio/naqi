# Contributing to Naqi

Thanks for wanting to help make AI workspaces cleaner!

## Quick Start

1. Fork the repo
2. Clone your fork
3. Install prerequisites (see [Development Guide](docs/technical/development-guide.md))
4. Run `pnpm install && pnpm tauri dev`
5. Make your changes
6. Run `pnpm check && cd src-tauri && cargo test`
7. Open a PR

## What to Work On

- Check [good first issues](../../labels/good%20first%20issue) for beginner-friendly tasks
- Check [help wanted](../../labels/help%20wanted) for issues that need contributors
- Check [Discussions > Feature Ideas](../../discussions/categories/feature-ideas) for popular requests

## Pull Request Guidelines

- Keep PRs focused — one feature or fix per PR
- Include tests for new Rust code (especially parsers)
- Update docs if you change behavior
- Screenshots for UI changes
- PR description should explain *what* and *why*

## Adding a New AI Client Parser

This is the most common type of contribution. See the
[Config Formats Reference](docs/technical/config-formats.md) for existing parser patterns.

1. Create `src-tauri/src/scanner/parsers/your_client.rs`
2. Implement the `ConfigParser` trait
3. Add fixture files in `src-tauri/tests/fixtures/`
4. Add tests
5. Register the parser in `scanner/parsers/mod.rs`
6. Update the config formats doc

## Code Style

- Rust: `cargo fmt` + `cargo clippy` with zero warnings
- TypeScript: `pnpm check` passes
- See [Development Guide](docs/technical/development-guide.md) for full conventions

## Questions?

Open a [Discussion](../../discussions) — we're happy to help.

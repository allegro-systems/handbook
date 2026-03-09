# Allegro Handbook

The handbook defines the Allegro ecosystem at three levels:

- doctrine: what the system is trying to preserve
- product requirements: what each product owns
- delivery tracking: what is built today versus still planned

## Documents

- [Design Philosophy](design-philosophy.md) — product standards and style guide (fonts, colors, syntax highlighting).
- [Ecosystem PRD](ecosystem-prd.md) — overarching ecosystem: systems thesis, product boundaries, dependency policy, build progress, Libretto, Allegro Site, glossary, ADRs, and changelog.
- [Score PRD](score-prd.md) — the Swift web framework: authoring model, runtime, three-level signal state model, CLI contract, build artifacts, storage, and full-stack web parity.
- [Stage PRD](stage-prd.md) — the hosting and operations product.
- [Composer PRD](composer-prd.md) — the native macOS and iPad visual editor for Score projects.

## Current Defaults

- Static-first output remains the default model.
- Runtime exists to support capabilities, not as a default mode.
- Score is the active build phase; Stage and Libretto remain downstream work.
- Score is at 85% with all modules implemented and covered (ScoreCore, ScoreHTML, ScoreCSS, ScoreRouter, ScoreRuntime, ScoreStorage, ScoreAuth, ScoreContent, ScoreAssets, ScoreUI, ScoreVendor); CLI tooling and examples are pending.

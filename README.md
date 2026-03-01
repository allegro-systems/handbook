# Allegro Handbook

[Handbook Index](README.md) | [Progress](progress.md) | [Systems Manifesto](manifesto.md) | [Design Philosophy](design-philosophy.md) | [Ecosystem PRD](ecosystem-prd.md) | [Allegro Score PRD](score-prd.md) | [Allegro Stage PRD](stage-prd.md) | [Allegro Libretto PRD](libretto-prd.md) | [Allegro Site PRD](allegro-site-prd.md)

The handbook defines the Allegro ecosystem at three levels:

- doctrine: what the system is trying to preserve
- product requirements: what each product owns
- delivery tracking: what is built today versus still planned

## Documents

- [Progress](progress.md) tracks delivery state across Score, Stage, Libretto, and Allegro Site.
- [Systems Manifesto](manifesto.md) captures the thesis behind the ecosystem.
- [Design Philosophy](design-philosophy.md) defines how Allegro's own products should feel.
- [Ecosystem PRD](ecosystem-prd.md) defines product boundaries across Score, Stage, and Libretto.
- [Allegro Score PRD](score-prd.md) describes Score's current baseline and forward plan.
- [Allegro Stage PRD](stage-prd.md) defines the hosting product boundary.
- [Allegro Libretto PRD](libretto-prd.md) defines the reference application product boundary.
- [Allegro Site PRD](allegro-site-prd.md) defines the public site and documentation boundary.

## Current Defaults

- Static-first output remains the default model.
- Runtime exists to support capabilities, not as a default mode.
- Score is the active build phase; Stage and Libretto remain downstream work.
- The package currently has working foundations in `ScoreCore`, `ScoreHTML`, and `ScoreCSS`, with the rest of the ecosystem at varying earlier stages.

Formerly a single `handbook.html` document, the handbook now lives as linked Markdown files for editor-first reading and maintenance.

# Allegro Site PRD

[Handbook Index](README.md) | [Progress](progress.md) | [Systems Manifesto](manifesto.md) | [Design Philosophy](design-philosophy.md) | [Ecosystem PRD](ecosystem-prd.md) | [Allegro Score PRD](score-prd.md) | [Allegro Stage PRD](stage-prd.md) | [Allegro Libretto PRD](libretto-prd.md) | [Allegro Site PRD](allegro-site-prd.md) | [Allegro Composer PRD](composer-prd.md) | [CLI PRD](cli-prd.md) | [Glossary](glossary.md) | [Dependencies](dependencies.md) | [Decisions](decisions.md) | [Changelog](changelog.md)

_PRD • public site and documentation_

## 1. Overview

Allegro Site is the canonical public site for the ecosystem. It unifies handbook doctrine, product boundaries, and implementation documentation into one coherent surface.

## 2. Product Goals

- provide a clear public entry point for Allegro
- publish handbook doctrine and product requirements as first-class docs
- aggregate documentation from Score libraries and Libretto
- keep navigation coherent across doctrine, PRDs, and implementation docs
- apply Allegro design philosophy to docs UX and presentation

## 3. Content Scope

The site includes:

- handbook content (manifesto, philosophy, PRDs, progress)
- Score documentation (guides, module docs, API references, examples)
- Stage documentation to the level supported by implementation maturity
- Libretto documentation (product story, architecture notes, and learnings)

> [!NOTE]
> The first page of each documentations section should be a marketing page for that product e.g. show features, benefits and quick examples on that page.

## 4. Information Architecture Requirements

- separate current implementation from future direction
- keep hierarchy shallow and legible
- preserve direct links between ecosystem doctrine and implementation docs
- avoid duplicate source-of-truth documents whenever possible

## 5. Documentation Ingestion Model

- handbook markdown remains source-of-truth for doctrine and PRDs
- Score docs are sourced from maintained package documentation
- Libretto docs are sourced from maintained application documentation
- published pages include source attribution and last-updated metadata

## 6. Non-Goals

- creating a parallel doctrine that conflicts with the handbook
- presenting planned capabilities as already shipped
- prioritizing ornamental marketing over documentation clarity

## 7. Delivery Milestones

1. PRD complete.
2. Documentation source map and publishing pipeline defined.
3. Handbook publication integrated.
4. Score and Libretto documentation integrated.
5. First production deploy.
6. `v1.0.0` release.
7. Secure repository controls enabled.

## 8. Success Criteria

- users can move from doctrine to actionable implementation docs without ambiguity
- handbook and implementation docs remain synchronized with source repositories
- Allegro Site becomes the canonical public docs surface for the ecosystem

Previous: [Allegro Libretto PRD](libretto-prd.md)
Next: [Allegro Composer PRD](composer-prd.md)

# Design Philosophy

[Handbook Index](README.md) | [Progress](progress.md) | [Systems Manifesto](manifesto.md) | [Design Philosophy](design-philosophy.md) | [Ecosystem PRD](ecosystem-prd.md) | [Allegro Score PRD](score-prd.md) | [Allegro Stage PRD](stage-prd.md) | [Allegro Libretto PRD](libretto-prd.md) | [Allegro Site PRD](allegro-site-prd.md)

_PHI • principles_

This document describes how Allegro's own products should feel. It is not a promise that Score ships every one of these ideas as a framework primitive.

## Product Standards

- Interfaces should feel immediate. Most interactions should acknowledge input within a fraction of a second.
- Navigation should stay shallow and legible. Users should not need deep hierarchies to understand where they are.
- Copy should be direct, active, and short. Explanatory UI is preferred over tours, overlays, and tutorial chrome.
- Motion should clarify state changes, not decorate them. If an animation can be removed without losing meaning, it probably should be.
- Hit targets must stay comfortable on touch devices. Small targets are a quality failure.
- Empty, loading, and error states should look intentional. They are part of the product, not fallback debris.
- Visual hierarchy should come from spacing, typography, and contrast before ornament.
- Forms should behave conservatively with user data. Loss of work should be rare and clearly signposted.
- Accessibility is a baseline quality requirement, especially for semantics, focus behaviour, and contrast.

## What We Avoid

- Autoplay and attention traps
- First-load modal interruptions
- Excessive tooltip dependency
- Deeply nested navigation for simple products
- Full-page loading spinners as the default answer to latency
- Decorative motion that obscures information or harms readability

## Relationship to Score

Score should make these outcomes easy where that aligns with the framework's core model, but the product standards live here because they are Allegro standards first and framework affordances second.

Previous: [Systems Manifesto](manifesto.md)
Next: [Ecosystem PRD](ecosystem-prd.md)

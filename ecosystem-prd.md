# Allegro Ecosystem PRD

[Handbook Index](README.md) | [Progress](progress.md) | [Systems Manifesto](manifesto.md) | [Design Philosophy](design-philosophy.md) | [Ecosystem PRD](ecosystem-prd.md) | [Allegro Score PRD](score-prd.md) | [Allegro Stage PRD](stage-prd.md) | [Allegro Libretto PRD](libretto-prd.md) | [Allegro Site PRD](allegro-site-prd.md)

_PRD • ecosystem_

## 1. Overview

Allegro is a Swift-first web ecosystem made of three products:

- Score: the framework and rendering/runtime foundation
- Stage: the hosting and operations product
- Libretto: the first-party application used to validate both

Each product has a hard boundary. They should reinforce each other without depending on private implementation details across those boundaries.

## 2. Responsibilities

### Score

Owns application authoring, rendering contracts, runtime capabilities, and first-party package surfaces.

### Stage

Owns deployment, execution, logs, environments, and workload hosting.

### Libretto

Owns dogfooding. It is where Allegro proves that the framework and hosting story hold up under real product pressure.

## 3. Delivery Order

The ecosystem builds in sequence:

1. Score becomes viable enough to build and ship real applications.
2. Stage becomes viable enough to host those applications well.
3. Libretto uses both in production and exposes the rough edges.

This order is not optional. Downstream products should not invent fake progress by speculating past the maturity of upstream ones.

## 4. Website and Docs Strategy

`allegro.tld` is the public face of the ecosystem. It should eventually host:

- marketing pages for the ecosystem
- Score documentation
- Stage documentation and pricing
- authenticated product surfaces where relevant

At the current phase, the most important documentation surface is Score. Stage and Libretto should remain documented at the level their maturity supports, without elaborate launch detail that implies those products already exist.

## 5. Documentation Rules

- Product docs must distinguish clearly between current implementation and future direction.
- Package targets are not equivalent to finished subsystems.
- Package/module names should match the codebase exactly.
- Placeholder modules may be named, but must not be described as complete systems.
- Repeated doctrine should live in the manifesto or owning PRD once, then be referenced elsewhere instead of duplicated.

## 6. Boundary Rules

- Score builds applications. Stage hosts them.
- Stage infrastructure must not depend on Score internals.
- Stage marketing and dashboard surfaces may use Score when useful.
- Libretto should use supported Score and Stage paths rather than custom escape hatches.

## 7. Dependency Policy

Dependencies across the ecosystem are restricted to the approved Swift Package Index collections recorded in the agent notes. Any exception requires explicit approval and written justification.

## 8. Success Criteria

- Score is capable enough to build credible Swift web applications.
- Stage can host Score workloads without becoming Score-coupled infrastructure.
- Libretto validates the real operating shape of the stack.

Previous: [Design Philosophy](design-philosophy.md)
Next: [Allegro Score PRD](score-prd.md)

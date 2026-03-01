# Systems Manifesto

[Handbook Index](README.md) | [Progress](progress.md) | [Systems Manifesto](manifesto.md) | [Design Philosophy](design-philosophy.md) | [Ecosystem PRD](ecosystem-prd.md) | [Allegro Score PRD](score-prd.md) | [Allegro Stage PRD](stage-prd.md) | [Allegro Libretto PRD](libretto-prd.md) | [Allegro Site PRD](allegro-site-prd.md)

_DOC • doctrine_

## Thesis

Most pages should be static. Runtime should appear only when the problem requires it. Performance and durability are product qualities, not optional polish.

## Principles

### Static is the base state

Static output is the simplest correct answer for most web pages. Dynamic behaviour must justify its cost.

### Runtime is a capability

Runtime is introduced to support side effects, persistence, authentication, or other server-backed behaviour. It is not a mode switch developers should have to micromanage.

### Progressive enhancement is required

HTML carries structure and baseline usability. CSS improves presentation. JavaScript and server runtime add capability on top of that baseline instead of replacing it.

### Performance is correctness

Slow output, excessive work, and unnecessary runtime are architecture failures, not merely optimisation opportunities.

### Durability matters when state matters

If a feature crosses a network or writes durable state, retries, timeouts, idempotency, and bounded failure behaviour need to be part of the system design.

### Boundaries stay hard

Score builds applications. Stage hosts workloads. Libretto proves the stack in production. These concerns reinforce each other, but they do not collapse into one product.

### Dependencies are architecture

Every dependency changes the shape of the system. Curated dependencies are part of the product discipline, not a procurement detail.

### Narrow focus is intentional

Allegro does not need to serve every web team. It needs to provide one coherent path for teams who value typed contracts, static-first delivery, and restrained system design.

## Consequence

The goal is not novelty. The goal is a web stack where the obvious path stays simple, the runtime boundary stays clear, and operational complexity appears only when it is truly needed.

Previous: [Progress](progress.md)
Next: [Design Philosophy](design-philosophy.md)

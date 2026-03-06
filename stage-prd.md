# Allegro Stage PRD

[Handbook Index](README.md) | [Progress](progress.md) | [Systems Manifesto](manifesto.md) | [Design Philosophy](design-philosophy.md) | [Ecosystem PRD](ecosystem-prd.md) | [Allegro Score PRD](score-prd.md) | [Allegro Stage PRD](stage-prd.md) | [Allegro Libretto PRD](libretto-prd.md) | [Allegro Site PRD](allegro-site-prd.md) | [Allegro Composer PRD](composer-prd.md) | [CLI PRD](cli-prd.md) | [Glossary](glossary.md) | [Dependencies](dependencies.md) | [Decisions](decisions.md) | [Changelog](changelog.md)

_PRD • hosting_

## 1. Overview

Stage is the hosting and operations product in the Allegro ecosystem. It exists to run workloads produced by Score and other Swift executables without coupling the infrastructure layer to Score internals.

## 2. Product Goals

- host static output cleanly
- host runtime binaries cleanly
- treat Swift workloads as a first-class target
- remain usable without requiring Score

## 3. Boundary with Score

Score builds applications. Stage hosts them.

- Stage infrastructure must remain Score-independent.
- Stage may use Score for its own web surfaces, such as docs or dashboards.
- Stage should not require privileged knowledge of Score internals to do its job.

## 4. Remote Build Compatibility

Stage is the remote build executor for production deploys. It must execute canonical Score builds without introducing a Stage-specific runtime.

Requirements:

- run remote builds using canonical `score build` semantics
- pin Score and Swift toolchain versions per build
- use reproducible build environments
- return deployment IDs and service URLs after successful activation
- keep artifacts portable so projects remain deployable outside Stage

## 5. Expected Capabilities

When Stage work begins in earnest, it should cover:

- local development and preview workflows
- deployment and hosting flows
- logs and environment management
- support for both static and runtime workloads

The exact CLI and dashboard surface should be documented once implementation begins, not before.

## 6. Non-Goals

- becoming a general JavaScript hosting platform
- forcing users into Score to use Stage
- recreating mature infrastructure primitives without a clear benefit

Previous: [Allegro Score PRD](score-prd.md)
Next: [Allegro Libretto PRD](libretto-prd.md)

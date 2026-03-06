# Allegro Composer PRD

[Handbook Index](README.md) | [Progress](progress.md) | [Systems Manifesto](manifesto.md) | [Design Philosophy](design-philosophy.md) | [Ecosystem PRD](ecosystem-prd.md) | [Allegro Score PRD](score-prd.md) | [Allegro Stage PRD](stage-prd.md) | [Allegro Libretto PRD](libretto-prd.md) | [Allegro Site PRD](allegro-site-prd.md) | [Allegro Composer PRD](composer-prd.md) | [CLI PRD](cli-prd.md) | [Glossary](glossary.md) | [Dependencies](dependencies.md) | [Decisions](decisions.md) | [Changelog](changelog.md)

_PRD • visual editor_

## 1. Overview

Composer is a native macOS and iPad application for editing canonical Score Swift projects with local preview. Production builds run remotely on Stage so deploy output is deterministic and reproducible.

Composer is intentionally thin:

- it edits canonical Score projects
- it orchestrates `score dev`, `score build`, and `score deploy` workflows
- it does not introduce a proprietary runtime or project format

## 2. Product Goals

### Primary goals

- visual-first onboarding to Score
- static-first builds by default
- deterministic, reproducible production builds
- atomic zero-downtime deploys through Stage
- portable build artifacts deployable beyond Stage

### Secondary goals

- GitHub optional, not required
- native support for macOS and iPad
- developers can drop to CLI at any time
- AI-assisted generation outputs canonical Score AST/project structure

## 3. System Architecture

### 3.1 Single-worker model

Composer runs exactly one sandboxed worker at a time:

- one hydrated project at a time
- inactive projects are compressed and evicted
- no concurrent build containers inside Composer

### 3.2 Build modes

| Mode | Trigger | Where | Purpose |
| --- | --- | --- | --- |
| Dev (Code) | Swift file save | Local | compile + preview |
| Dev (Composer) | Structural block edit | Local | compile + preview |
| Build (Local) | `score build` | Local (macOS/Linux) | production parity |
| Build (Deploy) | Publish | Stage | canonical production build |

## 4. CLI Integration Contract

Composer depends on Score CLI as its execution backbone.

### 4.1 `score dev`

Composer expects `score dev` to:

- watch project files
- recompile on file save and structural edits
- serve preview output with hot reload
- expose structured diagnostics for editor display

When no local Swift toolchain exists, Composer may operate in structural preview mode with reduced compile guarantees.

### 4.2 `score build`

Composer expects `score build` to:

- perform full project compilation
- produce deterministic output in `.build/score/`
- emit `manifest.json` with capability data
- emit machine-readable error payloads for editor integration

### 4.3 `score deploy`

Composer publish flow may call `score deploy`, or invoke equivalent Stage deployment API semantics:

- send canonical project snapshot
- trigger remote build on Stage
- return deployment ID and URLs

## 5. Stage Deploy Contract

For publish operations, Composer sends a canonical snapshot to Stage. Stage then:

- installs pinned Score version
- uses pinned Swift toolchain version
- executes canonical `score build`
- validates capability manifest
- packages runtime only when required
- atomically swaps deployment
- returns deployment ID plus resolved URLs

Composer must surface deployment status and errors without inventing alternate build logic.

## 6. Capability and UX Responsibilities

Composer must:

- clearly surface activated capabilities from build output
- highlight when runtime or persistence is activated
- keep static-first behavior as the default user path

Composer must not:

- interpret project semantics via an alternate runtime
- execute arbitrary controller graphs internally
- maintain a second configuration format

## 7. Developer Workflow

### 7.1 Local development

- run `score dev`
- edit in code or Composer visual surfaces
- preview updates on save/edit

### 7.2 Production build

- run `score build`
- produce portable `.build/score/` artifact

### 7.3 Stage deployment

- run `score deploy` or publish from Composer
- receive deployment ID and URLs

## 8. Non-Goals

- Composer-specific runtime
- visual-only logic engine
- runtime graph interpreter inside the editor
- platform-locked project format
- hidden infrastructure coupling

## 9. Risks and Mitigations

| Risk | Mitigation |
| --- | --- |
| Toolchain drift | pin Score + Swift versions in manifest/build environment |
| iPad toolchain limits | remote production build on Stage |
| compile latency | incremental build behavior in `score dev` |
| large project size | compression + eviction policy for inactive projects |
| debug complexity | structured logs/diagnostics from CLI commands |

## 10. Success Criteria

- Composer remains a thin orchestration layer over canonical Score projects.
- Local preview is fast and reliable for both code and visual edits.
- Deploy builds are reproducible and deterministic on Stage.
- Produced artifacts remain portable beyond Stage hosting.

Previous: [Allegro Site PRD](allegro-site-prd.md)
Next: [Progress](progress.md)

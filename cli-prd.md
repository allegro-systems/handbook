# Allegro Score CLI PRD

[Handbook Index](README.md) | [Progress](progress.md) | [Systems Manifesto](manifesto.md) | [Design Philosophy](design-philosophy.md) | [Ecosystem PRD](ecosystem-prd.md) | [Allegro Score PRD](score-prd.md) | [Allegro Stage PRD](stage-prd.md) | [Allegro Libretto PRD](libretto-prd.md) | [Allegro Site PRD](allegro-site-prd.md) | [Allegro Composer PRD](composer-prd.md) | [CLI PRD](cli-prd.md) | [Glossary](glossary.md) | [Dependencies](dependencies.md) | [Decisions](decisions.md) | [Changelog](changelog.md)

_PRD • CLI tooling_

## 1. Overview

Score CLI is the canonical execution interface for local development, local production builds, and remote deploy orchestration. This document expands on the CLI contract originally defined in [Score PRD Section 6](score-prd.md) to provide a standalone reference for CLI behavior, commands, exit codes, and structured error output.

Composer depends on Score CLI as its execution backbone. All CLI commands must remain compatible with both interactive terminal usage and programmatic invocation by Composer and other tooling.

## 2. `score init`

`score init` generates a canonical Score project from a built-in template.

Requirements:

- generate the canonical project structure (see [Score PRD Section 2](score-prd.md) for the directory layout)
- support selecting a built-in template interactively or by flag
- produce a runnable project rather than an empty placeholder
- keep template structure aligned with current documented Score conventions

### 2.1 Built-In Templates

`score init` must ship first-party templates for at least:

- `blog`
- `marketing`
- `docs`
- `saas`
- `social`
- `static`
- `calculator`
- `minimal`
- `localised`

Those templates are product examples, not empty stubs, and must reflect the canonical Score patterns for each use case.

### 2.2 Generated Structure

Every template must produce the canonical directory layout:

```text
Sources/
  App.swift
  Controllers/
  Pages/
  Middleware/
  Components/
  Models/
  Utilities/
Package.swift
Resources/
  Localizable.xcstrings
  Assets/
```

## 3. `score dev`

`score dev` provides the local development workflow with file watching, recompilation, and preview.

Requirements:

- watch project files
- recompile on Swift file save and Composer structural edits
- run a local preview server
- support hot reload
- run without requiring a fully containerized local toolchain

### 3.1 Toolchain Behavior

- when a local Swift toolchain is available, `score dev` performs normal compile validation
- when unavailable, `score dev` may run in structural preview mode with reduced compile validation and explicit warnings

### 3.2 Composer Integration

Composer expects `score dev` to:

- serve preview output with hot reload
- expose structured diagnostics for editor display
- support both code file saves and Composer structural block edits as recompilation triggers

## 4. `score build`

`score build` performs full project compilation and produces deterministic build output.

Requirements:

- fully compile the Swift project
- produce deterministic output in `.build/score/`
- emit `manifest.json` including capability data
- return structured JSON error output when requested by tooling integrations such as Composer
- emit machine-readable error payloads for editor integration

### 4.1 Build Output

The canonical build root is `.build/score/`. See [Score PRD Section 5](score-prd.md) for the full artifact structure and manifest requirements.

### 4.2 Deterministic Build Invariant

Given identical project input, Score version, Swift toolchain version, and build settings, `score build` must produce byte-equivalent outputs across compliant environments. See [Score PRD Section 7](score-prd.md) for the full invariant.

## 5. `score deploy`

`score deploy` packages and submits the canonical project snapshot to the selected deploy target.

Requirements:

- package and submit the canonical project snapshot to the selected deploy target (Stage by default)
- invoke a remote `score build` on Stage using pinned toolchain and Score versions
- return deployment identity and resolved service URLs

### 5.1 Remote Build Contract

Stage executes the canonical `score build` remotely:

- installs pinned Score version
- uses pinned Swift toolchain version
- executes canonical `score build`
- validates capability manifest
- packages runtime only when required
- atomically swaps deployment
- returns deployment ID plus resolved URLs

## 6. Exit Codes

CLI commands must use stable exit codes:

| Code | Meaning |
| --- | --- |
| `0` | Success |
| `1` | Compile, build, or deploy failure |
| `2` | Usage or argument error |
| `3` | Environment or toolchain prerequisite failure |

Exit codes are part of the public contract and must remain backward-compatible.

## 7. Structured Error Output

When JSON mode is enabled (for programmatic consumers such as Composer), command failures must emit machine-readable errors with this minimum schema:

```json
{
  "code": "compile_error",
  "message": "Human readable summary",
  "stage": "build",
  "file": "Sources/App/HomePage.swift",
  "line": 42,
  "column": 13,
  "details": []
}
```

### 7.1 Error Code Values

| Code | Stage | Description |
| --- | --- | --- |
| `compile_error` | `build` | Swift compilation failure |
| `link_error` | `build` | Linker failure |
| `manifest_error` | `build` | Invalid or inconsistent manifest |
| `deploy_error` | `deploy` | Remote deployment failure |
| `toolchain_error` | `init`, `dev`, `build` | Missing or incompatible Swift toolchain |
| `argument_error` | any | Invalid CLI arguments or flags |

### 7.2 Backward Compatibility

`score dev`, `score build`, and `score deploy` must keep this schema backward-compatible for Composer integrations. New fields may be added, but existing fields must not be removed or have their semantics changed.

## 8. CLI Interface

Score CLI tools are built with [Noora](https://noora.tuist.dev) for their terminal interfaces. This provides consistent rendering for prompts, steps, progress indicators, alerts, and tables across all CLI commands.

## 9. Non-Goals

- replacing the Swift Package Manager for dependency resolution
- providing a general-purpose Swift build system
- implementing a REPL or interactive Swift evaluation environment
- supporting non-Score Swift project types

## 10. Success Criteria

- `score init` produces a runnable project for every listed template.
- `score dev` provides fast incremental recompilation with preview.
- `score build` produces deterministic artifacts that pass capability validation.
- `score deploy` delivers atomic zero-downtime deploys through Stage.
- All commands produce structured error output compatible with Composer.
- Exit codes are stable and machine-parseable.

Previous: [Allegro Composer PRD](composer-prd.md)
Next: [Glossary](glossary.md)

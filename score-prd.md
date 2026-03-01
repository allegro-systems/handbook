[Handbook Index](README.md) | [Progress](progress.md) | [Systems Manifesto](manifesto.md) | [Design Philosophy](design-philosophy.md) | [Ecosystem PRD](ecosystem-prd.md) | [Allegro Score PRD](score-prd.md) | [Allegro Stage PRD](stage-prd.md) | [Allegro Libretto PRD](libretto-prd.md) | [Allegro Site PRD](allegro-site-prd.md)

_PRD • framework_

## 1. Overview

Score is a Swift web framework package. Its current implementation is strongest at the core authoring model and the HTML/CSS rendering layers. The broader ecosystem shape is visible in the package targets, but several of those targets are still placeholders rather than finished subsystems.

This document separates the current baseline from the planned surface so the handbook stays aligned with `Score/`.

## 2. Current Baseline

### Package shape

The package currently exposes these library products:

- `Score`
- `ScoreCore`
- `ScoreHTML`
- `ScoreCSS`
- `ScoreRouter`
- `ScoreRuntime`
- `ScoreDB`
- `ScoreKV`
- `ScoreAuth`
- `ScoreContent`
- `ScoreAssets`
- `ScoreUI`
- `ScoreVendor`

Today, the substantive implementation is concentrated in:

- `ScoreCore`
- `ScoreHTML`
- `ScoreCSS`
- parts of the routing surface in `ScoreRouter`

The remaining targets establish package boundaries and future expansion points, but most are not yet implemented beyond module placeholders.

### Application model

The current application contract is protocol-based and intentionally small:

- `Application` registers `pages`, `controllers`, `theme`, and `metadata`
- `Page` declares a static `path`, optional metadata patch, and a `Node` body
- `Controller` groups `Route` values under a `base` path
- `Theme`, `ThemePatch`, `Metadata`, and `MetadataPatch` define baseline rendering inputs

Representative shape:

```swift
@main
struct App: Application {
    var pages: [any Page] { [HomePage()] }
    var controllers: [any Controller] { [] }
    var theme: (any Theme)? { nil }
    var metadata: (any Metadata)? { nil }
}
```

### UI tree model

`ScoreCore` currently provides:

- the `Node` protocol
- `Never`-backed leaf nodes
- `NodeBuilder`
- content, layout, media, list, table, control, and interactive node types
- modifier values covering layout, typography, spacing, borders, visual styling, accessibility, and motion

The package is using a typed tree and renderer model today, not a macro-driven authoring model.

### Rendering

Current renderer support is focused on:

- `ScoreHTML` for HTML emission from node trees
- `ScoreCSS` for turning modifier values into CSS declarations and deduplicated rule sets

Tests currently exercise these areas heavily, which makes them the most trustworthy implemented surface in the package.

## 3. Current Gaps

These package targets exist but should still be treated as planned work rather than delivered product surfaces:

- `ScoreRuntime`
- `ScoreDB`
- `ScoreKV`
- `ScoreAuth`
- `ScoreContent`
- `ScoreAssets`
- `ScoreUI`
- `ScoreVendor`

The handbook must not describe these as complete systems until their APIs and behaviour exist in code.

## 4. Near-Term Product Direction

The next meaningful Score milestones are:

- finish the router/runtime integration around the existing `Application`, `Page`, and `Controller` contracts
- turn placeholder modules into real package surfaces one subsystem at a time
- keep documentation tied to checked-in APIs instead of speculative syntax
- expand test coverage alongside each newly-real module

## 5. Runtime Reactivity Model

> Compile-time simplicity. Runtime completeness.

Score’s reactive behaviour must provide fine-grained, deterministic, glitch-free updates comparable to modern signal systems, while preserving:

- inspectable DOM via `data-as-*`
- progressive enhancement
- deterministic compile output
- no userland JavaScript contract

The reactive engine is an internal subsystem of `ScoreRuntime`. It is not exposed as a public API surface.

### 5.1 Reactive Store Model

Each interactive `Page` produces a per-scope reactive store at runtime.

The store:

- Is object-shaped.
- Has a compiler-emitted schema.
- Contains top-level fields only.
- Is not dynamically extensible.
- Is not string-indexed by user code.

Conceptual generated shape:

```js
store = {
  counter: Cell<number>,
  name: Cell<string>,
  items: Cell<Array<Item>>,
}
```

Only top-level fields are writable.

Nested mutation must occur through compiler-emitted actions that replace the parent cell value.

This prevents stringly-typed mutation and uncontrolled dependency graphs.

### 5.2 Reactive Invariants

The runtime must guarantee the following invariants.

#### 1. Fine-Grained Dependency Tracking

Derived values and DOM bindings track dependencies based on actual reads during execution.

If a computed value conditionally reads different cells, the dependency graph must update dynamically.

#### 2. Batched Updates

Multiple cell writes within the same microtask must:

- Coalesce into a single dependency propagation pass.
- Trigger DOM updates only after state stabilises.

There must be no cascading synchronous DOM mutation per `.set()` call.

#### 3. Deterministic Effect Ordering

Reactive effects must execute in a stable and deterministic order:

- Parent scopes before children.
- Earlier bindings before later bindings within a scope.

The runtime must avoid nondeterministic ordering caused by subscription timing.

#### 4. Glitch-Free Derived State

Computed values must never observe partially-updated state.

Derived computations must observe either:

- previous stable graph
- or next stable graph

Never an intermediate inconsistent mix.

#### 5. Disposal Semantics

When DOM nodes are removed due to:

- conditional rendering
- list diffing
- scope unmounting

All reactive bindings associated with that subtree must be disposed.

The runtime must not leak subscriptions.

#### 6. Scoped Isolation

Reactive graphs are isolated per `data-as-scope`.

No reactive dependency may cross scope boundaries unless explicitly emitted by the compiler.

This prevents accidental global reactive graphs.

### 5.3 DOM Contract vs Reactive Engine

Score maintains a strict separation:

| Concern | Mechanism |
|----------|------------|
| DOM targeting | `data-as-*` attributes |
| State propagation | Reactive engine (Cells / Computed / Effects) |
| Styling | CSS + attribute selectors |
| Event wiring | Compiler-emitted action bindings |

The `data-as-*` contract remains stable and inspectable.

The reactive engine may internally use:

- A custom minimal cell implementation
- A TC39 Signals polyfill
- Native Signals (when standardised)

This choice must not affect DOM semantics.

### 5.4 Non-Goals of the Reactive Model

- No user-exposed `Signal` type in Swift.
- No arbitrary runtime mutation of store structure.
- No implicit global store.
- No runtime template parsing.

Reactivity is strictly a runtime execution detail behind a compile-time deterministic DOM protocol.

### 5.5 Success Criteria for Reactivity

Score’s reactive model is considered successful when:

- Complex UI state graphs feel mechanically boring to implement.
- Developers never think about subscription management.
- Removing a subtree never leaks.
- Multiple state writes never cause flicker.
- The DOM remains readable and stable.

---

## 6. Planned Subsystems

These remain valid parts of the product direction, but their exact APIs are intentionally not frozen here yet:

- runtime execution and request handling
- authentication primitives
- content ingestion and authoring helpers
- asset pipeline support
- first-party UI components
- vendor integrations

They should be added to the handbook in detail only when the codebase has a concrete implementation worth documenting.

### 6.1 FoundationDB-Backed Transactional KV Store

Score uses a single transactional key-value abstraction for persistent state.

The locked core API is intentionally small:

- `get(key)`
- `set(key, value)`
- `delete(key)`
- `scan(range | prefix)`
- transactional atomic operations

Production backing store is FoundationDB for strict serializability, ACID transactions, replication, and horizontal scale.

Local development uses a lightweight API-compatible backend (in-memory or disk-based) that preserves transactional semantics.

This storage contract is explicitly non-relational:

- no SQL or ad-hoc query language
- no implicit joins
- no automatic query planner
- no relational schema migration model in the storage core

Secondary indexes are explicit layers maintained by application writes (for example `index_by_email / email / userId -> ""`) and resolved by index scan then primary-key fetch.

### 6.2 Implementation Guardrails (Storage)

To keep behaviour predictable across local and production backends, Score storage implementations must follow these guardrails:

- Deterministic key encoding: keys are ordered tuples with stable binary encoding and clear namespace boundaries (`tenant / collection / recordId`).
- Transaction budget: keep transactions short and bounded; avoid long-running business logic inside transaction scope.
- No blocking I/O in transactions: network calls, file reads, and external API work must happen outside transaction closures.
- Explicit retry policy: transient conflicts must retry with bounded exponential backoff and jitter.
- Idempotent writes for retries: mutation paths that may replay must include idempotency safeguards.
- Atomic mutation scope: increments and conditional writes execute only within transactions.
- Index write invariants: primary record writes and related secondary index writes commit in the same transaction.
- Scan discipline: scans are prefix/range based; arbitrary filtering belongs in application logic after bounded key retrieval.
- Conflict minimisation: prefer narrow key ranges and avoid hot shared keys where possible.
- Local-prod semantic parity: local backend must preserve transaction atomicity, isolation guarantees, and key ordering behaviour.

## 7. Non-Goals for This Phase

- locking in macro syntax before the underlying runtime exists
- documenting CLIs or generated project layouts that are not implemented
- specifying large component catalogs before `ScoreUI` exists
- writing end-state architecture as if every package target were already operational

## 8. Success Criteria

- the current baseline APIs stay small, coherent, and well documented
- handbook claims remain honest relative to the package contents
- new modules graduate from placeholder to documented subsystem only when code supports it

Previous: [Ecosystem PRD](ecosystem-prd.md)
Next: [Allegro Stage PRD](stage-prd.md)


## 9. Post-v1 Repository Policy

At the moment `v1.0.0` is released, the Score repository must move to maintenance controls:

1. No direct pushes to `main`.
2. Pull requests are the only path to merge into `main`.
3. All PRs targeting `main` must pass `.github/workflows/ci.yml`.
4. Force pushes are disabled on `main`.
5. Local `--no-verify` bypass is not accepted as a merge path because server-side required checks remain mandatory.

Implementation in GitHub branch protection/rulesets:

- Require pull request before merging.
- Require status checks to pass from `ci.yml`.
- Restrict who can push to `main` (or block pushes entirely except merge queue/bot if used).
- Disallow force pushes.

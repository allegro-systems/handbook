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
- `ScoreStorage`
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

Score persistence direction is now locked: Score uses one transactional KV storage methodology. Any legacy storage module boundaries in the package layout are migration artifacts and not separate long-term product methodologies.

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
- unified transactional storage subsystem (`ScoreStorage`)
- `ScoreAuth`
- `ScoreContent`
- `ScoreAssets`
- `ScoreUI`
- `ScoreVendor`

The handbook must not describe these as complete systems until their APIs and behaviour exist in code.

## 4. Near-Term Product Direction

The next meaningful Score milestones are:

- ship `ScoreRuntime` in three concrete steps: server runtime + document assembly, browser runtime + reactive bindings, then development diagnostics
- keep the browser-side runtime aligned with platform primitives instead of inventing a custom client state system
- make CSS class injection, document assembly, and route handling real checked-in runtime behaviour before expanding adjacent modules
- defer higher-level interop such as WASM until the server/browser runtime contract is stable
- turn placeholder modules into real package surfaces one subsystem at a time
- keep documentation tied to checked-in APIs instead of speculative syntax
- expand test coverage alongside each newly-real module

## 5. Build Artifact Contract

All production-oriented Score builds must emit a portable, environment-agnostic artifact directory with this canonical structure:

```text
/dist
  /static
  /runtime (optional)
  manifest.json
```

Required constraints:

- `static` contains the static-first output and must always be present.
- `runtime` is optional and only emitted when runtime capabilities are required by the project.
- `manifest.json` is required and must describe build identity, toolchain versions, and activated capabilities.
- Output must remain deployable outside Stage with no Stage-specific runtime coupling.
- Runtime artifacts must not embed machine-specific absolute paths.

### 5.1 Manifest Requirements

`manifest.json` must include, at minimum:

- Score version
- Swift toolchain version
- build timestamp (UTC)
- deterministic build fingerprint
- capability manifest

### 5.2 Capability Manifest Schema

Every build must emit a capability manifest in `manifest.json` with the shape:

```json
{
  "static": true,
  "js_runtime": false,
  "server_runtime": true,
  "persistence": true,
  "auth": false
}
```

Semantics:

- `static` defaults to `true` and defines the static-first baseline.
- `js_runtime`, `server_runtime`, `persistence`, and `auth` are opt-in and must only activate when required by used framework features.

## 6. CLI Contract

Score CLI is the canonical execution interface for local development, local production builds, and remote deploy orchestration.

### 6.1 `score dev`

`score dev` must:

- watch project files
- recompile on Swift file save and Composer structural edits
- run a local preview server
- support hot reload
- run without requiring a fully containerized local toolchain

Toolchain behavior:

- when a local Swift toolchain is available, `score dev` performs normal compile validation
- when unavailable, `score dev` may run in structural preview mode with reduced compile validation and explicit warnings

### 6.2 `score build`

`score build` must:

- fully compile the Swift project
- produce deterministic output in `/dist`
- emit `manifest.json` including capability data
- return structured JSON error output when requested by tooling integrations such as Composer

### 6.3 `score deploy`

`score deploy` must:

- package and submit the canonical project snapshot to the selected deploy target (Stage by default)
- invoke a remote `score build` on Stage using pinned toolchain and Score versions
- return deployment identity and resolved service URLs

### 6.4 Exit Codes

CLI commands must use stable exit codes:

- `0`: success
- `1`: compile/build/deploy failure
- `2`: usage or argument error
- `3`: environment/toolchain prerequisite failure

### 6.5 Structured Error Output

When JSON mode is enabled, command failures must emit machine-readable errors with this minimum schema:

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

`score dev`, `score build`, and `score deploy` must keep this schema backward-compatible for Composer integrations.

## 7. Deterministic Build Invariant

Given:

- identical project input
- identical Score version
- identical Swift toolchain version
- identical build settings

Score must produce byte-equivalent build outputs across compliant environments.

Build systems must reject nondeterministic inputs and outputs that violate this invariant.

## 8. Capability Enforcement

Capability enforcement is mandatory across local and remote builds.

Rules:

- static-first behavior is the default for all projects
- runtime capabilities may only be enabled when required by project features
- persistence capability requires an explicit durability contract
- deployment targets must validate the capability manifest before activation

Stage and other deploy targets may use capabilities for runtime allocation, policy checks, and pricing tiers, but capability semantics are defined by Score, not by any host.


## 9. Runtime Model

> Compile-time simplicity. Runtime completeness.

Score runtime has two responsibilities:

- server-side route resolution and complete HTML document assembly
- browser-side enhancement for pages that require live behaviour

The browser path must stay platform-first. Score should compose with the browser’s DOM, CSS, events, navigation, and storage primitives instead of replacing them with a second application runtime.

Score’s reactive behaviour must provide fine-grained, deterministic, glitch-free updates comparable to modern signal systems, while preserving:

- progressive enhancement
- deterministic compile output
- no userland JavaScript contract
- an inspectable development experience without making debug attributes part of the production contract

The reactive engine is an internal subsystem of `ScoreRuntime`. It is not exposed as a public API surface.

### 9.1 Server Runtime Baseline

The first runtime milestone is not reactivity. It is correct server execution:

- route incoming requests through the existing `Application`, `Page`, and `Controller` contracts
- render a page body through `ScoreHTML`
- collect scoped CSS through `ScoreCSS`
- assemble a complete HTML document with metadata, theme variables, and runtime assets when needed
- return standards-compliant HTTP responses for page and controller routes

This phase establishes the canonical browser document that later enhancement layers attach to.

### 9.2 Browser Runtime Contract

For interactive pages, Score’s browser runtime is compiler-driven and signal-backed.

Current direction:

- `@State` lowers to `Signal.State`
- `@Computed` lowers to `Signal.Computed`
- `@Action` lowers to compiler-emitted JavaScript functions that perform state writes
- `.on` lowers to DOM event listeners bound to those emitted functions
- developers do not write effects directly; the compiler emits them for DOM bindings

```js
const count = new Signal.State(0)
const doubled = new Signal.Computed(() => count.get() * 2)
```

The initial implementation target is the TC39 Signals polyfill. When native browser signals become viable, Score may swap its internal engine without changing the public Swift authoring model.

### 9.3 DOM and Inspection Contract

Score no longer treats compiler-owned debug attributes as the primary production runtime contract.

Instead:

| Concern | Production mechanism | Development mechanism |
|----------|------------|------------|
| DOM targeting for reactive updates | compiler-emitted direct DOM references and traversal paths | `data-s` attributes may be emitted for inspectability |
| Styling | scoped class names emitted from modifier sets | same as production |
| Event wiring | compiler-emitted listener attachment | same as production |
| Component/source inspection | none required for correctness | debug annotations may be emitted for devtools and source navigation |

`data-as-*` is not a required production contract for the new runtime plan. Debug-oriented attributes may exist in development, but production correctness must not depend on them.

### 9.4 Reactive Invariants

The runtime must guarantee the following invariants.

#### 1. Fine-Grained Dependency Tracking

Derived values and DOM bindings must track dependencies based on actual signal reads during execution.

If a computed value conditionally reads different signals, the dependency graph must update dynamically.

#### 2. Batched Updates

Multiple writes within the same microtask must:

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

#### 6. Page-Local Isolation

Reactive graphs are isolated to the page runtime instance produced by the compiler.

No implicit global graph may emerge from ad-hoc runtime registration.

Shared state across tabs, workers, or documents must be an explicit higher-level capability built on browser standards rather than an accidental property of the reactive engine.

### 9.5 Browser-First Principles

Score should prefer existing browser behaviour before inventing framework abstractions:

- HTML forms should stay real forms
- links and navigation should stay real links unless runtime interception is explicitly enabled
- browser-managed UI such as dialog, disclosure, popover, focus, history, and scroll restoration should be used where it fits
- persistence and multi-context coordination should use browser storage and messaging standards where possible before adding Score-owned protocols
- source maps, module loading, and event dispatch should follow existing browser conventions

### 9.6 Non-Goals of the Runtime Model

- No user-exposed `Signal` type in Swift.
- No custom object-shaped client store as the primary runtime abstraction.
- No implicit global store.
- No stable production dependency on debug-only DOM attributes.
- No runtime template parsing.
- No second client runtime model separate from the browser platform.

Reactivity remains a runtime execution detail behind compile-time generated DOM bindings.

### 9.7 Success Criteria for Runtime Behaviour

Score’s runtime model is considered successful when:

- Complex UI state graphs feel mechanically boring to implement.
- Developers never think about subscription management.
- Removing a subtree never leaks.
- Multiple state writes never cause flicker.
- The browser remains the source of truth for platform behaviour.
- Development inspection is strong without imposing production runtime overhead.

---

## 10. Planned Subsystems

These remain valid parts of the product direction, but their exact APIs are intentionally not frozen here yet:

- runtime execution and request handling
- authentication primitives
- content ingestion and authoring helpers
- asset pipeline support
- first-party UI components
- vendor integrations

They should be added to the handbook in detail only when the codebase has a concrete implementation worth documenting.

### 10.1 ScoreRuntime Delivery Order

The checked-in runtime plan is phased:

1. Server runtime, CSS class injection, and full document assembly.
2. Browser runtime bundle, reactive lowering for `@State` / `@Computed` / `@Action`, and event binding.
3. Development diagnostics such as source maps, devtools, and error overlays.

This order is intentional:

- it fixes the current HTML/CSS/runtime integration gap first
- it keeps the browser execution model tied to standards-based DOM and JavaScript behaviour
- it delays speculative interop surfaces until the core runtime contract is proven

### 10.2 Runtime WASM Interop

After the core server/browser runtime contract is stable, Score runtime should provide WebAssembly interoperability as a capability of `ScoreRuntime`.

Requirements:

- easy module loading at `Application` scope for shared modules
- easy module loading at `Page` scope for page-local modules
- deterministic module initialization and lifecycle within runtime scopes
- ergonomic invocation from `.on` modifiers without manual bridge plumbing

Conceptual usage shape (illustrative, not syntax-locked):

```swift
@main
struct App: Application {
    var wasmModules: [WASMModule] {
        [
            .named("image", path: "./wasm/image.wasm")
        ]
    }
}

struct ProfilePage: Page {
    var body: some Node {
        button("Optimize")
            .on(.click, .wasm("image.optimize", args: [.string("avatar")]))
    }
}
```

Interop contract:

- invocation from `.on` must be one-line and type-safe at the callsite
- argument and return value marshalling must use explicit codecs
- runtime failures must surface as structured diagnostics compatible with Score tooling
- module access must remain sandboxed and capability-gated

Non-goals for this phase:

- introducing a second UI/runtime model for WASM
- bypassing reactive/event semantics through ad-hoc host hooks
- exposing unstable low-level memory APIs as the primary developer surface

### 10.3 FoundationDB-Backed Transactional KV Store

Score uses a single transactional key-value database abstraction for all persistent state.

This is the definitive persistence methodology for Score.

- Production backing store: FoundationDB.
- Local backend: lightweight API-compatible engine preserving transactional semantics.
- legacy storage module boundaries are transitional only and must converge into this single storage contract.

The model is intentionally aligned with the Deno KV pattern: a simple KV API locally and FoundationDB-backed semantics in production.

### 10.4 Locked Storage API Surface

The storage contract is intentionally minimal and stable:

- `get(key)`
- `set(key, value)`
- `delete(key)`
- `scan(range | prefix)`
- transactional atomic operations
- strong serializability guarantees for committed transaction results

Illustrative shape:

```swift
let user = try await store.get(Key("users", userId))
try await store.set(Key("users", userId), value: user)

for try await item in store.scan(prefix: Key("users")) {
    // consume item
}

try await store.transaction { tx in
    tx.set(...)
    tx.delete(...)
}
```

Atomic mutations such as increments and conditional checks must execute only within a transaction scope.

### 10.5 Engine Layers

Production (`FoundationDB`):

- strict serializability and ACID transactions
- replication and automatic sharding
- horizontal scale with consistent transaction semantics

Local development backend:

- API-compatible local engine (for example in-memory or SQLite-backed)
- preserves transaction atomicity and key ordering semantics at dev scale
- optimized for local iteration, not distributed throughput

### 10.6 Data Modeling Constraints

Score storage is ordered-key based. Canonical key strategy uses structured tuples such as:

- `tenant / collection / recordId`

This enables deterministic range scans, tenant isolation, and conflict minimization.

Implementers must not rely on arbitrary filtering or query planners inside the storage core.

### 10.7 Secondary Index Methodology

Secondary indexes are explicit, declarative layers on top of the KV API.

Example:

- `index_by_email / email / userId -> ""`

Lookup flow:

1. Scan index prefix.
2. Resolve primary keys.
3. Fetch primary records.

Primary record writes and index writes must commit in the same transaction.

### 10.8 Transaction and Conflict Discipline

To keep behavior predictable in distributed deployments, storage implementations must enforce:

- short bounded transactions
- no blocking I/O inside transaction closures
- explicit retry policy for transient conflicts with bounded exponential backoff and jitter
- idempotent mutation paths for retry-safe behavior
- narrow key ranges to avoid hot-key contention

### 10.9 Non-Goals in the Storage Core

The core storage layer explicitly excludes:

- SQL or ad-hoc query languages
- implicit joins
- automatic query planners
- relational schema migration systems

A relational or SQL-facing layer may be added later, but only as an explicit layer on top of the KV contract without expanding the core persistence surface.

### 10.10 Deployment and Operations Requirements

Production infrastructure requirements:

- FoundationDB cluster operated as shared persistent backend for Score runtimes
- regular backup/snapshot policy
- monitoring for throughput, latency, and conflict rates
- documented failover and high-availability topology

Local requirements:

- configurable in-memory or lightweight on-disk backend
- transactional and atomic semantic parity with production contract

### 10.11 Future Real-Time Extensions

Watch/observe primitives may be added later as an optional capability for change propagation to real-time clients.

Any such feature must remain layered on top of the same transactional KV contract.


## 11. Non-Goals for This Phase

- locking in macro syntax before the underlying runtime exists
- documenting CLIs or generated project layouts that are not implemented
- specifying large component catalogs before `ScoreUI` exists
- writing end-state architecture as if every package target were already operational

## 12. Success Criteria

- the current baseline APIs stay small, coherent, and well documented
- handbook claims remain honest relative to the package contents
- new modules graduate from placeholder to documented subsystem only when code supports it

Previous: [Ecosystem PRD](ecosystem-prd.md)
Next: [Allegro Stage PRD](stage-prd.md)


## 13. Post-v1 Repository Policy

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

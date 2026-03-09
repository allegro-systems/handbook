# Allegro Score PRD

_PRD · framework_

---

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

All library modules now have substantive implementations with test coverage. The foundational layers (`ScoreCore`, `ScoreHTML`, `ScoreCSS`, `ScoreRouter`) were implemented first, followed by `ScoreRuntime`, `ScoreStorage`, `ScoreAuth`, `ScoreContent`, `ScoreAssets`, `ScoreUI`, and `ScoreVendor`. Score is at 85% overall, with CLI tooling and canonical examples remaining as the primary gaps before v1.

Score persistence direction is now locked: Score uses one transactional KV storage methodology. Any legacy storage module boundaries in the package layout are migration artifacts and not separate long-term product methodologies.

### End Goal Default Directory Structure

```text
Sources/
  App.swift
  Controllers/
  Pages/
  Middleware/
  Components/
  Models/
  Utilities/
  Vendors/
Package.swift
Resources/
  Localizable.xcstrings
  Assets/
```

> [!NOTE]
> All examples should follow this structure and the CLI tool minimal template should generate this structure with one example per directory.

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

## 3. Delivered Modules

The following modules have been implemented with test coverage and are now delivered product surfaces:

- `ScoreRuntime` — HTTP server, page renderer, CSS/JS pipeline, reactive model, event bindings, JS emitter
- `ScoreStorage` — unified transactional KV store with FoundationDB-compatible protocol, InMemoryStore, TTL, transactions, scan, increment
- `ScoreAuth` — Magic Link email auth, Passkey challenge/credential types, session lifecycle, CSRF tokens
- `ScoreContent` — Markdown-to-Node conversion, syntax themes, code blocks, MathML rendering, front matter, content collections
- `ScoreAssets` — SHA256 fingerprinting, asset manifest, MIME types, gzip optimization, asset pipeline
- `ScoreUI` — 30 shadcn-equivalent components
- `ScoreVendor` — Script node for third-party injection, Analytics providers, VendorIntegration protocol

The remaining gaps before v1 are CLI tooling (`score init`, `score dev`, `score build`, `score deploy`) and canonical example applications.

## 4. Near-Term Product Direction

The next meaningful Score milestones are:

- ship `ScoreRuntime` in three concrete steps: server runtime + document assembly, browser runtime + reactive bindings, then development diagnostics
- keep the browser-side runtime aligned with platform primitives instead of inventing a custom client state system
- make CSS class injection, document assembly, and route handling real checked-in runtime behaviour before expanding adjacent modules
- defer higher-level interop such as WASM until the server/browser runtime contract is stable
- turn placeholder modules into real package surfaces one subsystem at a time
- keep documentation tied to checked-in APIs instead of speculative syntax
- expand test coverage alongside each newly-real module

### 4.1 Planned Authoring Surface

Score's intended end-state authoring flow remains:

- `Application`
- `Theme`
- `Metadata`
- `Page`
- `Controller`
- `Component`
- styling and responsive modifiers
- interactivity and data capabilities

The generated project scaffold for `score init` should make that shape explicit from day one:

```text
Sources/
  App.swift
  Controllers/
  Pages/
  Middleware/
  Components/
  Models/
  Utilities/
  Vendors/
Package.swift
Resources/
  Localizable.xcstrings
  Assets/
```

`score init` should ship first-party templates for at least:

- `blog`
- `marketing`
- `docs`
- `saas`
- `social`
- `static`
- `calculator`
- `minimal`
- `localised`
- `taskboard`

Those templates are product examples, not empty stubs, and must reflect the canonical Score patterns for each use case.

### 4.2 Theme Contract

The final Score theme model must preserve these rules:

- a theme declaration is optional; Score ships a complete default theme
- `Application.theme` is the canonical declaration site
- `ThemePatch` is the additive override mechanism for dark and named variants
- semantic roles, typography, spacing, radius, and syntax highlighting are all part of the same theme contract
- custom color roles must compile to typed Swift accessors rather than stringly lookups

The theme surface must include, at minimum:

- built-in semantic roles such as `surface`, `text`, `border`, and `accent`
- custom role shade scales defined from palette colors or explicit `oklch(...)` values
- `dark: ThemePatch(...)` declared inline with the base theme rather than mutated later
- `named: [String: ThemePatch]` for alternate named themes such as `ocean` or `forest`
- font families for sans, serif, mono, and optional brand usage
- type scale, spacing scale, radius scale, and syntax theme configuration

Color-mode and theme-selection behavior must be explicit:

- system appearance is the default baseline when no explicit user preference exists
- `.light { ... }` and `.dark { ... }` target explicit color-mode variants
- `.theme("name") { ... }` targets named themes orthogonally to light/dark handling
- the active named theme must be rendered on `<html>` as `data-theme="..."`
- named theme preference must persist via the `as_theme` cookie so SSR and static enhancement agree without flash
- non-JavaScript theme switching must work through a Score-owned endpoint such as `/_score/theme`
- JavaScript enhancement may swap the active theme immediately, but correctness must not depend on JavaScript

Syntax highlighting is part of the theme system, not an unrelated addon. `Theme` must be able to select a built-in syntax theme or a custom typed `SyntaxTheme` mapping.

## 5. Build Artifact Contract

The canonical local build root is `.build/score/`. Exported deploy artifacts may be copied elsewhere, but they must preserve this structure and meaning:

```text
.build/score/
  /static
    index.html
    about/index.html
    as-global.css
    as-group-N.css
    as-interactivity.js (optional)
    as-data.js (optional)
    as-scroll.js (optional)
    as-position.js (optional)
    as-transitions.js (optional)
    assets/
    manifest.webmanifest (optional)
    sw.js (optional)
  /server (optional)
  manifest.json
```

Required constraints:

- `static` contains the static-first output and must always be present.
- `server` is optional and only emitted when server runtime capabilities are required by the project.
- `manifest.json` is required and must describe build identity, toolchain versions, and activated capabilities.
- Output must remain deployable outside Stage with no Stage-specific runtime coupling.
- Static JS modules must only be emitted when their triggering features are present in the compiled program.
- Runtime artifacts must not embed machine-specific absolute paths.

### 5.1 Static Bundle Structure

- `as-global.css` contains reset and base token output shared across the build
- `as-group-N.css` contains route-group or feature-group CSS only for the surfaces that need it
- JS remains zero by default
- `as-interactivity.js` is emitted only for interactive pages
- `as-data.js` is emitted only for data/runtime features that require it
- focused helper modules such as `as-scroll.js`, `as-position.js`, and `as-transitions.js` are emitted only when the corresponding feature families are used
- PWA files such as `manifest.webmanifest` and `sw.js` are emitted only when PWA capabilities are declared

### 5.2 Manifest Requirements

`manifest.json` must include, at minimum:

- Score version
- Swift toolchain version
- build timestamp (UTC)
- deterministic build fingerprint
- capability manifest

### 5.3 Capability Manifest Schema

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

Score CLI is the canonical execution interface for local development, local production builds, and remote deploy orchestration. Composer depends on Score CLI as its execution backbone. All CLI commands must remain compatible with both interactive terminal usage and programmatic invocation.

### 6.1 `score init`

Generates a canonical Score project from a built-in template.

- generate the canonical project structure
- support selecting a built-in template interactively or by flag
- produce a runnable project rather than an empty placeholder
- keep template structure aligned with current documented Score conventions

### 6.2 `score dev`

Provides the local development workflow with file watching, recompilation, and preview.

- watch project files
- recompile on Swift file save and Composer structural edits
- run a local preview server
- support hot reload
- run without requiring a fully containerized local toolchain

Toolchain behavior:

- when a local Swift toolchain is available, `score dev` performs normal compile validation
- when unavailable, `score dev` may run in structural preview mode with reduced compile validation and explicit warnings

### 6.3 `score build`

Performs full project compilation and produces deterministic build output.

- fully compile the Swift project
- produce deterministic output in `.build/score/`
- emit `manifest.json` including capability data
- return structured JSON error output when requested by tooling integrations such as Composer

### 6.4 `score deploy`

Packages and submits the canonical project snapshot to the selected deploy target.

- package and submit the canonical project snapshot to the selected deploy target (Stage by default)
- invoke a remote `score build` on Stage using pinned toolchain and Score versions
- return deployment identity and resolved service URLs

### 6.5 Exit Codes

| Code | Meaning |
| --- | --- |
| `0` | Success |
| `1` | Compile, build, or deploy failure |
| `2` | Usage or argument error |
| `3` | Environment or toolchain prerequisite failure |

### 6.6 Structured Error Output

When JSON mode is enabled, command failures must emit machine-readable errors:

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

| Code | Stage | Description |
| --- | --- | --- |
| `compile_error` | `build` | Swift compilation failure |
| `link_error` | `build` | Linker failure |
| `manifest_error` | `build` | Invalid or inconsistent manifest |
| `deploy_error` | `deploy` | Remote deployment failure |
| `toolchain_error` | `init`, `dev`, `build` | Missing or incompatible Swift toolchain |
| `argument_error` | any | Invalid CLI arguments or flags |

This schema must remain backward-compatible for Composer integrations.

### 6.7 CLI Interface

Score CLI tools are built with [Noora](https://noora.tuist.dev) for their terminal interfaces.

## 7. Deterministic Build Invariant

Given identical project input, Score version, Swift toolchain version, and build settings, Score must produce byte-equivalent build outputs across compliant environments.

Build systems must reject nondeterministic inputs and outputs that violate this invariant.

## 8. Capability Enforcement

Capability enforcement is mandatory across local and remote builds.

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

The browser path must stay platform-first. Score should compose with the browser's DOM, CSS, events, navigation, and storage primitives instead of replacing them with a second application runtime.

Score's reactive behaviour must provide fine-grained, deterministic, glitch-free updates comparable to modern signal systems, while preserving:

- progressive enhancement
- deterministic compile output
- no userland JavaScript contract
- an inspectable development experience without making debug attributes part of the production contract

The reactive engine is an internal subsystem of `ScoreRuntime`. It is not exposed as a public API surface.

### 9.1 Server Runtime Baseline

The first runtime milestone is correct server execution:

- route incoming requests through the existing `Application`, `Page`, and `Controller` contracts
- render a page body through `ScoreHTML`
- collect scoped CSS through `ScoreCSS`
- assemble a complete HTML document with metadata, theme variables, and runtime assets when needed
- return standards-compliant HTTP responses for page and controller routes

This phase establishes the canonical browser document that later enhancement layers attach to.

### 9.2 Browser Runtime — Three-Level Signal State Model

For interactive pages, Score's browser runtime is compiler-driven and signal-backed. The compiler lowers Swift state declarations to JavaScript using the TC39 Signals polyfill (`signal-polyfill` npm package, until browser-native signals are available).

Score defines **three state scopes** that map directly to Swift protocol conformances:

#### Application State (`Application` conformance)

Application-scoped `@State` compiles to **module-level `Signal.State` singletons** exported from `app.js`. Shared across all pages and elements for the entire application session.

<div style="display:grid;grid-template-columns:1fr 1fr;gap:2px;border-radius:6px;overflow:hidden;border:1px solid #2e2a22;font-family:'DM Mono',monospace;font-size:12px;line-height:1.7;">
<div style="background:#13120f;">
<div style="display:flex;align-items:center;justify-content:space-between;padding:10px 16px;border-bottom:1px solid #2e2a22;background:#1a1814;">
  <span style="font-size:10px;letter-spacing:0.1em;text-transform:uppercase;color:#7a7060;">Score · Swift</span>
  <span style="font-size:10px;color:#3d3830;letter-spacing:0.05em;">App.swift</span>
</div>
<pre style="padding:20px 16px;margin:0;overflow-x:auto;color:#e8e0d0;"><code><span style="color:#3d3830;font-style:italic;">// Application conformance — singleton</span>
<span style="color:#3d3830;font-style:italic;">// shared across all pages and elements</span>

<span style="color:#c8a96e;">@main</span>
<span style="color:#c86e9e;">struct</span> <span style="color:#6e9ec8;">MyApp</span>: <span style="color:#6e9ec8;">Application</span> {
    <span style="color:#c8a96e;">@State</span> <span style="color:#c86e9e;">var</span> <span style="color:#c8c0a8;">isDarkMode</span>: <span style="color:#6e9ec8;">Bool</span> = <span style="color:#c86e9e;">true</span>
    <span style="color:#c8a96e;">@State</span> <span style="color:#c86e9e;">var</span> <span style="color:#c8c0a8;">currentUser</span>: <span style="color:#6e9ec8;">User?</span> = <span style="color:#c86e9e;">nil</span>

    <span style="color:#c8a96e;">@Action</span>
    <span style="color:#c86e9e;">func</span> <span style="color:#a09070;">toggleTheme</span>() {
        isDarkMode.toggle()
    }

    <span style="color:#c8a96e;">@Action</span>
    <span style="color:#c86e9e;">func</span> <span style="color:#a09070;">signIn</span>(<span style="color:#c8c0a8;">name</span>: <span style="color:#6e9ec8;">String</span>) {
        currentUser = <span style="color:#6e9ec8;">User</span>(<span style="color:#c8c0a8;">name</span>: name)
    }
}</code></pre>
</div>
<div style="background:#13120f;">
<div style="display:flex;align-items:center;justify-content:space-between;padding:10px 16px;border-bottom:1px solid #2e2a22;background:#1a1814;">
  <span style="font-size:10px;letter-spacing:0.1em;text-transform:uppercase;color:#7a7060;">Compiled Output</span>
  <span style="font-size:10px;color:#3d3830;letter-spacing:0.05em;">app.js</span>
</div>
<pre style="padding:20px 16px;margin:0;overflow-x:auto;color:#e8e0d0;"><code><span style="color:#3d3830;font-style:italic;">// Score runtime — app.js</span>
<span style="color:#3d3830;font-style:italic;">// Auto-generated. Do not edit.</span>
<span style="color:#c86e9e;">import</span> { <span style="color:#6e9ec8;">Signal</span> } <span style="color:#c86e9e;">from</span> <span style="color:#6ec88a;">"signal-polyfill"</span>;

<span style="color:#3d3830;font-style:italic;">// Application: @State — module singleton</span>
<span style="color:#c86e9e;">export const</span> <span style="color:#c8c0a8;">isDarkMode</span> =
  <span style="color:#c86e9e;">new</span> <span style="color:#6e9ec8;">Signal</span>.<span style="color:#6e9ec8;">State</span>(<span style="color:#c86e9e;">true</span>);

<span style="color:#c86e9e;">export const</span> <span style="color:#c8c0a8;">currentUser</span> =
  <span style="color:#c86e9e;">new</span> <span style="color:#6e9ec8;">Signal</span>.<span style="color:#6e9ec8;">State</span>(<span style="color:#c86e9e;">null</span>);

<span style="color:#3d3830;font-style:italic;">// @Action — plain setters</span>
<span style="color:#c86e9e;">export function</span> <span style="color:#c8a96e;">toggleTheme</span>() {
  isDarkMode.<span style="color:#c8a96e;">set</span>(!isDarkMode.<span style="color:#c8a96e;">get</span>());
}

<span style="color:#c86e9e;">export function</span> <span style="color:#c8a96e;">signIn</span>(name) {
  currentUser.<span style="color:#c8a96e;">set</span>({ name });
}</code></pre>
</div>
</div>

<div style="display:flex;align-items:center;gap:8px;padding:8px 0;font-family:'DM Mono',monospace;font-size:11px;color:#3d3830;">
  <span style="display:inline-block;width:8px;height:8px;border-radius:50%;background:#6e9ec8;"></span>
  <span style="color:#7a7060;">Lifetime:</span> Entire application session. Never torn down during navigation.
</div>

#### Page State (`Page` conformance)

Page-scoped `@State` compiles to **module-scoped (non-exported) `Signal.State`** in per-page JS modules. State is torn down on navigation away from the page.

<div style="display:grid;grid-template-columns:1fr 1fr;gap:2px;border-radius:6px;overflow:hidden;border:1px solid #2e2a22;font-family:'DM Mono',monospace;font-size:12px;line-height:1.7;">
<div style="background:#13120f;">
<div style="display:flex;align-items:center;justify-content:space-between;padding:10px 16px;border-bottom:1px solid #2e2a22;background:#1a1814;">
  <span style="font-size:10px;letter-spacing:0.1em;text-transform:uppercase;color:#7a7060;">Score · Swift</span>
  <span style="font-size:10px;color:#3d3830;letter-spacing:0.05em;">Shop.swift</span>
</div>
<pre style="padding:20px 16px;margin:0;overflow-x:auto;color:#e8e0d0;"><code><span style="color:#3d3830;font-style:italic;">// Page conformance — scoped to this</span>
<span style="color:#3d3830;font-style:italic;">// page's lifetime, torn down on navigation</span>

<span style="color:#c86e9e;">struct</span> <span style="color:#6e9ec8;">ShopPage</span>: <span style="color:#6e9ec8;">Page</span> {
    <span style="color:#c8a96e;">@State</span> <span style="color:#c86e9e;">var</span> <span style="color:#c8c0a8;">activeFilter</span>: <span style="color:#6e9ec8;">String</span> = <span style="color:#6ec88a;">"all"</span>

    <span style="color:#c8a96e;">@Computed</span>
    <span style="color:#c86e9e;">var</span> <span style="color:#c8c0a8;">visibleProducts</span>: [<span style="color:#6e9ec8;">Product</span>] {
        <span style="color:#c86e9e;">guard</span> activeFilter != <span style="color:#6ec88a;">"all"</span>
        <span style="color:#c86e9e;">else</span> { <span style="color:#c86e9e;">return</span> products }
        <span style="color:#c86e9e;">return</span> products.<span style="color:#a09070;">filter</span> {
            $0.category == activeFilter
        }
    }

    <span style="color:#c8a96e;">@Action</span>
    <span style="color:#c86e9e;">func</span> <span style="color:#a09070;">setFilter</span>(<span style="color:#c8c0a8;">_</span> f: <span style="color:#6e9ec8;">String</span>) {
        activeFilter = f
    }
}</code></pre>
</div>
<div style="background:#13120f;">
<div style="display:flex;align-items:center;justify-content:space-between;padding:10px 16px;border-bottom:1px solid #2e2a22;background:#1a1814;">
  <span style="font-size:10px;letter-spacing:0.1em;text-transform:uppercase;color:#7a7060;">Compiled Output</span>
  <span style="font-size:10px;color:#3d3830;letter-spacing:0.05em;">shop.js</span>
</div>
<pre style="padding:20px 16px;margin:0;overflow-x:auto;color:#e8e0d0;"><code><span style="color:#3d3830;font-style:italic;">// Score runtime — shop.js</span>
<span style="color:#3d3830;font-style:italic;">// Auto-generated. Do not edit.</span>
<span style="color:#c86e9e;">import</span> { <span style="color:#6e9ec8;">Signal</span> } <span style="color:#c86e9e;">from</span> <span style="color:#6ec88a;">"signal-polyfill"</span>;

<span style="color:#3d3830;font-style:italic;">// Page: @State — module-scoped, not exported</span>
<span style="color:#c86e9e;">const</span> <span style="color:#c8c0a8;">activeFilter</span> =
  <span style="color:#c86e9e;">new</span> <span style="color:#6e9ec8;">Signal</span>.<span style="color:#6e9ec8;">State</span>(<span style="color:#6ec88a;">"all"</span>);

<span style="color:#3d3830;font-style:italic;">// @Computed — derived, memoised</span>
<span style="color:#c86e9e;">const</span> <span style="color:#c8c0a8;">visibleProducts</span> =
  <span style="color:#c86e9e;">new</span> <span style="color:#6e9ec8;">Signal</span>.<span style="color:#6e9ec8;">Computed</span>(() => {
    <span style="color:#c86e9e;">const</span> f = activeFilter.<span style="color:#c8a96e;">get</span>();
    <span style="color:#c86e9e;">if</span> (f === <span style="color:#6ec88a;">"all"</span>) <span style="color:#c86e9e;">return</span> products;
    <span style="color:#c86e9e;">return</span> products.<span style="color:#c8a96e;">filter</span>(
      p => p.category === f
    );
  });

<span style="color:#3d3830;font-style:italic;">// @Action</span>
<span style="color:#c86e9e;">export function</span> <span style="color:#c8a96e;">setFilter</span>(f) {
  activeFilter.<span style="color:#c8a96e;">set</span>(f);
}</code></pre>
</div>
</div>

<div style="display:flex;align-items:center;gap:8px;padding:8px 0;font-family:'DM Mono',monospace;font-size:11px;color:#3d3830;">
  <span style="display:inline-block;width:8px;height:8px;border-radius:50%;background:#c86e9e;"></span>
  <span style="color:#7a7060;">Lifetime:</span> Page mount to unmount. Torn down on navigation.
</div>

#### Element State (`Element` conformance)

Element-scoped `@State` compiles to **instance-scoped `Signal.State` inside a factory function**. Each DOM mount gets its own isolated signal graph.

<div style="display:grid;grid-template-columns:1fr 1fr;gap:2px;border-radius:6px;overflow:hidden;border:1px solid #2e2a22;font-family:'DM Mono',monospace;font-size:12px;line-height:1.7;">
<div style="background:#13120f;">
<div style="display:flex;align-items:center;justify-content:space-between;padding:10px 16px;border-bottom:1px solid #2e2a22;background:#1a1814;">
  <span style="font-size:10px;letter-spacing:0.1em;text-transform:uppercase;color:#7a7060;">Score · Swift</span>
  <span style="font-size:10px;color:#3d3830;letter-spacing:0.05em;">QuantityPicker.swift</span>
</div>
<pre style="padding:20px 16px;margin:0;overflow-x:auto;color:#e8e0d0;"><code><span style="color:#3d3830;font-style:italic;">// Element conformance — each instance</span>
<span style="color:#3d3830;font-style:italic;">// gets its own isolated signal graph</span>

<span style="color:#c86e9e;">struct</span> <span style="color:#6e9ec8;">QuantityPicker</span>: <span style="color:#6e9ec8;">Element</span> {
    <span style="color:#c86e9e;">let</span> <span style="color:#c8c0a8;">product</span>: <span style="color:#6e9ec8;">Product</span>
    <span style="color:#c86e9e;">let</span> <span style="color:#c8c0a8;">max</span>: <span style="color:#6e9ec8;">Int</span> = <span style="color:#c8956e;">10</span>

    <span style="color:#c8a96e;">@State</span> <span style="color:#c86e9e;">var</span> <span style="color:#c8c0a8;">count</span>: <span style="color:#6e9ec8;">Int</span> = <span style="color:#c8956e;">0</span>

    <span style="color:#c8a96e;">@Computed</span>
    <span style="color:#c86e9e;">var</span> <span style="color:#c8c0a8;">canIncrement</span>: <span style="color:#6e9ec8;">Bool</span> {
        count &lt; max
    }

    <span style="color:#c8a96e;">@Action</span>
    <span style="color:#c86e9e;">func</span> <span style="color:#a09070;">increment</span>() {
        <span style="color:#c86e9e;">guard</span> canIncrement <span style="color:#c86e9e;">else</span> { <span style="color:#c86e9e;">return</span> }
        count += <span style="color:#c8956e;">1</span>
    }

    <span style="color:#c8a96e;">@Action</span>
    <span style="color:#c86e9e;">func</span> <span style="color:#a09070;">decrement</span>() {
        <span style="color:#c86e9e;">guard</span> count > <span style="color:#c8956e;">0</span> <span style="color:#c86e9e;">else</span> { <span style="color:#c86e9e;">return</span> }
        count -= <span style="color:#c8956e;">1</span>
    }
}</code></pre>
</div>
<div style="background:#13120f;">
<div style="display:flex;align-items:center;justify-content:space-between;padding:10px 16px;border-bottom:1px solid #2e2a22;background:#1a1814;">
  <span style="font-size:10px;letter-spacing:0.1em;text-transform:uppercase;color:#7a7060;">Compiled Output</span>
  <span style="font-size:10px;color:#3d3830;letter-spacing:0.05em;">quantity-picker.js</span>
</div>
<pre style="padding:20px 16px;margin:0;overflow-x:auto;color:#e8e0d0;"><code><span style="color:#3d3830;font-style:italic;">// Score runtime — quantity-picker.js</span>
<span style="color:#3d3830;font-style:italic;">// Auto-generated. Do not edit.</span>
<span style="color:#c86e9e;">import</span> { <span style="color:#6e9ec8;">Signal</span> } <span style="color:#c86e9e;">from</span> <span style="color:#6ec88a;">"signal-polyfill"</span>;

<span style="color:#3d3830;font-style:italic;">// Element: factory — called per mount</span>
<span style="color:#c86e9e;">export function</span> <span style="color:#c8a96e;">mountQuantityPicker</span>(
  el, { product, max = <span style="color:#c8956e;">10</span> }
) {
  <span style="color:#3d3830;font-style:italic;">// Element: @State — instance-scoped</span>
  <span style="color:#c86e9e;">const</span> <span style="color:#c8c0a8;">count</span> =
    <span style="color:#c86e9e;">new</span> <span style="color:#6e9ec8;">Signal</span>.<span style="color:#6e9ec8;">State</span>(<span style="color:#c8956e;">0</span>);

  <span style="color:#3d3830;font-style:italic;">// @Computed</span>
  <span style="color:#c86e9e;">const</span> <span style="color:#c8c0a8;">canIncrement</span> =
    <span style="color:#c86e9e;">new</span> <span style="color:#6e9ec8;">Signal</span>.<span style="color:#6e9ec8;">Computed</span>(
      () => count.<span style="color:#c8a96e;">get</span>() < max
    );

  <span style="color:#3d3830;font-style:italic;">// @Action</span>
  <span style="color:#c86e9e;">const</span> <span style="color:#c8a96e;">increment</span> = () => {
    <span style="color:#c86e9e;">if</span> (canIncrement.<span style="color:#c8a96e;">get</span>())
      count.<span style="color:#c8a96e;">set</span>(count.<span style="color:#c8a96e;">get</span>() + <span style="color:#c8956e;">1</span>);
  };
  <span style="color:#c86e9e;">const</span> <span style="color:#c8a96e;">decrement</span> = () => {
    <span style="color:#c86e9e;">if</span> (count.<span style="color:#c8a96e;">get</span>() > <span style="color:#c8956e;">0</span>)
      count.<span style="color:#c8a96e;">set</span>(count.<span style="color:#c8a96e;">get</span>() - <span style="color:#c8956e;">1</span>);
  };

  <span style="color:#3d3830;font-style:italic;">// Wire DOM + return teardown</span>
  <span style="color:#c86e9e;">return</span> { count, canIncrement,
           increment, decrement };
}</code></pre>
</div>
</div>

<div style="display:flex;align-items:center;gap:8px;padding:8px 0;font-family:'DM Mono',monospace;font-size:11px;color:#3d3830;">
  <span style="display:inline-block;width:8px;height:8px;border-radius:50%;background:#6ec88a;"></span>
  <span style="color:#7a7060;">Lifetime:</span> Element mount to unmount. Each instance is fully isolated.
</div>

#### Lowering Summary

| Swift construct | JS output | Scope |
|---|---|---|
| `@State` on `Application` | `export const x = new Signal.State(v)` in `app.js` | Module singleton (global) |
| `@State` on `Page` | `const x = new Signal.State(v)` in page module | Module-scoped (page lifetime) |
| `@State` on `Element` | `const x = new Signal.State(v)` inside factory | Instance-scoped (per mount) |
| `@Computed` | `new Signal.Computed(() => ...)` | Same scope as containing type |
| `@Action` | Plain function performing `.set()` calls | Same scope as containing type |
| `.on` event bindings | DOM event listeners bound to emitted functions | Compiler-generated |

Developers do not write effects directly; the compiler emits them for DOM bindings.

### 9.3 DOM and Inspection Contract

| Concern | Production mechanism | Development mechanism |
|----------|------------|------------|
| DOM targeting for reactive updates | compiler-emitted direct DOM references and traversal paths | `data-s` attributes may be emitted for inspectability |
| Styling | scoped class names emitted from modifier sets | same as production |
| Event wiring | compiler-emitted listener attachment | same as production |
| Component/source inspection | none required for correctness | debug annotations may be emitted for devtools and source navigation |

`data-as-*` is not a required production contract. Debug-oriented attributes may exist in development, but production correctness must not depend on them.

### 9.4 Reactive Invariants

#### 1. Fine-Grained Dependency Tracking

Derived values and DOM bindings must track dependencies based on actual signal reads during execution. If a computed value conditionally reads different signals, the dependency graph must update dynamically.

#### 2. Batched Updates

Multiple writes within the same microtask must coalesce into a single dependency propagation pass. DOM updates trigger only after state stabilises. No cascading synchronous DOM mutation per `.set()` call.

#### 3. Deterministic Effect Ordering

Reactive effects must execute in a stable and deterministic order: parent scopes before children, earlier bindings before later bindings within a scope.

#### 4. Glitch-Free Derived State

Computed values must never observe partially-updated state. Derived computations observe either the previous stable graph or the next stable graph, never an intermediate mix.

#### 5. Disposal Semantics

When DOM nodes are removed (conditional rendering, list diffing, scope unmounting), all reactive bindings associated with that subtree must be disposed. The runtime must not leak subscriptions.

#### 6. Scope Isolation

Application state is explicitly global (module-level singletons). Page state is module-scoped and torn down on navigation. Element state is instance-scoped and torn down on unmount. No implicit global graph may emerge from ad-hoc runtime registration.

### 9.5 Browser-First Principles

- HTML forms should stay real forms
- links and navigation should stay real links unless runtime interception is explicitly enabled
- browser-managed UI such as dialog, disclosure, popover, focus, history, and scroll restoration should be used where it fits
- persistence and multi-context coordination should use browser storage and messaging standards where possible
- source maps, module loading, and event dispatch should follow existing browser conventions

### 9.6 Non-Goals of the Runtime Model

- No user-exposed `Signal` type in Swift.
- No custom object-shaped client store as the primary runtime abstraction.
- No implicit global store.
- No stable production dependency on debug-only DOM attributes.
- No runtime template parsing.
- No second client runtime model separate from the browser platform.

### 9.7 Success Criteria for Runtime Behaviour

- Complex UI state graphs feel mechanically boring to implement.
- Developers never think about subscription management.
- Removing a subtree never leaks.
- Multiple state writes never cause flicker.
- The browser remains the source of truth for platform behaviour.
- Development inspection is strong without imposing production runtime overhead.

## 10. Planned Subsystems

These remain valid parts of the product direction, but their exact APIs are intentionally not frozen here yet:

- runtime execution and request handling
- authentication primitives
- content ingestion and authoring helpers
- asset pipeline support
- first-party UI components
- vendor integrations

### 10.1 ScoreRuntime Delivery Order

1. Server runtime, CSS class injection, and full document assembly.
2. Browser runtime bundle, reactive lowering for `@State` / `@Computed` / `@Action`, and event binding.
3. Development diagnostics such as source maps, devtools, and error overlays.

### 10.2 Runtime WASM Interop

After the core server/browser runtime contract is stable, Score runtime should provide WebAssembly interoperability as a capability of `ScoreRuntime`.

Requirements:

- easy module loading at `Application` scope for shared modules
- easy module loading at `Page` scope for page-local modules
- deterministic module initialization and lifecycle within runtime scopes
- ergonomic invocation from `.on` modifiers without manual bridge plumbing

### 10.3 FoundationDB-Backed Transactional KV Store

Score uses a single transactional key-value database abstraction for all persistent state. Production backing store: FoundationDB. Local backend: lightweight API-compatible engine preserving transactional semantics.

### 10.4 Locked Storage API Surface

- `get(key)`
- `set(key, value)`
- `delete(key)`
- `scan(range | prefix)`
- transactional atomic operations
- strong serializability guarantees for committed transaction results

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

### 10.5 Engine Layers

Production (`FoundationDB`): strict serializability, ACID transactions, replication, automatic sharding.

Local development: API-compatible local engine, preserves transaction atomicity and key ordering at dev scale.

### 10.6 Data Modeling Constraints

Score storage is ordered-key based. Canonical key strategy uses structured tuples: `tenant / collection / recordId`. Secondary indexes are explicit, declarative layers on top of the KV API.

### 10.7 Transaction and Conflict Discipline

- short bounded transactions
- no blocking I/O inside transaction closures
- explicit retry policy with bounded exponential backoff and jitter
- idempotent mutation paths for retry-safe behavior
- narrow key ranges to avoid hot-key contention

### 10.8 Non-Goals in the Storage Core

- SQL or ad-hoc query languages
- implicit joins
- automatic query planners
- relational schema migration systems

A relational layer may be added later on top of the KV contract.

### 10.9 Future Real-Time Extensions

Watch/observe primitives may be added later as an optional capability for change propagation to real-time clients.

## 11. Full-Stack Web Parity

Score's intended end state is to be the sole framework dependency for web applications built by the monthly creative build process (`new.sh`).

### 11.1 Current Coverage

| `new.sh` capability | Score equivalent | Status |
|---|---|---|
| SolidJS (UI framework) | ScoreCore + ScoreHTML + ScoreCSS + reactive model | Delivered |
| Vite (bundler/dev server) | ScoreCLI (`score dev` / `score build`) | Pending |
| TailwindCSS (utility CSS) | ScoreCSS modifier system | Delivered |
| Axum (HTTP server) | ScoreRuntime (NIO-based) | Delivered |
| Cookie sessions | ScoreAuth session lifecycle | Delivered |
| Magic link auth | ScoreAuth MagicLink | Delivered |
| Passkey auth | ScoreAuth Passkey | Delivered |
| CSRF protection | ScoreAuth CSRFToken | Delivered |
| Rate limiting | ScoreAuth RateLimitMiddleware | Delivered |
| Markdown content | ScoreContent | Delivered |
| Asset fingerprinting | ScoreAssets | Delivered |
| Analytics (Plausible, GA) | ScoreVendor | Delivered |
| Component library | ScoreUI (30 components) | Delivered |

### 11.2 Missing Capabilities

| `new.sh` capability | Required Score module | Priority |
|---|---|---|
| Drag and drop | ScoreUI `DragSource`/`DropTarget`/`Sortable` | High |
| File uploads | ScoreRuntime multipart parser | High |
| CORS | ScoreRuntime `CORSMiddleware` | High |
| Compression | ScoreRuntime `CompressionMiddleware` | Medium |
| JWT auth | ScoreAuth `JWTProvider` | High |
| OAuth2/OIDC | ScoreAuth `OAuthProvider` | High |
| Stripe payments | ScoreVendor `StripeIntegration` | Medium |
| SQLite/PostgreSQL | `ScoreData` relational adapter | High |
| Redis/Valkey | `ScoreCache` protocol | Medium |
| WebSocket | ScoreRuntime WebSocket handler | High |
| Structured logging | `ScoreLogger` | Medium |
| Component testing | `ScoreTesting` | Medium |
| E2E test harness | `score test` CLI | Low |

### 11.3 Delivery Order

1. **CLI tooling** — `score init`, `score dev`, `score build`, `score deploy`
2. **Core middleware** — CORS, compression, file uploads
3. **Data layer** — `ScoreData` relational adapter with SQLite local / PostgreSQL production
4. **Auth expansion** — JWT, OAuth2/OIDC
5. **Interactivity** — Drag and drop, WebSocket
6. **Payments** — Stripe integration
7. **DX** — Testing framework, structured logging

### 11.4 Success Criteria

- `score init saas` produces a runnable SaaS starter with auth, payments, and CRUD
- The monthly creative build (`new.sh`) can produce web apps using only Score
- All `score init` templates are production-quality Score applications

## 12. Non-Goals for This Phase

- locking in macro syntax before the underlying runtime exists
- documenting CLIs or generated project layouts that are not implemented
- specifying large component catalogs before `ScoreUI` exists
- writing end-state architecture as if every package target were already operational

## 13. Success Criteria

- the current baseline APIs stay small, coherent, and well documented
- handbook claims remain honest relative to the package contents
- new modules graduate from placeholder to documented subsystem only when code supports it

## 14. Post-v1 Repository Policy

At the moment `v1.0.0` is released, the Score repository must move to maintenance controls:

1. No direct pushes to `main`.
2. Pull requests are the only path to merge into `main`.
3. All PRs targeting `main` must pass `.github/workflows/ci.yml`.
4. Force pushes are disabled on `main`.
5. Local `--no-verify` bypass is not accepted as a merge path.

# Allegro Libretto PRD

[Handbook Index](README.md) | [Progress](progress.md) | [Systems Manifesto](manifesto.md) | [Design Philosophy](design-philosophy.md) | [Ecosystem PRD](ecosystem-prd.md) | [Allegro Score PRD](score-prd.md) | [Allegro Stage PRD](stage-prd.md) | [Allegro Libretto PRD](libretto-prd.md) | [Allegro Site PRD](allegro-site-prd.md) | [Allegro Composer PRD](composer-prd.md) | [CLI PRD](cli-prd.md) | [Glossary](glossary.md) | [Dependencies](dependencies.md) | [Decisions](decisions.md) | [Changelog](changelog.md)

_PRD • reference application_

## 1. Overview

Libretto is the first-party reference application in the Allegro ecosystem. It exists to validate Score and Stage under real product pressure and turn operational and UX learnings into concrete upstream requirements.

## 2. Problem Domain

Most creative professionals and small teams lack a unified place to plan, draft, and publish written work. Existing tools force a choice between powerful but opaque writing environments, rigid CMS platforms that prioritize publishing over authoring, or disconnected collections of notes and documents that never cohere into finished output.

Libretto is a focused writing and publishing workspace. It lets individuals and small teams move from early notes through structured drafts to published pages, all within a single product that treats the entire lifecycle as one continuous flow rather than a handoff between disconnected tools.

The domain is deliberately chosen to exercise the full depth of Score's capabilities: content authoring and rendering (ScoreContent), persistent storage (ScoreStorage), authentication and sessions (ScoreAuth), asset management (ScoreAssets), interactive UI (ScoreUI with reactive state), and server-side routing and rendering (ScoreRuntime). A writing and publishing product is a natural fit because it demands real data modeling, real auth, real content rendering, and real operational concerns without requiring exotic infrastructure.

## 3. Target Audience

**Primary: independent writers and small creative teams** who produce essays, articles, documentation, newsletters, or long-form content and want a clean, opinionated tool that handles the full write-to-publish cycle.

**Secondary: Allegro ecosystem evaluators** who want to see what a real Score application looks like in production, including its architecture, operational patterns, and UX quality.

Libretto is not targeting enterprise content management, social media publishing, or marketing automation. The audience is people who write seriously and want a tool that respects their workflow without burying them in configuration.

## 4. Product Goals

- prove Score can support credible product development beyond framework demos
- prove Stage can host and operate the product through supported public workflows
- provide a dogfooding loop that continuously improves Score and Stage
- demonstrate Allegro design and interaction standards in a shipped product

## 5. Product Boundary

Libretto sits downstream of Score and Stage.

- Score builds Libretto.
- Stage hosts Libretto.
- Libretto must use supported public APIs and workflows.
- Libretto must not rely on private or one-off internal escape hatches.

## 6. Core Feature Set

### Phase 1 — Foundation

The minimum surface required to call Libretto a usable product.

- **Workspace and authentication.** Magic link email auth and passkey support for account creation and login. Session management with CSRF protection. One workspace per account at launch.
- **Document editor.** Markdown-native editor with live preview rendering through ScoreContent. Support for front matter, headings, lists, code blocks, and inline formatting. Autosave to ScoreStorage.
- **Document management.** Create, rename, archive, and delete documents. Flat document list with search and sort. Persistent storage backed by ScoreStorage transactional KV.
- **Publishing.** One-click publish from draft to a public URL. Published pages rendered server-side through ScoreRuntime with ScoreHTML and ScoreCSS. Clean, readable output with no editor chrome.

### Phase 2 — Depth

Features that make Libretto a credible daily-use product.

- **Collections.** Group documents into ordered collections (series, sections, chapters). Collection landing pages with table of contents.
- **Asset handling.** Image uploads stored through ScoreAssets with SHA256 fingerprinting. Inline image embedding in documents. Asset gallery per workspace.
- **Theming.** Author-selectable themes for published output using Score's theme system. Light and dark mode support. Custom accent colors.
- **Revision history.** Version snapshots stored on save milestones. Diff view between revisions. Restore to any previous version.
- **Custom domains.** Map a custom domain to published workspace output. TLS and DNS verification through Stage.

### Phase 3 — Collaboration and Scale

Features that expand Libretto beyond solo use.

- **Team workspaces.** Invite collaborators by email. Role-based access (owner, editor, viewer).
- **Comments and annotations.** Inline comments on document sections. Comment threads with resolution.
- **Analytics.** Per-document and per-collection view counts and read-through rates surfaced through ScoreVendor analytics integration.
- **Newsletter delivery.** Publish documents as email newsletters. Subscriber management. Delivery tracking.
- **API access.** Read-only API for published content. Webhook notifications on publish events.

## 7. Use Cases

### Writer publishes an essay

A writer signs in with a magic link, creates a new document, writes in Markdown with live preview, and publishes. The essay appears at a clean public URL. The writer later edits the draft and republishes. Readers see the updated version with no manual cache management.

### Technical author builds a documentation series

An author creates a collection, adds ordered documents covering a technical topic, and publishes the collection. The collection page renders a navigable table of contents. Readers move between pages with previous/next navigation. The author reorders documents and the collection updates.

### Small team collaborates on a content calendar

A team owner creates a workspace and invites two editors. Each editor drafts documents independently. The owner reviews, leaves inline comments, and publishes approved pieces. Published analytics show which pieces perform well.

### Ecosystem evaluator reviews a Score application

A developer exploring Allegro visits the Libretto repository to understand how a production Score application is structured. They see the canonical directory layout, the use of ScoreContent for rendering, ScoreStorage for persistence, ScoreAuth for sessions, and ScoreRuntime for serving. The application serves as a concrete reference for their own Score projects.

## 8. Brand and Presentation Requirement

The primary lockup requirement is:

- `Libretto` wordmark
- small cursive `by Allegro` in the bottom-right of the lockup

## 9. Technical Requirements

### Score Integration Points

| Score Module | Libretto Usage |
|---|---|
| ScoreCore | Node tree for all page and component rendering |
| ScoreHTML | Server-side HTML emission for published pages and editor UI |
| ScoreCSS | Modifier-based styling, theme variables, responsive layout |
| ScoreRouter | Route table for editor, published content, auth flows, and API |
| ScoreRuntime | HTTP server, document assembly, reactive state in editor UI |
| ScoreStorage | Document storage, user records, workspace data, revision history |
| ScoreAuth | Magic link auth, passkey auth, sessions, CSRF |
| ScoreContent | Markdown-to-Node rendering for preview and published output |
| ScoreAssets | Image fingerprinting, asset manifest, MIME handling |
| ScoreUI | Editor controls, navigation, dialogs, toasts, command palette |
| ScoreVendor | Analytics integration for published content |

### Stage Integration Points

| Stage Capability | Libretto Usage |
|---|---|
| Static hosting | Published page output served from Stage CDN |
| Runtime hosting | Editor and API server running as a Stage workload |
| Deployment | `score deploy` pushes Libretto builds to Stage |
| Logs | Operational logs for debugging auth, storage, and rendering issues |
| Environments | Staging environment for pre-production validation |
| Custom domains | Author-mapped domains for published workspaces |

### Data Model

All persistent state uses ScoreStorage transactional KV with structured tuple keys:

- `workspace / {workspaceId}` — workspace metadata
- `workspace / {workspaceId} / documents / {documentId}` — document records
- `workspace / {workspaceId} / collections / {collectionId}` — collection metadata
- `workspace / {workspaceId} / assets / {assetId}` — asset metadata
- `workspace / {workspaceId} / revisions / {documentId} / {timestamp}` — revision snapshots
- `user / {userId}` — user records
- `index_by_email / {email} / {userId}` — email lookup index
- `index_by_slug / {workspaceId} / {slug} / {documentId}` — published URL lookup

Primary record writes and index writes must commit in the same transaction, consistent with Score's storage discipline.

## 10. Delivery Milestones

1. PRD complete.
2. Project scaffold with Score canonical directory structure.
3. Auth flow (magic link + passkey) and session management.
4. Document CRUD with Markdown editor and live preview.
5. Publishing flow with server-rendered public pages.
6. First usable build deployed to Stage.
7. Production dogfooding of Score + Stage.
8. Collections, assets, theming, and revision history.
9. Public launch.
10. `v1.0.0` release.
11. Secure repository controls enabled.

## 11. Non-Goals

- becoming a throwaway demo with no sustained operations
- introducing private framework/hosting shortcuts that bypass public boundaries
- committing to product scope that outpaces upstream platform maturity
- replacing established writing tools for enterprise content teams
- building a general-purpose CMS or blog engine
- supporting non-Markdown authoring formats in the initial phases

## 12. Success Criteria

- Libretto is built and operated fully through supported Score and Stage paths.
- Libretto usage produces concrete platform improvements filed as upstream issues.
- At least one real user outside the Allegro team uses Libretto for actual writing and publishing.
- Product quality reflects Allegro standards for UX clarity and reliability as defined in the design philosophy.
- Every Score module listed in the integration table is exercised in production Libretto usage.
- The Libretto codebase serves as a credible reference implementation for developers evaluating Score.

Previous: [Allegro Stage PRD](stage-prd.md)
Next: [Allegro Site PRD](allegro-site-prd.md)

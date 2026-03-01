# Allegro Build Progress

[Handbook Index](README.md) | [Progress](progress.md) | [Systems Manifesto](manifesto.md) | [Design Philosophy](design-philosophy.md) | [Ecosystem PRD](ecosystem-prd.md) | [Allegro Score PRD](score-prd.md) | [Allegro Stage PRD](stage-prd.md) | [Allegro Libretto PRD](libretto-prd.md) | [Allegro Site PRD](allegro-site-prd.md)

Score -> Stage -> Libretto -> Allegro Site

## Summary

| Phase | Status | Progress | Notes |
| --- | --- | --- | --- |
| Score | In Progress | 38% | Core + HTML/CSS renderers + Router are implemented and covered; downstream runtime/integration modules remain pending |
| Stage | Planning | 5% | Product boundary is defined, implementation has not started |
| Libretto | Planning | 2% | Reference application remains downstream of Stage |
| Allegro Site | Planning | 1% | Public docs and product surface is defined at a high level, implementation has not started |

## Score

Status: **In Progress**

Progress: **38%**

- Swift web framework

- [x] PRD + RFC complete
- [x] ScoreCore - node protocol, result builder, application/theme/metadata/routing contracts
- [x] ScoreHTML - HTML renderer with broad node coverage and conformance documentation
- [x] ScoreCSS - modifier emission, stylesheet rule deduplication, and conformance documentation
- [x] ScoreRouter - route table compilation, path matching with parameter extraction, 404/405 error handling
- [ ] ScoreRuntime - server runtime, build-mode detection, and browser/runtime assets
- [ ] ScoreDB - persistence contract and storage implementation
- [ ] ScoreKV - in-process key-value store
- [ ] ScoreAuth - authentication primitives
- [ ] ScoreContent - content ingestion and rendering helpers
- [ ] ScoreAssets - asset pipeline
- [ ] ScoreUI - first-party component library
- [ ] ScoreVendor - vendor integration boundary
- [ ] CLI tooling
- [ ] Canonical example applications
- [ ] v1.0.0 release
- [ ] Secure repo controls

## Stage

Status: **Planning**

Progress: **5%**

- Swift-native hosting platform
- Unblocked by: Score v1.0.0

- [x] PRD complete
- [ ] Local development flow
- [ ] Static hosting
- [ ] Runtime hosting
- [ ] Deployment and logs
- [ ] Dashboard and docs
- [ ] Self-hosted mode
- [ ] v1.0.0 release
- [ ] Secure repo controls

## Libretto

Status: **Planning**

Progress: **2%**

- First-party reference app
- Unblocked by: Stage v1.0.0
- Wordmark note: `Libretto` with a small cursive `by Allegro` in the bottom-right lockup

- [ ] PRD complete
- [ ] Product requirements
- [ ] First usable build
- [ ] Production dogfooding of Score + Stage
- [ ] Public launch
- [ ] v1.0.0 release
- [ ] Secure repo controls

## Allegro Site

Status: **Planning**

Progress: **1%**

- Public site and documentation surface
- Unblocked by: Libretto v1.0.0

- [ ] PRD complete
- [ ] Handbook publishing model
- [ ] Score API and guide documentation integration
- [ ] Stage documentation integration
- [ ] Libretto documentation and narrative integration
- [ ] Information architecture and navigation model
- [ ] First production deploy
- [ ] v1.0.0 release
- [ ] Secure repo controls

Next: [Systems Manifesto](manifesto.md)

## Post-v1 Repository Controls

Applies immediately when each repository reaches `v1.0.0`:

- `main` is PR-only (no direct pushes).
- Pull requests into `main` must pass required checks from `.github/workflows/ci.yml`.
- Force pushing to `main` is disabled.
- Pushes to `main` are rejected, including attempts with local `--no-verify`.

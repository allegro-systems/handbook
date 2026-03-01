# Allegro Libretto PRD

[Handbook Index](README.md) | [Progress](progress.md) | [Systems Manifesto](manifesto.md) | [Design Philosophy](design-philosophy.md) | [Ecosystem PRD](ecosystem-prd.md) | [Allegro Score PRD](score-prd.md) | [Allegro Stage PRD](stage-prd.md) | [Allegro Libretto PRD](libretto-prd.md) | [Allegro Site PRD](allegro-site-prd.md)

_PRD • reference application_

## 1. Overview

Libretto is the first-party reference application in the Allegro ecosystem. It exists to validate Score and Stage under real product pressure and turn operational and UX learnings into concrete upstream requirements.

## 2. Product Goals

- prove Score can support credible product development beyond framework demos
- prove Stage can host and operate the product through supported public workflows
- provide a dogfooding loop that continuously improves Score and Stage
- demonstrate Allegro design and interaction standards in a shipped product

## 3. Product Boundary

Libretto sits downstream of Score and Stage.

- Score builds Libretto.
- Stage hosts Libretto.
- Libretto must use supported public APIs and workflows.
- Libretto must not rely on private or one-off internal escape hatches.

## 4. Core Requirements

- clear problem domain and audience
- end-to-end product flows across routing, rendering, data, and auth
- deploy, logs, and operational workflows exercised in production-like environments
- explicit issue capture and prioritization for platform gaps found while shipping

## 5. Brand and Presentation Requirement

The primary lockup requirement is:

- `Libretto` wordmark
- small cursive `by Allegro` in the bottom-right of the lockup

## 6. Delivery Milestones

1. PRD complete.
2. First usable build.
3. Production dogfooding of Score + Stage.
4. Public launch.
5. `v1.0.0` release.
6. Secure repository controls enabled.

## 7. Non-Goals

- becoming a throwaway demo with no sustained operations
- introducing private framework/hosting shortcuts that bypass public boundaries
- committing to product scope that outpaces upstream platform maturity

## 8. Success Criteria

- Libretto is built and operated fully through supported Score and Stage paths.
- Libretto usage produces concrete platform improvements.
- Product quality reflects Allegro standards for UX clarity and reliability.

Previous: [Allegro Stage PRD](stage-prd.md)
Next: [Allegro Site PRD](allegro-site-prd.md)

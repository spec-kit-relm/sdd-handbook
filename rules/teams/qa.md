# Quality engineering rules (`@spec-kit-relm/qa`)

Applies to: `e2e-tests` and QA's work in `product-specs`.
Constitution layer: Part B `RELM:TEAM-QA` (QA-1 … QA-6) in `e2e-tests`; QA-1/QA-2 conventions are
also applied in `product-specs` through the review gates.

## MUST (mirrored in the constitution)

| ID | Rule | Why | How to comply |
|----|------|-----|---------------|
| QA-1 | QA co-owns acceptance criteria from `/speckit.specify`; runs `/speckit.checklist` before plans; humans tick items | Defects are cheapest in English | Gate 1 approval; checklist diffs |
| QA-2 | Scenario IDs `RELM-n/USk/ASm`; every test title starts with its ID | Coverage becomes computable | `scenario-coverage` job |
| QA-3 | E2E pins contract versions; selects expectations by version/capability, never branch | Environments mix versions during rollout | `contracts.lock.json` validation |
| QA-4 | No sleeps/order dependence/shared mutable data; flakes quarantined in 24 h, fixed in a sprint | A flaky gate is ignored | flake tracker; nightly rerun report |
| QA-5 | Synthetic data only | Privacy; reproducibility | seeding review; PII scan |
| QA-6 | Evidence-based release sign-off on the feature issue | Releases are decided on facts | Gate 4 checklist |

## SHOULD (team practices)
- One QA engineer is the **spec partner** for a feature (pairs with the PO); the other builds
  automation in parallel. Swap every feature.
- During `/speckit.clarify`, ask about: expiry/timeouts, concurrency, partial failure, retries,
  permissions, empty states, locale/currency.
- Prefer API-level setup (seed via service APIs) over UI setup in E2E tests.
- Keep the `@smoke` suite under 10 minutes.

## Review focus for any repository's PR
1. Do new tests carry scenario IDs?
2. Are negative contract cases (problem types, duplicates, timeouts) tested?
3. Does the PR change behavior that the product spec doesn't describe?

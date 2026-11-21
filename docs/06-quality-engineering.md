# 6. Quality engineering in an AI-native SDLC

QA at Relm is not a phase after development. QA engineers are **co-authors of the spec** and
**owners of the evidence** that the spec is met.

## What QA does in each phase

| Phase | QA activity | Artifact |
|-------|-------------|----------|
| 1 Product spec | Pair with the PO on user stories; challenge every acceptance scenario; review `/speckit.clarify` answers | `spec.md` scenarios |
| 1 Product spec | Run `/speckit.checklist` for `requirements`, `api`, `ux`, `qa`, `security` as relevant; tick items **by hand** during review | `checklists/*.md` |
| 1 Gate 1 | Approve scenarios + checklists; unresolved items are fixed or explicitly accepted by the PO | spec review PR |
| 2 Contracts | Review error/problem types and edge cases in contract PRs from a testability angle | contract PR comments |
| 3 Plans | Check each repo's `tasks.md` has scenario-ID test tasks and negative contract tests; fill the *QA: scenario-to-test strategy* in `e2e-tests/specs/NNN-*/plan.md` | plan reviews |
| 4 Build | Build E2E scenarios in `e2e-tests` against generated mocks / pinned versions, in parallel with development | `e2e-tests` PRs |
| 5 Verify | Run `@smoke` + feature suite in staging; track scenario coverage; quarantine flakes within 24 h | test reports |
| Gate 4 | Record sign-off on the feature issue with evidence links | release-readiness checklist |

## Checklists: "unit tests for English"

`/speckit.checklist` generates items that test the **requirements**, not the implementation:

- ✅ "Is behavior defined when the reservation expires while the payment is being authorized? [Spec §Edge Cases]"
- ❌ "Does the timer count down correctly?" (that is a test, not a requirement check)

The command never marks items `[x]`; QA does, during the spec review PR. An unticked item at Gate 1
is either resolved in the spec or explicitly accepted (with a reason) by the PO in the PR thread.

## Scenario IDs and coverage (QA-2)

Every Given/When/Then line in a spec is addressable: `RELM-<n>/US<k>/AS<m>` (story k, scenario m,
in the order written). Every automated test that verifies a scenario starts its title with the ID:

```ts
test('RELM-1/US1/AS1 reserving available stock shows a 15-minute hold', async ({ page }) => { … });
```

`e2e-tests` runs a `scenario-coverage` job that lists every scenario ID in the product specs and
reports which have at least one test (in any repo's test report). P1 scenarios without coverage
block Gate 4.

## The test pyramid across repositories

| Level | Where | Owner | Proves |
|-------|-------|-------|--------|
| Unit | each repo | feature owner | domain logic |
| Integration | each backend repo (real deps in containers) | feature owner | adapters, DB, broker |
| Provider verification | provider repo | provider | implementation conforms to *its own* contract |
| Consumer contract | consumer repo | consumer | consumer handles every documented response, incl. problems |
| Component / UI | web-storefront | FE | UI states, accessibility (axe) |
| E2E | e2e-tests | QA | user journeys across services, by scenario ID |

E2E is the smallest layer: only P1 journeys and cross-service edge cases. Everything that can be
proven lower in the pyramid is proven there.

## Flakiness policy (QA-4)

Quarantine within 24 h (tag `@quarantine`, excluded from gating runs), fix or delete within one
sprint. Agents are not allowed to "fix" a flaky test by adding sleeps or retries; use web-first
assertions and deterministic data.

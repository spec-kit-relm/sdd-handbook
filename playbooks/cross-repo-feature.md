# Playbook: a cross-repository feature, end to end

The worked example in this organization is **RELM-2 — Stock reservation at checkout**
(`product-specs#2`). It touches `inventory-service` (provider), `orders-service`
(consumer + provider), `web-storefront` (consumer) and `e2e-tests` (verification). The repositories
contain every artifact up to **Gate 3**; implementation has deliberately not started, so you can
run `/speckit.implement` yourself and watch the guardrails work.

> Commands are written in Spec Kit's dot form (`/speckit.plan`). In Claude Code, where Spec Kit
> installs skills, type the hyphen form (`/speckit-plan`).

## 0. One-time setup (each engineer)

```bash
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git@v1.4.3
mkdir -p ~/relm && cd ~/relm
git clone https://github.com/spec-kit-relm/product-specs
bash product-specs/workspace/clone-all.sh          # clones every repo as a sibling
code product-specs/workspace/relm.code-workspace   # optional multi-root view
```

## 1. Intake — PO (10 min)

1. `product-specs` → **New issue → Product feature**. Title: *Reserve stock at checkout*. It
   becomes `#2` → Feature ID **RELM-2** (in this org `#1` was taken by an early pull request —
   issues and PRs share one number sequence).
2. Add it to the **Product Delivery** project, `Phase = Spec`, lane cross-repo.

## 2. Product spec — PO + QA spec partner, in `product-specs` (½–1 day)

```text
cd ~/relm/product-specs
/speckit.specify RELM-2 stock-reservation: Shoppers sometimes pay for items that sold out while they
were checking out. When checkout starts, hold the items for a limited time so they cannot be sold to
someone else, show the shopper how long the hold lasts, and release holds that are not used.
```
→ branch `002-stock-reservation`, `specs/002-stock-reservation/spec.md`.

```text
/speckit.clarify                     # PO answers, QA challenges every answer
/speckit.checklist requirements      # QA
/speckit.checklist api               # QA
/speckit.checklist ux                # QA
/speckit.relm.impact                 # tech lead: impact.md + draft contracts.lock.md
```

Open the spec PR, set `**Status**: In Review`. **Gate 1:** PO + QA + tech lead approve; QA ticks
checklist items by hand. Merge → `**Status**: Approved`. Assign feature owners from `impact.md`.

## 3. Contracts — provider feature owners (½ day each)

**inventory-service** (provider of `inventory-api` and `inventory-events`):

```text
cd ~/relm/inventory-service
/speckit.relm.handoff RELM-2         # prints this repo's slice + the specify command
/speckit.specify RELM-2 stock-reservation: <slice from the handoff brief>
/speckit.clarify
/speckit.plan                        # drafts specs/002-stock-reservation/contracts/*.yaml
```

Promote the draft to `contracts/inventory-api.yaml` (v1.0.0 → **1.1.0**, additive) and
`contracts/inventory-events.yaml` (→ **1.1.0**). Open a **contract PR** on branch
`002-stock-reservation-contract`, label `contract-change`, reviewers: orders + web feature owners.

**orders-service** does the same for `orders-api` (1.2.0 → **1.3.0**: optional `reservationId`,
new problem type `reservation-expired`) — after inventory's contract is approved, because it
consumes it.

**Gate 2:** consumers + tech lead approve; `contract-check` green. After merge:

```bash
git tag inventory-api-v1.1.0 && git push origin inventory-api-v1.1.0     # in the provider repo
# in product-specs (tech lead): contracts.lock.md rows → locked, REGISTRY.md versions bumped
# in each consumer: copy the tagged file into contracts/vendor/ + update contracts/vendor/README.md
```

## 4. Repository plans — every feature owner (½ day each)

Consumers now run their own `handoff → specify → clarify → plan` against locked versions. Then,
**in every participating repo**:

```text
/speckit.tasks                       # tests mandatory, scenario IDs, Phase R at the end
/speckit.analyze                     # fix spec/plan/tasks until 0 CRITICAL
/speckit.github.taskstoissues        # issues in this repo; then link as sub-issues of product-specs#2
```

```bash
# link generated issues to the feature (example)
for i in $(gh issue list --label task --search "RELM-2" --json number -q '.[].number'); do
  gh issue edit "$i" --add-project "Product Delivery"
done
```

Open the **plan PR** (`plan.md`, `tasks.md`, `research.md`, `data-model.md`, `quickstart.md`).
**Gate 3:** one peer from the same discipline approves.

## 5. Implementation — feature owners (the bulk of the time)

```text
/speckit.implement Phase 1           # one phase per run (the Relm wrapper enforces it)
# review the diff, run the tests from AGENTS.md, commit, push, open/update the PR
/speckit.implement Phase 2
...
/speckit.converge                    # appends "Phase N: Convergence" if anything is missing
/speckit.implement Phase N           # until converge reports Converged
/speckit.relm.pr                     # assembles the PR description with evidence
```

PR rules: `lane:feature`, `Feature: RELM-2`, analyze + converge evidence (checked by `pr-check`),
one CODEOWNERS approval. Work proceeds in parallel: FE against mocks generated from
`orders-api@1.3.0` and `inventory-api@1.1.0`, BE against its own contract.

## 6. Verification — QA (in parallel from step 4)

```text
cd ~/relm/e2e-tests
/speckit.implement Phase 3           # E2E scenarios RELM-2/US1/AS1… against the lock file versions
```

Run `@smoke` + `@relm-2` in staging. **Gate 4** on `product-specs#2` (QA):

- [ ] inventory-service, orders-service, web-storefront, e2e-tests: `/speckit.converge` Converged
- [ ] provider verification + consumer contract tests green for the locked versions
- [ ] `@smoke` green in staging; all P1 scenarios pass; scenario coverage 100 % for P1
- [ ] dashboards for SC-001 … SC-004 linked; rollback rehearsed in staging

## 7. Rollout — tech lead

Follow `impact.md`: deploy inventory → orders → web (flag `checkout.stock-reservation` off) →
enable 5 % → 50 % → 100 %. Rollback: flag off, then web, orders, inventory. Set the product spec to
`**Status**: Released` and `Phase = Released`.

## 8. After release

Behavior changed during implementation? The PR that changed it links a `product-specs` PR
(Phase R, TR05). New ideas ("extend holds for logged-in users") → new Feature issue `RELM-m`
"Extends RELM-2".

## Time budget for RELM-2 (reference)

| Step | Who | Effort |
|------|-----|--------|
| Product spec + clarify + checklists + impact | PO, QA, tech lead | 1 day |
| Contracts (2 providers) | 2 BE | ½ day each |
| Plans + tasks + analyze (4 repos) | 2 BE, 1 FE, 1 QA | ½ day each |
| Implementation | 2 BE, 1 FE, 1 QA | 3–5 days each, in parallel |
| Verification + rollout | QA, tech lead | 1 day |

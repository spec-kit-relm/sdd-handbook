# 1. Operating model

## Why "AI-native"

At Relm, coding agents write most of the first draft of specs, plans, tasks, code and tests.
Humans own **intent** (what and why), **decisions** (contracts, architecture, trade-offs) and
**acceptance** (review and sign-off). The process is designed around three facts about agents:

1. They are only as good as the context they get. So intent is written down *before* code, in a
   predictable place and shape (specs, contracts, constitutions, `AGENTS.md`).
2. They follow instructions probabilistically. So every rule that matters has an automated check
   or a named human reviewer. Instructions are guidance; gates are enforcement.
3. They lose the plot on long runs and large contexts. So work is sliced: one repository slice per
   spec, one phase per implementation run, one interface per contract PR.

## Teams and roles

| Team | GitHub team | People (example staffing) | Owns |
|------|-------------|---------------------------|------|
| Product & Architecture | `@spec-kit-relm/architecture` | Product owner, tech lead | `product-specs`: product specs, impact maps, contract registry, rollout order |
| Backend | `@spec-kit-relm/backend` | 5 backend engineers | `orders-service`, `inventory-service` and their contracts |
| Frontend | `@spec-kit-relm/frontend` | 3 frontend engineers | `web-storefront` |
| Quality Engineering | `@spec-kit-relm/qa` | 2 QA engineers | acceptance criteria (with PO), checklists, `e2e-tests`, release sign-off |
| SDD Guild (virtual) | `@spec-kit-relm/sdd-guild` | tech lead + one "Spec Kit champion" per team | `speckit-platform`, `sdd-handbook`, constitution amendments |

### Role responsibilities in the SDD loop

| Role | Primary Spec Kit commands | Accountable for |
|------|---------------------------|-----------------|
| **Product owner (PO)** | `/speckit.specify`, `/speckit.clarify` (product-specs) | User stories, priorities, success criteria, approving Gate 1 |
| **Tech lead** | `/speckit.relm.impact`, reviews every plan | Impact map, interface ownership, rollout order, Gate 2 and Gate 4 |
| **Feature owner** (one engineer per repo slice) | `/speckit.relm.handoff`, `specify`, `clarify`, `plan`, `tasks`, `analyze`, `implement`, `converge`, `/speckit.relm.pr` | Their repository's slice end-to-end, including tests |
| **Contract owner** (provider-side feature owner) | `/speckit.plan` (contract draft) | The contract PR and consumer sign-off |
| **Peer reviewer** (same discipline) | reads `plan.md`, `tasks.md`, code | Gate 3 and code review |
| **QA — spec partner** | `/speckit.checklist`, reviews `clarify` answers | Acceptance-criteria quality before any plan exists |
| **QA — automation** | `specify/plan/tasks/implement` in `e2e-tests` | E2E + scenario coverage, Gate 4 evidence |

A practical staffing pattern for a feature that touches two services and the web app: one
backend engineer owns each participating service and two others review; one frontend engineer
owns the web slice; one QA engineer pairs with the PO on the spec while the other builds the E2E
suite in parallel.

## Where things live

Relm uses the **hybrid topology** recommended by Spec Kit's Contract-Driven Development guidance:
a thin hub repository for cross-cutting intent, and a full Spec Kit project in every
implementation repository.

| Artifact | Lives in | Why there |
|----------|----------|-----------|
| Product feature spec (`spec.md`), checklists, `impact.md`, `contracts.lock.md` | `product-specs/specs/NNN-name/` | One place to review cross-cutting intent; no code, so reviews stay about behavior |
| Contract registry (who owns which interface, current version) | `product-specs/contracts/REGISTRY.md` | Discoverability for every team and agent |
| **Authoritative** contracts (OpenAPI / AsyncAPI) | the **provider** repo, `contracts/` | The owner changes them with the code that implements them |
| Pinned consumer copies | the consumer repo, `contracts/vendor/` (read-only) | Consumers build and test against an exact version |
| Repository slice spec, `plan.md`, `tasks.md`, research, data model, quickstart | each repo, `specs/NNN-name/` | Reviewed with the code; context-sized for the agent |
| Constitution (Parts A, B, C) | each repo, `.specify/memory/constitution.md` | Every Spec Kit command reads the local constitution |
| Canonical org/team rules | `speckit-platform/governance/` | Versioned once, synced everywhere |
| Templates & command wrappers | `speckit-platform/presets/`, installed into `.specify/presets/` | Same artifact shape in every repo |
| How-to-work instructions | each repo, `AGENTS.md` (+ `CLAUDE.md`, Copilot file) | Read by every coding agent |
| Process & rationale | `sdd-handbook` | For humans |

### What we deliberately did **not** do

- **One central Spec Kit project that implements into all repos.** Spec Kit's scripts resolve one
  project at a time, a central `tasks.md` becomes a merge hotspot, PRs in service repos would lose
  their local spec, and FE/BE would be forced into one constitution. (Community extensions such as
  `speckit-multirepo` exist; we may pilot one later, but it is not our system of record.)
- **Specs only in service repos.** Cross-cutting intent would have no home and FE/BE specs would
  drift apart.
- **Git submodules.** We use a sibling-checkout workspace (`product-specs/workspace/`) as a
  *view* for agents during clarify/plan, never as the system of record.

## The local workspace

Every engineer clones all repositories as siblings:

```text
~/relm/
├── product-specs/       ← open this folder's workspace file: workspace/relm.code-workspace
├── speckit-platform/
├── orders-service/
├── inventory-service/
├── web-storefront/
└── e2e-tests/
```

`product-specs/workspace/clone-all.sh` sets this up. Agents can **read** siblings (e.g. the
central spec at `../product-specs/specs/001-*/spec.md`) during `/speckit.relm.handoff`,
`/speckit.clarify` and `/speckit.plan`. Spec Kit commands always run inside one repository
(`cd` into it, or set `SPECIFY_INIT_DIR`). Implementation sessions stay inside one repository.

# Relm rules — company, team, repository

Rules come in three levels. Each level has a **human-readable policy** (in this folder, with the
reasoning) and a **machine-readable, condensed MUST layer** in each repository's constitution.

| Level | Policy (why + how to comply) | Constitution layer (what MUST hold) | Canonical source | Changed by |
|-------|------------------------------|-------------------------------------|------------------|-----------|
| **Company** | [company.md](company.md), [ai-usage-policy.md](ai-usage-policy.md) | Part A `RELM:ORG-BASELINE` — ORG-1 … ORG-9 | [`presets/relm-org/…/constitution-template.md`](https://github.com/spec-kit-relm/speckit-platform/blob/main/presets/relm-org/templates/constitution-template.md) | SDD Guild + 1 approver per discipline |
| **Team** | [teams/backend.md](teams/backend.md), [teams/frontend.md](teams/frontend.md), [teams/qa.md](teams/qa.md), [teams/product-architecture.md](teams/product-architecture.md) | Part B `RELM:TEAM-<TEAM>` — BE-*, FE-*, QA-*, PRD-* | [`presets/relm-<team>/…/constitution-template.md`](https://github.com/spec-kit-relm/speckit-platform/blob/main/governance/README.md) | SDD Guild + the team |
| **Repository** | [repository-level.md](repository-level.md) (how to write them) | Part C — `ORD-*`, `INV-*`, `WEB-*`, `E2E-*`, `PS-*` + `AGENTS.md` | the repository itself | the owning team (`lane:governance`) |

```text
             ┌─────────────── more general, harder to change ───────────────┐
Company      │ ORG-1 … ORG-9      every repo                                 │
Team         │ BE-* / FE-* / QA-* / PRD-*   every repo of that team          │
Repository   │ ORD-* / INV-* / WEB-* / E2E-* / PS-*   one repo + AGENTS.md   │
             └─────────────── more specific, easier to change ──────────────┘
Precedence: Company > Team > Repository. Lower levels may only add or tighten.
```

## Where each repository sits

| Repository | Team overlay (Part B) | Repo code (Part C) |
|------------|-----------------------|--------------------|
| `product-specs` | `TEAM-PRODUCT` | `PS` |
| `orders-service` | `TEAM-BACKEND` | `ORD` |
| `inventory-service` | `TEAM-BACKEND` | `INV` |
| `web-storefront` | `TEAM-FRONTEND` | `WEB` |
| `e2e-tests` | `TEAM-QA` | `E2E` |

`speckit-platform`, `sdd-handbook` and `.github` are tooling/documentation repositories; they are
governed by CODEOWNERS and their own CI, not by a Spec Kit constitution.

## Policy vs. constitution: what goes where?

- **Constitution (MUST):** few rules that change a plan or block a task, each with a named check.
- **Policy (this folder):** the reasoning, examples, recommended practices (SHOULD), and how to
  comply. Policy may be longer and change more freely.
- **AGENTS.md:** how to do things in one repository (commands, layout, conventions).

If you find yourself writing SHOULD in a constitution, it belongs in policy. If a policy rule keeps
being violated and it matters, promote it to the constitution and give it a check.

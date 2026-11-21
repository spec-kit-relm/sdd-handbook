# Product & architecture rules (`@spec-kit-relm/architecture`)

Applies to: `product-specs` (PO, tech lead, QA spec partners).
Constitution layer: Part B `RELM:TEAM-PRODUCT` (PRD-1 … PRD-6).

## MUST (mirrored in the constitution)

| ID | Rule | Why | How to comply |
|----|------|-----|---------------|
| PRD-1 | One Feature issue per product feature; its number is the Spec Kit number in every repo | Same directory and branch everywhere, zero mapping tables | `/speckit.specify RELM-n …`; `spec-lint` |
| PRD-2 | Product specs are technology-agnostic | Repos own their designs | Gate 1 review; `/speckit.analyze` |
| PRD-3 | `impact.md`: repos, roles, interface owners, rollout + rollback order | The tech lead's main artifact; drives Gate 2 and release | `/speckit.relm.impact`; `spec-lint --kind product` |
| PRD-4 | `contracts.lock.md` + `REGISTRY.md` updated when contracts are approved | Service plans need one unambiguous version | Gate 2 |
| PRD-5 | Each repo slice ≤ ~5 dev-days, else split | Spec Kit works best on small features; agents degrade on big ones | *Slices* column in `impact.md` |
| PRD-6 | Product specs are living; repo specs flow-forward | Current behavior is always documented | PR checkbox; quarterly audit |

## SHOULD (team practices)
- Write the spec input as a problem statement + users + outcomes; let `/speckit.specify` structure it.
- Three to five user stories per feature; P1 must be a viable slice on its own.
- Name the short name once and reuse it everywhere (`stock-reservation`).
- Record "out of scope" explicitly in Assumptions — agents fill silence with features.

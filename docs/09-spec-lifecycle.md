# 9. Spec lifecycle — keeping specs alive after release

Spec Kit documents three persistence models and leaves the choice to the team. Relm uses two of
them on purpose:

| Where | Model | Meaning |
|-------|-------|---------|
| `product-specs/specs/NNN-*/spec.md` + contracts | **Living spec** | Always describes current shipped behavior. Change `spec.md` first, then regenerate or revise downstream artifacts. |
| Repository slice specs `*/specs/NNN-*/` | **Flow-forward** | Immutable history once the feature is Released. Changes get a new feature directory (new `RELM-m`) that links back. |

Why the split: product specs are what humans and agents read to learn "how does checkout work
today?", so they must be current. Repository specs are cheap, auditable records of *why the code
became what it is*; rewriting them causes merge fights and loses history.

## Status lifecycle (in every `spec.md`)

`Draft` → `In Review` → `Approved` → `Implemented` → `Released` (→ `Superseded by RELM-m`)

`Approved` requires zero `[NEEDS CLARIFICATION]` markers (`spec-lint`).

## Preventing drift

No tool updates the central spec when an implementation diverges: `/speckit.converge` only works
inside one repository. So we make drift a review item:

- **Phase R, TR05** in every tasks file: if shipped behavior or contract versions differ from the
  central spec, open a `product-specs` PR.
- **PR template** checkbox: "If behavior or contract versions differ from the central spec, a
  product-specs PR is linked."
- **Quarterly spec audit** (SDD Guild, 1 hour): sample five Released features, compare product spec
  vs. behavior in staging; file `lane:chore` PRs for differences.

## Post-release changes

| Change | What to do |
|--------|-----------|
| New behavior on top of a shipped feature | New Feature issue `RELM-m` "Extends RELM-n"; product spec of RELM-n is updated when RELM-m is Released |
| Bug (behavior differs from spec) | `lane:bugfix`; no spec change |
| Spec was wrong (behavior is right) | `lane:chore` PR on the product spec, approved by PO + QA |
| Breaking contract change | New feature with expand → migrate → contract steps across releases |

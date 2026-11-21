# Glossary

| Term | Meaning at Relm |
|------|-----------------|
| **Spec Kit** | GitHub's open-source toolkit for spec-driven development (`specify` CLI + agent commands). Pinned at v1.4.3. |
| **Spec Kit project** | A directory containing `.specify/`. At Relm: one per repository. |
| **Product feature** | A unit of user value tracked by a Feature issue in `product-specs`; may span repositories. |
| **Feature ID** | `RELM-<n>`, where `n` is the Feature issue number. Also the Spec Kit feature number in every repo. |
| **Slice** | One repository's part of a product feature (its stories, scenarios and interfaces). |
| **Scenario ID** | `RELM-<n>/US<k>/AS<m>` — user story k, acceptance scenario m. Prefix of every test that verifies it. |
| **Impact map** | `impact.md`: participating repos, roles, interface owners, slice sizes, rollout/rollback order. |
| **Contract** | OpenAPI/AsyncAPI file owned by one provider repo, semantically versioned. |
| **Contract lock** | `contracts.lock.md`: the contract versions one feature is built against. |
| **Vendored contract** | Read-only pinned copy of a provider's contract in a consumer's `contracts/vendor/`. |
| **Constitution** | `.specify/memory/constitution.md`: Part A (org), Part B (team), Part C (repo). |
| **Preset** | Spec Kit package of template/command overrides that compose at runtime (replace/append/prepend/wrap). |
| **Extension** | Spec Kit package that adds commands/hooks (`relm`, `git`, `github`). |
| **Bundle / catalog** | Versioned distribution unit / index of installable presets and extensions. |
| **Phase R** | Mandatory last phase in every `tasks.md`: verification & handoff. |
| **Gate 1–4** | Spec review, contracts locked, plan review, release readiness. |
| **Lane** | How much process a change needs: feature, bugfix, chore, governance. |
| **Converged** | `/speckit.converge` found no remaining gap between code and spec/plan/tasks. |
| **SDD Guild** | Virtual team owning `speckit-platform`, this handbook and constitution amendments. |

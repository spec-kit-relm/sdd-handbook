# 11. Pitfalls & FAQ

## Known pitfalls

| Pitfall | Symptom | Mitigation at Relm |
|---------|---------|--------------------|
| **Review overload** | "I'd rather review code than all these markdown files" | Review `spec.md` and contracts carefully; skim `plan.md`/`tasks.md` against the Constitution Check; trim templates in presets; use the lightweight lanes |
| **Ceremony for small work** | A small bug becomes four user stories | Work lanes; the bugfix lane never runs `/speckit.specify` |
| **False sense of control** | "The constitution says so" but the code doesn't | Rules without checks are not allowed; analyze/converge/CI/CODEOWNERS are the actual gates |
| **Context overload across repos** | Agent mixes up services, hallucinates files | Read siblings only during handoff/clarify/plan; implement inside one repo, one phase per run; pass only relevant contract and story excerpts |
| **Long implement runs** | Agent loses the plan after context compaction | One phase per `/speckit.implement` run (wrapper-enforced); sub-agents per task group |
| **Merge conflicts in shared files** | Constitution, registry, central tasks | Per-repo tasks; constitution edits only on `governance/*` branches; registry edited only by the tech lead |
| **Spec drift** | Product spec no longer matches production | Phase R TR05, PR checkbox, quarterly audit |
| **Moving target** | Spec Kit renames/moves commands between versions | Pinned CLI (`SPECKIT_VERSION`), smoke test in platform CI, read the upgrade guide before bumping |
| **Unvetted community extensions** | Supply-chain risk | Anything we use is reviewed by the SDD Guild and copied into `speckit-platform` |
| **Unreliable blog posts** | Advice for features Spec Kit doesn't have (e.g. "native two-level constitutions") | Trust the official docs and this handbook's "official vs. convention" notes |

## FAQ

**Do we need a separate repository for specs?**
Yes, a thin one: `product-specs` holds cross-cutting intent, impact maps and the contract registry.
It contains no code and no implementation plans. Each implementation repo is still a full Spec Kit
project with its own spec/plan/tasks for its slice.

**Why not put everything (all plans and tasks) in `product-specs`?**
Spec Kit is directory-scoped; its scripts work on one project. Central `tasks.md` files become
merge hotspots, service PRs lose their local context, and FE/BE would share one constitution.

**Can the agent in `orders-service` read `product-specs`?**
Yes — clone repos as siblings. It reads `../product-specs` during handoff/clarify/plan. It never
writes to another repository from a session started in this one.

**What if a feature only touches one repo?**
Repo-local lane: create the Feature issue (for the number), run the flow inside the repo, set
`**Central spec**: n/a — repository-local feature`.

**Which agent do we use?**
Repositories are initialized with the Claude Code integration and contain AGENTS.md/Copilot files,
so any agent that reads AGENTS.md works. Add another integration with
`specify integration install <agent>`; the presets register their wrappers for every integration.

**Who may tick checklist items?**
Humans only (QA-1). If you see `[x]` added by an agent in a diff, revert it.

**`/speckit.analyze` reports a CRITICAL I disagree with.**
Fix the artifact, or — if the rule is wrong for this case — add a Part C exception with an ADR and an
expiry date. Never edit Parts A/B locally (`constitution-check` will fail).

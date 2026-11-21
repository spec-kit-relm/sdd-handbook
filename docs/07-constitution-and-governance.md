# 7. The constitution and governance across many repositories

## What the constitution is for

`.specify/memory/constitution.md` is the set of **non-negotiable principles** every Spec Kit
command reads. `/speckit.plan` fills its *Constitution Check* from it, and `/speckit.analyze`
treats any MUST violation as **CRITICAL** — the finding must be fixed in the spec, plan or tasks,
not by diluting the rule. That makes the constitution the cheapest place to stop a bad plan.

It is **not** a handbook. Every rule is checked in every plan, so a weak rule creates noise
everywhere. Our test for a rule: *can it change a plan or block a task, and does it name its check?*

## Spec Kit has no inheritance — so we built a three-part constitution

Spec Kit projects are directory-scoped and there is no built-in base/inheritance mechanism. Relm
composes one constitution per repository from three parts:

```text
.specify/memory/constitution.md
├── Part A  RELM:ORG-BASELINE v1.0.0   ORG-1 … ORG-9      synced, read-only   (every repo)
├── Part B  RELM:TEAM-<TEAM> v1.0.0    BE-* | FE-* | QA-* | PRD-*  synced, read-only (by team)
└── Part C  Repository amendments      ORD-* | INV-* | WEB-* | E2E-* | PS-*   owned by the repo
```

| Part | Content | Source of truth | Changes via |
|------|---------|-----------------|-------------|
| A | Principles for everyone: spec before code, contract-first, tests prove the spec, security, observability, safe delivery, simplicity, humans own AI output | `speckit-platform/governance/org-baseline.md` | Governance PR in speckit-platform → sync PR |
| B | Team-wide technology-neutral rules (API style, idempotency, UI states, accessibility, scenario IDs…) | `speckit-platform/governance/overlays/<team>.md` | Same |
| C | Stack, layout, domain invariants, owned interfaces, exceptions | the repository | `lane:governance` PR in the repo |

**Precedence:** A > B > C. Lower parts may add or tighten, never relax. If an agent detects a
conflict it must stop and report it (stated in Part A's governance section).

## How it is distributed

1. `speckit-platform` presets carry the composed constitution template:
   `relm-org` provides Part A (priority 20, replace), `relm-<team>` appends Part B and the Part C
   skeleton (priority 10, append). `specify preset resolve constitution-template` shows the chain.
2. `bootstrap/init-repo.sh` seeds a new repo's constitution from the composed template; the
   feature owner runs `/speckit.constitution` to write Part C.
3. Released versions of every block are snapshotted in `governance/history/`.
4. On release, `propagate-governance.yml` opens a sync PR in every consumer repository that
   replaces Parts A/B (`relm_sdd.py constitution-sync`) and refreshes the presets.
5. `sdd-gates / constitution-check` runs on every PR and every push to `main`:
   - Parts A/B must be byte-identical to the released snapshot of the version they declare
     (local edits fail);
   - a repo on a different MAJOR, or more than one MINOR behind, fails;
   - one MINOR behind or a missing PATCH only warns;
   - template placeholders and a missing Part C fail.

## Versioning (semantic, as `/speckit.constitution` does it)

| Bump | When |
|------|------|
| MAJOR | A principle is removed or redefined in a backward-incompatible way |
| MINOR | A principle is added or materially expanded |
| PATCH | Wording, clarification, typo |

Repository constitutions carry their own `**Version**` for Part C, plus the block versions in the
markers. The PR template records all three.

## Writing good Part C amendments

- 3–6 rules, prefixed with the repo code (`ORD-1`, `INV-2`, `WEB-3`, `E2E-1`, `PS-1`).
- Each rule is specific and checkable: *"Domain package has no Spring imports (Check: ArchUnit test
  `DomainPurityTest`)"*, not *"Write clean code."*
- Put stack choices here (Java 21, Go 1.23, Next.js 15) — never in Parts A/B.
- Exceptions to A/B live in *Exceptions* with ADR, owner and expiry date; expired exceptions are
  CRITICAL in `/speckit.analyze` because they are no longer valid justifications.

## Anti-patterns we have seen (and banned)

| Anti-pattern | Why it hurts | Relm rule |
|--------------|--------------|-----------|
| Copy-pasting the org rules into each repo and editing them | Silent divergence, nobody can tell which version applies | Parts A/B are synced and drift-checked |
| A 40-rule constitution | Every plan gets a wall of irrelevant checks; agents start ignoring it | ≤ 10 rules per part, each with a check |
| Constitution edits inside feature branches | Merge conflicts, rule changes slipped in with code | Amend only on `governance/*` branches (`lane:governance`) |
| One constitution for FE and BE | Wrong rules for one side, constant exceptions | Shared Part A, different Part B |
| Rules without checks | False sense of control | No `*Check:*`, no rule |

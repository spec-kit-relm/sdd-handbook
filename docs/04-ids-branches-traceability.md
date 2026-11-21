# 4. Feature IDs, branches and traceability

Spec Kit numbers features **per project** (`001`, `002`, … in each repository), so out of the box
`001` in `orders-service` has nothing to do with `001` in `web-storefront`. Relm aligns them with
one convention that uses only documented Spec Kit options.

## The convention (PRD-1)

| Thing | Format | Example |
|-------|--------|---------|
| Product feature issue | issue `#n` in `spec-kit-relm/product-specs` | `#1 Reserve stock at checkout` |
| Feature ID | `RELM-n` | `RELM-1` |
| Spec Kit feature number | `n`, zero-padded to 3 by Spec Kit | `001` |
| Short name | kebab-case, 2–4 words, **same in every repo** | `stock-reservation` |
| Feature directory (every repo) | `specs/NNN-short-name/` | `specs/001-stock-reservation/` |
| Branch (every repo) | `NNN-short-name` (+ optional `-us1`, `-contract` suffixes for follow-up PRs) | `001-stock-reservation` |
| Scenario ID | `RELM-n/USk/ASm` | `RELM-1/US2/AS1` |
| Task issues | created by `/speckit.github.taskstoissues` in each repo, sub-issues of `#n` | `orders-service#14` |

How it is produced: the Relm wrapper around `/speckit.specify` passes `--number n
--short-name <short-name>` to Spec Kit's feature-creation script (and the `git` extension's branch
script). Both are documented options; verify with:

```bash
.specify/scripts/bash/create-new-feature.sh --json --dry-run --number 1 --short-name stock-reservation "x"
# {"BRANCH_NAME":"001-stock-reservation","SPEC_FILE":".../specs/001-stock-reservation/spec.md","FEATURE_NUM":"001",...}
```

If the number already exists locally (rare — only if someone created a feature without the
convention), Spec Kit auto-corrects it; fix the directory name by hand before the first PR.

**Repo-local features** still get a Feature issue in `product-specs` — it costs ten seconds and keeps
numbering global and conflict-free.

## The traceability chain

```text
product-specs#1  (Feature issue, RELM-1)
 ├─ product-specs/specs/001-stock-reservation/spec.md        ← living product spec
 │    ├─ impact.md            which repos, roles, rollout order
 │    └─ contracts.lock.md    inventory-api@1.1.0, orders-api@1.3.0
 ├─ inventory-service/specs/001-stock-reservation/spec.md    ← "**Feature ID**: RELM-1", "**Central spec**: …"
 │    └─ tasks.md → inventory-service issues (sub-issues of #1) → PRs "Feature: RELM-1"
 ├─ orders-service/specs/001-stock-reservation/…
 ├─ web-storefront/specs/001-stock-reservation/…
 └─ e2e-tests/specs/001-stock-reservation/… → tests titled "RELM-1/US1/AS1 …"
```

Every link is checked by a machine somewhere:

| Link | Check |
|------|-------|
| Directory number ↔ `**Feature ID**` line in `spec.md` | `sdd-gates / spec-lint` |
| PR ↔ feature | `sdd-gates / pr-check` (`Feature: RELM-n` + local `specs/NNN-*`) |
| Test ↔ scenario | `e2e-tests` `scenario-coverage` job, test-title convention (QA-2) |
| Plan ↔ contract version | `/speckit.analyze` + Gate 3 review against `contracts.lock.md` |

## Parallel worktrees

`.specify/feature.json` (the active feature pointer) is machine-local and git-ignored. If you work
on two features at once in separate worktrees, set the feature explicitly per shell:

```bash
export SPECIFY_FEATURE_DIRECTORY=specs/001-stock-reservation
```

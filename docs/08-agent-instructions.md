# 8. Making agents follow Relm practices

Think of enforcement as three layers. Only the last one actually enforces anything.

```text
Layer 1  Instructions the agent reads   constitution · AGENTS.md · CLAUDE.md · copilot-instructions    → guidance
Layer 2  Templates that shape output     Relm presets: spec/plan/tasks sections, command wrappers       → structure
Layer 3  Gates that verify               analyze · converge · CI (sdd-gates, contract-check, tests) · CODEOWNERS → enforcement
```

## Layer 1 — what goes where

| File | Answers | Read by | Owner |
|------|---------|---------|-------|
| `.specify/memory/constitution.md` | *What MUST be true?* Principles that change plans or block tasks | every Spec Kit command, `/speckit.analyze` | A/B: SDD Guild · C: repo |
| `AGENTS.md` | *How do we work here?* Commands, layout, conventions, do/don't, gotchas | Copilot, Codex, Cursor, Gemini, Claude (via import) — closest file wins | repo team |
| `CLAUDE.md` | `@AGENTS.md` + Claude-only notes | Claude Code (concatenated root → subdir) | repo team |
| `.github/copilot-instructions.md` | Pointer to AGENTS.md + PR-review focus | Copilot chat, code review, cloud agent | repo team |
| `.github/instructions/*.instructions.md` | Path-specific rules (e.g. `contracts/**`) | Copilot | repo team |
| Org custom instructions (GitHub settings) | Short org pointer to the handbook | Copilot on github.com only (not IDEs) | SDD Guild |

**Never duplicate** the constitution in `AGENTS.md` — reference rule IDs instead ("see ORD-1").
Duplicates drift, and agents resolve contradictions unpredictably.

### A good AGENTS.md (required sections are checked by `agent-files-check`)

- `## Commands` — exact build/test/lint/run commands. Agents run what you write here.
- `## Layout` — the directories that matter and what goes in each.
- `## Spec Kit in this repository` — kind, numbering convention, handoff/implement/converge flow.
- `## Conventions` — the 5–10 things reviewers keep commenting on.
- `## Do not` — paths and actions that are off-limits (vendored contracts, generated code, Parts A/B).
- `## Gotchas` — what regularly trips people up.

Keep it under ~150 lines. In nested folders, write a complete nested `AGENTS.md` for that
subtree rather than a diff, because tools differ in how they merge them.

## Layer 2 — templates and command wrappers (the Relm presets)

| Preset | Adds | Effect on the agent |
|--------|------|---------------------|
| `relm-org` | Traceability section in every spec; delivery sections (contracts, observability, flags, security) in every plan; Phase R in every tasks file; wrappers for `specify` (numbering + central spec), `plan` (contract-first), `tasks` (mandatory tests), `implement` (one phase, read-only paths) | The agent literally cannot produce a Relm artifact without these sections |
| `relm-backend` | idempotency, migrations, events, budgets tables in plans | BE plans always answer BE-2…BE-5 |
| `relm-frontend` | UI states, accessibility, performance, API clients, tracking plan | FE plans always answer FE-1…FE-6 |
| `relm-qa` | scenario-to-test strategy, pinned environments, sign-off evidence; checklist conventions | QA plans always map every scenario |
| `relm-product` | impact map + contract lock templates | product features always have an impact map |

Presets compose at runtime and are versioned centrally — a template improvement reaches every
repository with one release instead of eight hand edits.

## Layer 3 — the gates

| Gate | When | Fails on |
|------|------|----------|
| `/speckit.analyze` | after `tasks`, before `implement` | any CRITICAL (constitution MUST violations, uncovered requirements, contradictions) |
| `/speckit.converge` | after the last `implement` phase | remaining spec/plan/tasks gaps (appends a Convergence phase) |
| `sdd-gates / agent-files-check` | every PR | missing AGENTS.md sections, CLAUDE.md not importing AGENTS.md, no constitution |
| `sdd-gates / constitution-check` | every PR + main | edited Parts A/B, too-old governance versions, placeholders |
| `sdd-gates / spec-lint` | every PR + main | Feature ID ≠ directory, stories without Given/When/Then, clarification markers in approved specs, missing product files |
| `sdd-gates / pr-check` | every PR | no feature/lane, no local spec for the feature, no analyze/converge evidence |
| `contracts / contract-check` | PRs touching contracts | lint errors, unbumped versions, unannounced breaking changes, vendored edits without provenance |
| Repo CI | every PR | tests, coverage, linters, ArchUnit/golangci/eslint, axe, Lighthouse |
| CODEOWNERS + branch protection | every PR | missing human approval from the owning team |

## When the agent still gets it wrong

1. Fix the output, then ask: *which layer failed?*
2. If the rule was not written down → add it to `AGENTS.md` (how) or propose a Part C rule (must).
3. If it was written but ignored → make it structural (a template section) or checkable (a lint
   rule, a test, an ArchUnit rule). Rules that matter end up in Layer 3.
4. Share the case in the SDD Guild channel; recurring cases become preset or governance changes.

# Company-wide SDD rules

Applies to every person, agent and repository at Relm. The MUST rules below are mirrored, in
condensed form, in Part A of every constitution (`RELM:ORG-BASELINE`). This page explains them.

## C-1 Spec before code → ORG-1
Behavior, contract or data changes start from a spec. **Why:** agents produce the code you
describe; an undescribed change is an unreviewed change. **Comply:** PR carries `Feature: RELM-n`
or a lane label. **Recommended:** link the exact user story in the PR's "What and why".

## C-2 Specs are about WHAT and WHY → ORG-2
No technology in `spec.md`; every story has Given/When/Then; measurable success criteria;
clarifications resolved before approval. **Why:** technology in the spec freezes decisions before
the people who own them have planned; vague criteria can't be tested. **Comply:** `spec-lint`,
Gate 1. **Recommended:** write success criteria a non-engineer can verify ("95 % of shoppers
see their reservation within 1 s").

## C-3 Contracts are the only integration surface → ORG-3
One owner per interface, semantic versions, pinned consumers, read-only vendored copies,
expand → migrate → contract for breaking changes. **Why:** contracts let FE and BE work in parallel
and let agents in different repos agree without sharing context. **Comply:** `contract-check`,
Gate 2.

## C-4 Tests prove the spec → ORG-4
Scenario IDs in test titles, provider + consumer contract tests with negative cases, tests in the
same PR. **Why:** "the agent says it works" is not evidence. **Comply:** CI, `/speckit.converge`,
QA review.

## C-5 Secure and private by default → ORG-5
No secrets anywhere (code, specs, prompts, logs, fixtures); authN/Z on every endpoint; personal data
classified and never logged. **Comply:** secret scanning, CodeQL, dependency review.
See also the [AI usage policy](ai-usage-policy.md) for what may be sent to an agent.

## C-6 Observable by design → ORG-6
Structured logs with trace IDs, RED metrics, a measurement for every success criterion.
**Why:** a success criterion nobody can measure is a wish. **Comply:** plan's Observability table,
dashboards linked at Gate 4.

## C-7 Safe, reversible delivery → ORG-7
Feature flags default off, rollout in `impact.md` order, written rollback. **Comply:** plan review,
Gate 4.

## C-8 Simplicity → ORG-8
No new service/datastore/broker/major dependency without an ADR
([template](../templates/adr.md)). **Comply:** `/speckit.analyze`, architecture review.

## C-9 Humans own AI output → ORG-9
A named human owns every PR and reviews AI-generated code like their own. Analyze 0 CRITICAL
before implementing, one phase per implement run, converge before merge. **Comply:** `pr-check`,
CODEOWNERS.

## Company practices (SHOULD — not in the constitution)

- **Pin everything:** Spec Kit CLI version, presets, extensions, contract versions, base images.
- **Small PRs:** one phase or one user story per PR; ≤ ~400 changed lines excluding generated code.
- **Commit messages:** Conventional Commits (`feat(orders): …`), set via the git extension's
  `commit_style: conventional`.
- **Branch protection on `main`** in every repo: required checks `sdd / sdd-gates`, repo CI,
  `contracts / contract-check` where contracts exist; one CODEOWNERS approval; linear history.
- **Default to the lightest lane** that is honest about the change ([Work lanes](../docs/03-work-lanes.md)).
- **Write it down once.** If you explain the same thing to an agent twice, it belongs in
  `AGENTS.md`; if you explain it to a human twice, it belongs in this handbook.

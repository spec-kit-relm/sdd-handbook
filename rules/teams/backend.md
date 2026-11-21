# Backend team rules (`@spec-kit-relm/backend`)

Applies to: `orders-service`, `inventory-service` and every future backend service.
Constitution layer: Part B `RELM:TEAM-BACKEND` (BE-1 … BE-6) in each service's constitution.
Stack-specific rules (Java vs. Go) live in each repository's Part C.

## MUST (mirrored in the constitution)

| ID | Rule | Why | How to comply |
|----|------|-----|---------------|
| BE-1 | Consistent HTTP APIs: plural resources, RFC 9457 problem+json, cursor pagination, `X-Request-Id` | Consumers (and their agents) can predict any Relm API | Redocly `relm-api` ruleset in `contract-check`; provider tests validate responses against OpenAPI |
| BE-2 | Idempotent writes (`Idempotency-Key`, event de-dup), retries only on idempotent ops | Retries are everywhere: browsers, gateways, brokers | Plan table *idempotency & retries*; contract tests replay every write |
| BE-3 | Backward-compatible migrations (expand → migrate → contract) | Rolling deploys run old and new code together | Plan table *data changes*; migration lint |
| BE-4 | Reliable events: outbox, envelope (`eventId`, `occurredAt`, `schemaVersion`, `traceId`), AsyncAPI | Lost or duplicated events are the hardest bugs to find | AsyncAPI lint; consumer tests with duplicates and out-of-order fixtures |
| BE-5 | Explicit timeouts, p99/error budgets, graceful degradation | Unbounded waits cascade into outages | Plan table *performance & resilience*; load test for P1 |
| BE-6 | Test pyramid: domain unit ≥ 80 %, integration with real deps, provider verification | Fast feedback for agents, real evidence for reviewers | CI coverage gate; `/speckit.converge` |

## SHOULD (team practices)

- Hexagonal / ports-and-adapters layout: domain has no framework imports.
- Name problem types as URIs under `https://errors.relm.example/<service>/<type>`.
- Prefer one new endpoint over a flag parameter that changes behavior.
- In plans, describe *one* design and why alternatives were rejected (in `research.md`), not three.
- Ask the agent to write the migration and the rollback note in the same task.

## Review focus for backend PRs
1. Does every write handle a duplicate request?
2. Are new endpoints in the contract *and* covered by provider verification?
3. Is there a timeout on every outbound call?
4. Could this migration break the previous release?

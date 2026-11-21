# Playbook: changing a contract

## Additive change (MINOR) — e.g. `inventory-api` 1.0.0 → 1.1.0

1. In the provider repo, during `/speckit.plan`, the agent drafts the change under
   `specs/NNN-*/contracts/`. Review it: shapes **and** semantics (idempotency, retries, problem
   types, ordering, timeouts).
2. Copy the draft into `contracts/<name>.yaml`; bump `info.version`; add a `CHANGELOG` entry in
   `contracts/README.md`.
3. Branch `NNN-short-name-contract`, PR with labels `lane:feature`, `contract-change`; request
   review from every consumer listed in `product-specs/contracts/REGISTRY.md`.
4. `contracts / contract-check` must pass: lint (`relm-api`), version bumped, `oasdiff` reports no
   breaking change.
5. After merge: `git tag <name>-v<version> && git push origin <name>-v<version>`.
6. Tech lead updates `REGISTRY.md` and the feature's `contracts.lock.md` (`locked`).
7. Each consumer vendors the tagged copy:
   ```bash
   gh api repos/spec-kit-relm/inventory-service/contents/contracts/inventory-api.yaml?ref=inventory-api-v1.1.0 \
     -H "Accept: application/vnd.github.raw" > contracts/vendor/inventory-api.yaml
   # update contracts/vendor/README.md: version, tag, commit SHA, date
   ```
   and regenerates clients/mocks (see the consumer's AGENTS.md).

## Breaking change (MAJOR) — expand → migrate → contract

| Release | Provider | Consumers |
|---------|----------|-----------|
| 1 (MINOR) | Add the new field/endpoint alongside the old one; mark the old one `deprecated: true` | — |
| 2 | — | Move to the new field/endpoint; pin the new MINOR |
| 3 (MAJOR) | Remove the old field/endpoint once `REGISTRY.md` shows no consumer on the old version | — |

`oasdiff` fails a breaking change unless the MAJOR changed — that is the reminder to follow this
sequence. Each step is its own feature (or a phase of one) with its own Gate 2.

## Event contracts (AsyncAPI)

Same rules. Additionally: new event types are additive; changing the payload of an existing type
needs a new `schemaVersion` and consumers that accept both during migration (BE-4).

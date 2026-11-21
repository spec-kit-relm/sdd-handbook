# 5. Contract-first integration

Contracts are the backbone of multi-repo SDD: they are the only thing two repositories share.
Spec Kit's official multi-repo answer is the Contract-Driven Development guide; Relm adopts it
verbatim and adds versions, a registry and automation.

## Principles (ORG-3, BE-1, BE-2, BE-4, FE-3)

1. **One owner.** Each interface has exactly one provider repository that owns its contract file.
2. **Contracts are versioned semantically.** `info.version` in the file; Git tag
   `<contract>-v<version>` in the provider repo. Additive = MINOR, breaking = MAJOR.
3. **Consumers pin.** Consumers vendor an exact released version into `contracts/vendor/` with
   provenance (source repo, version, commit) in `contracts/vendor/README.md`. Vendored copies are
   read-only — changing one without updating the provenance file fails `contract-check`.
4. **Semantics, not just shapes.** The contract (or its description fields) states idempotency,
   retry safety, error problem types, ordering and timeouts. *"The shape of an order request does not
   explain whether retrying it can create a second order."*
5. **Contract PRs are reviewed by consumers.** CODEOWNERS on `contracts/` include the architecture
   team; the PR template asks the author to request every consumer listed in the registry.
6. **Breaking changes go expand → migrate → contract.** Add the new field/endpoint (MINOR), move
   every consumer, then remove the old one (MAJOR) in a later release.
7. **Provider verification + consumer tests.** Consumer tests against mocks are necessary but never
   sufficient; the provider verifies its implementation against its own contract in CI.

## Where contracts live

```text
inventory-service/contracts/
├── inventory-api.yaml            AUTHORITATIVE (OpenAPI 3.1) — owned here
├── inventory-events.yaml         AUTHORITATIVE (AsyncAPI 3)  — owned here
└── README.md                     how to release a version
orders-service/contracts/
├── orders-api.yaml               AUTHORITATIVE — owned here
└── vendor/
    ├── inventory-api.yaml        read-only copy of inventory-api@1.1.0
    ├── inventory-events.yaml     read-only copy of inventory-events@1.1.0
    └── README.md                 provenance: repo, version, tag, commit, sync procedure
product-specs/contracts/REGISTRY.md   who owns what, current version, consumers
product-specs/specs/NNN-*/contracts.lock.md   versions locked for one feature
```

## Lifecycle of a contract change inside a feature

1. During `/speckit.plan` in the provider repo, the agent drafts the change in
   `specs/NNN-*/contracts/<name>.yaml` (Spec Kit produces feature-scoped contract artifacts when
   external interfaces are involved).
2. The provider promotes the draft into `contracts/<name>.yaml`, bumps `info.version`, and opens a
   **contract PR** (`contract-change` label) — contract only, no implementation.
3. `contract-check` runs: Redocly lint with the `relm-api` ruleset, AsyncAPI validation, version-bump
   check, `oasdiff` breaking-change detection (fails unless MAJOR changed).
4. Consumers review semantics against their slice spec; the tech lead approves (Gate 2).
5. After merge: tag `<name>-v<version>`; update `REGISTRY.md` and the feature's
   `contracts.lock.md`; consumers vendor the copy (`lane:feature`, small PR).
6. Implementation in all repos proceeds against the locked version. Any later change re-opens
   Gate 2 for that interface.

See [Playbook: contract change](../playbooks/contract-change.md) for the commands.

# Repository-level rules

Every Spec Kit repository adds its own rules on top of the company and team layers, in two places:

| Place | Kind of rule | Example |
|-------|--------------|---------|
| Constitution **Part C** | MUST rules specific to this repo, each with a check | `ORD-1` hexagonal layout enforced by ArchUnit; `INV-2` stock never negative (DB constraint + property test) |
| `AGENTS.md` | How to work here: commands, layout, conventions, don'ts, gotchas | "Run `./mvnw verify` before marking a task done"; "never edit `contracts/vendor/`" |

## Current repository rule sets

| Repository | Part C rules | AGENTS.md |
|------------|--------------|-----------|
| `product-specs` | `PS-1` … `PS-4` (no code, required files, registry ownership, workspace) | [AGENTS.md](https://github.com/spec-kit-relm/product-specs/blob/main/AGENTS.md) |
| `orders-service` | `ORD-1` … `ORD-5` (Java 21/Spring Boot, hexagonal, Flyway, money, owned Orders API) | [AGENTS.md](https://github.com/spec-kit-relm/orders-service/blob/main/AGENTS.md) |
| `inventory-service` | `INV-1` … `INV-5` (Go, layout, stock invariant, reservation expiry, owned Inventory API + events) | [AGENTS.md](https://github.com/spec-kit-relm/inventory-service/blob/main/AGENTS.md) |
| `web-storefront` | `WEB-1` … `WEB-5` (Next.js App Router, generated clients, test stack, checkout privacy) | [AGENTS.md](https://github.com/spec-kit-relm/web-storefront/blob/main/AGENTS.md) |
| `e2e-tests` | `E2E-1` … `E2E-5` (Playwright, page objects, tags, lock file, no UI setup) | [AGENTS.md](https://github.com/spec-kit-relm/e2e-tests/blob/main/AGENTS.md) |

## How to write a Part C rule

```markdown
### ORD-2 Schema changes through Flyway only
All schema changes are Flyway migrations in `src/main/resources/db/migration`, named
`V<yyyymmddHHmm>__<what>.sql`, forward-only and compatible with the previous release (tightens BE-3).
*Check: `migration-lint` CI job, Testcontainers migration test.*
```

Checklist for a good rule:

- [ ] Prefixed with the repo code and numbered.
- [ ] Specific to this repo (stack, layout, domain invariant, owned interface). If it applies to
      every backend service, propose it for Part B instead.
- [ ] Adds or tightens a Part A/B rule — say which one in parentheses when it does.
- [ ] Ends with `*Check: …*` naming an automated check or a review step.
- [ ] 3–6 rules in total. More than that means some belong in `AGENTS.md`.

## How to change Part C

1. Branch `governance/<topic>` (never inside a feature branch).
2. Run `/speckit.constitution <what to change>` — it updates the file, bumps the repo
   constitution version, and writes a Sync Impact Report.
3. Check Parts A/B are untouched: `python ../speckit-platform/scripts/relm_sdd.py constitution-check`.
4. PR with label `lane:governance`; CODEOWNERS of `.specify/memory/` approve.

## Exceptions to company or team rules

Allowed only in Part C → *Exceptions*:

```markdown
| Rule | Exception | ADR | Owner | Expires |
|------|-----------|-----|-------|---------|
| BE-4 | `stock.adjusted` events published without outbox until the broker migration | ADR-0007 | @inventory-owner | 2027-03-31 |
```

Expired exceptions are treated as violations by `/speckit.analyze` and reviewers.

# Playbook: set up (or rebuild) the organization

One-time administration for an org owner. Everything after this is done through pull requests.
Steps are in dependency order; each says how to verify it.

## 1. Repositories

Create the org and these repositories (public, or private with the Actions access setting in
step 5): `.github`, `sdd-handbook`, `speckit-platform`, `product-specs`, `inventory-service`,
`orders-service`, `web-storefront`, `e2e-tests`.

The repository **must** be named exactly `.github`: GitHub uses it for the org profile
(`profile/README.md`), default issue/PR templates and workflow templates.

## 2. Platform tags

Every repository calls the reusable gates at `speckit-platform@v1`, so the tag must exist before
any gate can run:

```bash
git clone https://github.com/spec-kit-relm/speckit-platform && cd speckit-platform
V="v$(cat VERSION)"                       # e.g. v1.1.1
git tag -a "$V" -m "speckit-platform $V" && git tag -f v1
git push origin "$V" && git push -f origin v1
```

*Verify:* `sdd / sdd-gates` is green on the next commit of any repository.

## 3. Teams, labels, project

```bash
bash speckit-platform/bootstrap/org-setup.sh
```

Creates the teams used by CODEOWNERS (`architecture`, `backend`, `frontend`, `qa`, `sdd-guild`)
with repository permissions, the lane labels in every repository, and the **Product Delivery**
project (fields *Feature ID*, *Target repo*, *Phase*, *Contract version*). Safe to re-run.

## 4. Branch protection

Only after step 2 (otherwise `sdd / sdd-gates` can never pass and every PR is stuck):

```bash
bash speckit-platform/bootstrap/protect-branches.sh
```

Creates the ruleset `relm-protect-main` in every repository: changes only through PRs, one
CODEOWNER approval, required checks, no force-push or deletion. Org admins may bypass — remove the
bypass in *Settings → Rules → Rulesets* once the organization has more than one maintainer.

| Repository | Required checks |
|------------|-----------------|
| product-specs | `sdd / sdd-gates` |
| inventory-service, orders-service, web-storefront | `sdd / sdd-gates`, `contracts / contract-check`, `test` |
| e2e-tests | `sdd / sdd-gates`, `contracts / contract-check`, `static` |
| speckit-platform | `generated-files` |
| sdd-handbook, .github | PR + review only |

## 5. Actions settings

- Every caller workflow (`.github/workflows/sdd.yml`) declares
  `permissions: { contents: read, pull-requests: read }`. The org's default token has no
  pull-requests scope and a called workflow cannot exceed its caller — without the block the run
  fails at startup with no jobs.
- Private repositories only: in `speckit-platform` → *Settings → Actions → General → Access*,
  allow access from repositories in the organization, and give `e2e-tests` a token that can read
  `product-specs` (its CI checks the contract locks there).

## 6. Governance sync token (optional, recommended)

Lets `propagate-governance.yml` open sync PRs automatically when a platform version is tagged.

1. github.com → *Settings → Developer settings → Fine-grained tokens* → new token.
   Resource owner `spec-kit-relm`; repositories: `product-specs`, `inventory-service`,
   `orders-service`, `web-storefront`, `e2e-tests`; permissions **Contents: read and write**,
   **Pull requests: read and write**. Set an expiry and a reminder to rotate it.
2. `gh secret set RELM_GOVERNANCE_BOT_TOKEN -R spec-kit-relm/speckit-platform` (paste when prompted).
3. *Verify:* `gh workflow run propagate-governance.yml -R spec-kit-relm/speckit-platform` finishes
   green; with everything in sync it opens no PRs.

Without the token the workflow skips with a notice, and each repository syncs by hand:
`python ../speckit-platform/scripts/relm_sdd.py constitution-sync --write --platform ../speckit-platform`.

## 7. Feature numbering

Issues and pull requests share one number sequence per repository. In `product-specs`, open the
Feature issue **before** any pull request that belongs to the feature, so the issue gets the
number you expect (`RELM-<n>`).

## Rebuilding from scratch — checklist

- [ ] Repositories exist (step 1) and `main` is pushed in each
- [ ] `v1` and `v1.x.y` tags on speckit-platform; contract tags in provider repositories
- [ ] `org-setup.sh` ran without errors
- [ ] `sdd / sdd-gates` green on `main` everywhere
- [ ] `protect-branches.sh` ran
- [ ] (optional) `RELM_GOVERNANCE_BOT_TOKEN` set and verified

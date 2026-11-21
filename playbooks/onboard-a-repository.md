# Playbook: onboard a new repository (e.g. a new `payments-service`)

1. Create the repo in `spec-kit-relm` (empty). Clone it next to the others in `~/relm/`.
2. Bootstrap Spec Kit + Relm presets:
   ```bash
   bash ../speckit-platform/bootstrap/init-repo.sh backend   # product | backend | frontend | qa
   ```
3. Write Part C: `/speckit.constitution This is payments-service, Java 21 … Add 3–6 PAY-* rules.`
4. Copy `../speckit-platform/templates/repo/` files (`AGENTS.md`, `CLAUDE.md`, `.github/…`,
   `CODEOWNERS`) and adapt them. Fill `AGENTS.md → Commands` with real commands.
5. CI: `.github/workflows/sdd.yml` (set `repo-kind`) plus the repo's own build/test workflow.
6. Branch protection on `main`: required checks `sdd / sdd-gates`, `contracts / contract-check`
   (if it owns or vendors contracts), repo CI; 1 CODEOWNERS approval.
7. Register it:
   - `speckit-platform/consumers.json` (governance sync PRs),
   - `product-specs/workspace/repositories.yml` and `relm.code-workspace`,
   - `product-specs/contracts/REGISTRY.md` for every interface it owns or consumes.
8. Verify: `relm_sdd.py agent-files-check`, `constitution-check`, `spec-lint` all pass.

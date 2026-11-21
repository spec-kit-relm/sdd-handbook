# Playbook: onboard an engineer (day 1)

## Everyone
1. Read the handbook in the [reading order](../README.md#start-here-reading-order).
2. Install: `git`, `gh`, `python3`, `uv`, your agent (Claude Code by default), then the pinned
   Spec Kit CLI:
   ```bash
   uv tool install specify-cli --from git+https://github.com/github/spec-kit.git@v1.4.3
   ```
3. Clone the workspace: `git clone https://github.com/spec-kit-relm/product-specs ~/relm/product-specs && bash ~/relm/product-specs/workspace/clone-all.sh`
4. Walk through `RELM-2` in every repository you will work in: read the product spec, `impact.md`,
   then your repository's `specs/002-stock-reservation/` (spec → plan → tasks).
5. In one repo, run the gates locally:
   ```bash
   python ../speckit-platform/scripts/relm_sdd.py agent-files-check
   python ../speckit-platform/scripts/relm_sdd.py constitution-check --platform ../speckit-platform
   python ../speckit-platform/scripts/relm_sdd.py spec-lint
   ```

## By role — first task
| Role | First task |
|------|-----------|
| Backend | In `inventory-service`, run `/speckit.analyze`, then `/speckit.implement Phase 1` for RELM-2 on a scratch branch; compare the result with `tasks.md` |
| Frontend | In `web-storefront`, read the *UI states* table in the RELM-2 plan and run `/speckit.implement Phase 1` on a scratch branch |
| QA | In `product-specs`, review `checklists/qa.md` for RELM-2 and propose two missing checklist items in a PR |
| PO / tech lead | Draft a new feature in `product-specs` with `/speckit.specify` + `/speckit.clarify`; do not merge |

# Playbook: amend the constitution

## Part C (repository amendment) — owning team

1. `git switch -c governance/<topic>` (never inside a feature branch).
2. `/speckit.constitution Add rule ORD-6: <rule>. Check: <check>.`
   It bumps the repository constitution version and writes a Sync Impact Report.
3. `python ../speckit-platform/scripts/relm_sdd.py constitution-check --platform ../speckit-platform`
4. PR with `lane:governance`; CODEOWNERS of `.specify/memory/` approve.

## Part A or B (organization or team rule) — anyone proposes, SDD Guild decides

1. Open an issue in `speckit-platform` with the problem the rule solves and evidence (PRs,
   incidents, analyze findings). Rules without a check are rejected.
2. Branch `governance/<topic>` in `speckit-platform`; edit the rule block in
   `presets/relm-org/templates/constitution-template.md` (Part A) or
   `presets/relm-<team>/templates/constitution-template.md` (Part B), and bump the version in its
   `BEGIN RELM:` marker:
   - MAJOR — a principle removed or redefined
   - MINOR — a principle added or materially expanded
   - PATCH — wording only
3. Bump `version:` in that preset's `preset.yml`, plus `VERSION` and `CHANGELOG.md`;
   run `python scripts/relm_sdd.py governance-lint`.
4. PR — approvals: SDD Guild + one per discipline (org baseline) or the team (overlay).
5. Merge, then tag and move the floating tag:
   `git tag -a v<VERSION> -m … && git tag -f v1 && git push origin v<VERSION> && git push -f origin v1`.
   Pushing the version tag opens a sync PR in every repository listed in `consumers.json`.
6. Each repository merges its sync PR within one sprint (reviewing it against its Part C). After
   that, `constitution-check` turns from warning to failure.

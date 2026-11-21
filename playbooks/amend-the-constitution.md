# Playbook: amend the constitution

## Part C (repository amendment) — owning team

1. `git switch -c governance/<topic>` (never inside a feature branch).
2. `/speckit.constitution Add rule ORD-6: <rule>. Check: <check>.`
   It bumps the repository constitution version and writes a Sync Impact Report.
3. `python ../speckit-platform/scripts/relm_sdd.py constitution-check --governance ../speckit-platform/governance`
4. PR with `lane:governance`; CODEOWNERS of `.specify/memory/` approve.

## Part A or B (organization or team rule) — anyone proposes, SDD Guild decides

1. Open an issue in `speckit-platform` with the problem the rule solves and evidence (PRs,
   incidents, analyze findings). Rules without a check are rejected.
2. Branch `governance/<topic>` in `speckit-platform`; edit `governance/org-baseline.md` or
   `governance/overlays/<team>.md`; bump the version in its `BEGIN RELM:` marker:
   - MAJOR — a principle removed or redefined
   - MINOR — a principle added or materially expanded
   - PATCH — wording only
3. `python scripts/relm_sdd.py build` (regenerates preset templates + adds the history snapshot).
4. Bump the affected preset versions, `VERSION`, `CHANGELOG.md`; run
   `python scripts/relm_sdd.py catalog --tag v<VERSION>`.
5. PR — approvals: SDD Guild + one per discipline (org baseline) or the team (overlay).
6. Merge, then tag `v<VERSION>`. Automation: release assets, floating `v1` tag, and a sync PR in
   every repository listed in `governance/consumers.json`.
7. Each repository merges its sync PR within one sprint (reviewing it against its Part C). After
   that, `constitution-check` turns from warning to failure.

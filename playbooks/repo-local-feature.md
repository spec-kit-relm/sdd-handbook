# Playbook: a repository-local feature

Use when new behavior stays inside one repository and no contract changes
([Work lanes](../docs/03-work-lanes.md)). Example: *show "low stock" badges using data the web app
already receives*.

1. **Get a number.** Open a *Product feature* issue in `product-specs` (title + one paragraph, mark
   "repository-local"). Issue `#n` → `RELM-n`. Takes a minute; keeps numbering global.
2. **Spec + plan in your repo:**
   ```text
   /speckit.specify RELM-n low-stock-badge: <intent>
   /speckit.clarify
   /speckit.plan
   /speckit.tasks
   /speckit.analyze          # until 0 CRITICAL
   ```
   In the Traceability section set `**Central spec**: n/a — repository-local feature`.
3. **One PR for spec + plan + tasks** (Gate 3 combined with spec review): reviewers = a peer from
   your discipline + QA for the acceptance scenarios.
4. **Implement** one phase per run, `/speckit.converge`, `/speckit.relm.pr`.
5. **Release** behind a flag if user-visible (ORG-7). Close the Feature issue with a link to the PR.

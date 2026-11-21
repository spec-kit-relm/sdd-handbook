# 10. Measuring whether SDD works for us

We adopted Spec Kit as a hypothesis, not a religion. The SDD Guild reviews these metrics monthly
and after every 3–5 features; the process changes if the numbers say so.

| Metric | Definition | Source | Target (first quarter) |
|--------|------------|--------|------------------------|
| Lead time | Feature issue opened → Released | Product Delivery project | Trend down after the pilot |
| Spec review time | Gate 1 PR opened → merged | GitHub | ≤ 2 working days |
| Plan review time | Gate 3 PR opened → merged | GitHub | ≤ 1 working day |
| Analyze CRITICALs per feature | count at first `/speckit.analyze` run | PR evidence | trending down (= better specs/templates) |
| Converge loops | implement→converge iterations until Converged | tasks.md Convergence phases | ≤ 2 |
| Escaped defects | bugs on Released features within 30 days, per feature | `lane:bugfix` issues linked to `RELM-n` | trending down |
| Scenario coverage | P1 scenarios with ≥ 1 automated test | `scenario-coverage` job | 100 % at Gate 4 |
| Lane mix | % of PRs per lane | labels | Feature ≤ 50 % — most work should be light |
| Markdown burden | lines of spec/plan/tasks reviewed per feature | git | watch it: if reviewers stop reading, trim templates |

## Pilot protocol

1. Run 3–5 features through the full flow with explicit metrics.
2. Retro after each with the questions: *Which artifact did nobody read? Which gate caught a real
   problem? Where did the agent go off-plan?*
3. Trim: remove template sections nobody used, merge gates that never caught anything, add checks
   for the problems that slipped through.

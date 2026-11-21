# Playbook: bugfixes and chores (no full SDD flow)

## Bugfix lane (`lane:bugfix`)

The spec already says what should happen; the code doesn't do it.

1. Create an issue with: expected behavior (quote the spec scenario ID if there is one, e.g.
   `RELM-2/US2/AS2`), actual behavior, reproduction.
2. Write a **failing regression test first**. Title it with the scenario ID when one exists.
3. Fix. Optionally use Spec Kit's bundled `bug` extension (`assess → fix → test`) for non-trivial
   bugs: `specify extension add bug`.
4. PR with `lane:bugfix`, link the issue, describe the regression test. No `Feature:` line needed.
5. If the "bug" turns out to need a *decision* about new behavior → stop; it's a feature.

## Chore lane (`lane:chore`)

No behavior change: dependency bumps, refactors, docs, build, CI. PR description explains why
behavior is unchanged (e.g. "tests unchanged and green"). Agents may do chores end-to-end, but a
human still reviews and merges.

## Hotfix

Production incident: bugfix lane, fix first; the regression test follows within 24 h in a linked
PR. Post-incident: if the spec was ambiguous, open a `lane:chore` PR in `product-specs`.

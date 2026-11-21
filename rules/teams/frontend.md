# Frontend team rules (`@spec-kit-relm/frontend`)

Applies to: `web-storefront` and every future user-facing app.
Constitution layer: Part B `RELM:TEAM-FRONTEND` (FE-1 … FE-6).

## MUST (mirrored in the constitution)

| ID | Rule | Why | How to comply |
|----|------|-----|---------------|
| FE-1 | WCAG 2.2 AA; zero serious/critical axe violations on changed screens | Accessibility is a requirement, not polish | axe in component + E2E tests; keyboard walkthrough in review |
| FE-2 | Every touched screen specifies loading, empty, error, partial, success states + copy | Agents implement the happy path unless told otherwise | *UI states* table in every FE plan |
| FE-3 | Only generated API clients from the pinned contract; mocks from the same contract | FE and BE can build in parallel and still fit | `no-raw-fetch` lint rule; consumer contract tests |
| FE-4 | LCP < 2.5 s, INP < 200 ms at p75; bundle growth ≤ 10 % without justification | Performance regressions are invisible in review | Lighthouse CI + bundle-size check |
| FE-5 | Design-system components; i18n catalog for all copy | Consistency; we sell in several languages | lint rules; design review |
| FE-6 | No personal data in localStorage/URLs/analytics; tracking plan in the spec | Privacy and compliance | PR review; analytics schema validation |

## SHOULD (team practices)
- Server Components by default; client components only for interactivity.
- Co-locate component tests; name tests with scenario IDs when they verify a scenario.
- Ask the agent for the error states first — they are what it forgets.
- Review generated UI in the browser, not only in the diff.

## Review focus for frontend PRs
1. Are all five UI states implemented and translated?
2. Any request not going through the generated client?
3. Keyboard + screen-reader path for the new UI?
4. Bundle and Lighthouse deltas in the PR checks?

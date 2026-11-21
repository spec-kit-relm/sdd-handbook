# AI usage policy

Relm is AI-native: agents are expected to do most first drafts. This policy keeps that safe.

## Approved tools
- Coding agents with a Spec Kit integration configured by `bootstrap/init-repo.sh` (default: Claude
  Code; Copilot reads the same `AGENTS.md`). Other agents may be added via
  `specify integration install` after SDD Guild review.
- Only Spec Kit presets/extensions from the **Relm catalog** may be installed. Community catalogs are
  discovery-only; vetting = code review by the SDD Guild + re-hosting in `speckit-platform`.

## What may go into a prompt
| Allowed | Not allowed |
|---------|-------------|
| Code, specs, contracts and docs from `spec-kit-relm` repositories | Secrets, tokens, credentials, private keys (ever) |
| Synthetic test data | Production data, real customer personal data |
| Error messages and stack traces with personal data removed | Logs or dumps that contain personal data |

## Accountability
- The human who opens a PR is its author, regardless of how much an agent wrote (ORG-9).
- Agents never merge, never approve, and never tick checklist items.
- Agents may push branches and open draft PRs; a human marks them ready for review.
- MCP servers / tools that let an agent act on external systems (issue trackers, cloud consoles)
  are allowed only read-only by default; write access needs a named owner and an SDD Guild review.

## Disclosure
PRs keep the agent's co-author trailer if the tool adds one. No further disclosure is needed —
the review standard is the same for all code.

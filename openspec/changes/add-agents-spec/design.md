# Design

## Context

The harness has no agent-role reference; `docs/README.md` summarizes the pipeline at a high level and `docs/architecture.md` covers system design, but neither enumerates the four roles with their responsibilities, inputs, and outputs. See `proposal.md` for motivation. The only constraint is the existing documentation contract: every `docs/*.md` file must be non-empty, carry one H1 header, and use relative links for cross-references, and `./scripts/test-md.sh` must pass.

## Goals / Non-Goals

**Goals:**
- Add `docs/agents-spec.md` as the canonical reference for the `planner`, `committer`, `coder`, and `tester` roles.
- Make the new document discoverable from `docs/README.md` navigation.
- Keep the existing `project-documentation` capability as the single home for these requirements.

**Non-Goals:**
- No changes to `.opencode/agents/`, `.opencode/commands/pipeline.md`, or any tooling.
- No restructuring or expansion of `docs/README.md` and `docs/architecture.md` beyond the navigation link.
- No new capability or directory layout.

## Decisions

- **Extend `project-documentation` rather than create a capability.** The new document is authored Markdown governed by the same contract, so a delta on the existing capability is the correct home. Alternative considered: a new `agents` capability — rejected as a near-duplicate that would fragment the doc contract.
- **Source of truth for content is the agent files plus `pipeline.md`.** Each role section derives its responsibilities from `.opencode/agents/<role>.md` and its handoffs from `.opencode/commands/pipeline.md`, so the doc stays consistent with the wiring. Alternative considered: restating `architecture.md` — rejected because the new file must add role-level input/output detail, not duplicate the architecture view.
- **Structure with one H1 and one H2 per role.** Satisfies the header-hierarchy requirement and keeps `test-md.sh` green. A trailing H2 "Handoffs" or per-role handoff subsection captures the pipeline transitions.
- **Link via relative path.** `docs/README.md` gains `[agents-spec.md](agents-spec.md)` in its existing "See Also" section, matching the `architecture.md` link style.

## Risks / Trade-offs

- [Content drifts from the agent definitions] → Tasks require reading `.opencode/agents/*.md` and `.opencode/commands/pipeline.md` while drafting, and validation confirms the file parses as Markdown.
- [Link added to the wrong section breaks navigation] → Edit the existing "See Also" list only, keeping the relative-link format.
- [Header hierarchy regression fails the contract] → Final `./scripts/test-md.sh` run gates completion.

## Migration Plan

Not applicable — documentation-only change with no runtime, data, or deployment impact.

## Open Questions

None.

# Proposal

## Why

The harness documents its overall architecture and pipeline in `docs/`, but it has no single reference that explains the four agent roles (planner, committer, coder, tester) defined under `.opencode/agents/`. Readers must reverse-engineer each role from its agent file, its skill, and `.opencode/commands/pipeline.md`. A dedicated agents reference closes that gap and is reachable from the existing documentation.

## What Changes

- Create `docs/agents-spec.md`: a Markdown reference describing the four agent roles, their responsibilities, their inputs and outputs, and how they hand off within the pipeline defined in `.opencode/commands/pipeline.md`.
- Update `docs/README.md` navigation: add a relative link to `agents-spec.md` in the existing "See Also" section alongside the current `architecture.md` link.
- No behavior or tooling changes: this is a documentation-only change and produces no application code.

Target `.md` files:

- `docs/agents-spec.md` — **created** (new agent roles reference).
- `docs/README.md` — **modified** (add `agents-spec.md` to the "See Also" navigation).

## Capabilities

### New Capabilities
<!-- None: agent-role documentation extends the existing project-documentation capability. -->

### Modified Capabilities
- `project-documentation`: the required documentation file set gains `docs/agents-spec.md`; a new requirement defines the agent-roles reference document; and the README requirement gains a navigation scenario requiring a relative link to `agents-spec.md`.

## Impact

- Files: `docs/agents-spec.md` (new), `docs/README.md` (edited). Nothing else is touched.
- Tooling/dependencies: none. All content is standard Markdown with a single H1 and H2 sections.
- Validation: `./scripts/test-md.sh` remains the gate and must pass after both files are in place.
- Compatibility: no breaking changes; existing docs and links are preserved.

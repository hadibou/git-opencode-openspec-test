# Agent Roles Specification

## Overview

This harness coordinates four agents, each defined as a Markdown file under `.opencode/agents/` and wired together by `.opencode/commands/pipeline.md`. The four roles are:

| Role | Definition | Primary responsibility |
| --- | --- | --- |
| `planner` | `.opencode/agents/planner.md` | Turn a user request into complete OpenSpec artifacts. |
| `committer` | `.opencode/agents/committer.md` | Own the Git lifecycle and workspace isolation. |
| `coder` | `.opencode/agents/coder.md` | Implement the change tasks inside the worktree. |
| `tester` | `.opencode/agents/tester.md` | Validate the implementation and gate the merge. |

Every agent exposes `bash` as a tool so it can run OpenSpec commands, Git commands, and the project test suite directly. The pipeline runs planner → committer → coder → tester, with the committer returning at the end to merge the validated work. Each role is described below with its responsibilities, the inputs it consumes, and the outputs it produces.

## Planner

The `planner` agent is the software architect. It is the first agent invoked in the pipeline and owns the OpenSpec *propose* phase. Defined in `.opencode/agents/planner.md`, it turns a raw user request into a complete, coherent change plan.

**Responsibilities**

- Analyze the user request and initialize the OpenSpec artifacts by running `/openspec-propose` with the request as the argument.
- Review the generated `openspec/changes/<change>/` proposal, specs, design, and tasks so that they are complete and internally coherent.
- Signal that planning is complete so the next role can proceed.

**Inputs**

- The user request, supplied as the argument to `/openspec-propose`.
- The `openspec-propose` skill from `.opencode/skills/`.
- Project context and artifact rules from `openspec/config.yaml`.

**Outputs**

- A change directory `openspec/changes/<change>/` containing `proposal.md`, the capability spec deltas under `specs/`, `design.md`, and `tasks.md`.
- A completion signal indicating the planning artifacts are ready for the committer.

It hands off to the `committer`, which commits these planning artifacts and creates the isolated worktree for implementation.

## Committer

The `committer` agent manages the Git lifecycle and workspace isolation. Defined in `.opencode/agents/committer.md`, it is invoked twice: once after planning to commit artifacts and create the worktree, and again after verification to merge the validated branch and clean up.

**Responsibilities**

- Commit the planning artifacts to `master` once the planner finishes.
- Create and clean up Git worktrees for the development and testing phases.
- Perform safe merges from a validated worktree back into `master`, and remove the worktree and its feature branch.

**Inputs**

- The completed planning artifacts under `openspec/` produced by the planner.
- The approval signal from the `tester` that the implementation is stable and ready to merge.
- The Git repository, branch state, and the pipeline's worktree conventions (for example the `feat/openspec-exec` branch and sibling `../worktree-exec` directory).

**Outputs**

- Commits on `master` for the OpenSpec artifacts and, after verification, the implementation.
- An isolated worktree on a feature branch where the coder and tester operate.
- A merged `master` with the worktree removed and the feature branch deleted.

It hands the workspace off to the `coder` by providing the clean worktree for implementation, and it closes the pipeline by merging once the `tester` approves.

## Coder

The `coder` agent is the developer. Defined in `.opencode/agents/coder.md`, it works inside the isolated worktree and owns the OpenSpec *apply* phase, implementing the changes described in the change's task list.

**Responsibilities**

- Run `/openspec-apply-change` to execute the active task from the change's `tasks.md`.
- Write any supplementary implementation code or tests required by the spec.
- Verify that each task is marked as completed in `tasks.md` and keep the work scoped to what the change describes.

**Inputs**

- The change plan and task list at `openspec/changes/<change>/tasks.md`.
- The clean worktree created by the `committer`.
- The `openspec-apply-change` skill from `.opencode/skills/`, plus project context and apply guidance from `openspec/config.yaml`.
- On a failed verification, the detailed error report returned by the `tester`.

**Outputs**

- Implemented changes in the worktree that satisfy the spec and the pending tasks.
- An updated `tasks.md` with completed tasks checked off (`- [x]`).
- Any supplementary code or tests the spec requires, ready for the tester to validate.

It hands off to the `tester` for validation, and it receives work back from the `tester` when a test failure must be fixed.

## Tester

The `tester` agent is the quality gatekeeper. Defined in `.opencode/agents/tester.md`, it validates the coder's work inside the worktree and decides whether the change is stable enough to merge.

**Responsibilities**

- Run the project's test suite commands in the terminal.
- Analyze the execution output and logs for failures or regressions.
- Pause and request manual tests from the user when needed.
- On failure, return a detailed error report to the coder; otherwise, approve the changes for merging.

**Inputs**

- The implemented change and any supplementary tests produced by the `coder`, in the worktree.
- The project's test suite commands, including the documentation validation contract `scripts/test-md.sh`.
- The test output and logs from the terminal.

**Outputs**

- A pass/fail verdict on the implementation.
- On failure, a detailed error report routed back to the `coder` for a fix.
- On success, an approval that authorizes the `committer` to merge.

It hands off to the `coder` on failure and to the `committer` on success.

## Handoffs

`.opencode/commands/pipeline.md` orchestrates the four roles through the complete OpenSpec lifecycle across an isolated Git worktree. The handoffs run in four stages.

1. **Planning and spec generation.** The user request is passed to the `planner`, which triggers `/openspec-propose` and drafts the change artifacts. Once the spec files are drafted, the `planner` hands off to the `committer`, which commits them to `master` (`git add openspec/ && git commit -m "docs(openspec): generate spec artifacts"`).
2. **Workspace isolation.** The `committer` creates a clean worktree on a feature branch (`git worktree add -b feat/openspec-exec ../worktree-exec master`) and hands that isolated workspace to the `coder`.
3. **Execution and verification.** Inside the worktree, the `coder` runs `/openspec-apply-change` against the active tasks. The `coder` then hands off to the `tester`, which runs the project tests. A `tester` failure returns a detailed error log to the `coder`, who fixes it before proceeding — this tester → coder loop repeats until the tests pass. A `tester` pass ends the stage and hands back to the `committer`.
4. **Finalization and merge.** Once the `tester` passes, the `committer` commits the implementation (`git commit -am "feat: apply openspec changes"`), returns to the main repository root, merges the worktree branch (`git merge feat/openspec-exec`), and removes the worktree and its branch (`git worktree remove ../worktree-exec` and `git branch -d feat/openspec-exec`).

The final committer merge completes the pipeline by landing the validated work on `master`. A completed change can then be finalized with the archive step (`/openspec-archive-change`), which moves it under `openspec/changes/archive/` and keeps the active change list clean; the pipeline itself ends at the merge and worktree cleanup.

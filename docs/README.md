# OpenSpec + OpenCode Multi-Agent Test Harness

## Purpose

This repository is a test harness for the OpenSpec + OpenCode multi-agent pipeline. It exists to validate multi-agent execution and Git worktree automation end to end: a request is planned into OpenSpec artifacts, committed, implemented inside an isolated Git worktree, and verified by an automated test before it is accepted.

The harness deliberately contains no application code. Its only deliverable is documentation in the `docs/` directory, which makes it a small but realistic target for exercising the planner, committer, coder, and tester agents, the OpenSpec propose / apply / archive lifecycle, and the validation script that gates completion.

## Prerequisites

- **openspec CLI 1.13.1** — drives the spec-driven lifecycle (`propose`, `apply`, `archive`) and is the source of truth for change artifacts. Verify with `openspec --version`.
- **bash / WSL** — required to run `scripts/test-md.sh` and shell-based automation. On Windows, use WSL or a bash-compatible shell.
- **git** — provides the repository, branching, and the Git worktree isolation the pipeline relies on. Verify with `git --version`.

## Repository Layout

- **`.opencode/`** — OpenCode configuration for the harness:
  - **`.opencode/agents/`** — the four agent roles: `planner.md`, `committer.md`, `coder.md`, and `tester.md`.
  - **`.opencode/commands/`** — the orchestration entry point `pipeline.md`, plus the `opsx-*` OpenSpec commands.
  - **`.opencode/skills/`** — the OpenSpec skills (for example `openspec-apply-change`) that agents load to perform their work.
- **`openspec/`** — the OpenSpec root. `openspec/changes/` holds active and archived changes, `openspec/specs/` holds the main capability specs, and `openspec/config.yaml` carries the project context and per-operation guidance.
- **`scripts/test-md.sh`** — the validation contract for the documentation. It checks that `docs/` exists, that every `docs/*.md` file is non-empty, and that each file contains an H1 `# ` header.
- **`docs/`** — the authored Markdown documentation produced by this harness, including this `README.md` and `architecture.md`.

## How the Multi-Agent Pipeline Works

`.opencode/commands/pipeline.md` is the orchestration entry point. It runs the full OpenSpec lifecycle across an isolated Git worktree and hands off between four agents:

1. **Planning and spec generation** — the user request is passed to the `planner` agent, which triggers `/openspec-propose` to draft the change artifacts (proposal, specs, design, tasks). The `committer` agent then commits those artifacts.
2. **Workspace isolation** — the `committer` agent creates a dedicated Git worktree on a feature branch so implementation happens away from the main working tree.
3. **Execution and verification** — inside the worktree, the `coder` agent runs `/openspec-apply-change` against the active tasks, and the `tester` agent runs the project tests. If the tester fails, its error report is passed back to the coder to fix before proceeding.
4. **Finalization and merge** — once the tester passes, the `committer` agent commits the implementation, merges the worktree branch back, and removes the worktree.

Each agent is defined under `.opencode/agents/` and loads the matching OpenSpec skill from `.opencode/skills/`. See `architecture.md` for the detailed flow and lifecycle.

## Test Contract

The harness is gated by a single executable contract, `scripts/test-md.sh`, run from the repository root:

- `docs/` must exist.
- Every `docs/*.md` file must be non-empty.
- Every `docs/*.md` file must contain a top-level H1 header beginning with `# `.

The script exits `0` only when all checks pass and prints `=== All Markdown Tests Passed Successfully ===`. Any failure prints the offending file and exits non-zero, so a change is not complete until `bash scripts/test-md.sh` succeeds.

## See Also

- [architecture.md](architecture.md) — the detailed architecture: agent flow, OpenSpec lifecycle, Git worktree isolation, and the validation contract.
- [agents-spec.md](agents-spec.md) — the agent roles reference: the `planner`, `committer`, `coder`, and `tester` roles with their responsibilities, inputs, outputs, and pipeline handoffs.

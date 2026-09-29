---
description: Processes features sequentially using state tracking in docs/features.md.
---

# Roadmap Loop Protocol

1. **Find Next Actionable Feature:**
   - Scan `docs/features.md`:
     - If a feature is `[NEEDS_REVISION]`: Resume `@coder` in `../worktrees/<feature-id>`.
     - If a feature is `[BACKLOG]`: Mark as `[IN_PROGRESS]` and start new pipeline.

2. **Run Pipeline Stages:**
   - **Phase 1 (Plan/Code/Test):** `@planner` $\rightarrow$ `@coder` $\rightarrow$ `@tester`.
   - **Phase 2 (PR Creation):** `@committer` pushes branch, creates PR, sets state to `[IN_REVIEW]`.
   - **Phase 3 (Automated Review):** `@reviewer` audits PR diffs via `rtk gh pr`:
     - If `CHANGES_REQUESTED`: Set state to `[NEEDS_REVISION]`, route comments to `@coder`.
     - If `APPROVED`: `@reviewer` merges PR, set state to `[APPROVED]`.
   - **Phase 4 (Archiving):** `@committer` pulls `master`, runs `/openspec-archive`, cleans up worktree, and updates state in `docs/features.md` to `[ARCHIVED]`.

3. **Loop:** Repeat until all roadmap items reach `[ARCHIVED]`.
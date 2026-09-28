---
---
description: Executes OpenSpec lifecycle in a Worktree, opens a PR, watches for review results, and archives on master post-merge.
---

# Strategy 2 Execution Workflow

1. **Setup Worktree:**
   - Invoke `@committer` to create and push the `<change-name>` branch and worktree.

2. **Planning Phase (Active Spec):**
   - In `../worktrees/<change-name>`, invoke `@planner` to run `/openspec-propose`.
   - Invoke `@committer` to commit and push `.openspec/changes/<change-name>/`.

3. **Execution Phase:**
   - In `../worktrees/<change-name>`, invoke `@coder` to run `/openspec-apply-change`.
   - Invoke `@tester` to run `./scripts/test-md.sh`. Repeat with `@coder` if tests fail.
   - Invoke `@committer` to commit and push implementation code.

4. **PR Creation & Watching Loop:**
   - Invoke `@committer` to open the Pull Request via `gh pr create`.
   - `@committer` enters the watching loop (`gh pr view`).
   - If PR has feedback $\rightarrow$ `@coder` fixes inside the worktree $\rightarrow$ `@committer` pushes updates.
   - If PR merges $\rightarrow$ `@committer` checks out `master`, runs `/openspec-archive`, commits the archive, and deletes the worktree.
---
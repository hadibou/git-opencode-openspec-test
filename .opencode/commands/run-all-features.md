---
description: Iterates through docs/features.md and runs the pipeline for each feature sequentially.a
---

# Feature Loop Execution

1. **Read Roadmap:**
   
   - Inspect `docs/features.md` to find the first uncompleted checkbox (`- [ ]`).
   - Extract the feature ID (e.g., `feature-01`) and the task description.
   - If no uncompleted features remain, log "All features implemented and merged!" and exit.

2. **Execute Pipeline for Current Feature:**
   
   - Invoke `/pipeline` passing the extracted feature ID and description:
     `/pipeline "Implement <feature-id>: <description>"`

3. **Wait for Full Validation & Merge:**
   
   - Ensure the `/pipeline` completes the entire lifecycle:
     - Worktree creation & branch push
     - `@planner` spec proposal
     - `@coder` implementation & `@tester` passing
     - PR creation, watching, approval, and merge to `master`
     - `/openspec-archive` on `master` and worktree cleanup

4. **Mark Feature as Completed:**
   
   - On `master`, update `docs/features.md` to change `- [ ] **<feature-id>**` to `- [x] **<feature-id>**`.
   - Commit and push the roadmap update directly on `master`:
     `git add docs/features.md && git commit -m "docs: mark <feature-id> as completed" && git push`

5. **Loop Next Feature:**
   
   - Repeat from Step 1 until all items in `docs/features.md` are marked as completed `[x]`.
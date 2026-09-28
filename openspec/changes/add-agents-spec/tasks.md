# Tasks

## 1. Author docs/agents-spec.md

- [ ] 1.1 Create `docs/agents-spec.md` with a single H1 title and an H2 overview of the four agent roles defined under `.opencode/agents/`; verify the file exists and run `./scripts/test-md.sh` to confirm it passes
- [ ] 1.2 Add an H2 section for the `planner` role in `docs/agents-spec.md` covering its responsibilities, inputs, and outputs, then run `./scripts/test-md.sh` to confirm it passes
- [ ] 1.3 Add an H2 section for the `committer` role in `docs/agents-spec.md` covering its responsibilities, inputs, and outputs, then run `./scripts/test-md.sh` to confirm it passes
- [ ] 1.4 Add an H2 section for the `coder` role in `docs/agents-spec.md` covering its responsibilities, inputs, and outputs, then run `./scripts/test-md.sh` to confirm it passes
- [ ] 1.5 Add an H2 section for the `tester` role in `docs/agents-spec.md` covering its responsibilities, inputs, and outputs, then run `./scripts/test-md.sh` to confirm it passes
- [ ] 1.6 Add an H2 "Handoffs" section to `docs/agents-spec.md` describing how roles hand off within `.opencode/commands/pipeline.md`, including the tester→coder failure loop and the final committer merge, then run `./scripts/test-md.sh` to confirm it passes

## 2. Update docs/README.md navigation

- [ ] 2.1 Edit the "See Also" section of `docs/README.md` to add a relative link `[agents-spec.md](agents-spec.md)` alongside the existing `architecture.md` link, then run `./scripts/test-md.sh` to confirm it passes

## 3. Final validation

- [ ] 3.1 Run `./scripts/test-md.sh` from the repository root and confirm it exits 0, reporting that `docs/agents-spec.md` and the updated `docs/README.md` both have valid H1 headers and the relative `agents-spec.md` link resolves

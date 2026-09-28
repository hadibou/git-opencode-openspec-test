# Spec Delta

## MODIFIED Requirements

### Requirement: Documentation directory and required files
The repository SHALL contain a `docs/` directory holding the project's documentation, including `docs/README.md`, `docs/architecture.md`, and `docs/agents-spec.md`. The documentation SHALL be composed of Markdown (`.md`) files only.

#### Scenario: Documentation files exist
- **WHEN** the documentation change is complete
- **THEN** `docs/README.md`, `docs/architecture.md`, and `docs/agents-spec.md` all exist under the repository root

#### Scenario: Only Markdown files are produced
- **WHEN** the `docs/` directory is listed
- **THEN** every documented artifact is a Markdown file with a `.md` extension

### Requirement: README documents the test harness
`docs/README.md` SHALL document the repository as a test harness. It MUST state the purpose of the harness, list the prerequisites (openspec CLI 1.13.1, bash/WSL, and git), describe the repository layout (`.opencode/` agents + commands + skills, `openspec/`, `scripts/test-md.sh`, and `docs/`), explain how the multi-agent pipeline in `.opencode/commands/pipeline.md` works, describe the test contract, and provide a navigation or "See Also" section that links to the other documentation under `docs/` using relative Markdown links.

#### Scenario: Prerequisites are documented
- **WHEN** a reader opens `docs/README.md`
- **THEN** it lists openspec CLI 1.13.1, bash/WSL, and git as prerequisites

#### Scenario: Repository layout is documented
- **WHEN** a reader opens `docs/README.md`
- **THEN** it describes `.opencode/` (agents, commands, skills), `openspec/`, `scripts/test-md.sh`, and `docs/`

#### Scenario: Pipeline and test contract are documented
- **WHEN** a reader opens `docs/README.md`
- **THEN** it explains how `.opencode/commands/pipeline.md` coordinates the agents and states the repository's test contract

#### Scenario: Navigation links to the agents reference
- **WHEN** a reader opens the navigation or "See Also" section of `docs/README.md`
- **THEN** it contains a relative Markdown link to `agents-spec.md` alongside the existing link to `architecture.md`

## ADDED Requirements

### Requirement: Agents specification document describes the agent roles
`docs/agents-spec.md` SHALL serve as the reference for the four agent roles defined under `.opencode/agents/` (`planner`, `committer`, `coder`, `tester`). For each role it MUST describe the role's responsibilities, its inputs and outputs, and how it hands off within the workflow in `.opencode/commands/pipeline.md`. The document SHALL begin with a single H1 header and SHALL structure each role and topic under H2 headers.

#### Scenario: All four roles are documented
- **WHEN** a reader opens `docs/agents-spec.md`
- **THEN** it documents the `planner`, `committer`, `coder`, and `tester` roles

#### Scenario: Responsibilities, inputs, and outputs are documented
- **WHEN** a reader inspects the section for an agent role
- **THEN** it states that role's responsibilities and the inputs it consumes and outputs it produces

#### Scenario: Handoffs within the pipeline are documented
- **WHEN** a reader inspects `docs/agents-spec.md`
- **THEN** it explains how each role hands off to the next within `.opencode/commands/pipeline.md`, including the tester-to-coder failure loop and the final committer merge

#### Scenario: Header hierarchy is valid
- **WHEN** `docs/agents-spec.md` is inspected
- **THEN** it contains exactly one H1 header and introduces its role and topic sections with H2 headers

#### Scenario: Agents reference is reachable from the README
- **WHEN** a reader follows the `agents-spec.md` link in `docs/README.md`
- **THEN** it resolves to the existing `docs/agents-spec.md` file

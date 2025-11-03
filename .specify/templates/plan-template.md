# Implementation Plan: [FEATURE]

**Branch**: `[###-feature-name]` | **Date**: [DATE] | **Spec**: [link]
**Input**: Feature specification from `/specs/[###-feature-name]/spec.md`

**Note**: This template is filled in by the `/speckit.plan` command. See `.specify/templates/commands/plan.md` for the execution workflow.

## Summary

[Extract from feature spec: primary requirement + technical approach from research]

## Technical Context

<!--
  ACTION REQUIRED: Replace the content in this section with the technical details
  for the project. The structure here is presented in advisory capacity to guide
  the iteration process.
-->

**Primary Format**: Markdown with frontmatter (REQUIRED)  
**Scripting Language**: [Python/PowerShell/Bash - only if Markdown insufficient]  
**External APIs**: [e.g., Fireflies, Notion, context7 or N/A]  
**Target AI Agents**: OpenCode, Claude Code, GitHub Copilot (REQUIRED)  
**Platform Compatibility**: Windows, Linux, macOS (REQUIRED)  
**Validation Method**: Manual testing with example inputs/outputs (REQUIRED)  
**Integration Points**: File system, external APIs, multi-step workflows  
**Complexity Constraint**: Scripts only when Markdown cannot achieve requirement

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

- [ ] **Prompt-First**: Command starts as Markdown prompt, not code
- [ ] **Multi-Agent Compatibility**: Works across OpenCode, Claude Code, GitHub Copilot
- [ ] **Validation-Driven**: Includes input/output specifications and test examples
- [ ] **Integration Simplicity**: Minimal external dependencies, clear integration points
- [ ] **Minimal Implementation**: Scripts only when Markdown is insufficient

## Project Structure

### Documentation (this feature)

```text
specs/[###-feature]/
├── plan.md              # This file (/speckit.plan command output)
├── research.md          # Phase 0 output (/speckit.plan command)
├── data-model.md        # Phase 1 output (/speckit.plan command)
├── quickstart.md        # Phase 1 output (/speckit.plan command)
├── contracts/           # Phase 1 output (/speckit.plan command)
└── tasks.md             # Phase 2 output (/speckit.tasks command - NOT created by /speckit.plan)
```

### Source Code (repository root)
<!--
  ACTION REQUIRED: Replace the placeholder tree below with the concrete layout
  for this feature. Delete unused options and expand the chosen structure with
  real paths (e.g., apps/admin, packages/something). The delivered plan must
  not include Option labels.
-->

```text
# AI CLI Commands Project Structure (DEFAULT)
.claude/
├── commands/           # Slash command Markdown files
│   ├── CommandName.md  # Individual command definitions
│   └── ...
├── settings.local.json # Agent-specific configuration
└── ...

.scripts/              # Supporting scripts (ONLY if required)
├── python/            # Python utility scripts
├── powershell/        # PowerShell scripts for Windows
└── bash/             # Bash scripts for Linux/macOS

.specify/             # Project documentation and templates
├── memory/
│   └── constitution.md
├── templates/
│   ├── plan-template.md
│   ├── spec-template.md
│   └── tasks-template.md
└── ...

docs/                 # Additional documentation
├── integration/      # API integration guides
├── troubleshooting/  # Common issues and solutions
└── examples/         # Usage examples
```

**Structure Decision**: [Document the selected structure and reference the real
directories captured above]

## Complexity Tracking

> **Fill ONLY if Constitution Check has violations that must be justified**

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| [e.g., 4th project] | [current need] | [why 3 projects insufficient] |
| [e.g., Repository pattern] | [specific problem] | [why direct DB access insufficient] |

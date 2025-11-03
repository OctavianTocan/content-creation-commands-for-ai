# Implementation Plan: [FEATURE]

**Branch**: `[###-feature-name]` | **Date**: [DATE] | **Spec**: [link]
**Input**: Feature specification from `/specs/[###-feature-name]/spec.md`

**Note**: This template is filled in by the `/speckit.plan` command. See `.specify/templates/commands/plan.md` for the execution workflow.

## Summary

Convert Notion page transcripts to LinkedIn posts using a phased validation approach. The system first validates Notion MCP availability and page access before processing content. Upon successful validation, it extracts transcript content, analyzes themes, and generates multiple LinkedIn post variations using specified templates and style guides. All outputs are saved to structured file directories with clear naming conventions.

## Technical Context

<!--
  ACTION REQUIRED: Replace the content in this section with the technical details
  for the project. The structure here is presented in advisory capacity to guide
  the iteration process.
-->

**Primary Format**: Markdown with frontmatter (REQUIRED)  
**Scripting Language**: Bash (minimal - for file operations only)  
**External APIs**: Notion MCP (https://mcp.notion.com/mcp)  
**Target AI Agents**: OpenCode, Claude Code, GitHub Copilot (REQUIRED)  
**Platform Compatibility**: Windows, Linux, macOS (REQUIRED)  
**Validation Method**: Manual testing with example inputs/outputs (REQUIRED)  
**Integration Points**: File system operations, Notion MCP, structured content processing  
**Complexity Constraint**: Scripts only when Markdown cannot achieve requirement

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

- [x] **Prompt-First**: Command starts as Markdown prompt, not code
- [x] **Multi-Agent Compatibility**: Works across OpenCode, Claude Code, GitHub Copilot
- [x] **Validation-Driven**: Includes input/output specifications and test examples
- [x] **Integration Simplicity**: Minimal external dependencies, clear integration points
- [x] **Minimal Implementation**: Scripts only when Markdown is insufficient

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

**Structure Decision**: Using default AI CLI Commands structure with .claude/commands/ for slash commands and data/ directory for processed outputs. This aligns with existing project structure and supports cross-agent compatibility.

## Complexity Tracking

> **Fill ONLY if Constitution Check has violations that must be justified**

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| [e.g., 4th project] | [current need] | [why 3 projects insufficient] |
| [e.g., Repository pattern] | [specific problem] | [why direct DB access insufficient] |

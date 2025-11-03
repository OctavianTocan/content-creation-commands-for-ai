<!--
Sync Impact Report:
Version change: 1.0.0 → 1.0.0 (initial constitution)
Modified principles: N/A (initial creation)
Added sections: Core Principles, Content Standards, Development Workflow, Governance
Removed sections: N/A
Templates requiring updates: ✅ plan-template.md, ✅ spec-template.md, ✅ tasks-template.md (all aligned with new principles)
Follow-up TODOs: None
-->

# AI CLI Commands Constitution

## Core Principles

### I. Prompt-First Development
Every slash command starts as a well-structured Markdown prompt; Prompts must be self-contained, clearly documented, and independently usable; Clear purpose required - no utility-only commands without specific use cases.

### II. Multi-Agent Compatibility  
All commands must work across OpenCode, Claude Code, and GitHub Copilot; Use standard Markdown format with frontmatter for metadata; Avoid agent-specific syntax unless explicitly documented as optional.

### III. Validation-Driven Design
Commands must include validation criteria in their documentation; Each command must specify expected inputs, outputs, and success conditions; Test examples must be provided and verifiable before implementation.

### IV. Integration Simplicity
Focus areas requiring integration: External API connections (Fireflies, Notion, etc.), File system operations, Multi-step workflows, Cross-platform compatibility (Windows/Linux/macOS).

### V. Minimal Implementation
Start with Markdown-only solutions; Add scripts only when Markdown is insufficient; Prefer existing tools over custom code; Every script must have a clear justification that Markdown cannot achieve.

## Content Standards

### Command Structure
All slash commands must follow this structure:
- Frontmatter with argument hints and metadata
- Clear description of purpose and use case  
- Input/output specifications
- Example usage with expected results
- Error handling guidance

### Documentation Requirements
- Every command must be discoverable via directory structure
- Commands must include troubleshooting sections
- Version compatibility must be explicitly stated
- Dependencies must be clearly documented

## Development Workflow

### Creation Process
1. Define user scenario and acceptance criteria
2. Draft Markdown prompt with validation examples
3. Test prompt across target AI agents
4. Add supporting scripts only if required
5. Document integration points and dependencies

### Quality Gates
- Commands must work without custom scripts initially
- All examples must be tested and verifiable
- Cross-agent compatibility must be validated
- Documentation must be complete before integration

## Governance

This constitution supersedes all other development practices; Amendments require documentation, team approval, and migration plan for existing commands; All command reviews must verify compliance; Complexity beyond Markdown must be explicitly justified; Use `.specify/templates/` for runtime development guidance.

**Version**: 1.0.0 | **Ratified**: 2025-11-03 | **Last Amended**: 2025-11-03

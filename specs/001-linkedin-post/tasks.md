---

description: "Task list for Notion to LinkedIn Post Converter implementation"
---

# Tasks: 001-linkedin-post

**Input**: Design documents from `/specs/001-linkedin-post/`
**Prerequisites**: plan.md (required), spec.md (required for user stories), research.md, data-model.md, contracts/

**Tests**: The examples below include test tasks. Tests are OPTIONAL - only include them if explicitly requested in feature specification.

**Organization**: Tasks are grouped by user story to enable independent implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Path Conventions

- **Claude Code Slash Commands**: `.claude/commands/` for Markdown slash command files (stored in repository, shared with team)
- **Data Output**: `data/` for processed transcripts and posts

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Project initialization and basic structure

- [X] T001 Create data directory structure for transcripts and posts in data/

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Core infrastructure that MUST be complete before ANY user story can be implemented

**⚠️ CRITICAL**: No user story work can begin until this phase is complete

- [X] T002 Create basic slash command Markdown file in .claude/commands/Create-LinkedInPost.md

**Checkpoint**: Foundation ready - user story implementation can now begin in parallel

---

## Phase 3: User Story 1 - Basic Transcript to Post Conversion (Priority: P1) 🎯 MVP

**Goal**: Convert a single Notion page containing meeting transcripts into multiple LinkedIn post variations using default template and style guide

**Independent Test**: Provide a Notion page ID with transcript content and verify that the command produces 3 LinkedIn post variations following the specified template and style guidelines

### Validation for User Story 1 (REQUIRED) ⚠️

> **NOTE: Validate examples FIRST, ensure they work before implementation**

- [X] T003 [P] [US1] Test slash command invocation in .claude/commands/Create-LinkedInPost.md
- [X] T004 [P] [US1] Validate command handles Notion MCP unavailability with clear error message
- [X] T005 [P] [US1] Validate command handles invalid Notion page IDs with clear error message

### Implementation for User Story 1

- [X] T006 [US1] Create slash command with frontmatter and argument-hint in .claude/commands/Create-LinkedInPost.md
- [X] T007 [US1] Implement Notion MCP availability check with clear error message if not available
- [X] T008 [US1] Implement Notion page access validation using Notion MCP
- [X] T009 [US1] Implement transcript content extraction and save to data/transcripts/{page-name}/transcript.md
- [X] T010 [US1] Implement AI-powered theme extraction and save to data/transcripts/{page-name}/themes-analysis.md
- [X] T011 [US1] Implement LinkedIn post generation ONE BY ONE using extracted themes
- [X] T012 [US1] Save generated posts to data/linkedin-posts/{page-name}/{theme}:{post-name}.md
- [X] T013 [US1] Apply default template structure from https://www.notion.so/infimagames/LinkedIn-Post-Template-29f3c065308b8022aaecce22524bd32b
- [X] T014 [US1] Apply default style guide from https://www.notion.so/infimagames/Octavian-s-Writing-Style-Guide-9e7ae38cdf164ed88c09f442332e1e46

### Bug Fixes for User Story 1 (CRITICAL) 🔧

- [X] T015 [US1] Fix Windows path separator issues - change from `/` to `\\` in all mkdir and file paths
- [X] T016 [US1] Remove hardcoded PAGE_NAME variable - use dynamic page name generation from page-id
- [X] T017 [US1] Fix style guide fetching timing - fetch style guide before processing content
- [X] T018 [US1] Remove confirmation requirement from file operations
- [X] T019 [US1] Fix template fetching timing - fetch template before generating posts
- [X] T020 [US1] Fix shell command issues - avoid complex bash commands that fail on Windows
- [X] T021 [US1] Add error handling for when Notion MCP doesn't work
- [X] T022 [US1] Add error handling for when Notion page is inaccessible
- [X] T023 [US1] Add error handling for when page has no transcript content

**Checkpoint**: ✅ User Story 1 is fully functional and testable independently

---

## Phase 4: User Story 2 - Custom Post Configuration (Priority: P2)

**Goal**: Control the number of posts generated and use custom templates and style guides for different types of content

**Independent Test**: Run the command with various parameter combinations and verify that the output respects the specified number of posts, template structure, and style guidelines

### Implementation for User Story 2

- [X] T024 [P] [US2] Update slash command frontmatter argument-hint to include optional parameters (-n, -t, -w, -c) in .claude/commands/Create-LinkedInPost.md
- [X] T025 [US2] Implement custom post count logic for -n parameter
- [X] T026 [US2] Implement custom template fetching for -t parameter using Notion MCP
- [X] T027 [US2] Implement custom style guide fetching for -w parameter using Notion MCP
- [X] T028 [US2] Implement context string integration for -c parameter into theme extraction
- [X] T029 [US2] Add parameter validation with clear error messages
- [X] T030 [US2] Test with various parameter combinations

**Checkpoint**: At this point, User Stories 1 AND 2 should both work independently

---

## Phase 5: User Story 3 - Error Handling and Validation (Priority: P3)

**Goal**: Provide clear feedback when the command encounters issues with invalid inputs, inaccessible pages, or malformed content

**Independent Test**: Provide various invalid inputs and verify that appropriate error messages are displayed with actionable guidance

### Implementation for User Story 3

- [X] T031 [P] [US3] Enhance Notion MCP unavailable error with specific guidance
- [X] T032 [P] [US3] Enhance page access errors with permission troubleshooting
- [X] T033 [P] [US3] Add content validation for minimum transcript requirements
- [X] T034 [P] [US3] Add rate limit handling with retry suggestions
- [X] T035 [P] [US3] Add network error handling with connection troubleshooting
- [X] T036 [P] [US3] Create comprehensive error scenarios and responses

**Checkpoint**: All user stories should now be independently functional

---

## Phase 6: Quality Improvements & Validation (Priority: P2)

**Purpose**: Address Claude's feedback for better robustness and reliability

### Directory Structure & File Handling
- [X] T037 [P] Improve page title extraction and sanitization for directory names
- [X] T038 [P] Add directory existence checks before creation to avoid conflicts
- [X] T039 [P] Implement cross-platform file naming sanitization

### Content Validation & Quality Checks
- [X] T040 [P] Add character count validation (< 3000) for each generated post
- [X] T041 [P] Add hashtag count validation (≤ 3) for each post
- [X] T042 [P] Implement style guide compliance validation after post generation
- [X] T043 [P] Add template structure verification with variable mapping
- [X] T044 [P] Ensure content consistency across all generated posts

### Process Improvements
- [X] T045 [P] Add validation checkpoints after each major step
- [X] T046 [P] Create post-generation checklist for requirement verification
- [X] T047 [P] Implement better error handling with specific recovery procedures
- [X] T048 [P] Add progress reporting with detailed status updates

### Reporting & Metrics
- [X] T049 [P] Generate completion summary with statistics:
  - Total words processed
  - Number of themes identified  
  - Character counts for each post
  - Hashtag usage summary
  - Template compliance status

---

## Phase 7: Polish & Cross-Cutting Concerns

**Purpose**: Final improvements and documentation

- [X] T050 [P] Create usage examples in docs/examples/linkedin-post-examples.md
- [X] T051 [P] Update quickstart.md with real command examples and outputs
- [X] T052 [P] Add progress indicators for long-running operations
- [X] T053 [P] Test command across all target AI agents

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies - can start immediately
- **Foundational (Phase 2)**: Depends on Setup completion - BLOCKS all user stories
- **User Stories (Phase 3+)**: All depend on Foundational phase completion
  - User stories can then proceed in parallel (if staffed)
  - Or sequentially in priority order (P1 → P2 → P3)
- **Polish (Final Phase)**: Depends on all desired user stories being complete

### User Story Dependencies

- **User Story 1 (P1)**: Can start after Foundational (Phase 2) - No dependencies on other stories
- **User Story 2 (P2)**: Can start after Foundational (Phase 2) - Extends US1 functionality but should be independently testable
- **User Story 3 (P3)**: Can start after Foundational (Phase 2) - Enhances error handling for all stories but should be independently testable

### Within Each User Story

- Validation tasks MUST be completed before implementation tasks
- Core functionality before enhancements
- Basic error handling before comprehensive error handling
- Story complete before moving to next priority

### Parallel Opportunities

- All Setup tasks marked [P] can run in parallel
- All validation tasks for a user story marked [P] can run in parallel
- Different user stories can be worked on in parallel by different team members

---

## Parallel Example: User Story 1

```bash
# Launch all validation tasks for User Story 1 together:
Task: "Test slash command invocation in .claude/commands/Create-LinkedInPost.md"
Task: "Validate slash command handles Notion MCP unavailability with clear error message"
Task: "Validate slash command handles invalid Notion page IDs with clear error message"

# Launch core implementation tasks for User Story 1 together:
Task: "Create slash command with frontmatter and argument-hint in .claude/commands/Create-LinkedInPost.md"
Task: "Implement Notion MCP availability check with clear error message if not available"
Task: "Implement Notion page access validation using Notion MCP"
```

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Setup
2. Complete Phase 2: Foundational (CRITICAL - blocks all stories)
3. Complete Phase 3: User Story 1
4. **STOP and VALIDATE**: Test User Story 1 independently with real Notion page
5. Deploy/demo if ready

### Incremental Delivery

1. Complete Setup + Foundational → Foundation ready
2. Add User Story 1 → Test independently → Deploy/Demo (MVP!)
3. Add User Story 2 → Test independently → Deploy/Demo
4. Add User Story 3 → Test independently → Deploy/Demo
5. Each story adds value without breaking previous stories

---

## Notes

- [P] tasks = different files, no dependencies
- [Story] label maps task to specific user story for traceability
- Each user story should be independently completable and testable
- Validate examples work before implementing core functionality
- Focus on phased validation approach as specified in user requirements
- AI model handles file operations directly - no utilities needed
- Simple, direct implementation without unnecessary complexity
- These are Claude Code slash commands stored in `.claude/commands/` as Markdown files with frontmatter
- Commands are invoked with `/Create-LinkedInPost [arguments]` in Claude Code
- Frontmatter includes `argument-hint` for parameter documentation and `description` for help text
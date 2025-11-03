# Quickstart Guide: Notion to LinkedIn Post Converter

**Feature**: 001-linkedin-post  
**Version**: 1.0  
**Date**: 2025-11-03

## Overview

This command converts Notion page transcripts into LinkedIn posts using a phased validation approach. It first checks MCP availability, validates page access, then processes content into structured outputs.

## Prerequisites

### Required Setup
1. **Notion MCP Configuration**: Ensure `.mcp.json` contains the Notion server configuration
2. **AI Agent**: OpenCode, Claude Code, or GitHub Copilot with MCP support
3. **File Permissions**: Write access to project root directory
4. **Notion Access**: Logged into Notion with access to target pages

### Default Resources
- **Style Guide**: https://www.notion.so/infimagames/Octavian-s-Writing-Style-Guide-9e7ae38cdf164ed88c09f442332e1e46
- **Template**: https://www.notion.so/infimagames/LinkedIn-Post-Template-29f3c065308b8022aaecce22524bd32b

## Basic Usage

### Command Format
```
/Create-LinkedInPost [page-id] [options]
```

### Required Parameters
- `page-id`: Notion page UUID containing transcript content

### Optional Parameters
- `-n [number]`: Number of posts to generate (default: 3)
- `-t [template-id]`: Custom template page ID (uses default if not specified)
- `-w [style-guide-id]`: Custom style guide page ID (uses default if not specified)
- `-c [context]`: Additional context string for content generation

## Example Usage

### Basic Conversion
```
/Create-LinkedInPost a1b2c3d4-e5f6-7890-abcd-ef1234567890
```

### Custom Configuration
```
/Create-LinkedInPost a1b2c3d4-e5f6-7890-abcd-ef1234567890 -n 5 -t template-id -w style-id -c "Team meeting about Q4 planning"
```

## Processing Flow

### Phase 1: Validation
1. **MCP Check**: Verifies Notion MCP is available
2. **Page Access**: Validates page ID and permissions
3. **Content Check**: Ensures page contains transcript content

### Phase 2: Content Processing
1. **Transcript Save**: Saves raw content to `data/transcripts/{page-name}-{timestamp}/transcript.md`
2. **Theme Extraction**: Analyzes content and saves to `data/transcripts/{page-name}-{timestamp}/themes-analysis.md`
3. **Post Generation**: Creates individual posts in `data/linkedin-posts/{page-name}-{timestamp}/`

## Output Structure

### Directory Layout
```
data/
├── transcripts/
│   └── meeting-notes-2025-11-03-143022/
│       ├── transcript.md
│       └── themes-analysis.md
└── linkedin-posts/
    └── meeting-notes-2025-11-03-143022/
        ├── leadership:team-motivation.md
        ├── productivity:meeting-efficiency.md
        └── strategy:q4-planning-insights.md
```

### File Contents

#### transcript.md
Contains the raw Notion page content with metadata header.

#### themes-analysis.md
AI-generated analysis including:
- Key themes from the transcript
- Interesting topics for discussion
- Post generation guidance

#### {theme}:{post-name}.md
Individual LinkedIn post drafts following template structure and style guidelines.

## Error Handling

### Common Errors and Solutions

**MCP Unavailable**
```
Error: Notion MCP is not available
Solution: Ensure Notion MCP is configured for your AI agent
```

**Page Inaccessible**
```
Error: Cannot access Notion page with provided ID
Solution: Verify page ID and ensure you have access permissions
```

**No Content Found**
```
Error: Page contains no suitable transcript content
Solution: Ensure page contains text content for processing
```

## Success Criteria

### Expected Outputs
- ✅ Transcript saved to structured file location
- ✅ Theme analysis with actionable insights
- ✅ Multiple LinkedIn post variations (3-5 posts)
- ✅ All posts follow template and style guidelines
- ✅ Posts under 3000 characters each

### Quality Metrics
- Theme relevance: Posts focus on distinct transcript themes
- Style consistency: All posts follow writing guidelines
- Template compliance: Proper structure and formatting
- File organization: Clear, navigable directory structure

## Troubleshooting

### MCP Issues
1. Check `.mcp.json` configuration
2. Verify AI agent has MCP support enabled
3. Restart AI agent if needed

### Page Access Problems
1. Confirm you're logged into Notion
2. Verify page ID is correct
3. Check page sharing permissions

### File System Errors
1. Ensure write permissions to project directory
2. Check available disk space
3. Verify path length limits (Windows)
4. Use Windows path separators (\\) for directory creation

### Content Processing Issues
1. Verify page contains sufficient text content (>50 words)
2. Check for mixed content types
3. Ensure content is in supported language

## Advanced Usage

### Custom Templates
Create custom Notion pages with:
- Post structure definitions
- Placeholder patterns
- Formatting guidelines

### Custom Style Guides
Define writing preferences:
- Tone and voice guidelines
- Formatting rules
- Content restrictions

### Batch Processing
For multiple transcripts:
1. Run command for each page ID
2. Use consistent naming conventions
3. Organize outputs by date or topic

## Integration Tips

### Workflow Integration
- Schedule regular processing of meeting transcripts
- Integrate with content calendar planning
- Use context parameter for meeting-specific insights

### Content Strategy
- Review generated posts for brand alignment
- Customize templates for different content types
- Track engagement metrics for optimization

This quickstart provides the essential information for using the Notion to LinkedIn post converter effectively with proper validation and error handling.
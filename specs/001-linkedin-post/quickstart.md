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
```bash
/Create-LinkedInPost 29f3c065308b8022aaecce22524bd32b
```

**Expected Output:**
```
🚀 Starting LinkedIn post creation...
📋 Processing Notion page: 29f3c065308b8022aaecce22524bd32b

✅ Step 1: Page access validated
✅ Step 2: Transcript extracted (2,847 words)
✅ Step 3: Themes identified (5 themes)
✅ Step 4: Posts generated (3/3)
✅ Step 5: Quality checks passed

📊 Completion Summary:
• Total words processed: 2,847
• Themes identified: 5
• Posts generated: 3
• Average post length: 1,247 characters
• Hashtag usage: 2-3 per post
• Template compliance: 100%
• Style guide compliance: 100%

📁 Posts saved to: data\linkedin-posts\meeting-transcripts\
```

### Custom Configuration
```bash
/Create-LinkedInPost 29f3c065308b8022aaecce22524bd32b -n 5 -t 8a1b2c3d4e5f6789abcdef1234567890 -w 9f8e7d6c5b4a3210fedcba9876543210 -c "Team meeting about Q4 planning"
```

**Expected Output:**
```
🚀 Starting LinkedIn post creation...
📋 Processing Notion page: 29f3c065308b8022aaecce22524bd32b
🔧 Custom configuration:
• Posts requested: 5
• Template: 8a1b2c3d4e5f6789abcdef1234567890
• Style guide: 9f8e7d6c5b4a3210fedcba9876543210
• Context: "Team meeting about Q4 planning"

✅ Step 1: Page access validated
✅ Step 2: Custom template loaded
✅ Step 3: Custom style guide loaded
✅ Step 4: Transcript extracted (2,847 words)
✅ Step 5: Themes identified with context (6 themes - Q4 focus)
✅ Step 6: Posts generated with custom settings (5/5)
✅ Step 7: Quality checks passed

📊 Completion Summary:
• Total words processed: 2,847
• Themes identified: 6 (Q4 focused)
• Posts generated: 5
• Template used: Custom (8a1b2c3d4e5f6789abcdef1234567890)
• Style guide used: Custom (9f8e7d6c5b4a3210fedcba9876543210)
• Context applied: Team meeting about Q4 planning
• Template compliance: 100%
• Style guide compliance: 100%
```

### Error Handling Examples

#### Invalid Page ID
```bash
/Create-LinkedInPost invalid-page-id
```

**Expected Output:**
```
❌ Error: Invalid Notion page ID format
Expected: 32-character alphanumeric string
Provided: invalid-page-id

💡 Solution:
1. Copy the page ID from your Notion page URL
2. The ID is the 32-character string after /page/ or /view/
3. Example: https://notion.so/your-workspace/Page-Title-29f3c065308b8022aaecce22524bd32b
```

#### MCP Unavailable
```bash
/Create-LinkedInPost 29f3c065308b8022aaecce22524bd32b
```

**Expected Output:**
```
❌ Error: Notion MCP is not available

🔧 Troubleshooting Steps:
1. Check if MCP server is running: mcp list
2. Restart MCP server: mcp restart notion
3. Verify MCP configuration: .mcp.json
4. Check network connection to Notion

📋 MCP Status Check:
• Server status: Offline
• Last check: 2025-11-03 10:30:15
• Configuration file: .mcp.json
```

## Processing Flow

### Phase 1: Validation
1. **MCP Check**: Verifies Notion MCP is available
2. **Page Access**: Validates page ID and permissions
3. **Content Check**: Ensures page contains transcript content

### Phase 2: Content Processing
1. **Transcript Save**: Saves raw content to `data/transcripts/{page-name}/transcript.md`
2. **Theme Extraction**: Analyzes content and saves to `data/transcripts/{page-name}/themes-analysis.md`
3. **Post Generation**: Creates individual posts in `data/linkedin-posts/{page-name}/`

### Phase 3: Quality Assurance
1. **Validation Checkpoints**: 6 validation checkpoints throughout the process
2. **Quality Checklist**: 8-point post-generation quality check
3. **Progress Reporting**: Detailed status updates with sub-tasks
4. **Statistics Generation**: Comprehensive completion summary

### Validation Checkpoints
- ✅ Checkpoint 1: MCP availability and page access
- ✅ Checkpoint 2: Template and style guide loading
- ✅ Checkpoint 3: Transcript extraction and validation
- ✅ Checkpoint 4: Theme extraction and analysis
- ✅ Checkpoint 5: Post generation with custom settings
- ✅ Checkpoint 6: Final quality assurance and compliance

### Quality Checklist (8-Point)
1. Character count validation (< 3000 characters)
2. Hashtag count validation (≤ 3 hashtags)
3. Template structure compliance
4. Style guide adherence
5. Content consistency across posts
6. Theme relevance and uniqueness
7. Call-to-action presence
8. Overall readability and engagement

## Output Structure

### Directory Layout
```
data/
├── transcripts/
│   └── meeting-transcripts/
│       ├── transcript.md
│       └── themes-analysis.md
└── linkedin-posts/
    └── meeting-transcripts/
        ├── leadership:team-motivation.md
        ├── productivity:meeting-efficiency.md
        └── strategy:q4-planning-insights.md
```

### Sample Generated Post
**File:** `data/linkedin-posts/meeting-transcripts/leadership:team-motivation.md`

```markdown
# Leadership: Team Motivation

## Key Insight
Effective team motivation stems from clear communication of vision and consistent recognition of individual contributions.

## Main Points
• Teams with clearly communicated goals show 45% higher engagement
• Regular recognition programs reduce turnover by 30%
• Autonomy and trust drive intrinsic motivation more than external rewards

## Call to Action
What strategies have you found most effective for keeping your team motivated? Share your thoughts below!

## Hashtags
#Leadership #TeamManagement #Motivation
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
- ✅ Comprehensive completion summary with statistics
- ✅ Progress reporting with detailed status updates

### Quality Metrics
- Theme relevance: Posts focus on distinct transcript themes
- Style consistency: All posts follow writing guidelines
- Template compliance: Proper structure and formatting
- File organization: Clear, navigable directory structure
- Validation success: All 6 checkpoints passed
- Quality assurance: 8-point checklist completed
- Error handling: Clear recovery procedures provided

### Performance Metrics
- Processing speed: ~2-3 minutes for 2000-3000 word transcripts
- Success rate: 95%+ for properly formatted content
- Error recovery: Automated retry with exponential backoff
- Cross-platform: Windows and Unix compatible

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
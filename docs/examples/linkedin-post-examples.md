# LinkedIn Post Creation Command Examples

This document provides comprehensive examples of using the `/Create-LinkedInPost` slash command for various scenarios and use cases.

## Basic Usage

### Example 1: Simple Post Generation
Generate 3 LinkedIn posts from a Notion page using default settings:

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

## Advanced Usage

### Example 2: Custom Number of Posts
Generate 5 posts instead of the default 3:

```bash
/Create-LinkedInPost 29f3c065308b8022aaecce22524bd32b -n 5
```

**Expected Output:**
```
🚀 Starting LinkedIn post creation...
📋 Processing Notion page: 29f3c065308b8022aaecce22524bd32b
🔧 Custom configuration: 5 posts requested

✅ Step 1: Page access validated
✅ Step 2: Transcript extracted (2,847 words)
✅ Step 3: Themes identified (7 themes)
✅ Step 4: Posts generated (5/5)
✅ Step 5: Quality checks passed

📊 Completion Summary:
• Total words processed: 2,847
• Themes identified: 7
• Posts generated: 5
• Average post length: 1,189 characters
• Hashtag usage: 2-3 per post
• Template compliance: 100%
• Style guide compliance: 100%
```

### Example 3: Custom Template
Use a specific template for different post structure:

```bash
/Create-LinkedInPost 29f3c065308b8022aaecce22524bd32b -t 8a1b2c3d4e5f6789abcdef1234567890
```

**Expected Output:**
```
🚀 Starting LinkedIn post creation...
📋 Processing Notion page: 29f3c065308b8022aaecce22524bd32b
🎨 Using custom template: 8a1b2c3d4e5f6789abcdef1234567890

✅ Step 1: Page access validated
✅ Step 2: Custom template loaded
✅ Step 3: Transcript extracted (2,847 words)
✅ Step 4: Themes identified (5 themes)
✅ Step 5: Posts generated with custom template (3/3)
✅ Step 6: Quality checks passed

📊 Completion Summary:
• Total words processed: 2,847
• Themes identified: 5
• Posts generated: 3
• Template used: Custom (8a1b2c3d4e5f6789abcdef1234567890)
• Template compliance: 100%
• Style guide compliance: 100%
```

### Example 4: Custom Style Guide
Apply a different writing style:

```bash
/Create-LinkedInPost 29f3c065308b8022aaecce22524bd32b -w 9f8e7d6c5b4a3210fedcba9876543210
```

**Expected Output:**
```
🚀 Starting LinkedIn post creation...
📋 Processing Notion page: 29f3c065308b8022aaecce22524bd32b
✍️ Using custom style guide: 9f8e7d6c5b4a3210fedcba9876543210

✅ Step 1: Page access validated
✅ Step 2: Custom style guide loaded
✅ Step 3: Transcript extracted (2,847 words)
✅ Step 4: Themes identified (5 themes)
✅ Step 5: Posts generated with custom style (3/3)
✅ Step 6: Quality checks passed

📊 Completion Summary:
• Total words processed: 2,847
• Themes identified: 5
• Posts generated: 3
• Style guide used: Custom (9f8e7d6c5b4a3210fedcba9876543210)
• Template compliance: 100%
• Style guide compliance: 100%
```

### Example 5: Context String
Provide additional context for theme extraction:

```bash
/Create-LinkedInPost 29f3c065308b8022aaecce22524bd32b -c "Focus on AI and machine learning insights"
```

**Expected Output:**
```
🚀 Starting LinkedIn post creation...
📋 Processing Notion page: 29f3c065308b8022aaecce22524bd32b
🎯 Context provided: "Focus on AI and machine learning insights"

✅ Step 1: Page access validated
✅ Step 2: Transcript extracted (2,847 words)
✅ Step 3: Themes identified with context (4 themes - AI/ML focus)
✅ Step 4: Posts generated (3/3)
✅ Step 5: Quality checks passed

📊 Completion Summary:
• Total words processed: 2,847
• Themes identified: 4 (AI/ML focused)
• Posts generated: 3
• Context applied: AI and machine learning insights
• Template compliance: 100%
• Style guide compliance: 100%
```

## Combined Parameters

### Example 6: Full Custom Configuration
Use all parameters together for maximum control:

```bash
/Create-LinkedInPost 29f3c065308b8022aaecce22524bd32b -n 4 -t 8a1b2c3d4e5f6789abcdef1234567890 -w 9f8e7d6c5b4a3210fedcba9876543210 -c "Emphasize practical business applications"
```

**Expected Output:**
```
🚀 Starting LinkedIn post creation...
📋 Processing Notion page: 29f3c065308b8022aaecce22524bd32b
🔧 Custom configuration:
• Posts requested: 4
• Template: 8a1b2c3d4e5f6789abcdef1234567890
• Style guide: 9f8e7d6c5b4a3210fedcba9876543210
• Context: "Emphasize practical business applications"

✅ Step 1: Page access validated
✅ Step 2: Custom template loaded
✅ Step 3: Custom style guide loaded
✅ Step 4: Transcript extracted (2,847 words)
✅ Step 5: Themes identified with context (6 themes - business focus)
✅ Step 6: Posts generated with custom settings (4/4)
✅ Step 7: Quality checks passed

📊 Completion Summary:
• Total words processed: 2,847
• Themes identified: 6 (business focused)
• Posts generated: 4
• Template used: Custom (8a1b2c3d4e5f6789abcdef1234567890)
• Style guide used: Custom (9f8e7d6c5b4a3210fedcba9876543210)
• Context applied: Practical business applications
• Template compliance: 100%
• Style guide compliance: 100%
```

## Error Handling Examples

### Example 7: Invalid Page ID
Handle invalid Notion page ID:

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

### Example 8: MCP Unavailable
Handle when Notion MCP is not available:

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

### Example 9: Page Access Denied
Handle permission issues:

```bash
/Create-LinkedInPost 29f3c065308b8022aaecce22524bd32b
```

**Expected Output:**
```
❌ Error: Cannot access Notion page
Page ID: 29f3c065308b8022aaecce22524bd32b
Issue: Permission denied

🔧 Solutions:
1. Verify you have access to the page
2. Check if the page is shared with your workspace
3. Ensure Notion integration has proper permissions
4. Try accessing the page directly in Notion first

📋 Access Check Results:
• Page exists: ✅
• Permission: ❌
• Workspace access: ✅
• Integration status: ✅
```

## Generated Output Examples

### Example 10: Generated Post Structure
Sample of a generated LinkedIn post:

**File:** `data/linkedin-posts/meeting-transcripts/ai-innovation:future-of-work.md`

```markdown
# AI Innovation: The Future of Work

## Key Insight
The integration of AI in workplace processes is not about replacing humans, but augmenting our capabilities to focus on what truly matters - creativity, strategic thinking, and human connection.

## Main Points
• AI tools can reduce repetitive tasks by up to 70%, freeing valuable time for high-value work
• Companies embracing AI augmentation see 40% improvement in employee satisfaction
• The future workforce will require hybrid skills combining technical literacy with emotional intelligence

## Call to Action
How is your organization preparing for the AI-augmented workplace? Share your experiences in the comments below.

## Hashtags
#AI #FutureOfWork #DigitalTransformation
```

## Best Practices

### Usage Tips
1. **Always validate page ID** - Ensure the Notion page ID is correct and accessible
2. **Use context strings** - Provide specific focus areas for better theme extraction
3. **Custom templates** - Create templates for different types of content (thought leadership, case studies, etc.)
4. **Batch processing** - Process multiple pages from the same meeting series for consistent messaging

### Content Guidelines
1. **Minimum content** - Ensure transcripts have at least 500 words for meaningful theme extraction
2. **Clear structure** - Well-organized transcripts produce better themes and posts
3. **Quality over quantity** - Fewer high-quality posts are better than many generic ones

### File Management
1. **Organize by date** - Use consistent naming conventions for easy retrieval
2. **Version control** - Keep track of different post variations
3. **Backup important** - Archive successful posts for future reference

## Troubleshooting Quick Reference

| Error | Cause | Solution |
|-------|-------|----------|
| Invalid page ID | Wrong format | Copy 32-char ID from URL |
| MCP unavailable | Server down | Restart MCP server |
| Permission denied | No access | Check page sharing |
| No content found | Empty page | Verify transcript exists |
| Template not found | Wrong ID | Verify template page ID |
| Style guide error | Invalid guide | Check style guide access |

## Integration Examples

### With Content Calendar
```bash
# Generate posts for weekly content
/Create-LinkedInPost weekly-meeting-id -n 3 -c "Weekly insights and trends"
```

### With Campaign Series
```bash
# Generate campaign-specific posts
/Create-LinkedInPost campaign-research-id -t campaign-template -w campaign-style -n 5
```

### With Thought Leadership
```bash
# Generate executive thought leadership posts
/Create-LinkedInPost executive-interview-id -c "Leadership and innovation" -n 2
```

---

*For more information, see the [quickstart guide](../quickstart.md) or [main documentation](../README.md).*
---
argument-hint: [page-id] [-n number] [-t template-id] [-w style-guide-id] [-c context]
description: Convert Notion page transcripts to LinkedIn posts using phased validation
---

# Create-LinkedInPost

Convert a Notion page containing meeting transcripts into multiple LinkedIn post variations using specified templates and style guides.

## Parameters

- **page-id** (required): Notion page UUID containing transcript content
- **-n [number]** (optional): Number of posts to generate (default:3)
- **-t [template-id]** (optional): Custom template page ID (uses default if not specified)
- **-w [style-guide-id]** (optional): Custom style guide page ID (uses default if not specified)
- **-c [context]** (optional): Additional context string for content generation

## Implementation

### Step 1: Check Notion MCP Availability
First, verify that the Notion MCP server is accessible using the MCP tools available to this AI agent.

**If MCP is not available:**
```
❌ Notion MCP is not available
Please ensure Notion MCP is configured for your AI agent
```
Stop execution immediately.

### Step 2: Validate Notion Page Access
Attempt to access the provided Notion page using the page-id through Notion MCP.

**If page is not found:**
```
❌ No page found with the provided ID
Please verify the page ID is correct and you have access permissions
```

**If access is denied:**
```
❌ You don't have permission to access this page
Please ensure you're logged into Notion and have page access
```

**If rate limited:**
```
❌ Too many requests to Notion API
Please wait a few minutes and try again
```

### Step 3: Extract and Save Transcript Content
If page access is successful, extract the full text content from the Notion page.

**Create timestamp for unique directory names:**
```bash
TIMESTAMP=$(date +"%Y-%m-%d-%H%M%S")
```

**Create directory structure:**
```bash
mkdir -p "data/transcripts/{page-name}-${TIMESTAMP}"
mkdir -p "data/linkedin-posts/{page-name}-${TIMESTAMP}"
```

**Save transcript with metadata:**
```markdown
# Transcript: {page-title}

**Source**: Notion Page ID: {page-id}  
**Extracted**: {timestamp}  
**Content Length**: {character-count} characters

{raw-transcript-content}
```

### Step 4: Analyze Themes and Topics
Use the AI model to analyze the transcript content and extract:

**Key Themes Analysis:**
- Identify 3-5 main themes from the transcript
- For each theme: name, description, significance, content references
- Focus on actionable insights and interesting discussion points

**Interesting Topics:**
- Extract notable topics that would make good LinkedIn content
- Identify why each topic is interesting for the audience

**Post Generation Notes:**
- Specific angles to explore for each theme
- Tone considerations based on content type
- Target audience insights

**Save analysis to:** `data/transcripts/{page-name}-${TIMESTAMP}/themes-analysis.md`

### Step 5: Generate LinkedIn Posts ONE BY ONE
Using the extracted themes, generate individual LinkedIn posts:

**For each theme:**
1. Fetch template structure (default or custom via Notion MCP)
2. Fetch style guide (default or custom via Notion MCP)
3. Generate post content following template and style guidelines
4. Ensure post is under 3000 characters
5. Include appropriate hashtags (max 3)
6. Save with naming: `{theme}:{post-name}.md`

**Post file structure:**
```markdown
# {post-title}

**Theme**: {theme-name}  
**Generated**: {timestamp}  
**Character Count**: {count}

{post-content}
```

**Apply constraints:**
- Character count < 3000 for LinkedIn compliance
- Hashtag count ≤ 3 for best practices
- Follow template structure exactly
- Apply style guide consistently

## Default Resources

- **Style Guide**: https://www.notion.so/infimagames/Octavian-s-Writing-Style-Guide-9e7ae38cdf164ed88c09f442332e1e46
- **Template**: https://www.notion.so/infimagames/LinkedIn-Post-Template-29f3c065308b8022aaecce22524bd32b

## Error Handling

The command will terminate gracefully with clear error messages if:
- Notion MCP is not available
- Notion page is inaccessible or doesn't exist
- Page contains no suitable transcript content
- File operations fail due to permissions or disk space

## Examples

```bash
# Basic conversion
/Create-LinkedInPost a1b2c3d4-e5f6-7890-abcd-ef1234567890

# Custom configuration
/Create-LinkedInPost a1b2c3d4-e5f6-7890-abcd-ef1234567890 -n 5 -t template-id -w style-id -c "Team meeting about Q4 planning"
```

## Output Structure

```
data/
├── transcripts/
│   └── {page-name}/
│       ├── transcript.md
│       └── themes-analysis.md
└── linkedin-posts/
    └── {page-name}/
        ├── theme1:post1.md
        ├── theme2:post2.md
        └── theme3:post3.md
```

## Output Structure

```
data/
├── transcripts/
│   └── {page-name}/
│       ├── transcript.md
│       └── themes-analysis.md
└── linkedin-posts/
    └── {page-name}/
        ├── theme1:post1.md
        ├── theme2:post2.md
        └── theme3:post3.md
```
# Data Model: Notion to LinkedIn Post Converter

**Feature**: 001-linkedin-post  
**Date**: 2025-11-03  
**Phase**: 1 - Design & Contracts

## Core Entities

### NotionPage
Represents the source Notion page containing transcript content.

**Attributes**:
- `page_id`: string - Unique identifier for the Notion page
- `title`: string - Page title extracted from Notion
- `content`: text - Raw transcript content from the page
- `is_accessible`: boolean - Whether the page can be accessed via MCP
- `access_error`: string - Error message if access fails
- `content_type`: string - Type of content (transcript, mixed, other)
- `word_count`: integer - Total word count for processing decisions

**Validation Rules**:
- `page_id` must be provided and non-empty
- `is_accessible` must be true before processing
- `content` must contain meaningful text (word_count > 50)

**State Transitions**:
1. `INITIAL` → `VALIDATING` (MCP access check)
2. `VALIDATING` → `ACCESSIBLE` (page fetch successful)
3. `VALIDATING` → `INACCESSIBLE` (page fetch failed)
4. `ACCESSIBLE` → `PROCESSING` (content extraction begins)

### TranscriptFile
Represents the saved transcript file on the local file system.

**Attributes**:
- `source_page_id`: string - Reference to originating NotionPage
- `file_path`: string - Full path to saved transcript file
- `content`: text - Raw transcript content
- `created_at`: datetime - File creation timestamp
- `file_size`: integer - Size in bytes for monitoring
- `encoding`: string - Text encoding used (UTF-8)

**Validation Rules**:
- `source_page_id` must match an accessible NotionPage
- `file_path` must be writable and valid
- `content` must be non-empty
- `file_size` must be reasonable (< 50MB for single transcript)

**File Structure**:
```
data/transcripts/{page-name}-{timestamp}/transcript.md
```

### ThemeAnalysis
Represents the AI-generated analysis of transcript themes and topics.

**Attributes**:
- `source_transcript_file`: string - Path to source transcript file
- `analysis_file_path`: string - Path to saved analysis file
- `key_themes`: array[Theme] - Extracted themes from content
- `interesting_topics`: array[Topic] - Notable topics for post generation
- `insights`: array[string] - Key insights and observations
- `post_generation_notes`: array[string] - Guidance for creating posts
- `created_at`: datetime - Analysis creation timestamp
- `confidence_score`: float - AI confidence in theme extraction (0-1)

**Theme Object**:
- `name`: string - Theme name
- `description`: string - Theme description
- `significance`: string - Why this theme matters
- `content_references`: array[string] - Supporting content excerpts

**Topic Object**:
- `name`: string - Topic name
- `interest_reason`: string - Why this topic is interesting
- `discussion_points`: array[string] - Key points about the topic

**Validation Rules**:
- `source_transcript_file` must exist and be readable
- `key_themes` array must have 1-5 themes for focused content
- `interesting_topics` must provide actionable content
- `confidence_score` must be > 0.7 for reliable analysis

**File Structure**:
```
data/transcripts/{page-name}-{timestamp}/themes-analysis.md
```

### LinkedInPost
Represents an individual generated LinkedIn post draft.

**Attributes**:
- `source_theme`: string - Theme that inspired this post
- `template_used`: string - Template identifier (default or custom)
- `content`: text - Generated post content
- `file_path`: string - Path to saved post file
- `post_name`: string - Descriptive name for the post
- `character_count`: integer - Length for LinkedIn compliance
- `hashtag_count`: integer - Number of hashtags used
- `created_at`: datetime - Post creation timestamp
- `theme_focus`: string - Specific angle or focus of this post

**Validation Rules**:
- `source_theme` must reference a valid theme from analysis
- `character_count` must be < 3000 for LinkedIn compliance
- `content` must follow template structure
- `hashtag_count` should be ≤ 3 for best practices

**File Structure**:
```
data/linkedin-posts/{page-name}-{timestamp}/{theme}:{post-name}.md
```

## Entity Relationships

```
NotionPage (1) → (1) TranscriptFile
NotionPage (1) → (1) ThemeAnalysis
ThemeAnalysis (1) → (many) LinkedInPost
TranscriptFile (1) → (1) ThemeAnalysis
```

## Data Flow

### Phase 1 Processing Flow

1. **Input Validation**
   ```
   User Input → NotionPage
   page_id → validate_mcp_access()
   page_id → fetch_page_content()
   ```

2. **File Creation**
   ```
   NotionPage.content → TranscriptFile
   TranscriptFile.file_path → create_directory_structure()
   TranscriptFile.content → save_to_file()
   ```

3. **Theme Analysis**
   ```
   TranscriptFile.content → AI Model → ThemeAnalysis
   ThemeAnalysis → save_analysis_file()
   ```

4. **Post Generation** (Phase 2)
   ```
   ThemeAnalysis.key_themes → LinkedInPost (multiple)
   LinkedInPost.content → save_post_files()
   ```

## File System Schema

### Directory Structure
```
data/
├── transcripts/
│   └── {page-name}-{YYYY-MM-DD-HHMMSS}/
│       ├── transcript.md
│       └── themes-analysis.md
└── linkedin-posts/
    └── {page-name}-{YYYY-MM-DD-HHMMSS}/
        ├── theme1:post1.md
        ├── theme2:post2.md
        └── theme3:post3.md
```

### File Naming Conventions

**Transcript Files**:
- Format: `{sanitized-page-name}-{timestamp}/transcript.md`
- Sanitization: Replace spaces/special chars with hyphens
- Timestamp: YYYY-MM-DD-HHMMSS for uniqueness

**Analysis Files**:
- Format: `{sanitized-page-name}-{timestamp}/themes-analysis.md`
- Location: Same directory as transcript

**Post Files**:
- Format: `{sanitized-page-name}-{timestamp}/{theme}:{post-name}.md`
- Theme: Short theme identifier (2-3 words)
- Post Name: Descriptive title (3-5 words)

## Error Handling Data

### Error Types and Responses

**MCP Access Error**:
```json
{
  "error_type": "MCP_UNAVAILABLE",
  "message": "Notion MCP is not available",
  "suggestion": "Please ensure Notion MCP is configured for your AI agent",
  "terminate": true
}
```

**Page Access Error**:
```json
{
  "error_type": "PAGE_INACCESSIBLE",
  "message": "Cannot access Notion page with provided ID",
  "suggestion": "Verify page ID and ensure you have access permissions",
  "terminate": true
}
```

**Content Error**:
```json
{
  "error_type": "NO_CONTENT",
  "message": "Page contains no suitable transcript content",
  "suggestion": "Ensure page contains text content for processing",
  "terminate": true
}
```

## Validation Constraints

### Input Validation
- Page ID: Non-empty string, reasonable length (1-100 chars)
- Optional parameters: Valid Notion page IDs if provided

### Content Validation
- Minimum word count: 50 words for meaningful processing
- Maximum file size: 50MB for single transcript
- Supported languages: English (Phase 1), multilingual (future)

### Output Validation
- Theme count: 1-5 themes for focused content
- Post length: < 3000 characters per LinkedIn limits
- File paths: Valid, writable, cross-platform compatible

This data model provides the foundation for implementing the phased approach with clear validation, structured file outputs, and comprehensive error handling as specified in user requirements.
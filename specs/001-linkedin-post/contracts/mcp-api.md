# API Contracts: Notion MCP Integration

**Feature**: 001-linkedin-post  
**Date**: 2025-11-03  
**Version**: 1.0

## MCP Server Configuration

### Server Details
- **Name**: `notion`
- **Type**: `http`
- **URL**: `https://mcp.notion.com/mcp`
- **Configuration**: Defined in `.mcp.json`

## API Operations

### 1. Check MCP Availability

**Purpose**: Verify that the Notion MCP server is accessible before attempting operations.

**Operation**: `mcp.notion.ping` or equivalent availability check

**Expected Response**:
```json
{
  "success": true,
  "available": true,
  "server": "notion",
  "timestamp": "2025-11-03T10:30:00Z"
}
```

**Error Response**:
```json
{
  "success": false,
  "available": false,
  "error": "MCP_UNAVAILABLE",
  "message": "Notion MCP server is not accessible",
  "suggestion": "Please ensure Notion MCP is configured for your AI agent"
}
```

### 2. Validate Page Access

**Purpose**: Check if a specific Notion page ID is valid and accessible.

**Operation**: `mcp.notion.get_page`

**Input Parameters**:
```json
{
  "page_id": "string - Notion page UUID"
}
```

**Success Response**:
```json
{
  "success": true,
  "page": {
    "id": "page-uuid",
    "title": "Page Title",
    "created_time": "2025-11-01T10:00:00Z",
    "last_edited_time": "2025-11-03T09:30:00Z",
    "url": "https://notion.so/page-uuid"
  },
  "accessible": true
}
```

**Error Responses**:

**Page Not Found**:
```json
{
  "success": false,
  "error": "PAGE_NOT_FOUND",
  "message": "No page found with the provided ID",
  "suggestion": "Verify the page ID is correct and you have access permissions"
}
```

**Access Denied**:
```json
{
  "success": false,
  "error": "ACCESS_DENIED",
  "message": "You don't have permission to access this page",
  "suggestion": "Ensure you're logged into Notion and have page access"
}
```

**Rate Limited**:
```json
{
  "success": false,
  "error": "RATE_LIMITED",
  "message": "Too many requests to Notion API",
  "suggestion": "Wait a few minutes and try again"
}
```

### 3. Extract Page Content

**Purpose**: Retrieve the full text content from a Notion page for processing.

**Operation**: `mcp.notion.get_page_content`

**Input Parameters**:
```json
{
  "page_id": "string - Notion page UUID"
}
```

**Success Response**:
```json
{
  "success": true,
  "content": {
    "text": "Full page text content including transcripts",
    "word_count": 1500,
    "character_count": 8500,
    "blocks": [
      {
        "type": "paragraph",
        "content": "Block text content"
      }
    ]
  },
  "page_info": {
    "title": "Page Title",
    "id": "page-uuid"
  }
}
```

**Error Responses**:

**No Content**:
```json
{
  "success": false,
  "error": "NO_CONTENT",
  "message": "Page contains no text content",
  "suggestion": "Ensure the page contains transcript or text content for processing"
}
```

**Content Too Large**:
```json
{
  "success": false,
  "error": "CONTENT_TOO_LARGE",
  "message": "Page content exceeds processing limits",
  "suggestion": "Consider splitting content into smaller pages"
}
```

## Template and Style Guide Access

### Default Style Guide
- **URL**: https://www.notion.so/infimagames/Octavian-s-Writing-Style-Guide-9e7ae38cdf164ed88c09f442332e1e46
- **Purpose**: Default writing guidelines for post generation
- **Fallback**: Use basic professional writing style if inaccessible

### Default Template
- **URL**: https://www.notion.so/infimagames/LinkedIn-Post-Template-29f3c065308b8022aaecce22524bd32b
- **Purpose**: Default structure for LinkedIn posts
- **Fallback**: Use standard LinkedIn post format if inaccessible

## Error Handling Contract

### Error Response Format
All error responses must follow this structure:

```json
{
  "success": false,
  "error": "ERROR_CODE",
  "message": "Human-readable error description",
  "suggestion": "Actionable guidance for user",
  "terminate": boolean,
  "retry_possible": boolean,
  "technical_details": "Optional technical context"
}
```

### Error Codes

| Error Code | Description | Terminate | Retry |
|------------|-------------|-----------|-------|
| MCP_UNAVAILABLE | Notion MCP server not accessible | Yes | No |
| PAGE_NOT_FOUND | Invalid page ID | Yes | No |
| ACCESS_DENIED | No permission to access page | Yes | No |
| RATE_LIMITED | API rate limit exceeded | Yes | Yes (after delay) |
| NO_CONTENT | No suitable content found | Yes | No |
| CONTENT_TOO_LARGE | Content exceeds limits | Yes | No |
| NETWORK_ERROR | Connection issues | Yes | Yes |
| INVALID_INPUT | Malformed input parameters | Yes | No |

## Integration Guidelines

### Request Flow
1. **Check MCP Availability** → If failed, terminate with guidance
2. **Validate Page Access** → If failed, terminate with specific error
3. **Extract Content** → If failed, terminate with content-specific error
4. **Process Content** → Only if all above succeed

### Retry Logic
- Only retry for `RATE_LIMITED` and `NETWORK_ERROR`
- Use exponential backoff: 1s, 2s, 4s, 8s maximum
- Maximum 3 retry attempts
- Provide user feedback during retries

### Timeout Constraints
- MCP availability check: 5 seconds
- Page validation: 10 seconds
- Content extraction: 30 seconds
- Total operation timeout: 60 seconds

## Cross-Agent Compatibility

### OpenCode
- Uses standard MCP protocol
- File operations through bash commands
- Error handling through structured responses

### Claude Code
- MCP integration through built-in MCP client
- File operations through standard tools
- Compatible with same error format

### GitHub Copilot
- MCP access through VS Code extension
- File operations through workspace API
- Error responses in compatible format

This contract ensures consistent behavior across all target AI agents while providing clear error handling and user guidance.
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

**Progress Reporting Framework:**
```bash
function show_progress() {
  local step="$1"
  local status="$2"
  local details="$3"
  local total_steps=6
  
  echo ""
  echo "📍 Step $step/$total_steps: $status"
  if [[ -n "$details" ]]; then
    echo "📋 $details"
  fi
  echo "⏳ Progress: $((step * 100 / total_steps))% complete"
  echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
}

function show_sub_progress() {
  local subtask="$1"
  local current="$2"
  local total="$3"
  
  echo "   🔄 $subtask ($current/$total)"
}
```

**Progress Tracking:**
```bash
show_progress 1 "MCP Availability Check" "Verifying Notion MCP server connectivity"
show_sub_progress "Testing MCP connection" 1 3
show_sub_progress "Validating authentication" 2 3
show_sub_progress "Checking API permissions" 3 3
```

**Retry Logic for Rate Limits:**
```bash
MAX_RETRIES=3
RETRY_DELAY=30

function retry_mcp_operation() {
  local operation="$1"
  local operation_type="$2"
  local attempt=1
  
  while [[ $attempt -le $MAX_RETRIES ]]; do
    echo "🔄 Attempt $attempt of $MAX_RETRIES: $operation_type"
    
    if eval "$operation"; then
      echo "✅ $operation_type successful on attempt $attempt"
      return 0
    fi
    
    # Specific recovery procedures based on operation type
    case $operation_type in
      "MCP Availability Check")
        echo "💡 Recovery: Check .mcp.json configuration and restart AI agent"
        ;;
      "Page Access")
        echo "💡 Recovery: Verify page ID, check login status, confirm permissions"
        ;;
      "Content Extraction")
        echo "💡 Recovery: Check if page has text content, try different page"
        ;;
      "Template Fetch")
        echo "💡 Recovery: Using default template instead"
        return 0  # Continue with default
        ;;
      "Style Guide Fetch")
        echo "💡 Recovery: Using default style guide instead"
        return 0  # Continue with default
        ;;
    esac
    
    if [[ $attempt -lt $MAX_RETRIES ]]; then
      echo "⏱️ Waiting $RETRY_DELAY seconds before retry..."
      sleep $RETRY_DELAY
      RETRY_DELAY=$((RETRY_DELAY * 2))  # Exponential backoff
    fi
    
    attempt=$((attempt + 1))
  done
  
  echo "❌ $operation_type failed after $MAX_RETRIES attempts"
  
  # Final recovery suggestions
  case $operation_type in
    "MCP Availability Check")
      echo "🔧 Final Recovery Steps:"
      echo "   1. Restart your AI agent completely"
      echo "   2. Check internet connection to notion.com"
      echo "   3. Verify .mcp.json syntax and API key"
      echo "   4. Try a different AI agent if available"
      ;;
    "Page Access")
      echo "🔧 Final Recovery Steps:"
      echo "   1. Copy page ID directly from Notion URL"
      echo "   2. Check if you're logged into correct Notion account"
      echo "   3. Request page access from owner"
      echo "   4. Try with a different page you own"
      ;;
  esac
  
  return 1
}
```

**If MCP is not available:**
```
❌ Notion MCP is not available

🔧 Troubleshooting Steps:
1. Check if .mcp.json exists in project root
2. Verify Notion MCP server configuration:
   - Server URL: https://mcp.notion.com/mcp
   - Authentication token is valid
3. Restart your AI agent (OpenCode/Claude Code/GitHub Copilot)
4. Check network connectivity to notion.com

📋 Configuration Example (.mcp.json):
{
  "mcpServers": {
    "notion": {
      "command": "npx",
      "args": ["-y", "@notionhq/mcp-server"],
      "env": {
        "NOTION_API_KEY": "your-api-key-here"
      }
    }
  }
}

📚 For setup help: https://docs.notion.com/mcp-setup
```
Stop execution immediately.

### Step 2: Parse and Validate Parameters
Parse the command arguments to extract configuration parameters:

**Parameter Parsing:**
```bash
# Default values
POST_COUNT=3
TEMPLATE_ID=""
STYLE_GUIDE_ID=""
CONTEXT_STRING=""

# Parse arguments (simplified parsing logic)
for arg in "$@"; do
  case $arg in
    -n)
      POST_COUNT="$2"
      shift 2
      ;;
    -t)
      TEMPLATE_ID="$2"
      shift 2
      ;;
    -w)
      STYLE_GUIDE_ID="$2"
      shift 2
      ;;
    -c)
      CONTEXT_STRING="$2"
      shift 2
      ;;
  esac
done

# Validate post count is a reasonable number (1-10)
if [[ "$POST_COUNT" -lt 1 || "$POST_COUNT" -gt 10 ]]; then
  echo "❌ Invalid post count: $POST_COUNT"
  echo "💡 Post count must be between 1 and 10"
  echo "📝 Example: /Create-LinkedInPost page-id -n 5"
  exit 1
fi

# Validate template ID format if provided
if [[ -n "$TEMPLATE_ID" ]]; then
  if [[ ! "$TEMPLATE_ID" =~ ^[a-f0-9]{8}-[a-f0-9]{4}-[a-f0-9]{4}-[a-f0-9]{4}-[a-f0-9]{12}$ ]]; then
    echo "❌ Invalid template ID format: $TEMPLATE_ID"
    echo "💡 Template ID must be a valid Notion page UUID"
    echo "📝 Example: /Create-LinkedInPost page-id -t a1b2c3d4-e5f6-7890-abcd-ef1234567890"
    exit 1
  fi
fi

# Validate style guide ID format if provided
if [[ -n "$STYLE_GUIDE_ID" ]]; then
  if [[ ! "$STYLE_GUIDE_ID" =~ ^[a-f0-9]{8}-[a-f0-9]{4}-[a-f0-9]{4}-[a-f0-9]{4}-[a-f0-9]{12}$ ]]; then
    echo "❌ Invalid style guide ID format: $STYLE_GUIDE_ID"
    echo "💡 Style guide ID must be a valid Notion page UUID"
    echo "📝 Example: /Create-LinkedInPost page-id -w a1b2c3d4-e5f6-7890-abcd-ef1234567890"
    exit 1
  fi
fi

# Validate context string length if provided
if [[ -n "$CONTEXT_STRING" ]]; then
  if [[ ${#CONTEXT_STRING} -gt 500 ]]; then
    echo "❌ Context string too long: ${#CONTEXT_STRING} characters"
    echo "💡 Context string must be 500 characters or less"
    echo "📝 Please provide a more concise context description"
    exit 1
  fi
fi

echo "✅ Parameter validation successful"
echo "📊 Configuration: Posts=$POST_COUNT, Template=${TEMPLATE_ID:-default}, Style Guide=${STYLE_GUIDE_ID:-default}, Context=${CONTEXT_STRING:-none}"

**Validation Checkpoint 1: Parameters**
```bash
echo "🔍 Validation Checkpoint 1/6: Parameters"
if [[ $POST_COUNT =~ ^[0-9]+$ && $POST_COUNT -ge 1 && $POST_COUNT -le 10 ]]; then
  echo "✅ Post count validated: $POST_COUNT"
else
  echo "❌ Invalid post count"
  exit 1
fi

if [[ -z "$TEMPLATE_ID" || "$TEMPLATE_ID" =~ ^[a-f0-9-]{36}$ ]]; then
  echo "✅ Template ID validated: ${TEMPLATE_ID:-default}"
else
  echo "❌ Invalid template ID format"
  exit 1
fi

echo "✅ Checkpoint 1 passed"
```

### Step 3: Validate Notion Page Access
Attempt to access the provided Notion page using the page-id through Notion MCP.

**Progress Tracking:**
```bash
show_progress 2 "Page Access Validation" "Validating Notion page ID and permissions"
show_sub_progress "Checking page ID format" 1 4
show_sub_progress "Testing page accessibility" 2 4
show_sub_progress "Validating read permissions" 3 4
show_sub_progress "Confirming page content exists" 4 4
```

**If page is not found:**
```
❌ No page found with the provided ID

🔍 Page ID Verification:
1. Copy the full page ID from Notion URL:
   - URL: https://notion.so/your-workspace/a1b2c3d4-e5f6-7890-abcd-ef1234567890
   - ID: a1b2c3d4-e5f6-7890-abcd-ef1234567890
2. Ensure the ID is 36 characters with proper format
3. Check if the page exists in your Notion workspace
4. Verify the page hasn't been deleted or moved

📋 Common Issues:
- Missing characters from copied ID
- Wrong workspace (page in different Notion account)
- Page URL shortened (use full URL)
```

**If access is denied:**
```
❌ You don't have permission to access this page

🔐 Permission Troubleshooting:
1. Check if you're logged into the correct Notion account
2. Verify page sharing settings:
   - Private: Only you can access
   - Workspace: Anyone in workspace can access
   - Web: Anyone with link can access
3. Request access from page owner if needed
4. Check if your API token has proper permissions

📋 API Token Requirements:
- Must have read_content capability
- Must have access to the workspace containing the page
- Token must be active and not expired
```

**If rate limited:**
```
❌ Too many requests to Notion API

⏱️ Rate Limit Handling:
1. Wait 1-2 minutes before retrying
2. Notion API limits: ~3 requests per second
3. Consider batching operations if processing multiple pages
4. Upgrade to Notion Plus/Pro for higher limits

🔄 Retry Strategy:
- First retry: Wait 30 seconds
- Second retry: Wait 2 minutes
- Third retry: Wait 5 minutes
- Contact support if limits persist
```

**If network error occurs:**
```
❌ Network connection error

🌐 Network Troubleshooting:
1. Check internet connectivity:
   - Ping: ping notion.com
   - DNS: nslookup notion.com
2. Verify firewall/antivirus settings:
   - Allow outbound connections to notion.com
   - Check corporate network restrictions
3. Test alternative connection:
   - Try different network (WiFi/ethernet)
   - Use VPN if on restricted network
4. Check proxy settings:
   - Verify HTTP_PROXY/HTTPS_PROXY environment variables
   - Test with direct connection

🔧 Connection Tests:
- Browser: https://notion.so (should load)
- API: curl -I https://mcp.notion.com/mcp
- MCP: Test with simple MCP command

📡 If issues persist:
- Contact network administrator
- Check Notion status page: https://status.notion.com
- Try again in 10-15 minutes
```

### Step 3: Extract and Save Transcript Content
If page access is successful, extract the full text content from the Notion page.

**Progress Tracking:**
```bash
show_progress 3 "Content Extraction" "Extracting and validating transcript content"
show_sub_progress "Fetching page content" 1 5
show_sub_progress "Analyzing content structure" 2 5
show_sub_progress "Validating minimum requirements" 3 5
show_sub_progress "Creating output directories" 4 5
show_sub_progress "Saving transcript file" 5 5
```

**Create timestamp for unique directory names:**
```bash
TIMESTAMP=$(date +"%Y-%m-%d-%H%M%S")

# Extract and sanitize page title from Notion
PAGE_TITLE=$(mcp.notion.get_page --page_id="$1" | jq -r '.title // "Untitled"')
PAGE_NAME=$(echo "$PAGE_TITLE" | \
  sed 's/[^a-zA-Z0-9\s-]/ /g' | \  # Replace special chars with spaces
  sed 's/\s\+/ /g' | \                # Multiple spaces to single
  sed 's/^\s\+//' | \                 # Trim leading spaces
  sed 's/\s\+$//' | \                 # Trim trailing spaces
  tr '[:upper:]' '[:lower:]' | \      # Convert to lowercase
  sed 's/\s/-/g' | \                  # Spaces to hyphens
  sed 's/--\+/-/g' | \                # Multiple hyphens to single
  sed 's/^-//' | \                    # Remove leading hyphen
  sed 's/-$//' | \                    # Remove trailing hyphen
  cut -c1-50)                         # Limit to 50 characters

# Fallback to page ID if title extraction fails
if [[ -z "$PAGE_NAME" || "$PAGE_NAME" == "untitled" ]]; then
  PAGE_NAME="notion-page-$(echo "$1" | cut -c1-8)"
fi

echo "📁 Directory name: $PAGE_NAME-$TIMESTAMP"
```

**Create directory structure:**
```bash
TRANSCRIPT_DIR="data\\transcripts\\${PAGE_NAME}-${TIMESTAMP}"
POSTS_DIR="data\\linkedin-posts\\${PAGE_NAME}-${TIMESTAMP}"

# Check if directories already exist and handle conflicts
if [[ -d "$TRANSCRIPT_DIR" || -d "$POSTS_DIR" ]]; then
  echo "⚠️ Directories already exist for this timestamp"
  echo "📁 Transcript: $TRANSCRIPT_DIR"
  echo "📁 Posts: $POSTS_DIR"
  echo ""
  echo "💡 Options:"
  echo "   1. Use existing directories (overwrite files)"
  echo "   2. Generate new timestamp"
  echo "   3. Cancel operation"
  echo ""
  read -p "Choose option (1/2/3): " choice
  
  case $choice in
    1)
      echo "✅ Using existing directories"
      ;;
    2)
      TIMESTAMP=$(date +"%Y-%m-%d-%H%M%S")
      TRANSCRIPT_DIR="data\\transcripts\\${PAGE_NAME}-${TIMESTAMP}"
      POSTS_DIR="data\\linkedin-posts\\${PAGE_NAME}-${TIMESTAMP}"
      echo "✅ Generated new timestamp: $TIMESTAMP"
      ;;
    3)
      echo "❌ Operation cancelled by user"
      exit 1
      ;;
    *)
      echo "❌ Invalid choice. Operation cancelled."
      exit 1
      ;;
  esac
fi

# Create directories with error handling
mkdir -p "$TRANSCRIPT_DIR" || {
  echo "❌ Failed to create transcript directory: $TRANSCRIPT_DIR"
  echo "💡 Check permissions and disk space"
  exit 1
}

mkdir -p "$POSTS_DIR" || {
  echo "❌ Failed to create posts directory: $POSTS_DIR"
  echo "💡 Check permissions and disk space"
  exit 1
}

echo "✅ Directories created successfully"
echo "📁 Transcript: $TRANSCRIPT_DIR"
echo "📁 Posts: $POSTS_DIR"

**Cross-platform file naming sanitization:**
```bash
function sanitize_filename() {
  local filename="$1"
  # Remove/replace problematic characters across platforms
  echo "$filename" | \
    sed 's/[<>:"/\\|?*]//g' | \      # Windows invalid chars
    sed 's/\.\./\./g' | \              # Remove double dots
    sed 's/^\.\///g' | \               # Remove leading ./
    sed 's/\.$//' | \                  # Remove trailing dot
    sed 's/^$//' | \                   # Empty string check
    cut -c1-100                        # Limit length
}

function sanitize_theme_name() {
  local theme="$1"
  echo "$theme" | \
    tr '[:upper:]' '[:lower:]' | \
    sed 's/[^a-z0-9\s-]/ /g' | \
    sed 's/\s\+/ /g' | \
    sed 's/^\s\+//' | \
    sed 's/\s\+$//' | \
    sed 's/\s/-/g' | \
    cut -c1-30
}

function sanitize_post_name() {
  local post="$1"
  echo "$post" | \
    tr '[:upper:]' '[:lower:]' | \
    sed 's/[^a-z0-9\s-]/ /g' | \
    sed 's/\s\+/ /g' | \
    sed 's/^\s\+//' | \
    sed 's/\s\+$//' | \
    sed 's/\s/-/g' | \
    cut -c1-40
}
```
```

**Content Validation:**
```bash
# Validate extracted content meets minimum requirements
WORD_COUNT=$(echo "$TRANSCRIPT_CONTENT" | wc -w)
CHARACTER_COUNT=$(echo "$TRANSCRIPT_CONTENT" | wc -c)

if [[ $WORD_COUNT -lt 50 ]]; then
  echo "❌ Insufficient content for processing"
  echo "📊 Content Analysis:"
  echo "   - Words: $WORD_COUNT (minimum: 50)"
  echo "   - Characters: $CHARACTER_COUNT"
  echo ""
  echo "💡 Suggestions:"
  echo "   1. Ensure page contains meaningful transcript content"
  echo "   2. Check if content is in text format (not just images/tables)"
  echo "   3. Verify page has sufficient discussion material"
  echo "   4. Consider combining multiple related pages"
  exit 1
fi

if [[ $CHARACTER_COUNT -gt 100000 ]]; then
  echo "⚠️ Large content detected: $CHARACTER_COUNT characters"
  echo "💡 Processing may take longer. Consider splitting into smaller pages."
fi

echo "✅ Content validation passed: $WORD_COUNT words, $CHARACTER_COUNT characters"

**Validation Checkpoint 2: Content Extraction**
```bash
echo "🔍 Validation Checkpoint 2/6: Content Extraction"
if [[ $WORD_COUNT -ge 50 && $CHARACTER_COUNT -le 100000 ]]; then
  echo "✅ Content extraction validated"
  echo "📊 Words: $WORD_COUNT, Characters: $CHARACTER_COUNT"
else
  echo "❌ Content extraction failed validation"
  exit 1
fi

echo "✅ Checkpoint 2 passed"
```
```

**Save transcript with metadata:**
```markdown
# Transcript: {page-title}

**Source**: Notion Page ID: {page-id}  
**Extracted**: {timestamp}  
**Content Length**: {character-count} characters  
**Word Count**: {word-count} words

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
- Fetch and apply style guide from Notion
- Target audience insights
- Context integration: Apply provided context "${CONTEXT_STRING}" to theme analysis and post generation

**Context Integration Logic:**
```bash
# If context string is provided, include it in theme analysis prompt
if [[ -n "$CONTEXT_STRING" ]]; then
  CONTEXT_PROMPT="Additional Context: $CONTEXT_STRING

Please consider this context when analyzing themes and generating posts. This context should influence:
- Which themes are most relevant
- How topics are framed for the target audience
- Specific angles or insights to emphasize"
else
  CONTEXT_PROMPT=""
fi
```

**Save analysis to:** `data\\transcripts\\${PAGE_NAME}-${TIMESTAMP}\\themes-analysis.md`

**Validation Checkpoint 3: Theme Analysis**
```bash
echo "🔍 Validation Checkpoint 3/6: Theme Analysis"
if [[ -f "data\\transcripts\\${PAGE_NAME}-${TIMESTAMP}\\themes-analysis.md" ]]; then
  echo "✅ Theme analysis saved successfully"
  echo "📁 File: data/transcripts/${PAGE_NAME}-${TIMESTAMP}/themes-analysis.md"
else
  echo "❌ Theme analysis file not found"
  exit 1
fi

echo "✅ Checkpoint 3 passed"
```

### Step 5: Generate LinkedIn Posts ONE BY ONE
Using the extracted themes, generate individual LinkedIn posts:

**Post Generation Logic:**
- Generate up to ${POST_COUNT} posts based on available themes
- If fewer themes than POST_COUNT, generate multiple posts per theme
- If more themes than POST_COUNT, prioritize most significant themes

**Template Fetching Logic:**
```bash
# Determine template URL
if [[ -n "$TEMPLATE_ID" ]]; then
  echo "Fetching custom template from Notion page: $TEMPLATE_ID"
  TEMPLATE_CONTENT=$(mcp.notion.get_page_content --page_id="$TEMPLATE_ID")
  if [[ $? -ne 0 ]]; then
    echo "⚠️ Failed to fetch custom template, using default template"
    TEMPLATE_URL="https://www.notion.so/infimagames/LinkedIn-Post-Template-29f3c065308b8022aaecce22524bd32b"
  else
    echo "✅ Custom template fetched successfully"
  fi
else
  TEMPLATE_URL="https://www.notion.so/infimagames/LinkedIn-Post-Template-29f3c065308b8022aaecce22524bd32b"
fi
```

**Style Guide Fetching Logic:**
```bash
# Determine style guide URL
if [[ -n "$STYLE_GUIDE_ID" ]]; then
  echo "Fetching custom style guide from Notion page: $STYLE_GUIDE_ID"
  STYLE_GUIDE_CONTENT=$(mcp.notion.get_page_content --page_id="$STYLE_GUIDE_ID")
  if [[ $? -ne 0 ]]; then
    echo "⚠️ Failed to fetch custom style guide, using default style guide"
    STYLE_GUIDE_URL="https://www.notion.so/infimagames/Octavian-s-Writing-Style-Guide-9e7ae38cdf164ed88c09f442332e1e46"
  else
    echo "✅ Custom style guide fetched successfully"
  fi
else
  STYLE_GUIDE_URL="https://www.notion.so/infimagames/Octavian-s-Writing-Style-Guide-9e7ae38cdf164ed88c09f442332e1e46"
fi
```

**Post Validation Function:**
```bash
function validate_post() {
  local post_content="$1"
  local post_title="$2"
  
  local char_count=$(echo "$post_content" | wc -c)
  local word_count=$(echo "$post_content" | wc -w)
  local hashtag_count=$(echo "$post_content" | grep -o '#[a-zA-Z0-9_]\+' | wc -l)
  
  if [[ $char_count -gt 3000 ]]; then
    echo "❌ Post too long: $char_count characters (max: 3000)"
    echo "📊 Post: $post_title"
    echo "💡 Suggestions:"
    echo "   - Remove redundant sentences"
    echo "   - Shorten examples"
    echo "   - Combine related points"
    echo "   - Focus on key message"
    return 1
  fi
  
  if [[ $char_count -lt 100 ]]; then
    echo "⚠️ Post very short: $char_count characters"
    echo "💡 Consider adding more value or examples"
  fi
  
  if [[ $hashtag_count -gt 3 ]]; then
    echo "❌ Too many hashtags: $hashtag_count (max: 3)"
    echo "📊 Post: $post_title"
    echo "💡 Suggestions:"
    echo "   - Keep only most relevant hashtags"
    echo "   - Combine related concepts into single hashtag"
    echo "   - Focus on 1-3 core topics"
    echo "   - Remove generic hashtags (#innovation, #leadership)"
    return 1
  fi
  
  if [[ $hashtag_count -eq 0 ]]; then
    echo "⚠️ No hashtags found"
    echo "💡 Consider adding 1-3 relevant hashtags for better reach"
  fi
  
  echo "✅ Post validated: $char_count chars, $word_count words, $hashtag_count hashtags"
  return 0
}
```

**Style Guide Compliance Validation:**
```bash
function validate_style_guide() {
  local post_content="$1"
  local style_guide="$2"
  
  echo "🔍 Checking style guide compliance..."
  
  # Check for professional tone indicators
  local slang_count=$(echo "$post_content" | grep -i -c -E "(awesome|cool|dude|gonna|wanna)")
  local emoji_count=$(echo "$post_content" | grep -o '[😀-🿿]' | wc -l)
  local exclamation_count=$(echo "$post_content" | grep -o '!' | wc -l)
  
  # Check for actionable insights
  local actionable_words=$(echo "$post_content" | grep -i -c -E "(how to|steps|tips|strategies|approach|method)")
  
  # Check for APA title case in headings
  local heading_count=$(echo "$post_content" | grep -c '^#')
  
  local issues=0
  
  if [[ $slang_count -gt 0 ]]; then
    echo "⚠️ Found $slang_count informal words (consider professional alternatives)"
    issues=$((issues + 1))
  fi
  
  if [[ $emoji_count -gt 2 ]]; then
    echo "⚠️ Found $emoji_count emojis (limit to 1-2 for professional tone)"
    issues=$((issues + 1))
  fi
  
  if [[ $exclamation_count -gt 3 ]]; then
    echo "⚠️ Found $exclamation_count exclamation marks (use sparingly for emphasis)"
    issues=$((issues + 1))
  fi
  
  if [[ $actionable_words -eq 0 ]]; then
    echo "⚠️ No actionable insights found (add practical tips or strategies)"
    issues=$((issues + 1))
  fi
  
  if [[ $issues -eq 0 ]]; then
    echo "✅ Style guide compliance validated"
  else
    echo "⚠️ $issues style guide issues found"
  fi
  
  return $issues
}
```

**Template Structure Verification:**
```bash
function validate_template_structure() {
  local post_content="$1"
  local template_content="$2"
  
  echo "🔍 Verifying template structure..."
  
  # Check for required template elements
  local has_hook=$(echo "$post_content" | grep -q -E "(^\s*•|^\s*-|^\s*[0-9]\.|^\s*🎯|^\s*💡)" && echo 1 || echo 0)
  local has_value=$(echo "$post_content" | grep -i -c -E "(value|benefit|insight|takeaway)")
  local has_cta=$(echo "$post_content" | grep -i -c -E "(comment|share|thought|what do you think|let me know)")
  
  # Check narrative structure elements
  local has_problem=$(echo "$post_content" | grep -i -c -E "(challenge|problem|issue|struggle)")
  local has_solution=$(echo "$post_content" | grep -i -c -E "(solution|approach|strategy|method)")
  local has_outcome=$(echo "$post_content" | grep -i -c -E "(result|outcome|achievement|success)")
  
  local missing_elements=0
  
  if [[ $has_hook -eq 0 ]]; then
    echo "⚠️ Missing hook element (start with bullet, number, or engaging opener)"
    missing_elements=$((missing_elements + 1))
  fi
  
  if [[ $has_value -eq 0 ]]; then
    echo "⚠️ Missing clear value proposition (what's in it for reader?)"
    missing_elements=$((missing_elements + 1))
  fi
  
  if [[ $has_cta -eq 0 ]]; then
    echo "⚠️ Missing call-to-action (encourage engagement)"
    missing_elements=$((missing_elements + 1))
  fi
  
  # Check for variable mapping
  local product_vars=$(echo "$post_content" | grep -c '\[Product/Service Name\]')
  local audience_vars=$(echo "$post_content" | grep -c '\[Target Audience\]')
  
  if [[ $product_vars -gt 0 ]]; then
    echo "⚠️ Unfilled template variables found: $product_vars product references"
    missing_elements=$((missing_elements + 1))
  fi
  
  if [[ $audience_vars -gt 0 ]]; then
    echo "⚠️ Unfilled template variables found: $audience_vars audience references"
    missing_elements=$((missing_elements + 1))
  fi
  
  if [[ $missing_elements -eq 0 ]]; then
    echo "✅ Template structure verified"
  else
    echo "⚠️ $missing_elements template structure issues found"
  fi
  
  return $missing_elements
}
```

**For each post to generate (up to POST_COUNT):**
1. Fetch template from Notion using logic above
2. Fetch style guide from Notion using logic above
3. Generate post content following template and style guidelines
4. Apply context string if provided: "${CONTEXT_STRING}"
5. Validate template structure using validate_template_structure() function
6. Validate style guide compliance using validate_style_guide() function
7. Validate character count and hashtags using validate_post() function
8. Save with naming: `{theme}:{post-name}.md`

**Validation Checkpoint 4: Post Generation**
```bash
echo "🔍 Validation Checkpoint 4/6: Post Generation"
generated_posts=($(find "$POSTS_DIR" -name "*.md" -type f))

if [[ ${#generated_posts[@]} -eq $POST_COUNT ]]; then
  echo "✅ All $POST_COUNT posts generated successfully"
  echo "📁 Posts directory: $POSTS_DIR"
else
  echo "❌ Post count mismatch: Expected $POST_COUNT, got ${#generated_posts[@]}"
  exit 1
fi

echo "✅ Checkpoint 4 passed"
```

**Content Consistency Validation:**
```bash
function validate_consistency() {
  local posts_array=("$@")
  local total_posts=${#posts_array[@]}
  
  echo "🔍 Checking content consistency across $total_posts posts..."
  
  # Analyze tone consistency
  local formal_count=0
  local casual_count=0
  
  # Analyze hashtag patterns
  local unique_hashtags=()
  local total_hashtags=0
  
  # Analyze post length distribution
  local lengths=()
  
  for post_file in "${posts_array[@]}"; do
    local content=$(cat "$post_file")
    local length=$(echo "$content" | wc -c)
    lengths+=($length)
    
    # Count formal vs casual indicators
    local formal=$(echo "$content" | grep -i -c -E "(therefore|furthermore|consequently|however)")
    local casual=$(echo "$content" | grep -i -c -E "(hey|guys|awesome|cool)")
    
    formal_count=$((formal_count + formal))
    casual_count=$((casual_count + casual))
    
    # Extract hashtags
    local post_hashtags=$(echo "$content" | grep -o '#[a-zA-Z0-9_]\+' | sort | uniq)
    for hashtag in $post_hashtags; do
      if [[ ! " ${unique_hashtags[@]} " =~ " ${hashtag} " ]]; then
        unique_hashtags+=("$hashtag")
      fi
      total_hashtags=$((total_hashtags + 1))
    done
  done
  
  # Calculate consistency metrics
  local avg_length=$(IFS="+"; bc <<< "scale=0; (${lengths[*]})/$total_posts")
  local length_variance=0
  for length in "${lengths[@]}"; do
    local diff=$((length - avg_length))
    length_variance=$((length_variance + diff * diff))
  done
  length_variance=$((length_variance / total_posts))
  
  # Consistency checks
  local issues=0
  
  if [[ $casual_count -gt $formal_count ]]; then
    echo "⚠️ Tone inconsistency: More casual than formal language detected"
    issues=$((issues + 1))
  fi
  
  if [[ ${#unique_hashtags[@]} -gt 8 ]]; then
    echo "⚠️ Hashtag inconsistency: Too many unique hashtags (${#unique_hashtags[@]})"
    echo "💡 Consider using consistent core hashtags across posts"
    issues=$((issues + 1))
  fi
  
  if [[ $length_variance -gt 500000 ]]; then  # sqrt(500000) ≈ 707 chars variance
    echo "⚠️ Length inconsistency: High variance in post lengths"
    echo "💡 Aim for more consistent post lengths (±200 characters)"
    issues=$((issues + 1))
  fi
  
  if [[ $issues -eq 0 ]]; then
    echo "✅ Content consistency validated"
    echo "📊 Average length: $avg_length characters"
    echo "🏷️  Unique hashtags: ${#unique_hashtags[@]}"
  else
    echo "⚠️ $issues consistency issues found"
  fi
  
  return $issues
}
```

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
- Apply my writing style consistently

## Default Resources

- **Style Guide**: https://www.notion.so/infimagames/Octavian-s-Writing-Style-Guide-9e7ae38cdf164ed88c09f442332e1e46
- **Template**: https://www.notion.so/infimagames/LinkedIn-Post-Template-29f3c065308b8022aaecce22524bd32b

## Error Handling

The command will terminate gracefully with clear error messages if:
- Notion MCP is not available
- Notion page is inaccessible or doesn't exist
- Page contains no suitable transcript content
- File operations fail due to permissions or disk space

## Comprehensive Error Scenarios

### Configuration Errors
| Scenario | Error Message | Solution |
|----------|---------------|----------|
| Invalid page ID format | ❌ Invalid page ID format | Use 36-character UUID from Notion URL |
| Post count out of range | ❌ Post count must be 1-10 | Specify number between 1 and 10 |
| Context too long | ❌ Context string exceeds 500 chars | Provide shorter context description |
| Invalid template ID | ❌ Invalid template ID format | Use valid Notion page UUID |

### MCP Connection Errors
| Scenario | Error Message | Solution |
|----------|---------------|----------|
| MCP server down | ❌ Notion MCP unavailable | Check .mcp.json configuration |
| Authentication failed | ❌ Invalid API token | Update NOTION_API_KEY in config |
| Network timeout | ❌ Connection timeout | Check internet connectivity |
| Rate limit exceeded | ❌ Too many requests | Wait and retry with backoff |

### Content Processing Errors
| Scenario | Error Message | Solution |
|----------|---------------|----------|
| Page not found | ❌ Page not found | Verify page ID and access permissions |
| Access denied | ❌ Permission denied | Check page sharing settings |
| Empty content | ❌ No content found | Ensure page has text content |
| Content too short | ❌ Insufficient content | Minimum 50 words required |
| Content too large | ⚠️ Large content | Consider splitting into smaller pages |

### File System Errors
| Scenario | Error Message | Solution |
|----------|---------------|----------|
| Permission denied | ❌ Cannot create directory | Check write permissions |
| Disk full | ❌ Insufficient disk space | Free up disk space |
| Path too long | ❌ Path length exceeded | Use shorter page names |
| Invalid characters | ❌ Invalid filename | Sanitize special characters |

### Recovery Procedures
1. **Immediate Retry**: For network/rate errors with exponential backoff
2. **Configuration Fix**: For auth/permission errors with specific guidance
3. **Content Preparation**: For content errors with clear requirements
4. **System Check**: For file system errors with troubleshooting steps

### Error Logging
All errors include:
- Clear emoji indicators (❌ ⚠️ ✅)
- Specific error codes for reference
- Actionable troubleshooting steps
- Example commands for resolution
- Links to documentation when relevant

## Examples

```bash
# Basic conversion (default 3 posts)
/Create-LinkedInPost a1b2c3d4-e5f6-7890-abcd-ef1234567890

# Custom post count
/Create-LinkedInPost a1b2c3d4-e5f6-7890-abcd-ef1234567890 -n 5

# Custom template only
/Create-LinkedInPost a1b2c3d4-e5f6-7890-abcd-ef1234567890 -t 29f3c065308b8022aaecce22524bd32b

# Custom style guide only
/Create-LinkedInPost a1b2c3d4-e5f6-7890-abcd-ef1234567890 -w 9e7ae38cdf164ed88c09f442332e1e46

# Context string only
/Create-LinkedInPost a1b2c3d4-e5f6-7890-abcd-ef1234567890 -c "Team meeting about Q4 planning and product roadmap"

# Full custom configuration
/Create-LinkedInPost a1b2c3d4-e5f6-7890-abcd-ef1234567890 -n 5 -t 29f3c065308b8022aaecce22524bd32b -w 9e7ae38cdf164ed88c09f442332e1e46 -c "Team meeting about Q4 planning"

# Test parameter validation (should show error)
/Create-LinkedInPost a1b2c3d4-e5f6-7890-abcd-ef1234567890 -n 15
/Create-LinkedInPost a1b2c3d4-e5f6-7890-abcd-ef1234567890 -t invalid-template-id
```

## Process Validation Checkpoints

**Validation Checkpoint 5: Quality Assurance**
```bash
echo "🔍 Validation Checkpoint 5/6: Quality Assurance"
total_issues=0

# Run all validation functions
for post_file in "${generated_posts[@]}"; do
  validate_post "$(cat "$post_file")" "$(basename "$post_file")"
  total_issues=$((total_issues + $?))
done

validate_consistency "${generated_posts[@]}"
total_issues=$((total_issues + $?))

if [[ $total_issues -eq 0 ]]; then
  echo "✅ All quality checks passed"
else
  echo "⚠️ $total_issues quality issues found (posts still saved)"
fi

echo "✅ Checkpoint 5 passed"
```

**Validation Checkpoint 6: Completion**
```bash
echo "🔍 Validation Checkpoint 6/6: Completion"
echo "📊 Final Summary:"
echo "   - Posts generated: ${#generated_posts[@]}"
echo "   - Quality issues: $total_issues"
echo "   - Output directory: $POSTS_DIR"
echo "   - Analysis file: $TRANSCRIPT_DIR/themes-analysis.md"

if [[ ${#generated_posts[@]} -gt 0 && -f "$TRANSCRIPT_DIR/themes-analysis.md" ]]; then
  echo "✅ All checkpoints passed - Process completed successfully"
else
  echo "❌ Process incomplete - Check error messages above"
  exit 1
fi

echo "✅ Checkpoint 6 passed"
```

**Post-Generation Checklist**
```bash
function run_post_generation_checklist() {
  local post_file="$1"
  local post_content=$(cat "$post_file")
  local checklist_passed=0
  local total_checks=8
  
  echo "📋 Running post-generation checklist for $(basename "$post_file")"
  
  # Check 1: Character count
  local char_count=$(echo "$post_content" | wc -c)
  if [[ $char_count -le 3000 ]]; then
    echo "✅ Character count: $char_count (≤ 3000)"
    checklist_passed=$((checklist_passed + 1))
  else
    echo "❌ Character count: $char_count (> 3000)"
  fi
  
  # Check 2: Hashtag count
  local hashtag_count=$(echo "$post_content" | grep -o '#[a-zA-Z0-9_]\+' | wc -l)
  if [[ $hashtag_count -le 3 && $hashtag_count -gt 0 ]]; then
    echo "✅ Hashtag count: $hashtag_count (1-3)"
    checklist_passed=$((checklist_passed + 1))
  else
    echo "❌ Hashtag count: $hashtag_count (should be 1-3)"
  fi
  
  # Check 3: Template structure
  if echo "$post_content" | grep -q -E "(^\s*•|^\s*-|^\s*[0-9]\.|^\s*🎯|^\s*💡)"; then
    echo "✅ Template structure: Hook element present"
    checklist_passed=$((checklist_passed + 1))
  else
    echo "❌ Template structure: Missing hook element"
  fi
  
  # Check 4: Value proposition
  if echo "$post_content" | grep -i -q -E "(value|benefit|insight|takeaway)"; then
    echo "✅ Value proposition: Present"
    checklist_passed=$((checklist_passed + 1))
  else
    echo "❌ Value proposition: Missing"
  fi
  
  # Check 5: Call to action
  if echo "$post_content" | grep -i -q -E "(comment|share|thought|what do you think|let me know)"; then
    echo "✅ Call to action: Present"
    checklist_passed=$((checklist_passed + 1))
  else
    echo "❌ Call to action: Missing"
  fi
  
  # Check 6: Professional tone
  local slang_count=$(echo "$post_content" | grep -i -c -E "(awesome|cool|dude|gonna|wanna)")
  if [[ $slang_count -eq 0 ]]; then
    echo "✅ Professional tone: No informal language"
    checklist_passed=$((checklist_passed + 1))
  else
    echo "❌ Professional tone: $slang_count informal words found"
  fi
  
  # Check 7: Actionable content
  if echo "$post_content" | grep -i -q -E "(how to|steps|tips|strategies|approach|method)"; then
    echo "✅ Actionable content: Present"
    checklist_passed=$((checklist_passed + 1))
  else
    echo "❌ Actionable content: Missing"
  fi
  
  # Check 8: File metadata
  if echo "$post_content" | grep -q "^# " && echo "$post_content" | grep -q "\*\*Theme\*\*"; then
    echo "✅ File metadata: Title and theme present"
    checklist_passed=$((checklist_passed + 1))
  else
    echo "❌ File metadata: Missing title or theme"
  fi
  
  # Summary
  echo "📊 Checklist Result: $checklist_passed/$total_checks checks passed"
  if [[ $checklist_passed -ge 6 ]]; then
    echo "✅ Post meets quality standards"
    return 0
  else
    echo "⚠️ Post needs improvement"
    return 1
  fi
}
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

**Completion Summary with Statistics**
```bash
function generate_completion_summary() {
  local transcript_file="$TRANSCRIPT_DIR/transcript.md"
  local analysis_file="$TRANSCRIPT_DIR/themes-analysis.md"
  local posts_array=($(find "$POSTS_DIR" -name "*.md" -type f))
  
  echo ""
  echo "🎉 ================================================"
  echo "📊 LINKEDIN POST GENERATION COMPLETION SUMMARY"
  echo "🎉 ================================================"
  echo ""
  
  # Content Statistics
  if [[ -f "$transcript_file" ]]; then
    local word_count=$(cat "$transcript_file" | wc -w)
    local char_count=$(cat "$transcript_file" | wc -c)
    echo "📄 CONTENT PROCESSING:"
    echo "   📝 Total words processed: $word_count"
    echo "   🔤 Total characters: $char_count"
    echo "   📁 Transcript file: $transcript_file"
  fi
  
  # Theme Analysis Statistics
  if [[ -f "$analysis_file" ]]; then
    local theme_count=$(grep -c "## Theme" "$analysis_file" || echo "0")
    echo ""
    echo "🧠 THEME ANALYSIS:"
    echo "   🎯 Themes identified: $theme_count"
    echo "   📁 Analysis file: $analysis_file"
  fi
  
  # Post Generation Statistics
  echo ""
  echo "✍️  POST GENERATION:"
  echo "   📄 Posts generated: ${#posts_array[@]}"
  echo "   📁 Output directory: $POSTS_DIR"
  
  # Quality Metrics
  local total_chars=0
  local total_hashtags=0
  local avg_chars=0
  local posts_with_issues=0
  
  for post_file in "${posts_array[@]}"; do
    local content=$(cat "$post_file")
    local chars=$(echo "$content" | wc -c)
    local hashtags=$(echo "$content" | grep -o '#[a-zA-Z0-9_]\+' | wc -l)
    
    total_chars=$((total_chars + chars))
    total_hashtags=$((total_hashtags + hashtags))
    
    if [[ $chars -gt 3000 || $hashtags -gt 3 ]]; then
      posts_with_issues=$((posts_with_issues + 1))
    fi
  done
  
  if [[ ${#posts_array[@]} -gt 0 ]]; then
    avg_chars=$((total_chars / ${#posts_array[@]}))
  fi
  
  echo ""
  echo "📈 QUALITY METRICS:"
  echo "   📏 Average post length: $avg_chars characters"
  echo "   🏷️  Total hashtags used: $total_hashtags"
  echo "   ⚠️  Posts with issues: $posts_with_issues"
  echo "   ✅ Posts compliant: $((${#posts_array[@]} - posts_with_issues))"
  
  # Template & Style Guide Usage
  echo ""
  echo "🎨 TEMPLATE & STYLE USAGE:"
  echo "   📋 Template: ${TEMPLATE_ID:-"Default LinkedIn Template"}"
  echo "   ✍️  Style Guide: ${STYLE_GUIDE_ID:-"Default Professional Style"}"
  echo "   📝 Context applied: ${CONTEXT_STRING:-"None"}"
  
  # Success Rate
  local success_rate=0
  if [[ ${#posts_array[@]} -gt 0 ]]; then
    success_rate=$(((${#posts_array[@]} - posts_with_issues) * 100 / ${#posts_array[@]}))
  fi
  
  echo ""
  echo "🎯 SUCCESS RATE: $success_rate% (${#posts_array[@]} - posts_with_issues)/${#posts_array[@]} posts)"
  
  if [[ $success_rate -ge 80 ]]; then
    echo "✅ EXCELLENT: High-quality post generation"
  elif [[ $success_rate -ge 60 ]]; then
    echo "👍 GOOD: Acceptable quality with minor issues"
  else
    echo "⚠️  NEEDS IMPROVEMENT: Review quality issues"
  fi
  
  echo ""
  echo "🎉 ================================================"
  echo "🏁 PROCESS COMPLETED"
  echo "🎉 ================================================"
}
```
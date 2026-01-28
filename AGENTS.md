# n8n Workflow Templates - Agent Instructions

Instructions for AI agents (Claude Code, etc.) working on n8n workflow templates in this repository.

## Repository Purpose

This repository contains n8n automation templates for social media cross-posting and content management. Templates should be production-ready, well-documented, and follow n8n community standards for potential submission to the [n8n Template Library](https://n8n.io/workflows/).

## Template Structure Standards

### File Organization

**Simple Workflows:**
```
WorkflowName/
  template.json       # Original/stable version
  template-v2.json    # Improved version (if applicable)
  README.md           # Setup instructions and documentation
```

**Multi-Workflow Systems:**
```
WorkflowName/
  01-setup.json       # Initial setup/schema definition
  02-sync.json        # Data synchronization
  03-monitor.json     # Monitoring/detection
  04-action.json      # Action/posting
  README.md           # Complete system documentation
```

**Complex Workflows with Extensive Documentation:**
```
WorkflowName/
  template.json                    # Main template
  template-v2.json                 # Enhanced version
  template-[variant].json          # Alternative implementations
  README.md                        # Main documentation
  QUICK-START.md                   # Fast-track setup
  TROUBLESHOOTING.md               # Common issues
  CHANGELOG.md                     # Version history
  [workflow-specific].csv          # Data files (if applicable)
  [workflow-specific].json         # Schema definitions (if applicable)
```

### Required Elements in Every Template

1. **Sticky Note - Instructions**: Large note explaining the template purpose, setup steps, and configuration options
2. **Configuration Node** (if applicable): Centralized Set node with all configurable parameters as JSON
3. **Section Sticky Notes**: Color-coded notes grouping related nodes
4. **Descriptive Node Names**: Clear, action-oriented names (e.g., "Fetch Instagram Posts" not "HTTP Request")

## n8n Template Best Practices

### Node Guidelines

- **Target ~15 nodes per single workflow** for maintainability
  - Complex workflows may exceed this if necessary
  - Multi-workflow systems: Split concerns across multiple workflows instead
- Use **descriptive node names** that explain the action
  - Good: "Fetch Instagram Posts", "Check if Video Already Uploaded"
  - Bad: "HTTP Request", "IF"
- Add **Sticky Notes** to document each workflow section
- Use **color-coding** consistently:
  - Purple (5): Configuration
  - Blue (6): Data fetching
  - Yellow (7): Processing/Logic
  - Green (3): Output/Final actions
  - Red (2): Error handling
- **Group related nodes** with Sticky Notes for visual organization

### Configuration Pattern

For templates with multiple settings, use a Configuration node at the start:

```json
{
  "settingName": defaultValue,  // Comment explaining the setting
  "anotherSetting": "value"
}
```

Reference settings with: `={{ $('Configuration').item.json.settingName }}`

### Error Handling

- Enable `alwaysOutputData: true` for nodes that might return empty
- Use `onError: "continueRegularOutput"` for non-critical HTTP requests
- Add IF nodes to check for error conditions
- Include error logging or notification branches for production workflows

### Deduplication Pattern

Use n8n Data Tables to prevent duplicate processing:

```
[Fetch Data] → [Check Table] → [If Not Exists] → [Process] → [Save to Table]
```

### Rate Limiting

Add Wait nodes between API calls:
- Use configurable timeout via Configuration node
- Typical values: 2-900 seconds depending on API limits
- For batch operations: Use Split In Batches with delay between batches

### Backend Abstraction Pattern

For workflows supporting multiple backends (n8n Data Tables + Airtable):

```
[Configuration] → [IF: Check Backend] → [n8n Path] / [Airtable Path] → [Merge]
```

Configuration example:
```json
{
  "backend": "n8n",  // or "airtable"
  "dataTableId": "...",
  "airtableBaseId": "...",
  "airtableTableName": "..."
}
```

Benefits:
- Users choose backend without modifying workflow logic
- Migrate from n8n to Airtable when advanced features needed
- Keep workflow portable across environments

### Multi-Workflow Coordination

For complex systems split across multiple workflows:

**Data Flow:**
```
[Setup Workflow] → [Central Data Store] ← [Sync Workflow]
                          ↓
                   [Monitor Workflow] → [Action Workflow]
```

**Best Practices:**
1. Number workflows sequentially (01-, 02-, 03-)
2. Use shared data store (Airtable or n8n Data Tables)
3. Each workflow operates independently on schedule
4. Document execution order in main README
5. Include deduplication in each workflow
6. Test workflows in sequence before activating schedules

### Schema-as-Code Pattern

For workflows using JSON-driven table schemas:

```
[Schema JSON] → [Schema Maker Workflow] → [Table Creation] → [Tracker Workflow]
```

Benefits:
- Version control table schemas in git
- Generate tables programmatically
- Reuse schemas across projects
- Switch backends without changing schema

Example schema:
```json
{
  "tableName": "Instagram Metrics",
  "backend": "n8n",
  "fields": [
    {
      "name": "accountId",
      "type": "string",
      "required": true
    }
  ]
}
```

### AI Agent Pattern

For interactive AI workflows with tools and memory:

```
[Chat Trigger] → [AI Agent] → [Tool: Web Search] → [Tool: Fetch] → [Format] → [Response]
```

Key components:
- Chat Trigger for user interaction
- AI Agent with conversation memory
- Tools for external data (SerpAPI, HTTP fetch)
- Output formatting (Markdown, JSON, ADR)
- Memory window for context retention

## Template Publishing Checklist

Before submitting to n8n's template library:

### Documentation

- [ ] Clear template title describing the use case
- [ ] Sticky Note with setup instructions inside the workflow
- [ ] All required credentials documented
- [ ] Configuration options explained with examples

### Quality

- [ ] All nodes have descriptive names
- [ ] No hardcoded credentials or personal data
- [ ] Credential names are generic (e.g., "YouTube Account" not "My Personal Account")
- [ ] Error handling for critical operations
- [ ] Tested with real data

### Technical

- [ ] Works with latest n8n version
- [ ] No deprecated nodes
- [ ] Validate against n8n schema: `api.n8n.io/schema`
- [ ] Remove any `pinData` before publishing
- [ ] Remove personal `instanceId` if sharing publicly

### Metadata for Submission

When submitting to n8n Creator Hub, prepare:
- Template name (clear, descriptive)
- Category (e.g., "Social Media", "Content Creation")
- Short description (1-2 sentences)
- Detailed description with use cases
- Required credentials list
- Setup instructions

## Common Patterns in This Repository

### Instagram API Pattern

```javascript
// Fetch posts via Meta Graph API
URL: https://graph.instagram.com/me/media
Query: { "fields": "id,caption,media_url,media_type,timestamp,permalink", "limit": "25" }
Auth: Bearer Token (httpBearerAuth)
```

### YouTube Upload Pattern

```javascript
// Key options for YouTube uploads
{
  "privacyStatus": "public",           // public, private, unlisted
  "madeForKids": false,                // COPPA compliance
  "selfDeclaredMadeForKids": false,    // Self-declaration
  "embeddable": true,                  // Allow embedding
  "notifySubscribers": true,           // Send notifications
  "ageRestricted": false               // 18+ content
}
```

### Caption/Title Processing Pattern

```javascript
// Extract hashtags and format title
const hashtagRegex = /#[\w\u00C0-\u024F]+/gu;
const hashtags = caption.match(hashtagRegex) || [];
const title = caption.split('#')[0].trim().substring(0, 100);
const tags = hashtags.map(h => h.replace('#', '')).slice(0, 10);
```

### Airtable Integration Pattern

```javascript
// Search for existing record
GET /v0/{baseId}/{tableName}
Query: { "filterByFormula": "{accountId} = 'value'" }

// Create new record
POST /v0/{baseId}/{tableName}
Body: { "fields": { "fieldName": "value" } }

// Update existing record
PATCH /v0/{baseId}/{tableName}/{recordId}
Body: { "fields": { "fieldName": "newValue" } }
```

Key considerations:
- Airtable uses recordId for updates (different from primary key)
- Filter formula syntax: `{fieldName} = 'value'`
- Rate limit: 5 requests/second on free tier
- Field names are case-sensitive

### Trend Monitoring Pattern

For content automation based on trends:

```
[Fetch Trends] → [Filter by Keywords] → [Search Content] → [Match & Score] → [Post Best Match]
```

Components:
1. **Trend Source**: Google Trends RSS, Reddit, Twitter
2. **Filtering**: Niche-specific keywords to reduce noise
3. **Content Matching**: Search existing content by keywords/tags
4. **Scoring**: Relevance score based on keyword overlap
5. **Cooldown**: Prevent over-sharing same content (track lastSharedAt)
6. **Rate Limiting**: Max posts per day

### AI Content Analysis Pattern

For workflows using AI to analyze and optimize:

```javascript
// Build context from top content
const topVideos = items.slice(0, 10);
const context = topVideos.map(v => ({
  title: v.title,
  views: v.viewCount,
  engagement: v.likeCount / v.viewCount
}));

// Send to AI with structured prompt
const prompt = `Analyze these videos and identify patterns:
${JSON.stringify(context, null, 2)}

Return structured recommendations as JSON.`;
```

Best practices:
- Limit input data to top performers only
- Use structured prompts for consistent output
- Request JSON format for easy parsing
- Include examples in prompt for better results

### CSV Import Pattern

For fastest workflow setup:

1. Include pre-generated CSV files with schema
2. Provide both template (headers only) and sample data versions
3. Document CSV import process in dedicated guide
4. Field names in CSV must exactly match n8n Data Table schema

Files to include:
- `{workflow}-table.csv` - With sample data
- `{workflow}-table-template.csv` - Headers only
- `CSV-IMPORT-GUIDE.md` - Step-by-step instructions

## Template Complexity Levels

**Level 1: Simple Cross-Posting**
- Single workflow, ~10-15 nodes
- One API to another (Instagram → X)
- n8n Data Tables for deduplication
- Single README
- Example: Instagram to X

**Level 2: Single Workflow with AI**
- Single workflow, ~15-25 nodes
- Multiple APIs + AI processing
- Configuration node for settings
- Single comprehensive README
- Example: AI-Powered YouTube Channel Optimizer

**Level 3: Multi-Backend Support**
- Single workflow, ~20-30 nodes
- Backend routing (n8n Data Tables OR Airtable)
- Schema-as-code approach
- Multiple documentation files (QUICK-START, TROUBLESHOOTING)
- CSV import option
- Example: Instagram Account Information Tracker

**Level 4: Multi-Workflow System**
- 3-4 workflows, ~15-20 nodes each
- Shared data store (Airtable)
- Complex coordination
- Comprehensive documentation
- Example: Trend Reshare System

**Level 5: Interactive AI Agent**
- Single workflow with AI agent node
- Chat interface with memory
- External tools (web search, fetch)
- Multiple output formats
- Example: ADR Researcher

## Resources

### External Documentation
- [n8n Documentation](https://docs.n8n.io/)
- [n8n Template Library](https://n8n.io/workflows/)
- [n8n Creator Hub](https://n8n.io/creators/)
- [n8n Community Forum](https://community.n8n.io/)
- [API Schema Validation](https://api.n8n.io/schema)

### Repository Documentation
- `CLAUDE.md` - Complete architecture guide for AI agents
- `README.md` - User-facing template catalog
- `docs/n8n-deep-dive-guide.md` - Comprehensive n8n concepts guide
- `docs/workflow-analysis.md` - Detailed analysis and improvements
- `YouTube Channel Management - Real World Lessons.md` - Practical insights

### Template-Specific Documentation
Each complex template includes:
- Main README with setup instructions
- QUICK-START for fastest setup path
- TROUBLESHOOTING for common issues
- CHANGELOG for version history
- Field/schema references for data structures

## Creating New Workflows

### Step 1: Choose Complexity Level

**Ask these questions:**
1. Single workflow or multi-workflow system?
2. Simple data storage (n8n) or advanced features (Airtable)?
3. AI integration needed?
4. Interactive chat interface or scheduled automation?

### Step 2: Select Architecture Pattern

Based on complexity:
- **Simple**: Single workflow, n8n Data Tables, basic README
- **AI Enhanced**: Add AI agent, structured prompts, JSON parsing
- **Multi-Backend**: Add backend routing, schema-as-code, CSV import
- **Multi-Workflow**: Split concerns, shared Airtable, numbered files
- **AI Agent**: Chat trigger, memory, tools, multiple output formats

### Step 3: Create File Structure

Follow the appropriate pattern from "File Organization" section above.

### Step 4: Implement Core Patterns

Include these based on your workflow type:
- Configuration node (always)
- Deduplication logic (if fetching data)
- Rate limiting (if making API calls)
- Error handling (for critical operations)
- Backend routing (if multi-backend)
- Schema definition (if using structured data)

### Step 5: Document Thoroughly

**Minimum documentation:**
- README with setup steps
- Required credentials list
- Configuration options
- Data Table/Airtable schema

**For complex workflows, add:**
- QUICK-START guide
- TROUBLESHOOTING guide
- CSV files (if applicable)
- Schema JSON (if applicable)
- CHANGELOG for versions

### Step 6: Update Repository Files

1. Add workflow to `README.md` in appropriate category
2. Add workflow details to `CLAUDE.md` under "Available Workflows"
3. Mark testing status (✅ or ⏳) in README
4. Document any new patterns in this file (AGENTS.md)
5. Note API quota impact in CLAUDE.md if significant

### Step 7: Version Management

Follow versioning strategy:
- `template.json` - Original version
- `template-v2.json` - Improvements/bug fixes
- `template-v2-enhanced.json` - Feature additions
- `template-[variant].json` - Alternative implementations (e.g., different LLM)

Document version differences in README or create WHICH-VERSION.md for major differences.

## Common Mistakes to Avoid

1. **Hardcoding credentials** - Always use n8n credential system
2. **Not removing personal data** - Remove instanceId, personal account names, test data
3. **Skipping deduplication** - Every data-fetching workflow needs it
4. **Missing error handling** - At minimum, set `alwaysOutputData: true`
5. **Poor node names** - "HTTP Request" tells nothing, "Fetch Instagram Posts" tells everything
6. **No rate limiting** - Add Wait nodes between API calls
7. **Forgetting to test both backends** - If supporting n8n + Airtable, test both paths
8. **Not documenting API quotas** - Users need to know cost implications
9. **Missing configuration node** - Centralize all settings in one place
10. **No sticky notes** - Visual documentation helps users understand workflow

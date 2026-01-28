# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a collection of n8n workflow automation templates for social media cross-posting and content management. The workflows are distributed as JSON template files that users import into their n8n instances.

**Key Technologies:**
- n8n (workflow automation platform)
- Instagram Graph API (Meta)
- YouTube Data API v3 (Google)
- Twitter/X API v2
- Google Gemini AI

## Workflow Templates

Each template is a JSON file containing n8n workflow configuration with nodes, credentials, and execution logic.

### Available Workflows

1. **Instagram to X** (`Instagram to X/template.json`)
   - Automatically cross-posts Instagram content to X/Twitter
   - Uses Data Tables for deduplication (postId tracking)
   - Schedule-triggered (every 12 hours)

2. **Auto-Translate YouTube Video** (`Auto-Translate YouTube Video Content with Google Gemini AI/template.json`)
   - Translates YouTube video titles/descriptions using Google Gemini AI
   - Manual trigger workflow
   - Processes multiple language localizations

3. **Auto-Translate YouTube Video (Loop Lookup Ver)** (`Auto-Translate YouTube Video (Loop Lookup Ver)/template.json`)
   - Optimized version with loop-based processing
   - Enhanced lookup mechanism for better performance

4. **Upload from Instagram to YouTube** (`Upload from Instagram To YouTube/template.json`)
   - Downloads Instagram videos and uploads to YouTube
   - Includes title truncation logic and hashtag processing
   - Daily schedule trigger (midnight)

5. **YouTube Channel Watermark Updater** (`YouTube Channel Watermark Updater/template.json`)
   - Sets/updates YouTube channel watermark
   - Manual trigger for one-time operations
   - Auto-detects channel ID from OAuth credentials
   - V2 available for multi-account updates (7 channels at once)

6. **YouTube Channel Settings Audit** (`YouTube Channel Settings Audit/template.json`)
   - Audits YouTube channel optimization across branding, SEO, and content
   - Scores channel on 165-point scale (normalized to 100)
   - Identifies critical issues and priority actions
   - **V2 Enhanced** (`template-v2-enhanced.json`):
     - Algorithm-focused analysis (upload consistency, engagement, CTR, watch time)
     - Visual HTML report with color-coded dashboard
     - 100-point scoring system weighted toward algorithm factors
     - Critical settings detection (Made for Kids, privacy, category)

7. **AI-Powered YouTube Channel Optimizer** (`AI-Powered YouTube Channel Optimizer/template.json`)
   - Uses Google Gemini AI to analyze channel performance
   - Generates optimized channel descriptions and keywords
   - Creates video title and description templates
   - Provides tag strategy and content insights
   - Based on analysis of top-performing videos (data-driven recommendations)

8. **YouTube Featured Channels Updater** (`YouTube Featured Channels Updater/template.json`)
   - Sets/updates featured channels on YouTube home tab layout
   - Places featured channels section as first row (position 0)
   - Supports predefined list of channels in specific order
   - Validates all channels are present after update
   - Creates new section or updates existing one automatically
   - **V2 Personal** (`template-v2-personal.json`):
     - Pre-configured for Mechenable account
     - Section title: "Cultured Corner 🍷"
     - Features: Jueguenable, Legoenable, Dollenable, Posenable, Statuenable

9. **YouTube Channel Subscriber** (`YouTube Channel Subscriber/template.json`)
   - Subscribes to a predefined list of channels
   - Automatically excludes the calling channel (can't subscribe to yourself)
   - Handles "already subscribed" gracefully
   - Rate-limited to avoid API restrictions (2s delay between subscriptions)
   - Generates detailed report with subscription status

## Working with n8n Workflow Templates

### Template Structure

n8n templates are JSON files with this structure:

```json
{
  "meta": { "instanceId": "...", "templateCredsSetupCompleted": true },
  "nodes": [ /* array of workflow nodes */ ],
  "connections": { /* node connections */ },
  "settings": { /* workflow settings */ }
}
```

### Key Node Types in These Workflows

- **Schedule Trigger**: Cron-based automation (every 12h, daily, etc.)
- **Manual Trigger**: User-initiated workflow execution
- **HTTP Request**: API calls to Instagram, YouTube, Twitter, Google Gemini
- **Data Table** (n8n-nodes-base.dataTable): Built-in database for deduplication
  - Operations: `get`, `insert`, `update`
  - Used to track processed posts by ID
- **Code**: JavaScript execution for data transformation, analysis, and report generation
- **Loop Over Items**: Sequential processing of arrays
- **Split In Batches**: Process items in groups with optional delay
- **Merge**: Combine data from multiple branches
- **Set**: Transform and structure data
- **NoOp**: No-operation node for workflow organization
- **IF**: Conditional branching based on expressions
- **AI Agent**: Google Gemini integration for translations and content optimization
- **Sticky Note**: Documentation and instructions within workflows

### Important Patterns

#### Deduplication Pattern
All workflows use Data Tables to prevent duplicate actions:

```
[Fetch Posts] → [Get row(s) from Table] → [IF Not Found] → [Process] → [Insert row]
```

Data Table schema typically has a primary key field (e.g., `postId`, `videoId`).

#### Expression Syntax
n8n uses `{{ }}` for dynamic values:

```javascript
{{ $json.fieldName }}                          // Current item
{{ $('Node Name').item.json.fieldName }}       // Specific node
{{ $('Node Name').first().json.fieldName }}    // First item
```

#### Rate Limiting
Wait nodes are used between API calls (typically 15 seconds) to avoid rate limits.

#### YouTube Optimization Workflow Pattern
The Settings Audit and AI Optimizer workflows follow a specialized pattern:

```
[Manual Trigger] → [Get Channel Data] → [Get Videos] → [Get Stats] → [Analyze] → [Generate Report]
```

**Settings Audit V2:**
- Generates visual HTML reports with embedded CSS
- Returns binary data for downloadable files
- Scores on 100-point scale with algorithm-focused metrics
- Critical settings detection (Made for Kids, privacy, category)

**AI Optimizer:**
- Builds AI context from top-performing videos
- Uses Google Gemini for pattern recognition
- Generates structured JSON recommendations
- Formats output as readable text report

**Integration pattern:**
1. Run Settings Audit first (identify technical issues)
2. Fix critical issues found
3. Run AI Optimizer (generate content recommendations)
4. Re-run Settings Audit after 30 days (measure improvement)

## Credential Types Used

When modifying workflows, these credential types are referenced:

1. **Meta (Instagram)**: HTTP Bearer Token (Header Auth)
   - 60-day token expiration - requires manual refresh

2. **YouTube OAuth2 API**: Google OAuth 2.0
   - Scopes: `youtube.force-ssl`, `youtube.upload`

3. **Twitter/X**: OAuth 2.0 with PKCE
   - Scopes: `tweet.read`, `tweet.write`, `users.read`

4. **Google Gemini**: API Key
   - Header: `x-goog-api-key`

Credentials are stored encrypted in n8n and referenced by ID in workflow nodes.

## Common Operations

### Testing a Workflow
1. Import the `template.json` file into n8n
2. Configure credentials in each node that requires them
3. Create required Data Tables (see workflow README for schema)
4. Use Manual Trigger to test execution
5. Check execution history for errors

### Modifying Workflows
- **DO NOT** hardcode API keys or tokens in Code nodes
- Always use n8n's credential system
- Maintain deduplication logic when fetching multiple items
- Test with Manual Trigger before activating Schedule Triggers

### Version Management
- `template.json`: Original/stable version
- `template-v2.json`: Updated version with improvements
- When creating new versions, increment version number in filename

## Architecture Notes

### Data Flow
All workflows follow this general pattern:

```
[Trigger] → [Fetch] → [Dedup Check] → [Process] → [Save ID] → [External Action]
```

### API Quota Considerations
- Instagram Graph API: Rate limits apply per user token
- YouTube Data API v3: 10,000 quota units/day
  - `channels.list`: 1 unit
  - `videos.list`: 1 unit
  - `videos.update`: 50 units
  - `watermarks.set`: 50 units
  - `channelSections.list`: 1 unit
  - `channelSections.insert`: 50 units
  - `channelSections.update`: 50 units
  - `subscriptions.insert`: 50 units
  - `search.list`: 100 units
  - **Settings Audit workflow**: ~102 units per run
  - **AI Optimizer workflow**: ~102 units per run
  - **Featured Channels Updater workflow**: ~53 units per run
- Twitter/X API: Free tier has strict limits
- Google Gemini API: Free tier (60 requests/minute)
  - **AI Optimizer workflow**: 1 request per run (~8,000 tokens)
  - Free tier is effectively unlimited for normal usage

### Known Issues and Limitations

Documented in `docs/workflow-analysis.md`:

1. **Instagram to X**: Only fetches latest post (limit=1), may miss posts
2. **Instagram to YouTube**: No media type filtering, attempts to upload images as videos
3. **YouTube Translate**: Manual trigger only, no automatic scheduling
4. **Token expiration**: No automated refresh mechanism

See `docs/workflow-analysis.md` for detailed issue analysis and improvement recommendations.

## File Organization

```
.
├── Instagram to X/
│   ├── template.json
│   ├── template-v2.json
│   └── README.md
├── Auto-Translate YouTube Video Content with Google Gemini AI/
│   ├── template.json
│   └── README.md
├── Auto-Translate YouTube Video (Loop Lookup Ver)/
│   ├── template.json
│   └── template-v2.json
├── Upload from Instagram To YouTube/
│   ├── template.json
│   └── template-v2.json
├── YouTube Channel Watermark Updater/
│   ├── template.json
│   ├── template-v2-multi-account.json
│   └── README.md
├── YouTube Channel Settings Audit/
│   ├── template.json                # V1 Basic - Text-based audit
│   ├── template-v2-enhanced.json    # V2 Enhanced - HTML reports, algorithm focus
│   ├── README.md                    # V1 documentation
│   ├── README-V2.md                 # V2 documentation
│   └── WHICH-VERSION.md             # Version comparison guide
├── AI-Powered YouTube Channel Optimizer/
│   ├── template.json
│   └── README.md
├── YouTube Featured Channels Updater/
│   ├── template.json                  # Generic version with placeholders
│   ├── template-v2-personal.json      # Personal version for Mechenable
│   └── README.md
├── YouTube Channel Subscriber/
│   ├── template.json
│   └── README.md
├── docs/
│   ├── n8n-deep-dive-guide.md           # Comprehensive n8n tutorial
│   └── workflow-analysis.md              # Critical analysis and improvements
├── YouTube Channel Management - Real World Lessons.md  # Personal experiences and insights
└── README.md                              # Main repository documentation
```

## Documentation Standards

### When Creating New Workflows

Each workflow folder should contain:

1. **template.json**: The n8n workflow export
2. **README.md**: Setup instructions including:
   - What it does
   - Required credentials
   - Data Table schema
   - Configuration steps
   - API references

### Workflow README Template

```markdown
## Description
### How it works
* Brief explanation of workflow logic

### Setup Steps
* Credential configuration
* Data Table creation (with field names)
* Node-specific settings

### Additional Links
* Related Medium articles or documentation
```

## Git Workflow

- Main branch: `main`
- Commit style: Descriptive messages (e.g., "Add Instagram to YouTube uploader")
- No commit signing or pre-commit hooks detected
- Large commits are acceptable for workflow JSON files

## External Resources

- Author's n8n Creator Profile: https://n8n.io/creators/misterfunable/
- Medium Article Collection: https://medium.com/@mister.funable/list/n8n-fc9fe38c3afe
- n8n Deep Dive Guide: `docs/n8n-deep-dive-guide.md`
- YouTube Channel Management - Real World Lessons: `YouTube Channel Management - Real World Lessons.md`
  - Personal experiences from managing multiple channels
  - Feature eligibility tiers and phone verification limits
  - Category selection impact on views
  - Upload defaults and workflow optimization
  - Monetization reality check (4000 hour validation issues)

## Important Context

- This is a **template repository**, not a running application
- Users download and import templates into their own n8n instances
- Each template requires user-specific credential configuration
- Templates use n8n cloud features (Data Tables) - may not work on older self-hosted versions
- Target audience: Content creators automating social media workflows

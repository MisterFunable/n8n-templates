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
- OpenAI GPT-4o
- Anthropic Claude
- Airtable API
- SerpAPI / Google Custom Search

## Workflow Templates

Each template is a JSON file containing n8n workflow configuration with nodes, credentials, and execution logic.

### Workflow Categories

**Social Media Cross-Posting:**
- Instagram to X
- Upload from Instagram to YouTube

**YouTube Management:**
- Auto-Translate YouTube Video (2 versions)
- YouTube Channel Watermark Updater
- YouTube Channel Settings Audit (V1 and V2)
- AI-Powered YouTube Channel Optimizer
- YouTube Featured Channels Updater
- YouTube Channel Subscriber

**Analytics & Tracking:**
- Instagram Account Information Tracker

**Content Automation Systems:**
- Trend Reshare System (multi-workflow)

**AI Research & Documentation:**
- ADR Researcher (2 LLM versions)
- Web Research Social Links Finder (3 search methods)

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

10. **Instagram Account Information Tracker** (`Instagram Account Information Tracker/`)
   - Tracks Instagram account metrics over time
   - Supports both n8n Data Tables and Airtable backends
   - Schema-as-code approach with JSON definitions
   - Multiple setup methods: CSV import (fastest), schema workflow, or manual
   - Tracks account info, follower counts, insights (impressions, reach, profile views)
   - Automatic deduplication and update logic
   - Schedule-triggered (every 6 hours default)

11. **Trend Reshare System** (`Trend Reshare System/`)
   - Multi-workflow system for trend-based content resharing
   - Monitors Google Trends for niche-relevant topics (dolls, action figures, LEGO, board games)
   - Syncs YouTube channel videos to Airtable
   - Matches trending topics to existing video content
   - Automatically posts to X/Twitter with optimized formatting
   - Prevents over-sharing with cooldown tracking
   - 4 workflows: Airtable setup, YouTube sync, trend monitor, trend poster

12. **ADR Researcher** (`ADR Researcher/`)
   - AI-powered engineering documentation assistant
   - Creates Architecture Decision Records (ADRs) following MADR template
   - Generates procedures, runbooks, and technical documentation
   - Interactive conversation with memory for follow-up questions
   - Web research with source citations via SerpAPI
   - Two versions: OpenAI GPT-4o (template.json) and Anthropic Claude (template-anthropic.json)
   - Outputs: Markdown, JSON, or structured ADR format

13. **Web Research Social Links Finder** (`Web Research Social Links Finder/`)
   - Automated website research and social media link extraction
   - Uses Gemini AI for intelligent link discovery
   - Three search methods: direct URLs, SerpAPI, or Google Custom Search
   - Extracts Instagram, Facebook, Twitter/X, YouTube, TikTok, Pinterest, LinkedIn, Etsy, emails
   - Regex extraction + AI verification for accuracy
   - Deduplicates and aggregates results across multiple sites
   - Free tier compatible (Gemini 1.5 Flash)

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
- **Chat Trigger**: Interactive chat interface for AI agents
- **HTTP Request**: API calls to Instagram, YouTube, Twitter, Google Gemini
- **Data Table** (n8n-nodes-base.dataTable): Built-in database for deduplication
  - Operations: `get`, `insert`, `update`
  - Used to track processed posts by ID
- **Airtable**: External database with advanced features (formulas, rollups, lookups)
  - Operations: `create`, `update`, `search`, `list`
  - Alternative to n8n Data Tables for complex data structures
- **Code**: JavaScript execution for data transformation, analysis, and report generation
- **Loop Over Items**: Sequential processing of arrays
- **Split In Batches**: Process items in groups with optional delay
- **Merge**: Combine data from multiple branches
- **Set**: Transform and structure data
- **NoOp**: No-operation node for workflow organization
- **IF**: Conditional branching based on expressions
- **AI Agent**: LLM integration (Google Gemini, OpenAI, Anthropic) for translations, content optimization, and research
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
Wait nodes are used between API calls (typically 2-15 seconds) to avoid rate limits.

#### Schema-as-Code Pattern (Instagram Tracker)
Define data structures in JSON, then generate table schemas programmatically:

```
[JSON Schema] → [Schema Maker] → [Table Creation Instructions] → [Tracker Workflow]
```

Benefits: Version control, portability between n8n Data Tables and Airtable, reusability.

#### Multi-Workflow System Pattern (Trend Reshare)
Complex automation split into specialized workflows:

```
[Setup Workflow] → [Sync Workflow] → [Monitor Workflow] → [Action Workflow]
```

Data flows through Airtable as the central data store, allowing each workflow to operate independently on a schedule.

#### AI Research Pattern (ADR Researcher)
Interactive AI agent with tools for web research:

```
[Chat Trigger] → [AI Agent with Memory] → [Tool: Web Search] → [Tool: Fetch Page] → [Format Output]
```

Maintains conversation context, performs multi-step research, cites sources.

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
   - Free tier: 60 requests/minute, 1M tokens/day

5. **OpenAI API**: API Key
   - Used in ADR Researcher (template.json)
   - GPT-4o model for research and documentation

6. **Anthropic API**: API Key
   - Used in ADR Researcher (template-anthropic.json)
   - Claude model alternative to OpenAI

7. **Airtable API**: API Key
   - Used in Trend Reshare System and Instagram Tracker
   - Free tier: 1,000 records per base, 5 requests/second

8. **SerpAPI**: API Key
   - Used in Web Research and ADR Researcher
   - Free tier: 100 searches/month

9. **Google Custom Search**: API Key + Search Engine ID
   - Alternative to SerpAPI for Web Research
   - Free tier: 100 searches/day

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
- For multi-workflow systems, always test workflows in sequence (setup → sync → monitor → action)
- When modifying backend routing (n8n vs Airtable), test both paths
- For AI agent workflows, test conversation memory and tool calling separately

### Working with Multi-Workflow Systems

**Trend Reshare System:**
1. Run `01-airtable-setup.json` first to understand schema
2. Manually create Airtable tables with correct field types
3. Run `02-youtube-sync.json` to populate Videos table
4. Activate `03-trend-monitor.json` for continuous trend detection
5. Activate `04-trend-poster.json` after trends exist

**Instagram Account Tracker:**
1. Choose backend first (n8n Data Tables or Airtable)
2. Use CSV import method for fastest setup
3. OR use `01-table-schema-maker-v2.json` to generate schema
4. Then run `02-instagram-account-tracker.json` on schedule

### Debugging Multi-Workflow Systems

When debugging systems with multiple workflows:
1. Check the shared data store (Airtable or n8n Data Tables) first
2. Verify each workflow's deduplication logic independently
3. Look for timing issues (Workflow B running before Workflow A populates data)
4. Check rate limits across all workflows (cumulative API usage)
5. Review execution order in schedule triggers

### Version Management
- `template.json`: Original/stable version
- `template-v2.json`: Updated version with improvements
- `template-v2-enhanced.json`: Specialized enhanced version
- `template-v2-personal.json`: Personal configuration version
- `template-anthropic.json`: Alternative LLM provider version
- When creating new versions, increment version number in filename
- Document version differences in README or WHICH-VERSION.md

## Architecture Notes

### High-Level Design Principles

**1. Deduplication-First Design**
Most workflows prevent duplicate processing using either n8n Data Tables or Airtable. The primary key strategy varies:
- Social media workflows: Use post/video ID
- Tracker workflows: Use account ID with update-or-insert logic
- Trend systems: Use trend ID + timestamp combinations

**2. Backend Abstraction**
Several workflows (Instagram Tracker, Trend Reshare) support multiple backends:
- n8n Data Tables: Self-contained, simple queries, no external dependencies
- Airtable: Advanced features (formulas, rollups), visual interface, collaboration

Workflows route data through IF nodes based on backend configuration.

**3. Multi-Workflow Coordination**
Complex systems split concerns across specialized workflows sharing a data store:
- Trend Reshare: Setup → Sync → Monitor → Post (4 workflows, Airtable-coordinated)
- Instagram Tracker: Schema Maker → Tracker (2 workflows, optional Airtable)

Each workflow operates independently on its own schedule but reads/writes shared tables.

**4. Configuration Centralization**
Production-ready workflows use a Configuration node (Set node) at the start:
- All API keys, IDs, and settings in one JSON object
- Easy to export/import across environments
- Referenced throughout workflow with `{{ $('Configuration').item.json.settingName }}`

**5. AI Agent Architecture**
Research workflows (ADR Researcher, Web Research) use the AI Agent pattern:
- Chat Trigger for interactive sessions
- Memory for conversation context
- Tools for external data (SerpAPI, HTTP fetch)
- Output formatting for different use cases (Markdown, JSON, ADR)

### Data Flow Patterns

**Simple Cross-Posting:**
```
[Trigger] → [Fetch] → [Dedup Check] → [Process] → [Save ID] → [External Action]
```

**Multi-Backend:**
```
[Trigger] → [Config] → [Fetch] → [Route by Backend] → [n8n Path] / [Airtable Path] → [Merge]
```

**AI Research:**
```
[Chat] → [AI Agent] → [Search Tool] → [Fetch Tool] → [AI Processing] → [Format] → [Response]
```

**Multi-Workflow System:**
```
[Workflow A: Setup] → [Central Store: Airtable] ← [Workflow B: Sync]
                              ↓
                    [Workflow C: Monitor] → [Workflow D: Action]
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
- Google Gemini API: Free tier (60 requests/minute, 1M tokens/day)
  - **AI Optimizer workflow**: 1 request per run (~8,000 tokens)
  - **Web Research workflow**: 1-10 requests per run
  - Free tier is effectively unlimited for normal usage
- Airtable API: Free tier (1,000 records per base, 5 requests/second)
  - **Trend Reshare System**: ~20-50 requests per day
  - **Instagram Tracker**: ~5-10 requests per run
- SerpAPI: Free tier (100 searches/month)
  - **Web Research workflow**: 1-10 searches per run
  - **ADR Researcher workflow**: 3-5 searches per query
- OpenAI API: Pay-per-use (GPT-4o)
  - **ADR Researcher workflow**: ~2,000-5,000 tokens per query
- Anthropic API: Pay-per-use (Claude)
  - **ADR Researcher workflow**: ~2,000-5,000 tokens per query

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
│   ├── template.json                # Generic version with placeholders
│   ├── template-v2-personal.json    # Personal version for Mechenable
│   └── README.md
├── YouTube Channel Subscriber/
│   ├── template.json
│   └── README.md
├── Instagram Account Information Tracker/
│   ├── README.md
│   ├── CSV-IMPORT-GUIDE.md
│   ├── TROUBLESHOOTING.md
│   ├── ADVANTAGES.md
│   ├── CHANGELOG.md
│   ├── FILES-OVERVIEW.md
│   ├── QUICK-START.md
│   ├── field-types-reference.md
│   ├── schema-format.json
│   ├── instagram-metrics-schema.json
│   ├── instagram-metrics-table.csv
│   ├── instagram-metrics-table-template.csv
│   ├── 01-table-schema-maker.json
│   ├── 01-table-schema-maker-v2.json
│   ├── 01-table-schema-to-csv.json
│   └── 02-instagram-account-tracker.json
├── Trend Reshare System/
│   ├── README.md
│   ├── 01-airtable-setup.json
│   ├── 02-youtube-sync.json
│   ├── 03-trend-monitor.json
│   └── 04-trend-poster.json
├── ADR Researcher/
│   ├── README.md
│   ├── template.json              # OpenAI GPT-4o version
│   └── template-anthropic.json    # Anthropic Claude version
├── Web Research Social Links Finder/
│   ├── README.md
│   ├── template.json                # Google Custom Search version
│   ├── template-serpapi.json        # SerpAPI version
│   └── template-direct-urls.json    # Direct URL input (no search API)
├── docs/
│   ├── n8n-deep-dive-guide.md           # Comprehensive n8n tutorial
│   └── workflow-analysis.md              # Critical analysis and improvements
├── AGENTS.md                                # AI agent instructions for working with this repo
├── YouTube Channel Management - Real World Lessons.md  # Personal experiences and insights
└── README.md                                # Main repository documentation
```

## Template Quality Standards

### n8n Community Template Requirements

When creating templates intended for n8n's template library (see AGENTS.md):

**Required Elements:**
1. **Sticky Note - Instructions**: Large note explaining purpose, setup steps, and configuration
2. **Configuration Node** (if applicable): Centralized Set node with all configurable parameters
3. **Section Sticky Notes**: Color-coded notes grouping related nodes
4. **Descriptive Node Names**: Clear, action-oriented (e.g., "Fetch Instagram Posts" not "HTTP Request")

**Quality Guidelines:**
- Max 15 nodes per template for maintainability
- Color-coding: Purple (config), Blue (fetch), Yellow (process), Green (output)
- No hardcoded credentials or personal data
- Error handling for critical operations (`alwaysOutputData: true`, `onError: "continueRegularOutput"`)
- Remove `pinData` and personal `instanceId` before publishing
- Test with latest n8n version

## Documentation Standards

### When Creating New Workflows

Each workflow folder should contain:

1. **template.json**: The n8n workflow export
2. **README.md**: Setup instructions including:
   - What it does
   - Required credentials
   - Data Table schema (if applicable)
   - Airtable schema (if applicable)
   - Configuration steps
   - API references
   - Troubleshooting section

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

### Advanced Documentation Patterns

Some complex workflows include specialized documentation:

**Instagram Account Information Tracker:**
- `QUICK-START.md`: Fast-track setup guide
- `CSV-IMPORT-GUIDE.md`: Fastest setup method
- `TROUBLESHOOTING.md`: Common issues and solutions
- `ADVANTAGES.md`: Why use this approach
- `FILES-OVERVIEW.md`: Navigation guide
- `field-types-reference.md`: Complete schema reference
- `CHANGELOG.md`: Version history

**YouTube Channel Settings Audit:**
- `WHICH-VERSION.md`: Comparison between V1 and V2
- `README-V2.md`: Enhanced version documentation
- Separate READMEs for different use cases

Use this pattern when:
- Workflow has multiple setup methods (CSV vs schema workflow)
- Multiple versions serve different use cases
- Complex troubleshooting requires dedicated documentation
- Field/schema reference is extensive

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

## Testing Status

**Fully Tested & Working:**
- Instagram to X
- Upload from Instagram to YouTube
- Auto-Translate YouTube Video (both versions)
- YouTube Channel Watermark Updater
- AI-Powered YouTube Channel Optimizer
- YouTube Featured Channels Updater
- YouTube Channel Subscriber

**Needs End-to-End Testing:**
- YouTube Channel Settings Audit (V1 and V2)
- Instagram Account Information Tracker
- Trend Reshare System
- ADR Researcher
- Web Research Social Links Finder

These untested workflows are structurally complete with full documentation but require live API credentials for full validation.

## Key Architectural Insights

**Evolution from Simple to Complex:**
The repository shows progression from simple single-workflow automation to sophisticated multi-workflow systems:
1. Simple: Instagram to X (single workflow, one API to another)
2. Intermediate: YouTube optimization workflows (single workflow, multiple APIs, AI processing)
3. Advanced: Trend Reshare System (4 workflows, shared data store, AI matching)
4. Infrastructure: Instagram Tracker (schema-as-code, backend abstraction, multiple setup paths)

**Backend Strategy:**
- Early workflows: n8n Data Tables only
- Later workflows: Support both n8n Data Tables AND Airtable
- Rationale: Start simple (n8n), migrate to Airtable only if advanced features needed
- Pattern: Configuration-driven backend routing with IF nodes

**AI Integration Patterns:**
- Translation: Single AI call with structured prompt (YouTube translate)
- Analysis: AI analyzes batch data, returns structured JSON (Channel Optimizer)
- Research: AI agent with tools, memory, iterative conversation (ADR Researcher)
- Extraction: AI + regex combination for accuracy (Web Research)

**Versioning Strategy:**
- `template.json`: Original, stable
- `template-v2.json`: Improvements (bug fixes, better logic)
- `template-v2-enhanced.json`: Feature additions (HTML reports, algorithm focus)
- `template-v2-personal.json`: Pre-configured for specific use case
- `template-anthropic.json`: Alternative technology stack (LLM provider)

**Documentation Philosophy:**
- Single README for simple workflows
- Multiple docs (QUICK-START, TROUBLESHOOTING, etc.) for complex ones
- CSV import guides for fastest adoption
- WHICH-VERSION.md when users need to choose between approaches

## Working with This Repository

**When adding a new workflow:**
1. Determine category: Cross-posting, YouTube management, analytics, AI research
2. Choose backend: n8n Data Tables only, or support both n8n + Airtable
3. Follow naming: `template.json` for base, `-v2` for improvements, `-[variant]` for alternatives
4. Create README with setup instructions and required credentials
5. For complex workflows, add specialized docs (QUICK-START, TROUBLESHOOTING, etc.)
6. Update this CLAUDE.md with workflow details

**When modifying existing workflows:**
1. Read the full README and any specialized docs first
2. For multi-workflow systems, understand the data flow through shared storage
3. Test deduplication logic separately
4. For AI workflows, verify prompt quality and token usage
5. Check API quota impact (document in CLAUDE.md if significant)
6. Create `-v2` rather than overwriting `template.json`
7. Document changes in README or CHANGELOG.md

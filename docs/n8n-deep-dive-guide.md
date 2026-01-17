# N8N Deep Dive Guide: From Zero to Automation Hero

A comprehensive guide to understanding n8n automation workflows, specifically designed for beginners who want to understand the concepts used in this repository.

---

## Table of Contents

1. [What is n8n?](#what-is-n8n)
2. [Core Concepts](#core-concepts)
3. [Understanding Nodes](#understanding-nodes)
4. [Data Flow & Expressions](#data-flow--expressions)
5. [Triggers & Scheduling](#triggers--scheduling)
6. [Credentials & Authentication](#credentials--authentication)
7. [Data Tables (Built-in Database)](#data-tables-built-in-database)
8. [Error Handling & Debugging](#error-handling--debugging)
9. [Best Practices](#best-practices)
10. [How These Workflows Work](#how-these-workflows-work)

---

## What is n8n?

**n8n** (pronounced "n-eight-n") is an open-source workflow automation tool that allows you to connect different services and automate repetitive tasks. Think of it as a visual programming environment where you connect "nodes" (blocks) together to create automated workflows.

### Key Characteristics

- **Visual Builder**: Drag-and-drop interface for creating workflows
- **Self-Hosted or Cloud**: Run on your own server or use n8n's cloud service
- **400+ Integrations**: Pre-built connectors for popular services
- **Code When Needed**: Write JavaScript/Python for custom logic
- **Fair-Code License**: Free to use, with some restrictions for commercial hosting

### Common Use Cases

- Social media cross-posting (like our Instagram to X workflow)
- Content syndication
- Data synchronization between platforms
- Automated reporting
- AI-powered content processing

---

## Core Concepts

### Workflows

A **workflow** is a collection of connected nodes that execute in sequence or parallel. Each workflow:

- Has a **trigger** that starts execution
- Contains **action nodes** that do work
- Can have **conditional logic** to branch execution
- Stores results in the **output**

### Executions

Every time a workflow runs, it creates an **execution**. Executions:

- Have a unique ID
- Store input/output data for each node
- Can be viewed in the execution history
- Help with debugging (you can see what went wrong)

### Items

Data in n8n flows as **items**. An item is a JSON object that passes from node to node. When you fetch 10 Instagram posts, you get 10 items, each containing one post's data.

```json
// Example: One item from Instagram API
{
  "id": "17841405822304914",
  "caption": "Check out this sunset! #photography",
  "permalink": "https://www.instagram.com/p/ABC123/",
  "media_type": "IMAGE"
}
```

---

## Understanding Nodes

Nodes are the building blocks of n8n workflows. There are several types:

### 1. Trigger Nodes (Starting Points)

Trigger nodes start your workflow. They have a lightning bolt icon.

| Trigger Type | Description | Example Use |
|--------------|-------------|-------------|
| **Schedule Trigger** | Runs at specified intervals | Check Instagram every 12 hours |
| **Webhook** | Runs when receiving HTTP request | Real-time notifications |
| **Manual Trigger** | Runs when you click "Execute" | Testing or on-demand tasks |
| **App Triggers** | Watches for events in apps | New email, new file, etc. |

### 2. Action Nodes (Do Something)

Action nodes perform operations on data.

| Node Type | Purpose | Example |
|-----------|---------|---------|
| **HTTP Request** | Call any API | Fetch Instagram posts |
| **Set** | Transform/modify data | Rename fields, add values |
| **Code** | Run JavaScript/Python | Custom data processing |
| **IF** | Conditional branching | Check if post exists |
| **Switch** | Multiple conditions | Route based on media type |
| **Loop Over Items** | Process items one by one | Upload videos sequentially |

### 3. Integration Nodes (App-Specific)

Pre-built connectors for popular services:

- **YouTube**: Upload videos, update metadata, get video info
- **Twitter/X**: Post tweets, read timeline
- **Google Sheets**: Read/write spreadsheet data
- **Slack**: Send messages, create channels

### 4. Data Nodes

Nodes for working with data:

- **Split Out**: Extract array items into separate items
- **Merge**: Combine data from multiple branches
- **Aggregate**: Combine items into one
- **Filter**: Keep only items matching criteria

### 5. Utility Nodes

- **Wait**: Pause execution (rate limiting)
- **Sticky Note**: Documentation within workflow
- **No Operation (NoOp)**: Placeholder for branching

---

## Data Flow & Expressions

### How Data Moves

Data flows left-to-right through connected nodes. Each node:

1. Receives input items from the previous node
2. Processes the data
3. Outputs items to the next node

```
[Trigger] → [Fetch Data] → [Process] → [Save/Send]
    ↓            ↓             ↓            ↓
  Start      10 items      10 items     Success
```

### Expressions

n8n uses expressions to access data dynamically. The syntax is:

```javascript
// Access current item's data
{{ $json.fieldName }}

// Access specific item from node
{{ $('Node Name').item.json.fieldName }}

// Access all items from a node
{{ $('Node Name').all() }}

// JavaScript functions work
{{ $json.caption.substring(0, 100) }}
```

### Common Expression Patterns

```javascript
// Get the first item's ID from "Fetch Posts" node
{{ $('Fetch Posts').first().json.id }}

// Check if field exists
{{ $json.caption || 'No caption' }}

// Format a date
{{ new Date($json.timestamp).toISOString() }}

// Combine fields
{{ $json.firstName + ' ' + $json.lastName }}
```

---

## Triggers & Scheduling

### Schedule Trigger Deep Dive

The Schedule Trigger is the most common way to run workflows automatically.

#### Interval Mode
```
Rule: Every 12 hours
Result: Runs at 00:00, 12:00, 00:00...
```

#### Cron Mode
More precise control using cron expressions:

```
┌───────────── minute (0-59)
│ ┌───────────── hour (0-23)
│ │ ┌───────────── day of month (1-31)
│ │ │ ┌───────────── month (1-12)
│ │ │ │ ┌───────────── day of week (0-6, Sunday=0)
│ │ │ │ │
* * * * *
```

**Examples:**
- `0 0 * * *` - Daily at midnight
- `0 */12 * * *` - Every 12 hours
- `0 9 * * 1` - Every Monday at 9 AM
- `*/15 * * * *` - Every 15 minutes

### Webhook Triggers

Webhooks allow external services to trigger your workflow:

1. n8n provides a unique URL
2. External service sends HTTP request to that URL
3. Workflow executes with the request data

```
External Service → POST https://your-n8n.com/webhook/abc123 → Workflow Runs
```

---

## Credentials & Authentication

### What Are Credentials?

Credentials store authentication details (API keys, OAuth tokens, etc.) securely. They're:

- **Encrypted**: Stored securely in n8n's database
- **Reusable**: Use across multiple workflows
- **Scoped**: Each credential type works with specific nodes

### Credential Types Used in This Repository

#### 1. Meta (Instagram) - HTTP Bearer Token

```
Type: Header Auth
Header Name: Authorization
Header Value: Bearer YOUR_ACCESS_TOKEN
```

**How to get it:**
1. Create a Meta Developer account
2. Create an app with Instagram Graph API access
3. Generate a long-lived access token
4. Token expires every 60 days (refresh it!)

#### 2. Twitter/X - OAuth 2.0

```
Type: OAuth 2.0
Grant Type: PKCE
Scopes: tweet.read, tweet.write, users.read
```

**How to get it:**
1. Create a Twitter Developer account
2. Create a project with OAuth 2.0 credentials
3. Connect in n8n (redirects to Twitter for authorization)

#### 3. YouTube - OAuth 2.0

```
Type: OAuth 2.0
Scopes: youtube.force-ssl, youtube.upload
```

**How to get it:**
1. Create a Google Cloud project
2. Enable YouTube Data API v3
3. Create OAuth 2.0 credentials
4. Connect in n8n

#### 4. Google Gemini - API Key

```
Type: API Key
Header: x-goog-api-key
```

**How to get it:**
1. Go to Google AI Studio
2. Generate an API key
3. Add to n8n credentials

---

## Data Tables (Built-in Database)

n8n has a built-in database feature called **Data Tables** (formerly "n8n Tables"). This is used extensively in these workflows for deduplication.

### Why Use Data Tables?

1. **Prevent Duplicate Actions**: Track what's been processed
2. **Maintain State**: Remember information between executions
3. **Simple Storage**: No external database needed

### How It Works

```
[Fetch Posts] → [Check Table] → [If Not Exists] → [Process] → [Save to Table]
                     ↓                                              ↓
              "Already done?"                              "Mark as done"
```

### Table Structure Example

**Instagram Posts Table:**
| postId (Primary Key) |
|---------------------|
| 17841405822304914 |
| 17841405822304915 |
| 17841405822304916 |

### Operations

```javascript
// Get row(s) - Check if post exists
Filter: postId equals {{ $json.id }}
Result: Returns row if found, empty if not

// Insert row - Save new post
Fields: postId = {{ $json.id }}

// Update row - Modify existing
Filter + New Values

// Delete row - Remove entry
Filter condition
```

---

## Error Handling & Debugging

### Execution History

Every workflow execution is logged. Access it to:

- See which nodes succeeded/failed
- View input/output data at each step
- Replay failed executions

### Common Error Types

| Error | Cause | Solution |
|-------|-------|----------|
| **401 Unauthorized** | Bad credentials | Refresh/update tokens |
| **429 Too Many Requests** | Rate limited | Add Wait nodes |
| **404 Not Found** | Wrong endpoint/ID | Check API documentation |
| **Timeout** | Slow API response | Increase timeout setting |

### Debugging Techniques

1. **Check Execution Data**: Click on execution to see all data
2. **Test Nodes Individually**: Execute single nodes to isolate issues
3. **Use Sticky Notes**: Document expected behavior
4. **Add Logging**: Use Code nodes to log intermediate values

### Error Handling Nodes

```
                    ┌─ [Process] → [Save]
[Trigger] → [Try] ─┤
                    └─ [Error Handler] → [Notify]
```

---

## Best Practices

### 1. Rate Limiting

Always add delays between API calls to avoid rate limits:

```
[Fetch] → [Wait 15s] → [Process] → [Wait 15s] → [Next]
```

### 2. Deduplication

Track processed items to prevent duplicate actions:

```
[Fetch] → [Check Table] → [If New] → [Process] → [Save ID]
```

### 3. Batch Processing

When dealing with many items, process in batches:

```
[Fetch All] → [Loop Over Items] → [Process One] → [Continue Loop]
         ↑                                              │
         └──────────────────────────────────────────────┘
```

### 4. Documentation

Use Sticky Notes to document:
- What each section does
- Required credentials
- Expected inputs/outputs

### 5. Credentials Security

- Never hardcode API keys in Code nodes
- Use n8n's credential system
- Rotate tokens regularly
- Use least-privilege access

### 6. Testing

- Test with Manual Trigger first
- Verify each node's output
- Use small datasets for testing
- Check execution history after runs

---

## How These Workflows Work

### Instagram to X Workflow

```
┌─────────────────────────────────────────────────────────────┐
│                    Every 12 Hours                           │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│  HTTP Request: Fetch latest Instagram post via Graph API    │
│  URL: https://graph.facebook.com/v21.0/{user}/media         │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│  Data Table Lookup: Has this post been shared before?       │
│  Query: SELECT * WHERE postId = {{ $json.id }}              │
└─────────────────────────────────────────────────────────────┘
                            │
              ┌─────────────┴─────────────┐
              │                           │
         Found (Skip)              Not Found (Continue)
              │                           │
              ▼                           ▼
┌──────────────────────┐    ┌──────────────────────────────────┐
│  Do Nothing          │    │  Create Tweet with formatted text │
│  (Already posted)    │    │  "▶️ IG: {caption} {permalink}"   │
└──────────────────────┘    └──────────────────────────────────┘
                                          │
                                          ▼
                            ┌──────────────────────────────────┐
                            │  Save postId to Data Table       │
                            │  (Prevents future duplicates)    │
                            └──────────────────────────────────┘
```

### YouTube Auto-Translate Workflow

```
┌─────────────────────────────────────────────────────────────┐
│                    Manual Trigger                            │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│  Define Languages: Configure target languages                │
│  Example: { "Spanish": "es-419", "Japanese": "ja" }         │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│  YouTube Node: Get all videos (up to 15)                    │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│  Loop Over Items: Process each video sequentially            │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│  Data Table Check: Already translated?                       │
└─────────────────────────────────────────────────────────────┘
                            │
              ┌─────────────┴─────────────┐
              │                           │
         Found (Skip)              Not Found (Translate)
              │                           │
              ▼                           ▼
        [Continue Loop]     ┌──────────────────────────────────┐
                            │  Wait 15 seconds (rate limit)    │
                            └──────────────────────────────────┘
                                          │
                                          ▼
                            ┌──────────────────────────────────┐
                            │  AI Agent: Google Gemini         │
                            │  Translates title + description  │
                            │  into all target languages       │
                            └──────────────────────────────────┘
                                          │
                                          ▼
                            ┌──────────────────────────────────┐
                            │  HTTP Request: PUT to YouTube    │
                            │  Updates video localizations     │
                            └──────────────────────────────────┘
                                          │
                                          ▼
                            ┌──────────────────────────────────┐
                            │  Save videoId to Data Table      │
                            └──────────────────────────────────┘
```

### Instagram to YouTube Upload Workflow

```
┌─────────────────────────────────────────────────────────────┐
│                Daily at Midnight (0 0 * * *)                 │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│  HTTP Request: Fetch 25 recent Instagram posts              │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│  Split Out: Extract each post as separate item              │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│  Loop Over Items: Process each post sequentially            │
└─────────────────────────────────────────────────────────────┘
                            │
              ┌─────────────┴─────────────┐
              │                           │
         Already Uploaded           Not Uploaded
              │                           │
              ▼                           ▼
        [Continue Loop]     ┌──────────────────────────────────┐
                            │  Wait 15 seconds                 │
                            └──────────────────────────────────┘
                                          │
                                          ▼
                            ┌──────────────────────────────────┐
                            │  Code: Shorten title to 100 chars│
                            │  - Truncate at first hashtag     │
                            │  - Include hashtag if space      │
                            └──────────────────────────────────┘
                                          │
                                          ▼
                            ┌──────────────────────────────────┐
                            │  HTTP Request: Download media    │
                            └──────────────────────────────────┘
                                          │
                                          ▼
                            ┌──────────────────────────────────┐
                            │  YouTube Node: Upload video      │
                            │  Category: Entertainment         │
                            │  Privacy: Public                 │
                            └──────────────────────────────────┘
                                          │
                                          ▼
                            ┌──────────────────────────────────┐
                            │  Save postId + youtubeId         │
                            └──────────────────────────────────┘
```

---

## Glossary

| Term | Definition |
|------|------------|
| **Node** | A single step/block in a workflow |
| **Execution** | One run of a workflow |
| **Item** | A unit of data (JSON object) flowing through nodes |
| **Expression** | Dynamic value using `{{ }}` syntax |
| **Credential** | Stored authentication for external services |
| **Trigger** | Node that starts workflow execution |
| **Webhook** | URL endpoint that triggers workflow on HTTP request |
| **Cron** | Schedule format for precise timing control |
| **Data Table** | n8n's built-in database for storing data |

---

## Next Steps

1. **Import a Workflow**: Download `.json` file and import into n8n
2. **Set Up Credentials**: Create and connect required API credentials
3. **Test Manually**: Run with Manual Trigger to verify
4. **Activate**: Enable Schedule Trigger for automatic execution
5. **Monitor**: Check execution history for issues

For specific implementation details and troubleshooting, see the [Workflow Analysis](./workflow-analysis.md) document.

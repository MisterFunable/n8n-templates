# Trend-Based Content Resharing System

Automatically monitor trends relevant to dolls, action figures, LEGO, and board games, match them with your YouTube channel content, and post to X with engagement optimization.

## Overview

This system consists of 4 n8n workflows:

1. **01-airtable-setup.json** - Documents the Airtable schema (run once)
2. **02-youtube-sync.json** - Syncs your YouTube channel videos to Airtable
3. **03-trend-monitor.json** - Monitors Google Trends for niche-relevant topics
4. **04-trend-poster.json** - Matches trends to videos and posts to X

## Prerequisites

### Required Accounts & API Keys

1. **Airtable Account** (Free tier: 1,000 records per base)
   - Create account at https://airtable.com
   - Get API key from https://airtable.com/account

2. **YouTube Data API v3**
   - Enable at https://console.cloud.google.com
   - Create OAuth 2.0 credentials

3. **X/Twitter API v2**
   - Apply for developer access at https://developer.twitter.com
   - Create OAuth 2.0 credentials with read/write permissions

## Setup Instructions

### Step 1: Create Airtable Base

Create a new Airtable base called "Trend Reshare System" with 3 tables:

#### Table: Videos

| Field Name | Field Type | Notes |
|------------|------------|-------|
| videoId | Single line text | Primary field |
| title | Single line text | For keyword matching |
| description | Long text | Full description |
| tags | Single line text | Comma-separated |
| publishedAt | Date | When uploaded |
| thumbnailUrl | URL | Video thumbnail |
| viewCount | Number | Track performance |
| duration | Single line text | Video length |
| lastSharedAt | Date | Prevent over-sharing |
| shareCount | Number | Default: 0 |
| searchableText | Long text | Combined title+desc+tags |

#### Table: Trends

| Field Name | Field Type | Notes |
|------------|------------|-------|
| trendId | Single line text | Primary field |
| keyword | Single line text | The trending term |
| source | Single select | Options: google, reddit, twitter |
| category | Single select | Options: dolls, figures, lego, boardgames, general |
| detectedAt | Date | When first seen |
| trafficVolume | Single line text | From Google Trends |
| relatedQueries | Long text | Related search terms |
| processed | Checkbox | Already acted on |
| matchedVideoId | Link to Videos | Which video matched |

#### Table: Posts

| Field Name | Field Type | Notes |
|------------|------------|-------|
| postId | Single line text | Primary field (X post ID) |
| videoId | Link to Videos | Which video was shared |
| trendId | Link to Trends | Which trend triggered it |
| postText | Long text | The actual post content |
| postedAt | Date | Timestamp |
| likes | Number | Default: 0 |
| retweets | Number | Default: 0 |

### Step 2: Configure n8n Credentials

In n8n, create the following credentials:

1. **Airtable API** - Add your API key and base ID
2. **YouTube OAuth2** - Configure OAuth with your Google credentials
3. **Twitter OAuth2** - Configure with your X developer credentials

### Step 3: Import Workflows

Import each workflow JSON file into n8n:

1. Go to n8n dashboard
2. Click "Import from File"
3. Select each `.json` file
4. Update credential references in each workflow

### Step 4: Configure Settings

In each workflow, update the configuration node with your values:

```json
{
  "youtubeChannelId": "YOUR_CHANNEL_ID",
  "airtableBaseId": "YOUR_BASE_ID"
}
```

## Workflow Details

### Workflow 1: Airtable Setup (01-airtable-setup.json)

**Purpose:** Documentation workflow - shows the schema to create manually

**Run:** Once, manually

### Workflow 2: YouTube Sync (02-youtube-sync.json)

**Purpose:** Loads and syncs your YouTube channel videos to Airtable

**Schedule:** Daily or manual for first load

**Flow:**
```
[Trigger] → [Config] → [YouTube API] → [Loop Videos] →
[Check Airtable] → [Insert New / Update Existing]
```

### Workflow 3: Trend Monitor (03-trend-monitor.json)

**Purpose:** Fetches Google Trends and filters for niche relevance

**Schedule:** Every 6-12 hours

**Niche Keywords Monitored:**
- Dolls: doll, barbie, american girl, fashion doll
- Figures: action figure, collectible, marvel, dc, star wars
- LEGO: lego, brick, minifig, moc
- Board Games: board game, tabletop, dice, gaming

**Flow:**
```
[Schedule] → [Fetch Google Trends RSS] → [Parse XML] →
[Filter by Keywords] → [Save to Airtable]
```

### Workflow 4: Trend Poster (04-trend-poster.json)

**Purpose:** Matches trends to videos and posts to X

**Schedule:** After trend detection or every few hours

**Post Template:**
```
[Video summary - ~150 chars]

#[TrendHashtag] #[VideoTag1] #[VideoTag2]

[YouTube Link]
```

**Flow:**
```
[Trigger] → [Get Unprocessed Trends] → [Search Videos] →
[Check Share Cooldown] → [Generate Post] → [Post to X] →
[Record & Mark Processed]
```

## Configuration Options

Edit the Config nodes in each workflow to customize:

```json
{
  "nicheKeywords": ["doll", "lego", "action figure", "board game", "collectible", "minifig", "tabletop"],
  "excludeKeywords": ["politics", "news", "sports", "crypto"],
  "maxPostsPerDay": 3,
  "minHoursBetweenShares": 24,
  "trendCheckIntervalHours": 12
}
```

## Verification Checklist

- [ ] Airtable tables created with correct field types
- [ ] n8n credentials configured and tested
- [ ] YouTube Sync runs and populates Videos table
- [ ] Trend Monitor detects and filters trends correctly
- [ ] Trend Poster creates properly formatted X posts
- [ ] Posts are recorded in Posts table

## Troubleshooting

### YouTube API Quota Exceeded
- The free quota is 10,000 units/day
- Channel listing uses ~100 units per call
- Run sync less frequently if hitting limits

### No Trends Detected
- Check Google Trends RSS feed is accessible
- Verify niche keywords match your content
- Try broader keywords initially

### X Post Fails
- Verify OAuth 2.0 has write permissions
- Check post doesn't exceed 280 characters
- Ensure no duplicate content (X rejects duplicates)

### Airtable Rate Limits
- Free tier: 5 requests/second
- Add delay nodes between Airtable operations if needed

## Data Flow Diagram

```
┌─────────────────┐     ┌─────────────────┐
│  YouTube API    │     │  Google Trends  │
│  (Your Channel) │     │  (RSS Feed)     │
└────────┬────────┘     └────────┬────────┘
         │                       │
         ▼                       ▼
┌─────────────────┐     ┌─────────────────┐
│  02-youtube-    │     │  03-trend-      │
│  sync.json      │     │  monitor.json   │
└────────┬────────┘     └────────┬────────┘
         │                       │
         ▼                       ▼
┌─────────────────────────────────────────┐
│              AIRTABLE BASE              │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐│
│  │ Videos   │ │ Trends   │ │ Posts    ││
│  └──────────┘ └──────────┘ └──────────┘│
└────────────────────┬────────────────────┘
                     │
                     ▼
         ┌─────────────────────┐
         │  04-trend-poster    │
         │  .json              │
         └──────────┬──────────┘
                    │
                    ▼
         ┌─────────────────────┐
         │    X/Twitter API    │
         │    (Post Content)   │
         └─────────────────────┘
```

## License

For personal/educational use.

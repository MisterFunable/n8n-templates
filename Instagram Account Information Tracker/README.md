# Instagram Account Information Tracker

A flexible n8n workflow system for tracking Instagram account metrics with support for both n8n Data Tables and Airtable backends.

## Overview

This system consists of two workflows that work together:

1. **Table Schema Maker** - Creates table schemas dynamically from JSON definitions
2. **Instagram Account Tracker** - Fetches Instagram metrics and stores them in your chosen backend

### Why This Approach?

Unlike tools like Airtable Omni, this system offers:

- **No Vendor Lock-in**: Switch between n8n Data Tables and Airtable without changing your schema
- **Infrastructure Flexibility**: Start with free n8n Data Tables, migrate to Airtable only if needed
- **Schema as Code**: Version control your table definitions as JSON
- **Reusable Patterns**: Define once, reuse across multiple projects
- **Cost Optimization**: Avoid external service costs until necessary
- **Portability**: Move schemas between environments easily
- **Automation-First**: Built for programmatic table creation vs manual UI work

### When to Use Each Backend

**Use n8n Data Tables when:**
- You want everything self-contained in n8n
- You don't need advanced computed fields
- You want to avoid additional service costs
- You're working with simple to moderate data complexity

**Use Airtable when:**
- You need rich computed fields (rollup, lookup, formula)
- You want a visual interface for data management
- You need collaboration features
- You want automatic metadata tracking

## Repository Structure

```
Instagram Account Information Tracker/
├── README.md                              # This file
├── CSV-IMPORT-GUIDE.md                    # ⚡ Quick CSV import guide
├── TROUBLESHOOTING.md                     # Solutions to common issues
├── ADVANTAGES.md                          # Why use this approach
├── CHANGELOG.md                           # Version history
├── schema-format.json                     # JSON schema specification
├── field-types-reference.md               # Complete field types documentation
├── instagram-metrics-schema.json          # Example schema for Instagram metrics
├── instagram-metrics-table.csv            # ⚡ Ready-to-import CSV (with sample data)
├── instagram-metrics-table-template.csv   # CSV template (headers only)
├── 01-table-schema-maker.json             # Workflow v1: Full-featured
├── 01-table-schema-maker-v2.json          # Workflow v2: Simplified
├── 01-table-schema-to-csv.json            # ⚡ Workflow: Generate CSV from schema
└── 02-instagram-account-tracker.json      # Workflow: Tracks Instagram metrics
```

## Quick Start

> **⚡ Fastest Method:** Import the pre-made CSV file to n8n Data Tables (2 minutes) - See [CSV-IMPORT-GUIDE.md](CSV-IMPORT-GUIDE.md)

> **Alternative:** Use the v2 (simplified) workflow to generate schemas and CSVs

### Method 1: CSV Import (Fastest - Recommended)

1. **Download CSV**: Use `instagram-metrics-table.csv` (or `*-template.csv` for headers only)
2. **Import to n8n**: Data Tables → Add Data Table → Import from CSV
3. **Copy Table ID**: From the URL after creation
4. **Configure Tracker**: Paste ID in `02-instagram-account-tracker.json`

**Complete guide:** [CSV-IMPORT-GUIDE.md](CSV-IMPORT-GUIDE.md)

### Method 2: Schema Workflow

### 1. Create Your Table Schema

First, create or use the example schema (`instagram-metrics-schema.json`):

```json
{
  "tableName": "Instagram Account Metrics",
  "backend": "n8n",
  "fields": [
    {
      "name": "accountId",
      "type": "string",
      "description": "Instagram account ID",
      "required": true
    },
    {
      "name": "followersCount",
      "type": "number",
      "description": "Number of followers"
    }
    // ... more fields
  ]
}
```

### 2. Generate Table Configuration

**Option A: Simplified (Recommended)**

Import `01-table-schema-maker-v2.json` into n8n:

1. In n8n, go to Workflows → Import
2. Select `01-table-schema-maker-v2.json`
3. Open the Configuration node
4. Paste your schema JSON in the `schemaJSON` field
5. Click "Test workflow"
6. Follow the output instructions to create the table

**Option B: Full-Featured**

Import `01-table-schema-maker.json` for file-based input:

1. In n8n, go to Workflows → Import
2. Select `01-table-schema-maker.json`
3. In the Configuration node, set:
   - `schemaSource`: `"file"` or `"manual"`
   - `schemaFilePath`: Path to your schema JSON file (if using file)
   - `schemaJSON`: Paste JSON directly (if using manual)
4. Execute the workflow
5. Follow the output instructions

> **Tip:** Having issues? Check [TROUBLESHOOTING.md](TROUBLESHOOTING.md) for solutions.

### 3. Create the Table

**For n8n Data Tables:**
1. Go to Data Tables in your n8n instance
2. Click "Add Data Table"
3. Name it as specified in the schema output
4. Add columns according to the schema
5. Copy the Data Table ID

**For Airtable:**
1. Use the Airtable API example from the workflow output, or
2. Manually create the table in Airtable UI
3. Copy the Base ID and Table Name

### 4. Set Up Instagram Tracking

Import `02-instagram-account-tracker.json` into n8n:

1. Import the workflow
2. Configure the **Configuration** node:
   ```javascript
   {
     "backend": "n8n",  // or "airtable"
     "dataTableId": "YOUR_DATA_TABLE_ID",  // if using n8n
     "airtableBaseId": "YOUR_AIRTABLE_BASE_ID",  // if using Airtable
     "airtableTableName": "Instagram Account Metrics",
     "insightsPeriod": "day",
     "insightsDays": 7
   }
   ```
3. Add Instagram credentials:
   - Create an Instagram Bearer Token credential
   - Add your Instagram access token
4. Adjust the Schedule Trigger (default: every 6 hours)
5. Activate the workflow

## Instagram API Setup

### Prerequisites

You need an Instagram Business or Creator account connected to a Facebook Page.

### Steps

1. **Create Facebook App**:
   - Go to [Facebook Developers](https://developers.facebook.com/)
   - Create a new app
   - Add "Instagram Basic Display" product

2. **Get Access Token**:
   - Configure Instagram Basic Display
   - Add your Instagram account
   - Generate a long-lived access token
   - Required permissions: `instagram_basic`, `instagram_manage_insights`

3. **Add Token to n8n**:
   - In n8n: Credentials → Add Credential
   - Choose "HTTP Bearer Auth"
   - Enter your access token
   - Name it "Instagram Bearer Token"

### Token Refresh

Instagram access tokens expire. Options:
- Manually refresh every 60 days
- Use the Instagram Refresh Token endpoint
- Consider adding a token refresh workflow

## Metrics Tracked

The default schema tracks:

### Account Information
- Account ID
- Username
- Account Type (BUSINESS, CREATOR, PERSONAL)
- Verification status
- Biography
- Website URL
- Profile picture URL
- Category

### Counts
- Followers
- Following
- Total media posts

### Insights (7-day period)
- Impressions
- Reach
- Profile views
- Website clicks

### Calculated Metrics
- Engagement Rate: `(Reach / Followers) × 100`

### Metadata
- Last checked timestamp
- Status (active, paused, error)
- Notes

## Customizing the Schema

### Adding New Fields

Edit your schema JSON:

```json
{
  "fields": [
    // ... existing fields
    {
      "name": "averageLikes",
      "type": "number",
      "description": "Average likes per post",
      "options": {
        "precision": 2
      }
    }
  ]
}
```

### Using Advanced Field Types

See `field-types-reference.md` for all supported types:

```json
{
  "name": "performanceRating",
  "type": "rating",
  "description": "Manual performance rating",
  "options": {
    "max": 5,
    "icon": "star",
    "color": "yellow"
  }
}
```

### Backend-Specific Features

For Airtable-only features (formulas, rollups):

```json
{
  "name": "engagementScore",
  "type": "formula",
  "description": "Calculated engagement score",
  "options": {
    "formula": "({reach} / {followersCount}) * 100"
  }
}
```

Note: These won't work in n8n Data Tables - handle in the workflow instead.

## Tracking Multiple Accounts

To track multiple Instagram accounts:

### Option 1: Multiple Workflows

1. Duplicate the tracker workflow
2. Configure each with different credentials
3. All write to the same table

### Option 2: Loop Through Accounts

Modify the tracker workflow:

1. Add a "Configuration" entry with account list:
   ```javascript
   {
     "accounts": [
       { "name": "Account 1", "token": "TOKEN_1" },
       { "name": "Account 2", "token": "TOKEN_2" }
     ]
   }
   ```
2. Add a Loop node after Configuration
3. Fetch data for each account
4. All results stored in same table

### Option 3: Separate Tables

1. Create separate schemas for each account
2. Use separate workflows and tables
3. Useful for completely different tracking needs

## Workflow Details

### 01-table-schema-maker.json

**Purpose**: Converts JSON schema into table configuration

**Inputs**:
- JSON schema file or manual input

**Outputs**:
- Table creation instructions
- Field definitions
- n8n or Airtable-specific configuration

**Key Nodes**:
- Configuration: Set schema source
- Check Schema Source: File vs manual input
- Parse JSON: Validate schema
- Check Backend Type: Route to n8n or Airtable logic
- Create n8n/Airtable Schema: Generate backend-specific config

### 02-instagram-account-tracker.json

**Purpose**: Fetches Instagram data and stores it

**Flow**:
1. Schedule Trigger: Runs every 6 hours (configurable)
2. Configuration: Backend and table settings
3. Fetch Account Info: Basic account data
4. Fetch Insights: Metrics for specified period
5. Process & Calculate Metrics: Compute engagement rate
6. Route by Backend: n8n or Airtable path
7. Check if Exists: Deduplication check
8. Insert or Update: Store/update data

**Key Features**:
- Automatic deduplication by accountId
- Calculates engagement rate
- Handles API errors gracefully
- Updates existing records instead of duplicating

## Troubleshooting

Having issues? See **[TROUBLESHOOTING.md](TROUBLESHOOTING.md)** for detailed solutions to common problems.

### Quick Fixes

**Merge node error:** ✅ Fixed in updated workflows - use v2 or re-download v1

**API errors:** Check Instagram token and permissions

**Table not found:** Verify Data Table ID or Airtable Base/Table name

**No data:** Check execution logs and API responses

**For detailed help:** See [TROUBLESHOOTING.md](TROUBLESHOOTING.md)

### Debugging

Enable detailed logging:

1. In n8n workflow settings, enable "Save Execution Progress"
2. Check execution logs for API responses
3. Inspect node outputs to see data flow

### API Rate Limits

Instagram API has rate limits:
- Be conservative with insights period
- Don't run tracker too frequently
- Use 6+ hour intervals for continuous tracking

## Advanced Use Cases

### Historical Tracking

Create a time-series view:

1. Modify schema to include timestamp as part of unique key
2. Store daily snapshots instead of updating
3. Analyze growth trends over time

### Multi-Account Dashboard

Combine with n8n or Airtable views:

1. Store all accounts in one table
2. Create filtered views per account
3. Build aggregate metrics across accounts

### Alerting on Metrics

Add conditions to the workflow:

```javascript
// In a Code node after Process & Calculate Metrics
const data = $input.item.json;

if (data.followersCount < 1000) {
  // Trigger alert workflow
}

if (data.engagementRate < 2.0) {
  // Send notification
}
```

### Integration with Other Workflows

Use tracked data in other n8n workflows:

1. Read from Data Table/Airtable
2. Use in content planning workflows
3. Trigger actions based on metrics

## Schema Version Control

Store schemas in git:

```bash
git add instagram-metrics-schema.json
git commit -m "Add follower growth tracking"
```

Benefits:
- Track schema changes over time
- Collaborate on schema design
- Rollback if needed
- Document schema evolution

## Migration Between Backends

To switch from n8n to Airtable (or vice versa):

1. Update schema `backend` field
2. Run Table Schema Maker workflow
3. Create table in new backend
4. Update tracker workflow Configuration
5. Export data from old backend
6. Import into new backend
7. Activate tracker

## Performance Considerations

### n8n Data Tables
- Fast for simple queries
- Limited to n8n instance capacity
- No external API calls
- Good for < 10k records

### Airtable
- More powerful for complex queries
- API rate limits apply
- Additional cost at scale
- Rich UI for exploration

## Security

### API Tokens

- Store Instagram tokens securely in n8n credentials
- Never commit tokens to git
- Rotate tokens regularly
- Use environment variables for sensitive data

### Data Privacy

- Instagram data may contain personal information
- Ensure compliance with data protection regulations
- Limit data retention as appropriate
- Secure your n8n instance

## Contributing

To add new schemas or improve workflows:

1. Create new schema JSON files
2. Test with both backends
3. Document in README
4. Submit PR (if public repo)

## Related Workflows

This system follows patterns from:
- **Instagram to X**: Cross-posting with deduplication
- **Auto-Translate YouTube Video Content**: n8n Data Tables usage
- **Trend Reshare System**: Airtable integration patterns

## Support

For issues:
1. Check troubleshooting section
2. Review field-types-reference.md
3. Inspect workflow execution logs
4. Verify API credentials and permissions

## License

Same as parent repository.

## Changelog

### Version 1.0.0 (2026-01-23)
- Initial release
- Table Schema Maker workflow
- Instagram Account Tracker workflow
- Support for n8n Data Tables and Airtable
- Comprehensive field types
- Documentation and examples

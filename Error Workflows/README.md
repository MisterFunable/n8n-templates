# Error Workflows

A collection of error handler workflows that send alerts when other n8n workflows fail.

## Available Notifiers

| Notifier | Description | Complexity |
|----------|-------------|------------|
| [Slack Notifier](./Slack%20Notifier) | Send alerts to Slack channels | Medium (requires Slack App) |
| [Discord Notifier](./Discord%20Notifier) | Send alerts to Discord channels | Easy (just needs webhook URL) |

## How Error Workflows Work

In n8n, you can designate any workflow as an "Error Workflow" that triggers when another workflow fails:

```
[Your Workflow] → (fails) → [Error Workflow] → [Send Alert]
```

### Setting Up

1. Import one of the error workflows below
2. Configure it with your notification settings
3. In your other workflows, go to **Settings** → **Error Workflow** → Select the error workflow
4. When that workflow fails, you'll receive an alert!

## Quick Comparison

| Feature | Slack | Discord |
|---------|-------|---------|
| Setup complexity | Medium | Easy |
| Requires app/bot | Yes (Slack App) | No (just webhook) |
| Rich formatting | Block Kit | Embeds |
| Role/user mentions | Yes | Yes |
| Private channels | Requires bot invite | N/A |
| Cost | Free | Free |

## Which One Should I Use?

**Choose Slack if:**
- Your team already uses Slack
- You need advanced features (threads, reactions)
- You want bot interactions

**Choose Discord if:**
- You want the simplest setup
- You don't want to manage a Slack app
- You use Discord for team communication

## Alert Contents (Both)

All error workflows capture:
- Workflow name that failed
- Environment label (production, staging, etc.)
- Node that caused the error
- Error message (truncated if needed)
- Direct link to the failed execution
- Timestamp

## Folder Structure

```
Error Workflows/
├── README.md                 # This file
├── Slack Notifier/
│   ├── template.json         # n8n workflow
│   └── README.md             # Setup instructions
└── Discord Notifier/
    ├── template.json         # n8n workflow
    └── README.md             # Setup instructions
```

## Future Additions

Potential notifiers to add:
- Email Notifier
- Microsoft Teams Notifier
- Telegram Notifier
- PagerDuty Notifier
- Webhook (generic) Notifier

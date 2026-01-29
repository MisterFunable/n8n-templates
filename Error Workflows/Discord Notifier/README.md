# Error Workflow - Discord Notifier

A dedicated error handler workflow that sends formatted alerts to Discord when other workflows fail.

## What It Does

When any linked workflow fails, this workflow:
1. Captures the error details
2. Formats a rich Discord embed message
3. Sends an alert via webhook with a direct link to the failed execution

## Alert Preview

The Discord alert includes:
- **Color-coded embeds** based on error type:
  - Red: Default errors
  - Yellow: Timeout errors
  - Orange: Rate limit errors
  - Purple: Authentication errors (401/403)
- Workflow name that failed
- Environment label (production, staging, etc.)
- Failed node name
- Error message (in code block)
- Clickable "View Execution" link
- Timestamp and execution IDs in footer

---

## Quick Start

### Step 1: Create a Discord Webhook

1. Open Discord and go to your server
2. Right-click the channel where you want alerts → **Edit Channel**
3. Go to **Integrations** → **Webhooks**
4. Click **New Webhook**
5. (Optional) Customize the name and avatar
6. Click **Copy Webhook URL**

### Step 2: Import and Configure Workflow

1. Import `template.json` into n8n
2. Update the **Configuration** node:

| Setting | Description | Example |
|---------|-------------|---------|
| `webhookUrl` | Discord webhook URL | `https://discord.com/api/webhooks/...` |
| `mentionRole` | Role to mention (optional) | `<@&123456789>` or empty |
| `n8nBaseUrl` | Your n8n instance URL | `https://n8n.yourcompany.com` |
| `environment` | Label for alerts | `production` |
| `botName` | Bot display name | `n8n Alerts` |

### Step 3: Link to Other Workflows

For each workflow you want to monitor:
1. Open the workflow
2. Click **Settings** (gear icon)
3. Find **Error Workflow**
4. Select "Error Workflow - Discord Notifier"
5. Save

---

## Configuration Examples

### Production with role mention
```json
{
  "webhookUrl": "https://discord.com/api/webhooks/123/abc...",
  "mentionRole": "<@&123456789012345678>",
  "n8nBaseUrl": "https://n8n.yourcompany.com",
  "environment": "production",
  "botName": "n8n Production Alerts"
}
```

### Staging without mentions
```json
{
  "webhookUrl": "https://discord.com/api/webhooks/456/def...",
  "mentionRole": "",
  "n8nBaseUrl": "https://n8n-staging.yourcompany.com",
  "environment": "staging",
  "botName": "n8n Staging"
}
```

---

## Discord Mention Formats

| Type | Format | Example |
|------|--------|---------|
| Role | `<@&ROLE_ID>` | `<@&123456789012345678>` |
| User | `<@USER_ID>` | `<@123456789012345678>` |
| Everyone | `@everyone` | Mentions all members |
| Here | `@here` | Mentions online members |

### How to Get Role ID

1. Go to Discord Server Settings → Roles
2. Right-click the role → **Copy ID**
   - (Enable Developer Mode in Discord settings if you don't see this option)

---

## How It Works

```
[Error Trigger] → [Configuration] → [Extract Details] → [Send Discord Alert]
```

1. **Error Trigger** - Fires when a linked workflow fails
2. **Configuration** - Holds webhook URL and settings
3. **Extract Details** - Parses error data, determines embed color
4. **Send Discord Alert** - Posts embed message via webhook

---

## Webhook vs Bot

This workflow uses **Discord Webhooks**, not a Discord bot:

| Feature | Webhook (this workflow) | Bot |
|---------|------------------------|-----|
| Setup | Just copy URL | Create app, add to server |
| Credentials | None needed in n8n | Requires bot token |
| Messages | One-way (send only) | Can read & respond |
| Best for | Simple notifications | Interactive features |

**Webhooks are perfect for error alerts** - simple setup, no bot management!

---

## Troubleshooting

### Messages not appearing

1. Verify the webhook URL is correct and complete
2. Check the webhook hasn't been deleted in Discord
3. Test the webhook with a simple curl command:
   ```bash
   curl -H "Content-Type: application/json" \
        -d '{"content": "Test message"}' \
        YOUR_WEBHOOK_URL
   ```

### "Invalid Webhook Token" error

- The webhook was deleted or regenerated
- Copy a fresh webhook URL from Discord

### Embed not showing properly

- Check the JSON body for syntax errors
- Error messages with special characters might need escaping
- Keep error message under 1024 characters per field

### Role mention not working

- Verify the role ID format: `<@&ROLE_ID>`
- The webhook must have permission to mention the role
- Try `@everyone` or `@here` to test mentions work

---

## Embed Color Reference

The workflow automatically sets colors based on error type:

| Error Type | Color | Decimal Value |
|------------|-------|---------------|
| Default | Red | 15548997 |
| Timeout | Yellow | 16776960 |
| Rate Limit | Orange | 16744448 |
| Auth (401/403) | Purple | 10038562 |

---

## Extending the Workflow

### Add multiple webhooks

Duplicate the "Send Discord Alert" node and configure different webhook URLs for different channels (e.g., #critical-alerts vs #all-alerts).

### Filter by workflow

Add an IF node before sending:
```javascript
{{ $json.workflowName.includes("Production") }}
```

### Add custom fields

Modify the JSON body in "Send Discord Alert" to add more embed fields:
```json
{
  "name": "Custom Field",
  "value": "Your value",
  "inline": true
}
```

---

## Related Documentation

- [Discord Webhooks Guide](https://support.discord.com/hc/en-us/articles/228383668-Intro-to-Webhooks)
- [Discord Embed Visualizer](https://discohook.org/)
- [n8n Error Handling](https://docs.n8n.io/flow-logic/error-handling/)

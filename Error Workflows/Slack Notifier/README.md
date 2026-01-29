# Error Workflow - Slack Notifier

A dedicated error handler workflow that sends formatted alerts to Slack when other workflows fail.

## What It Does

When any linked workflow fails, this workflow:
1. Captures the error details
2. Formats a rich Slack message with Block Kit
3. Sends an alert with a direct link to the failed execution

## Alert Preview

The Slack alert includes:
- **Dynamic severity emoji** based on error type:
  - :rotating_light: Default errors
  - :hourglass: Timeout errors
  - :snail: Rate limit errors
  - :lock: Authentication errors (401/403)
- Workflow name that failed
- Environment label (production, staging, etc.)
- Failed node name
- Timestamp (UTC)
- Error message (cleaned and truncated if too long)
- "View Execution" button linking directly to the failed run
- Execution and Workflow IDs

---

## Quick Start

### Step 1: Create a Slack App

1. Go to [api.slack.com/apps](https://api.slack.com/apps)
2. Click **Create New App** → **From scratch**
3. Name it (e.g., "n8n Alerts") and select your workspace

### Step 2: Configure Bot Scopes

In your Slack App → **OAuth & Permissions** → **Bot Token Scopes**, add:

| Scope | Required For |
|-------|--------------|
| `chat:write` | Post messages |
| `chat:write.public` | Post to public channels without joining |
| `groups:read` | **Required for private channels** |

> **Important**: After adding scopes, click **Install to Workspace** (or **Reinstall** if already installed).

### Step 3: Copy the Bot Token

Still in **OAuth & Permissions**:
1. Find **Bot User OAuth Token**
2. Copy the token (starts with `xoxb-`)

### Step 4: Create n8n Credential

In n8n:
1. Go to **Credentials** → **Add Credential**
2. Search for **"Slack API"** (NOT "Slack OAuth2 API")
3. Paste your Bot Token (`xoxb-...`)
4. Save

> **Why "Slack API" not "Slack OAuth2 API"?**
> - OAuth2 sends messages as your personal user
> - Slack API with Bot Token sends messages as your app (recommended)

### Step 5: Invite Bot to Private Channels

**This step is required for private channels!**

In Slack, go to your private channel and type:
```
/invite @YourBotName
```

Or: Click channel name → **Integrations** → **Add apps** → Select your app

### Step 6: Configure the Workflow

Import `template.json` into n8n, then update the **Configuration** node:

| Setting | Description | Example |
|---------|-------------|---------|
| `slackChannel` | Channel name or ID | `#n8n-alerts` or `C0123456789` |
| `mentionOnError` | Optional mention | `<!channel>`, `<@U12345>`, or empty |
| `n8nBaseUrl` | Your n8n instance URL | `https://n8n.yourcompany.com` |
| `environment` | Label for alerts | `production` |

### Step 7: Link to Other Workflows

For each workflow you want to monitor:
1. Open the workflow
2. Click **Settings** (gear icon)
3. Find **Error Workflow**
4. Select "Error Workflow - Slack Notifier"
5. Save

---

## Channel Configuration

### Public Channels

Use the channel name with `#`:
```json
"slackChannel": "#n8n-alerts"
```

### Private Channels (Recommended: Use Channel ID)

For private channels, using the Channel ID is more reliable:

1. In Slack, right-click the private channel
2. Click **Copy link**
3. Extract the ID from the URL: `https://workspace.slack.com/archives/C0123456789`
4. Use the ID (no `#`):

```json
"slackChannel": "C0123456789"
```

**Remember**: The bot must be invited to private channels first!

---

## Configuration Examples

### Production with @channel mention
```json
{
  "slackChannel": "#n8n-production-alerts",
  "mentionOnError": "<!channel>",
  "n8nBaseUrl": "https://n8n.yourcompany.com",
  "environment": "production"
}
```

### Private channel with Channel ID
```json
{
  "slackChannel": "C0123456789",
  "mentionOnError": "",
  "n8nBaseUrl": "https://app.n8n.cloud/workflow",
  "environment": "production"
}
```

### Mention specific user on error
```json
{
  "slackChannel": "#n8n-alerts",
  "mentionOnError": "<@U0123456789>",
  "n8nBaseUrl": "https://n8n.yourcompany.com",
  "environment": "staging"
}
```

---

## Slack Mention Formats

| Type | Format | Result |
|------|--------|--------|
| Everyone | `<!channel>` | Notifies all channel members |
| Active only | `<!here>` | Notifies active members |
| Specific user | `<@USER_ID>` | `<@U0123456789>` |
| User group | `<!subteam^GROUP_ID>` | `<!subteam^S0123456789>` |

**To find a User ID**: Click on a user in Slack → View profile → More → Copy member ID

---

## How It Works

```
[Error Trigger] → [Configuration] → [Extract Details] → [Send Slack Alert]
```

1. **Error Trigger** - Fires when a linked workflow fails
2. **Configuration** - Holds Slack channel and settings
3. **Extract Details** - Parses error data, determines severity emoji
4. **Send Slack Alert** - Posts Block Kit message to Slack

---

## Troubleshooting

### "channel_not_found" Error

**For private channels:**
1. Verify the bot is invited to the channel: `/invite @YourBotName`
2. Add `groups:read` scope in Slack App → OAuth & Permissions
3. Reinstall the app after adding scopes
4. Try using Channel ID instead of name

**For public channels:**
- Verify channel name is correct (case-sensitive)
- Include `#` prefix if using name
- Try using Channel ID instead

### Messages appearing as my user (not bot)

You're using the wrong credential type:

| Credential | Token Type | Result |
|------------|------------|--------|
| Slack OAuth2 API | User Token (`xoxp-`) | Messages as you |
| **Slack API** | Bot Token (`xoxb-`) | Messages as app |

**Fix**: Create a new "Slack API" credential with your Bot Token (`xoxb-...`)

### "missing_scope" Error

1. Go to [api.slack.com/apps](https://api.slack.com/apps) → Your App
2. Go to **OAuth & Permissions**
3. Add the missing scope under **Bot Token Scopes**
4. **Reinstall the app** (button at top of page)
5. Update your n8n credential with the new token

### "View Execution" link not working

- Verify `n8nBaseUrl` matches your n8n instance URL
- Don't include trailing slash
- For n8n Cloud: `https://app.n8n.cloud`
- For self-hosted: `https://your-domain.com`

### No alerts being sent

1. Verify this workflow is **saved and active**
2. Check that other workflows have this set as their Error Workflow
3. Check this workflow's execution history for errors
4. Test by manually triggering an error in a linked workflow

---

## Required Slack Scopes Reference

| Scope | Purpose | Required? |
|-------|---------|-----------|
| `chat:write` | Post messages | Yes |
| `chat:write.public` | Post to public channels without joining | Recommended |
| `groups:read` | Access private channels | For private channels |

---

## Extending the Workflow

### Filter alerts by workflow name
Add an IF node before "Send Slack Alert":
```javascript
{{ $json.workflowName.includes("Production") }}
```

### Send to different channels by severity
Duplicate the Slack node and add routing logic based on error type.

### Add email notifications
Add an Email node after "Extract Details" to send email alongside Slack.

---

## Related Documentation

- [n8n Error Handling](https://docs.n8n.io/flow-logic/error-handling/)
- [Slack Block Kit Builder](https://app.slack.com/block-kit-builder)
- [Slack API Documentation](https://api.slack.com/docs)
- [Slack Bot Token Scopes](https://api.slack.com/scopes)

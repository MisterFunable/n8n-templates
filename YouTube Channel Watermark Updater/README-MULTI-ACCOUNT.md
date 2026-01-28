# YouTube Channel Watermark Updater - Multi-Account Version

Update watermarks for up to 7 YouTube channels in a single workflow run.

## What It Does

Processes multiple YouTube accounts sequentially, setting the same watermark image on all channels. Perfect for managing multiple channels efficiently.

**Key Features:**
- Process up to 7 accounts in one run
- Automatic rate limiting (3-second delay between accounts)
- Result logging for each account (success/failure)
- Uses the same watermark image across all channels

## Requirements

### Google Cloud Setup
1. **Google Cloud Project** with YouTube Data API v3 enabled
2. **OAuth 2.0 Credentials** for each YouTube account

### n8n Setup
- **YouTube OAuth2 API** credentials for each account (up to 7)
- All credentials must be configured in n8n before running

## Setup Instructions

### 1. Create YouTube OAuth2 Credentials for Each Account

For each YouTube channel:
1. In n8n: Credentials → Add Credential → YouTube OAuth2 API
2. Enter Google Cloud Client ID & Secret
3. Connect and authorize for that specific YouTube account
4. **Note the Credential ID** (see "Finding Credential IDs" below)

### 2. Configure the Workflow

1. **Import the Template**
   - In n8n: Workflows → Import from File
   - Select `template-v2-multi-account.json`

2. **Edit "Setup Accounts" Node**
   - Click the "Setup Accounts" Code node
   - Replace the credential IDs:
   ```javascript
   const accounts = [
     { name: 'Account 1', credentialId: 'abc123xyz' },  // Your actual ID
     { name: 'Account 2', credentialId: 'def456uvw' },  // Your actual ID
     // ... up to 7 accounts
   ];
   ```
   - Update account names (optional, for logging)
   - Change `watermarkUrl` if using a different image

3. **Run the Workflow**
   - Click "Execute Workflow"
   - Monitor the "Log Result" node to see success/failure for each account

## Finding Credential IDs

**Method 1: Via URL**
1. Go to n8n Credentials page
2. Click on a YouTube OAuth2 API credential
3. Look at browser URL: `https://your-n8n.com/credentials/[CREDENTIAL_ID]`
4. Copy that ID

**Method 2: Via n8n API**
Run this in a Code node:
```javascript
// This won't work directly, but you can see IDs in execution data
return [{ json: { credentialId: 'visible in execution' } }];
```

**Method 3: Check credential configuration**
Some n8n versions show the ID in the credential edit screen.

## Workflow Structure

```
Manual Trigger
    ↓
Setup Accounts (list of 7 accounts)
    ↓
Loop Over Accounts ←─────────────────────┐
    ↓                                     │
Get Channel ID                            │
    ↓                                     │
Extract Channel Info                      │
    ↓                                     │
Download Watermark                        │
    ↓                                     │
    ├─→ Initiate Upload                   │
    └─→ Keep Binary                       │
         ↓                                │
       Merge                              │
         ↓                                │
    Upload Image                          │
         ↓                                │
    Log Result (shows success/failure)    │
         ↓                                │
    Wait 3 Seconds (rate limiting)        │
         │                                │
         └──────────────────────────────┘
```

## Understanding the Output

After execution, check the "Log Result" node. Each item shows:

```json
{
  "status": "SUCCESS",
  "channelName": "Your Channel Name",
  "channelId": "UCxxxxxxxxxxxxxxxxxxxxxx",
  "responseCode": 200
}
```

- **status**: `SUCCESS` or `FAILED`
- **channelName**: The YouTube channel name
- **channelId**: The YouTube channel ID
- **responseCode**: HTTP status (200/204 = success)

## Rate Limiting & Quotas

- **3-second delay** between accounts (prevents rate limiting)
- **~51 quota units** per account (1 for channel + 50 for watermark)
- **7 accounts** = ~357 quota units total
- **Daily limit**: 10,000 units (you can run this ~28 times per day)

## Important Notes

### Watermark Activation Delay

**The workflow may complete successfully but watermarks might not appear immediately.**

- YouTube processing can take 5-10 minutes per channel
- Check YouTube Studio: Customization → Branding → Video watermark
- Refresh in incognito mode to see changes faster

### Common Issues

| Issue | Solution |
|-------|----------|
| Wrong credential ID | Verify credential IDs in n8n credentials page |
| Only some accounts succeed | Check failed accounts' OAuth permissions |
| Watermark not visible | Wait 10 minutes, refresh YouTube Studio |
| Rate limit errors | Increase wait time in "Wait 3 Seconds" node |

## Customization

### Change Watermark Image

Edit the "Setup Accounts" node:
```javascript
const watermarkUrl = 'https://your-image-url.com/watermark.png';
```

### Adjust Wait Time

Edit the "Wait 3 Seconds" node:
- Increase to 5-10 seconds if experiencing rate limits
- Decrease to 1-2 seconds if confident about API limits

### Process Fewer Accounts

In "Setup Accounts" node, remove or comment out accounts you don't need:
```javascript
const accounts = [
  { name: 'Account 1', credentialId: 'abc123' },
  { name: 'Account 2', credentialId: 'def456' },
  // { name: 'Account 3', credentialId: 'ghi789' },  // Commented out
];
```

## Comparison: Single vs Multi-Account

| Feature | Single Account (template.json) | Multi-Account (template-v2) |
|---------|-------------------------------|----------------------------|
| Accounts per run | 1 | Up to 7 |
| Setup complexity | Low | Medium |
| Result logging | Manual check | Automatic per account |
| Use case | One-time or single channel | Managing multiple channels |
| Execution time | ~5-10 seconds | ~30-60 seconds (all accounts) |

## Troubleshooting

### All Accounts Fail

1. Check if YouTube Data API v3 is enabled in Google Cloud
2. Verify all credentials are properly authorized
3. Check n8n logs for detailed error messages

### Some Accounts Succeed, Others Fail

1. Re-authorize failed accounts' OAuth credentials
2. Check if those accounts have existing watermarks (remove first)
3. Verify quota hasn't been exceeded

### Execution Stops Mid-Run

1. Check n8n timeout settings
2. Increase wait time between accounts
3. Process fewer accounts per run

## API Reference

- [YouTube Data API v3 - Channels: list](https://developers.google.com/youtube/v3/docs/channels/list)
- [YouTube Data API v3 - Watermarks: set](https://developers.google.com/youtube/v3/docs/watermarks/set)

# YouTube Channel Subscriber

An n8n workflow to subscribe to a predefined list of YouTube channels, automatically excluding the calling channel.

## What It Does

Subscribes your YouTube account to a list of channels with intelligent handling:

1. Automatically detects your channel ID
2. Filters out YOUR channel (can't subscribe to yourself)
3. Loops through remaining channels with rate limiting
4. Handles "already subscribed" gracefully
5. Generates a detailed report

## Default Channels

| # | Channel | ID |
|---|---------|---|
| 1 | Mechenable | `UC434hYDRQ_tzPUtSUAKNxwA` |
| 2 | Jueguenable | `UCEH0sxRdQsy-y6-I-7V0pvw` |
| 3 | Dollenable | `UCvF_vV9iGZowLL_SxmKJ7_Q` |
| 4 | Legoenable | `UCXhYzdDMIFpKWJgY2PRpfnQ` |
| 5 | Posenable | `UCG2lQGYhvB9MdKiPZK7_XKQ` |
| 6 | Statuenable | `UC306rH2OGwvmuI1E-I3SsHg` |

## Requirements

### Google Cloud Setup
1. **Google Cloud Project** with YouTube Data API v3 enabled
2. **OAuth 2.0 Credentials** (Client ID and Client Secret)

### n8n Setup
- **YouTube OAuth2 API** credential with `youtube.force-ssl` scope

## Setup Instructions

1. **Import the Template**
   - In n8n: Workflows → Import from File
   - Select `template.json`

2. **Configure YouTube OAuth2 Credentials**
   - Connect credentials to:
     - "Get My Channel" node
     - "Subscribe to Channel" node

3. **Run the Workflow**
   - Click "Execute Workflow"
   - Review the final report

## Workflow Structure

```
Manual Trigger → Get My Channel → Set Configuration → Filter & Split Channels
                                                              ↓
                                                      Loop Over Channels
                                                       ↙           ↘
                                            Subscribe to Channel    Generate Report
                                                    ↓
                                              Process Result
                                                    ↓
                                                  Wait (2s)
                                                    ↓
                                              (back to loop)
```

## Output Report

```json
{
  "summary": {
    "callingChannel": "Mechenable",
    "totalProcessed": 5,
    "newSubscriptions": 3,
    "alreadySubscribed": 2,
    "errors": 0
  },
  "details": {
    "newlySubscribed": ["Jueguenable", "Dollenable", "Legoenable"],
    "alreadySubscribed": ["Posenable", "Statuenable"],
    "failed": []
  }
}
```

## API Reference

- [YouTube Data API v3 - Subscriptions: insert](https://developers.google.com/youtube/v3/docs/subscriptions/insert)

**Quota Cost:**
- `channels.list`: 1 unit
- `subscriptions.insert`: 50 units each

**Total:** ~251 units for 5 subscriptions (1 + 50×5)

## Rate Limiting

The workflow includes a 2-second delay between subscriptions to avoid YouTube's "too many recent subscriptions" error. If you still encounter this error, increase the Wait node duration.

## Error Handling

| Status | Meaning |
|--------|---------|
| `SUBSCRIBED` | Successfully subscribed |
| `ALREADY_SUBSCRIBED` | Was already subscribed (not an error) |
| `FORBIDDEN` | Rate limited or other restriction |
| `ERROR` | API error occurred |

## Troubleshooting

| Error | Solution |
|-------|----------|
| `subscriptionDuplicate` | Already subscribed - handled gracefully |
| `subscriptionForbidden` | Rate limited - wait a few hours and try again |
| `subscriptionForbidden` | Max subscriptions reached - unsubscribe from some channels |
| `publisherNotFound` | Channel ID is invalid or channel was deleted |

## Related Workflows

- **YouTube Featured Channels Updater** - Set featured channels on home tab
- **YouTube Channel Watermark Updater** - Set channel watermark

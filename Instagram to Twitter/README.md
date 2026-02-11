# Instagram to Twitter

Automatically cross-post Instagram videos to Twitter with full caption preservation and intelligent deduplication.

## Description

This workflow fetches your latest Instagram posts, filters for video content (REELS and VIDEO only), and automatically posts them to Twitter with the original caption and media. Perfect for maintaining a consistent presence across both platforms without manual work.

## How It Works

1. **Schedule Trigger**: Runs daily at midnight (customizable cron expression)
2. **Fetch Instagram Posts**: Retrieves latest 25 posts from your Instagram account
3. **Filter Videos Only**: Processes only VIDEO and REELS media types (skips images/carousels)
4. **Deduplication Check**: Queries Data Table to prevent posting the same content twice
5. **Wait for Rate Limiting**: 15-second delay between posts to respect API limits
6. **Process Caption**: Extracts caption text and handles character limits for Twitter
7. **Download Video**: Fetches video file from Instagram CDN
8. **Upload to Twitter**: Two-step process:
   - Upload media to Twitter's media endpoint (gets media_id)
   - Create tweet with media_id and caption text
9. **Save Record**: Stores postId and tweetId in Data Table for future deduplication

## Configuration Options

Edit the **Configuration** node to customize behavior:

```json
{
  "includeSourceLink": true,    // Include Instagram permalink in tweet
  "waitTimeoutSeconds": 15,     // Delay between posts (rate limiting)
  "videoOnly": true             // Only process VIDEO and REELS
}
```

### Configuration Details

| Setting | Type | Default | Description |
|---------|------|---------|-------------|
| `includeSourceLink` | boolean | `true` | Adds "Originally posted on Instagram: [link]" to tweet text |
| `waitTimeoutSeconds` | number | `15` | Seconds to wait between processing each post |
| `videoOnly` | boolean | `true` | Filter to only VIDEO/REELS (skip images) |

## Requirements

### Instagram Credentials
- **Meta Developer Portal** account with Instagram Basic Display API or Instagram Graph API
- **Bearer Token** for authentication (60-day expiration)
- Token must have permissions: `instagram_basic`, `pages_read_engagement`

### Twitter Credentials
- **Twitter Developer Account** (Free tier works)
- **Generic OAuth 2.0 API** credentials (required for media upload)
- Scopes required: `tweet.read`, `tweet.write`, `users.read`, `offline.access`, `media.write`
- **Important**: Must use Generic OAuth2 credential, not n8n's built-in Twitter OAuth2 (missing `media.write` scope)

### n8n Data Table
Create a Data Table named **"Instagram to Twitter"** with the following schema:

| Field Name | Type | Description |
|------------|------|-------------|
| `postId` | string | Instagram post ID (primary key) |
| `tweetId` | string | Twitter tweet ID |

## Setup Steps

### 1. Configure Instagram Credentials

1. Go to [Meta for Developers](https://developers.facebook.com/)
2. Create an app with Instagram Basic Display or Instagram Graph API
3. Generate a User Access Token with required permissions
4. In n8n, create a new **HTTP Header Auth** credential:
   - **Credential Name**: `Instagram Bearer Auth`
   - **Name**: `Authorization`
   - **Value**: `Bearer YOUR_ACCESS_TOKEN_HERE`

### 2. Configure Twitter Credentials

**Important**: You must use **Generic OAuth2 API** credential (not n8n's built-in Twitter OAuth2) to access the `media.write` scope required for uploading videos.

1. Go to [Twitter Developer Portal](https://developer.twitter.com/)
2. Create an app (or use existing app)
3. Under **User authentication settings**, configure OAuth 2.0:
   - **App permissions**: Read and Write
   - **Type of App**: Web App
   - **Callback URL**: `https://your-n8n-instance.com/rest/oauth2-credential/callback`
   - **Website URL**: Your website or n8n instance URL
4. Copy your **Client ID** and **Client Secret**
5. In n8n, create a new **Generic OAuth2 API** credential (NOT Twitter OAuth2):
   - **Credential Name**: `Twitter OAuth2 Generic`
   - **Grant Type**: `PKCE` (Proof Key for Code Exchange)
   - **Authorization URL**: `https://x.com/i/oauth2/authorize`
   - **Access Token URL**: `https://api.x.com/2/oauth2/token`
   - **Client ID**: Paste from Twitter Developer Portal
   - **Client Secret**: Paste from Twitter Developer Portal
   - **Scope**: `tweet.read users.read tweet.write offline.access media.write`
   - **Auth URI Query Parameters**: Leave empty
   - **Authentication**: `Body`
6. Click **Connect my account** and authorize access

### 3. Create Data Table

1. In n8n, navigate to **Projects** → **Data Tables**
2. Click **Create Data Table**
3. Name: `Instagram to Twitter`
4. Add fields:
   - Field 1: `postId` (type: String)
   - Field 2: `tweetId` (type: String)
5. Set `postId` as the primary key

### 4. Import and Configure Workflow

1. Import `template.json` into n8n
2. Update the following nodes with your credential IDs:
   - **Fetch Instagram Posts**: Select your Instagram Bearer Auth credential
   - **Upload Media to Twitter**: Select your Twitter OAuth2 credential
   - **Create Tweet**: Select your Twitter OAuth2 credential
3. Update Data Table IDs in:
   - **Check If Already Posted** node
   - **Save Post Record** node
4. (Optional) Adjust the **Configuration** node settings
5. (Optional) Modify cron expression in **Schedule Trigger** for different timing

### 5. Test the Workflow

1. Click **Test workflow** in n8n editor
2. Manually execute to verify:
   - Instagram posts are fetched
   - Videos are filtered correctly
   - Caption processing works
   - Twitter upload succeeds
   - Data Table records are saved
3. Check your Twitter account for the posted tweet
4. Activate the workflow when ready

## Twitter API Limits

### Free Tier
- **Tweets per month**: 1,500 (write)
- **Tweets per 24 hours**: ~50

### Basic Tier ($100/month)
- **Tweets per month**: 3,000 (write)
- **Tweets per 24 hours**: 100

### Media Upload Limits
- **Video size**: Max 512MB
- **Video length**: Max 2 minutes 20 seconds (Free tier)
- **Character limit**: 280 characters (media doesn't count toward limit)

### Rate Limiting
The workflow includes a 15-second wait between posts to prevent hitting rate limits. Adjust `waitTimeoutSeconds` if needed.

## Troubleshooting

### Instagram Token Expired
**Error**: `HTTP 401 Unauthorized`

**Solution**: Instagram tokens expire after 60 days. Generate a new token and update the Bearer Auth credential.

### Video Too Large for Twitter
**Error**: `Media upload failed - file too large`

**Solution**:
- Twitter Free tier supports videos up to 512MB and 2:20 duration
- Consider upgrading to Twitter Basic tier for larger limits
- Or add a filter to skip videos longer than 2 minutes

### Character Limit Exceeded
**Error**: `Tweet text exceeds 280 characters`

**Solution**: The workflow automatically truncates captions. If you're still hitting this error:
1. Disable `includeSourceLink` in Configuration to save characters
2. Adjust truncation logic in **Process Caption** Code node

### Duplicate Posts
**Issue**: Same post appears multiple times on Twitter

**Solution**:
1. Check Data Table has correct `postId` values
2. Verify **Check If Already Posted** node is querying the right table
3. Ensure primary key is set on `postId` field

### No Videos Found
**Issue**: Workflow runs but doesn't post anything

**Solution**:
1. Verify your Instagram account has VIDEO or REELS posts
2. Check **Filter Videos Only** node is configured correctly
3. Ensure Instagram token has correct permissions

## Advanced Customization

### Change Schedule Timing
Edit the **Schedule Trigger** node's cron expression:

- Every 6 hours: `0 */6 * * *`
- Twice daily (6am, 6pm): `0 6,18 * * *`
- Every Monday at 9am: `0 9 * * 1`

### Include All Media Types
To post images and carousels (not just videos):

1. Delete or disable the **Filter Videos Only** node
2. Update Twitter upload logic to handle images
3. Note: This requires additional development for image handling

### Customize Caption Format
Edit the **Process Caption** Code node to:
- Add prefixes/suffixes to tweets
- Remove specific hashtags
- Apply text transformations

## Related Workflows

- **Instagram to YouTube + Twitter**: Dual-posting to both YouTube and Twitter
- **Instagram to Twitter V2 (Smart Filter)**: AI-powered with NSFW filtering

## API References

- [Instagram Graph API Documentation](https://developers.facebook.com/docs/instagram-api)
- [Twitter API v2 Documentation](https://developer.twitter.com/en/docs/twitter-api)
- [n8n HTTP Request Node](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.httprequest/)
- [n8n Data Table Documentation](https://docs.n8n.io/data/data-table/)

## Testing Status

⏳ **Needs End-to-End Testing**

This workflow is structurally complete with full documentation but requires live API credentials for full validation.

## Version History

- **v1.0** (2026-01): Initial release - Basic Instagram to Twitter cross-posting

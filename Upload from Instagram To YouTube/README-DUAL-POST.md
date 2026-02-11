# Instagram to YouTube + Twitter (Dual Post)

Automatically upload Instagram videos to **BOTH** YouTube and Twitter simultaneously with a single workflow.

## Description

This is a V2 enhancement of the Instagram to YouTube workflow that adds parallel Twitter posting. Perfect for content creators who want to maximize their reach across platforms without running multiple workflows.

## Key Features

- **Parallel Upload**: Uploads to YouTube and Twitter simultaneously (not sequential)
- **Independent Error Handling**: If one platform fails, the other still completes
- **Shared Processing**: Downloads video once, uses same caption/tags for both platforms
- **Platform Tracking**: Records which platforms succeeded for each post
- **Configurable**: Toggle YouTube/Twitter posting independently

## How It Works

1. **Schedule Trigger**: Runs daily at midnight (customizable)
2. **Fetch Instagram Posts**: Retrieves latest 25 posts
3. **Filter Videos Only**: Processes only VIDEO and REELS
4. **Deduplication Check**: Queries Data Table to skip already-uploaded content
5. **Wait for Rate Limiting**: 15-second delay between batches
6. **Process Content for Both Platforms**:
   - YouTube: Generates title (max 100 chars), description, tags
   - Twitter: Formats tweet text (max 280 chars)
7. **Download Video**: Fetches video once from Instagram
8. **PARALLEL UPLOAD**:
   - **Branch 1 (YouTube)**: Uploads video with metadata to YouTube
   - **Branch 2 (Twitter)**: Uploads media → Creates tweet
9. **Merge Results**: Combines outcomes from both platforms
10. **Prepare Upload Record**: Determines which platforms succeeded
11. **Save Record**: Stores comprehensive tracking data

## Configuration Options

Edit the **Configuration** node:

```json
{
  "includeSourceLink": true,    // Include Instagram link in description/tweet
  "waitTimeoutSeconds": 15,     // Delay between posts
  "maxTitleLength": 100,        // Max YouTube title length
  "categoryId": "24",           // YouTube category (24=Entertainment)
  "privacyStatus": "public",    // YouTube: public, private, or unlisted
  "defaultLanguage": "en",      // Video language code
  "postToYouTube": true,        // Enable YouTube posting
  "postToTwitter": true         // Enable Twitter posting
}
```

### Configuration Details

| Setting | Type | Default | Description |
|---------|------|---------|-------------|
| `includeSourceLink` | boolean | `true` | Adds Instagram permalink to both platforms |
| `waitTimeoutSeconds` | number | `15` | Delay between processing posts |
| `maxTitleLength` | number | `100` | YouTube title character limit |
| `categoryId` | string | `"24"` | YouTube category ID |
| `privacyStatus` | string | `"public"` | YouTube privacy setting |
| `defaultLanguage` | string | `"en"` | Video language (ISO 639-1 code) |
| `postToYouTube` | boolean | `true` | Enable/disable YouTube uploads |
| `postToTwitter` | boolean | `true` | Enable/disable Twitter posts |

### YouTube Category IDs

| ID | Category |
|----|----------|
| 1 | Film & Animation |
| 10 | Music |
| 17 | Sports |
| 20 | Gaming |
| 22 | People & Blogs |
| 23 | Comedy |
| 24 | Entertainment |
| 25 | News & Politics |
| 27 | Education |
| 28 | Science & Technology |

## Requirements

### Instagram Credentials
- Meta Developer Portal account
- Bearer Token with permissions: `instagram_basic`, `pages_read_engagement`
- Token expiration: 60 days

### YouTube Credentials
- YouTube OAuth2 API credentials
- Scopes: `youtube.force-ssl`, `youtube.upload`

### Twitter Credentials
- Twitter Developer Account with Elevated access
- OAuth 2.0 API credentials
- Scopes: `tweet.read`, `tweet.write`, `users.read`, `offline.access`

### n8n Data Table

Create a Data Table named **"Instagram Dual Upload"** with this schema:

| Field Name | Type | Description |
|------------|------|-------------|
| `postId` | string | Instagram post ID (primary key) |
| `youtubeId` | string | YouTube video ID (nullable) |
| `tweetId` | string | Twitter tweet ID (nullable) |
| `platform` | string | Which succeeded: "both", "youtube", "twitter", "none" |
| `uploadedAt` | string | ISO timestamp of upload |

## Setup Steps

### 1. Configure All Credentials

**Instagram**:
1. Create Meta Developer app with Instagram API
2. Generate User Access Token
3. Create **HTTP Header Auth** credential in n8n
4. Set header: `Authorization: Bearer YOUR_TOKEN`

**YouTube**:
1. Enable YouTube Data API v3 in Google Cloud Console
2. Create OAuth 2.0 credentials
3. Create **YouTube OAuth2 API** credential in n8n
4. Authorize your account

**Twitter**:
1. Create Twitter Developer app with OAuth 2.0
2. Create **Twitter OAuth2 API** credential in n8n
3. Authorize your account

### 2. Create Data Table

1. Navigate to **Projects** → **Data Tables** in n8n
2. Click **Create Data Table**
3. Name: `Instagram Dual Upload`
4. Add 5 fields as shown in schema above
5. Set `postId` as primary key

### 3. Import and Configure Workflow

1. Import `template-v2-dual-post.json`
2. Update credential IDs in:
   - **Fetch Instagram Posts**: Instagram Bearer Auth
   - **Upload to YouTube**: YouTube OAuth2
   - **Upload Media to Twitter**: Twitter OAuth2
   - **Create Tweet**: Twitter OAuth2
3. Update Data Table IDs in:
   - **Check If Already Uploaded**
   - **Save Dual Upload Record**
4. Adjust **Configuration** node if needed
5. Modify schedule if desired

### 4. Test the Workflow

1. Click **Test workflow**
2. Verify:
   - Instagram posts fetched
   - Videos filtered
   - Content processed for both platforms
   - Both uploads succeed
   - Merge node combines results correctly
   - Data Table record includes both IDs
3. Check both YouTube and Twitter accounts
4. Activate when ready

## Platform Limits & Quotas

### YouTube Data API v3
- **Daily quota**: 10,000 units
- **Upload cost**: 50 units per video
- **Max daily uploads**: ~200 videos
- **File size**: Max 256GB
- **Duration**: Max 12 hours (verified accounts)

### Twitter API
- **Free tier**: 1,500 tweets/month
- **Basic tier** ($100/month): 3,000 tweets/month
- **Video size**: Max 512MB
- **Video duration**: Max 2:20 (Free), 10:00 (Basic)
- **Character limit**: 280 (media doesn't count)

### Rate Limiting Strategy
The workflow includes:
- 15-second wait between posts
- Independent error handling (`continueOnFail: true`)
- Parallel uploads to minimize total time

## Error Handling

### Independent Platform Processing

Each platform upload has `continueOnFail: true`, meaning:
- If YouTube fails → Twitter still attempts
- If Twitter fails → YouTube success is still recorded
- Both failures → Record saved with `platform: "none"`

### Platform Status Values

The `platform` field indicates which succeeded:
- `"both"`: YouTube and Twitter both succeeded
- `"youtube"`: Only YouTube succeeded
- `"twitter"`: Only Twitter succeeded
- `"none"`: Both failed

### Retry Strategy

To retry failed uploads:
1. Query Data Table for records where `platform != "both"`
2. Delete those records
3. Re-run workflow (will process them as new)

## Troubleshooting

### YouTube Upload Fails
**Common causes**:
- Quota exceeded (check [Google Cloud Console](https://console.cloud.google.com/))
- Video too long/large
- Title contains prohibited words
- Description contains external links (unverified accounts)

**Solutions**:
- Set `includeSourceLink: false` to remove Instagram link
- Reduce `maxTitleLength` if titles are problematic
- Check YouTube Studio for specific rejection reasons

### Twitter Upload Fails
**Common causes**:
- Rate limit exceeded (300 tweets/3 hours)
- Video exceeds size/duration limits
- OAuth token expired

**Solutions**:
- Increase `waitTimeoutSeconds` to slow down posting
- Re-authorize Twitter OAuth credential
- Check video meets platform requirements

### Both Platforms Fail
**Check**:
1. Video download succeeds (check **Download Video** node output)
2. Credentials are valid and not expired
3. Instagram video meets size/format requirements
4. Network connectivity

### Partial Success
**Scenario**: Some posts succeed on both platforms, others only on one

**Normal behavior**: This workflow is designed to handle partial failures. Check the `platform` field in Data Table to see which succeeded.

## Advanced Customization

### Disable One Platform Temporarily

In **Configuration** node:
```json
{
  "postToYouTube": false,  // Disable YouTube
  "postToTwitter": true    // Keep Twitter active
}
```

Then add IF nodes before each platform branch to check these flags.

### Different Content for Each Platform

Modify **Process Content for Both Platforms** Code node to:
- Use different titles for YouTube vs Twitter
- Apply platform-specific hashtag strategies
- Customize descriptions separately

### Add More Platforms

Duplicate the Twitter branch and adapt for:
- Facebook/Instagram Reels (cross-post back to Instagram)
- TikTok API
- LinkedIn video posts

## Comparison with Other Workflows

| Feature | Instagram to Twitter | Instagram to YouTube + Twitter | Instagram to Twitter V2 (Smart Filter) |
|---------|---------------------|-------------------------------|---------------------------------------|
| YouTube Upload | ❌ | ✅ | ✅ (filtered) |
| Twitter Upload | ✅ | ✅ | ✅ (always) |
| AI Tag Generation | ❌ | ❌ | ✅ |
| NSFW Filtering | ❌ | ❌ | ✅ |
| Parallel Processing | N/A | ✅ | ✅ |
| Complexity | Simple | Medium | Advanced |

**Use this workflow if**:
- You want to post to both platforms equally
- You don't need content filtering
- You want fast, parallel uploads

**Use Instagram to Twitter V2 (Smart Filter) if**:
- You need NSFW content filtering
- You want AI-generated tags
- YouTube should only get safe content

## Related Workflows

- **Instagram to Twitter** (Basic): Twitter-only cross-posting
- **Instagram to Twitter V2 (Smart Filter)**: AI + NSFW filtering
- **Upload from Instagram to YouTube** (v2): YouTube-only upload

## API References

- [Instagram Graph API](https://developers.facebook.com/docs/instagram-api)
- [YouTube Data API v3](https://developers.google.com/youtube/v3)
- [Twitter API v2](https://developer.twitter.com/en/docs/twitter-api)
- [n8n Merge Node](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.merge/)

## Testing Status

⏳ **Needs End-to-End Testing**

This workflow is structurally complete with full documentation but requires live API credentials for comprehensive validation.

## Version History

- **v2-dual-post** (2026-01): Initial release - Dual platform posting with parallel uploads

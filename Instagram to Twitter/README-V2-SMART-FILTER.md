# Instagram to Twitter V2 - Smart Filter

AI-powered Instagram cross-posting with intelligent NSFW content filtering and automatic tag generation.

## Description

The most advanced Instagram cross-posting workflow that uses AI to generate tags and intelligently routes content based on NSFW detection. NSFW content goes to Twitter only (keeping YouTube brand-safe), while safe content gets dual-posted to both platforms.

## Key Features

- **AI Tag Generation**: Uses Google Gemini to automatically create relevant tags
- **NSFW Filtering**: Keyword-based detection protects YouTube from sensitive content
- **Smart Routing**: NSFW → Twitter only, Safe → Both platforms
- **Preserves Original Titles**: Maintains Instagram caption authenticity
- **Comprehensive Tracking**: Records filtering decisions and AI-generated tags

## How It Works

1. **Schedule Trigger**: Runs daily at midnight
2. **Fetch Instagram Posts**: Retrieves latest 25 posts
3. **Filter Videos Only**: Processes only VIDEO and REELS
4. **Deduplication Check**: Skips already-posted content
5. **Wait for Rate Limiting**: 15-second delay between posts
6. **Process Title and Caption**: Prepares content for both platforms
7. **AI Tag Generation**: Gemini analyzes caption and generates 5-10 relevant tags
8. **NSFW Content Detector**: Checks caption against keyword list
9. **Download Video**: Fetches video from Instagram
10. **Smart Routing**:
    - **If NSFW**: Posts to Twitter only (YouTube skipped)
    - **If Safe**: Posts to both Twitter AND YouTube
11. **Merge Results**: Combines outcomes from routing paths
12. **Save Record**: Tracks filtering decisions and platform success

## Configuration Options

Edit the **Configuration** node:

```json
{
  "includeSourceLink": true,
  "waitTimeoutSeconds": 15,
  "maxTitleLength": 100,
  "categoryId": "24",
  "privacyStatus": "public",
  "defaultLanguage": "en",
  "useAITags": true,
  "preserveOriginalTitle": true,
  "nsfwKeywords": [
    "nsfw", "adult", "18+", "explicit",
    "onlyfans", "spicy", "🔞", "mature", "sensitive"
  ]
}
```

### Configuration Details

| Setting | Type | Default | Description |
|---------|------|---------|-------------|
| `includeSourceLink` | boolean | `true` | Include Instagram permalink |
| `waitTimeoutSeconds` | number | `15` | Delay between posts |
| `maxTitleLength` | number | `100` | Max YouTube title length |
| `categoryId` | string | `"24"` | YouTube category ID |
| `privacyStatus` | string | `"public"` | YouTube privacy setting |
| `defaultLanguage` | string | `"en"` | Video language code |
| `useAITags` | boolean | `true` | Enable AI tag generation |
| `preserveOriginalTitle` | boolean | `true` | Use Instagram caption as title |
| `nsfwKeywords` | array | See above | Keywords for NSFW detection |

## NSFW Filtering Logic

### How Detection Works

The workflow checks the Instagram caption for any keywords in the `nsfwKeywords` array:
- **Case-insensitive matching**: "NSFW", "nsfw", "NsFw" all match
- **Substring matching**: "This is nsfw content" will be detected
- **Emoji support**: Supports Unicode emojis like 🔞

### Default NSFW Keywords

```json
[
  "nsfw", "adult", "18+", "explicit",
  "onlyfans", "spicy", "🔞", "mature", "sensitive"
]
```

### Customizing NSFW Keywords

Edit the Configuration node to add/remove keywords:

**For adult content creators**:
```json
"nsfwKeywords": ["nsfw", "adult", "18+", "explicit", "🔞"]
```

**For general audiences** (more conservative):
```json
"nsfwKeywords": [
  "nsfw", "adult", "18+", "explicit", "onlyfans",
  "spicy", "🔞", "mature", "sensitive", "bikini",
  "swimsuit", "lingerie", "revealing"
]
```

**For specific niches**:
```json
"nsfwKeywords": ["gore", "blood", "violence", "horror", "graphic"]
```

### Important Notes

- **NSFW content is NOT skipped**: It still posts to Twitter
- **YouTube is protected**: Only safe content reaches YouTube
- **Twitter gets everything**: All content (NSFW and safe) goes to Twitter
- **Filtering is logged**: Check `filterReason` field in Data Table

## AI Tag Generation

### How It Works

The workflow uses **Google Gemini 1.5 Flash** (free tier) to:
1. Analyze the Instagram caption
2. Understand content context
3. Generate 5-10 relevant tags
4. Apply tags to both YouTube and Twitter

### Example

**Instagram Caption**:
```
Just finished my latest gaming setup! RGB everything 🎮✨
#gaming #pcgaming #setupgoals #tech
```

**AI-Generated Tags**:
```
gaming,pc setup,rgb,tech,gaming room,battlestation,computer,hardware,gaming pc,setup tour
```

### AI Prompt

The workflow sends this prompt to Gemini:
```
Analyze this Instagram caption and generate 5-10 relevant hashtags/tags for YouTube and Twitter.

Caption: [Instagram caption here]

Return ONLY a comma-separated list of tags (without # symbols). Focus on:
- Content category (gaming, fashion, tech, etc.)
- Target audience
- Content type (tutorial, review, vlog, etc.)

Format example: gaming,tech,review,tutorial,educational
```

### Free Tier Limits

Google Gemini 1.5 Flash free tier:
- **60 requests per minute**
- **1,500 requests per day**
- **1 million tokens per day**

Each post uses ~100 tokens, so you can process ~10,000 posts/day (way more than needed).

## Requirements

### Instagram Credentials
- Meta Developer Portal account
- Bearer Token with permissions: `instagram_basic`, `pages_read_engagement`
- Token expiration: 60 days (manual refresh required)

### YouTube Credentials
- YouTube OAuth2 API credentials
- Scopes: `youtube.force-ssl`, `youtube.upload`
- Daily quota: 10,000 units (50 per upload)

### Twitter Credentials
- Twitter Developer Account with Elevated access
- OAuth 2.0 API credentials
- Scopes: `tweet.read`, `tweet.write`, `users.read`, `offline.access`

### Google Gemini Credentials
- Google AI Studio account (free)
- API Key from [Google AI Studio](https://aistudio.google.com/app/apikey)
- Free tier: 60 requests/minute

### n8n Data Table

Create a Data Table named **"Instagram Smart Filter"** with this schema:

| Field Name | Type | Description |
|------------|------|-------------|
| `postId` | string | Instagram post ID (primary key) |
| `youtubeId` | string | YouTube video ID (nullable) |
| `tweetId` | string | Twitter tweet ID (nullable) |
| `platform` | string | Which succeeded: "both", "twitter", "youtube", "none" |
| `wasFiltered` | boolean | True if NSFW detected |
| `filterReason` | string | Explanation of filtering decision |
| `aiGeneratedTags` | string | Comma-separated AI tags |
| `uploadedAt` | string | ISO timestamp of upload |

## Setup Steps

### 1. Create Google Gemini API Key

1. Go to [Google AI Studio](https://aistudio.google.com/app/apikey)
2. Click **Get API key**
3. Create new API key or use existing
4. Copy the API key

### 2. Configure All Credentials in n8n

**Instagram**:
1. Create Meta Developer app
2. Generate User Access Token
3. Create **HTTP Header Auth** credential
4. Header: `Authorization: Bearer YOUR_TOKEN`

**YouTube**:
1. Enable YouTube Data API v3 in Google Cloud Console
2. Create OAuth 2.0 credentials
3. Create **YouTube OAuth2 API** credential in n8n
4. Authorize your account

**Twitter**:
1. Create Twitter Developer app with OAuth 2.0
2. Create **Twitter OAuth2 API** credential in n8n
3. Authorize your account

**Google Gemini**:
1. In n8n, create **Google Gemini API** credential
2. Enter your API key from Google AI Studio

### 3. Create Data Table

1. Navigate to **Projects** → **Data Tables**
2. Click **Create Data Table**
3. Name: `Instagram Smart Filter`
4. Add 8 fields as shown in schema above
5. Set `postId` as primary key
6. Set `wasFiltered` as type: Boolean
7. All others as type: String

### 4. Import and Configure Workflow

1. Import `template-v2-smart-filter.json`
2. Update credential IDs in:
   - **Fetch Instagram Posts**: Instagram Bearer Auth
   - **Generate Tags with AI**: Google Gemini API
   - **Upload to YouTube (Safe)**: YouTube OAuth2
   - **Upload Media to Twitter (NSFW)**: Twitter OAuth2
   - **Create Tweet (NSFW Only)**: Twitter OAuth2
   - **Upload Media to Twitter (Safe)**: Twitter OAuth2
   - **Create Tweet (Safe)**: Twitter OAuth2
3. Update Data Table IDs in:
   - **Check If Already Posted**
   - **Save Smart Filter Record**
4. Customize **Configuration** node (especially `nsfwKeywords`)

### 5. Test the Workflow

**Test with NSFW content**:
1. Post an Instagram video with caption containing "nsfw" or "18+"
2. Run workflow manually
3. Verify:
   - NSFW detected (`wasFiltered: true`)
   - Posted to Twitter only
   - `youtubeId` is null in Data Table
   - `filterReason` explains why

**Test with safe content**:
1. Post a regular Instagram video
2. Run workflow manually
3. Verify:
   - No NSFW detected (`wasFiltered: false`)
   - Posted to both platforms
   - Both `youtubeId` and `tweetId` in Data Table
   - AI-generated tags applied

## Platform Routing Examples

### Example 1: Safe Content

**Instagram Caption**:
```
New cooking tutorial! Making homemade pasta 🍝
#cooking #pasta #tutorial #foodie
```

**Routing Decision**:
- NSFW Keywords: None detected
- Platform: `both`
- YouTube: ✅ Posted with AI tags
- Twitter: ✅ Posted with AI tags
- Filter Reason: "Safe content - approved for all platforms"

### Example 2: NSFW Content

**Instagram Caption**:
```
New photoshoot results 📸 18+ content
#photography #model #spicy
```

**Routing Decision**:
- NSFW Keywords: "18+" detected
- Platform: `twitter`
- YouTube: ❌ Skipped
- Twitter: ✅ Posted
- Filter Reason: "NSFW keyword detected: \"18+\""

## API Quota Considerations

### Google Gemini API (Free Tier)
- **Cost per post**: 1 request (~100 tokens)
- **Daily limit**: 1,500 requests
- **Workflow impact**: Can process 1,500 posts/day
- **Recommendation**: Free tier is sufficient for normal usage

### YouTube Data API v3
- **Daily quota**: 10,000 units
- **Upload cost**: 50 units per video
- **Max daily uploads**: ~200 videos
- **Workflow impact**: Only safe content uses quota

### Twitter API
- **Free tier**: 1,500 tweets/month
- **Basic tier** ($100/month): 3,000 tweets/month
- **Workflow impact**: All content (NSFW + safe) uses quota

### Cost Analysis

Assuming 25 Instagram posts/day, 30% NSFW:

| Platform | Posts/Day | API Cost |
|----------|-----------|----------|
| Gemini AI | 25 | 25 requests (FREE) |
| YouTube | 17.5 (safe) | 875 units/day |
| Twitter | 25 (all) | 750 tweets/month |

**Result**: All within free/basic tier limits.

## Troubleshooting

### AI Tag Generation Fails

**Error**: `Gemini API request failed`

**Solutions**:
1. Check API key is valid
2. Verify free tier limits not exceeded
3. Check caption has sufficient content (empty captions may fail)
4. Set `useAITags: false` in Configuration to disable

### NSFW Detection Not Working

**Issue**: Safe content being marked as NSFW (or vice versa)

**Solutions**:
1. Check `nsfwKeywords` array in Configuration
2. Remember matching is case-insensitive and substring-based
3. Add/remove keywords to tune sensitivity
4. Check Data Table `filterReason` field for matched keyword

### YouTube Upload Fails for Safe Content

**Error**: YouTube upload fails even for non-NSFW content

**Solutions**:
1. Check YouTube quota not exceeded (10,000 units/day)
2. Set `includeSourceLink: false` (unverified accounts can't include external links)
3. Verify video meets YouTube requirements (size, duration, format)
4. Check `youtubeTitle` doesn't contain prohibited words

### Twitter Posts Duplicated

**Issue**: NSFW content posts twice to Twitter

**Check**:
- Verify NSFW path only has one Twitter post node
- Check Data Table for duplicate `tweetId` entries
- Ensure deduplication is working (postId check)

### All Content Marked as NSFW

**Issue**: Even innocent posts are filtered

**Solution**: Keywords too broad. Examples of overly broad keywords:
- "hot" (matches "hot sauce", "hot weather")
- "sex" (matches "Sussex", "sextant")
- "adult" (matches "adult learning")

Use more specific phrases or emoji combinations.

### No AI Tags Generated

**Issue**: `aiGeneratedTags` field is empty

**Check**:
1. Gemini API credential is configured
2. Caption has enough context (minimum 10 characters)
3. Check **Generate Tags with AI** node output for errors
4. Try manually testing the prompt in Google AI Studio

## Advanced Customization

### Different Tags for Each Platform

Modify **NSFW Content Detector** Code node to generate separate tag lists:

```javascript
// YouTube tags (max 10, professional)
const youtubeTags = aiTags.filter(t =>
  !['meme', 'funny', 'lol'].includes(t)
).slice(0, 10);

// Twitter tags (can include casual ones)
const twitterTags = aiTags.slice(0, 5);

return [{
  json: {
    ...processedData,
    youtubeTags: youtubeTags,
    twitterTags: twitterTags,
    // ...
  }
}];
```

### AI-Enhanced NSFW Detection

Instead of keyword matching, use Gemini to detect NSFW content:

1. Add a new AI Agent node after caption processing
2. Prompt: "Is this content suitable for YouTube? Answer YES or NO: [caption]"
3. Route based on AI response instead of keywords

### Whitelist Specific Words

Allow certain keywords that would otherwise trigger NSFW:

```javascript
const nsfwKeywords = config.nsfwKeywords || [];
const whitelist = ['adult learning', 'mature audience'];

// Check whitelist first
const hasWhitelisted = whitelist.some(phrase =>
  caption.toLowerCase().includes(phrase.toLowerCase())
);

if (hasWhitelisted) {
  return [{ json: { isNSFW: false, ... } }];
}

// Then check NSFW keywords
// ...
```

### Schedule Different Times for Each Platform

Split into two workflows:
1. **Twitter workflow**: Runs hourly (high frequency)
2. **YouTube workflow**: Runs daily (saves quota)

Use the same Data Table for deduplication.

## Comparison with Other Workflows

| Feature | Basic Twitter | Dual Post | Smart Filter (this) |
|---------|--------------|-----------|-------------------|
| YouTube Upload | ❌ | ✅ | ✅ (filtered) |
| Twitter Upload | ✅ | ✅ | ✅ (always) |
| AI Tag Generation | ❌ | ❌ | ✅ |
| NSFW Filtering | ❌ | ❌ | ✅ |
| Platform Routing | N/A | Parallel | Conditional |
| Complexity | Simple | Medium | Advanced |
| API Costs | Low | Medium | Medium-High |

**Use this workflow if**:
- You post NSFW and safe content
- You want AI-generated tags
- YouTube must stay brand-safe
- You need comprehensive tracking

**Use Dual Post if**:
- All content is safe
- You don't need AI features
- You want simple parallel posting

**Use Basic Twitter if**:
- You only need Twitter posting
- No YouTube required
- Minimal complexity

## Related Workflows

- **Instagram to Twitter** (Basic): Simple Twitter-only posting
- **Instagram to YouTube + Twitter** (Dual Post): Parallel posting to both platforms
- **Upload from Instagram to YouTube** (v2): YouTube-only upload

## API References

- [Google Gemini API Documentation](https://ai.google.dev/docs)
- [Instagram Graph API](https://developers.facebook.com/docs/instagram-api)
- [YouTube Data API v3](https://developers.google.com/youtube/v3)
- [Twitter API v2](https://developer.twitter.com/en/docs/twitter-api)
- [n8n AI Agent Node](https://docs.n8n.io/integrations/builtin/cluster-nodes/root-nodes/n8n-nodes-langchain.agent/)

## Testing Status

⏳ **Needs End-to-End Testing**

This workflow is structurally complete with full documentation but requires live API credentials for comprehensive validation.

## Version History

- **v2-smart-filter** (2026-01): Initial release - AI tags + NSFW filtering + smart routing

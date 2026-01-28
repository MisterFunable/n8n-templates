# Web Research - Social Links Finder

Automated n8n workflows to research websites and extract social media links using Gemini AI (free tier compatible).

## Available Templates

| Template | Search Method | Best For |
|----------|---------------|----------|
| `template-direct-urls.json` | None - direct URL input | Testing, known websites |
| `template-serpapi.json` | SerpAPI (100 free/mo) | General web research |
| `template.json` | Google Custom Search | High volume (100/day free) |

## Quick Start (Direct URLs - No Search API)

1. Get a **Gemini API Key** from [Google AI Studio](https://makersuite.google.com/app/apikey) (free)
2. Import `template-direct-urls.json` into n8n
3. Edit the **URL List** node:
   ```json
   {
     "urls": [
       "https://some-doll-company.com",
       "https://another-shop.com/about"
     ],
     "geminiApiKey": "YOUR_GEMINI_API_KEY_HERE"
   }
   ```
4. Run the workflow

## Setup with Search APIs

### Option A: SerpAPI (Recommended)
- **100 free searches/month**
- Sign up at [serpapi.com](https://serpapi.com)
- Use `template-serpapi.json`

### Option B: Google Custom Search
- **100 free searches/day**
- Setup at [Programmable Search Engine](https://programmablesearchengine.google.com/)
- Get API key from [Google Cloud Console](https://console.cloud.google.com/apis/credentials)
- Use `template.json`

## Configuration

Edit the **Configuration** node in each template:

```json
{
  "searchQueries": [
    "BJD doll manufacturers official website",
    "reborn doll artists shop"
  ],
  "maxResultsPerQuery": 5,
  "serpApiKey": "YOUR_KEY",        // for SerpAPI template
  "googleSearchApiKey": "YOUR_KEY", // for Google template
  "googleSearchEngineId": "YOUR_CX", // for Google template
  "geminiApiKey": "YOUR_GEMINI_KEY"
}
```

## Workflow Architecture

```
Input (Search/URLs)
       ↓
  Fetch Page HTML
       ↓
  Code: Regex extraction (finds obvious social links)
       ↓
  Gemini API: Analyzes text, finds hidden handles, verifies
       ↓
  Parse & Merge Results
       ↓
  Aggregate All
       ↓
  Final Summary (deduplicated)
```

## What It Extracts

| Platform | Pattern |
|----------|---------|
| Instagram | instagram.com/username |
| Facebook | facebook.com/page |
| Twitter/X | twitter.com/handle, x.com/handle |
| YouTube | youtube.com/channel, @handle |
| TikTok | tiktok.com/@username |
| Pinterest | pinterest.com/username |
| LinkedIn | linkedin.com/company/, /in/ |
| Etsy | etsy.com/shop/name |
| Emails | standard email format |

## Output Structure

```json
{
  "summary": {
    "totalProcessed": 10,
    "successfulAI": 8,
    "failedAI": 2
  },
  "companies": [
    {
      "name": "Company Name",
      "url": "https://...",
      "description": "Brief description",
      "success": true
    }
  ],
  "socialLinks": {
    "instagram": ["https://instagram.com/account1", "..."],
    "facebook": ["..."],
    "twitter": ["..."]
  },
  "allEmails": ["contact@example.com"],
  "rawData": [/* individual results */]
}
```

## Free Tier Limits

| Service | Free Limit |
|---------|------------|
| Gemini 1.5 Flash | 60 req/min, 1M tokens/day |
| SerpAPI | 100 searches/month |
| Google Custom Search | 100 searches/day |

## Troubleshooting

**"continueOnFail" errors**: Expected - some pages block scraping. The workflow continues with partial results.

**Empty Gemini response**: Check your API key. The workflow falls back to regex-only results.

**Rate limits**: Add a Wait node between batches if processing many URLs.

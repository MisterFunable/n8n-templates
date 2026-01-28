# YouTube Channel Settings Audit

Comprehensive audit workflow that analyzes your YouTube channel's optimization and provides a scored report with actionable recommendations.

## 🆕 V2 Enhanced Now Available!

**New: Beautiful visual HTML reports with advanced algorithm analysis.**

- 📊 **[V2 Enhanced](./README-V2.md)** - Visual HTML dashboard, algorithm-focused (RECOMMENDED)
- 📄 **[V1 Basic](./README.md)** - Text-based reports (this file)
- 🔍 **[Which Version?](./WHICH-VERSION.md)** - Comparison guide

**Quick recommendation:** Use V2 Enhanced for most use cases.

## What It Does

Performs a complete audit of your YouTube channel across 4 key areas:

### 1. Branding (65 points)
- ✅ Profile picture exists and quality
- ✅ Channel banner exists and dimensions
- ✅ Watermark branding

### 2. Channel Info (40 points)
- ✅ Channel description quality and length
- ✅ Custom URL status
- ✅ Country and language settings

### 3. Settings (30 points)
- ✅ Channel keywords (count and relevance)
- ✅ Channel trailer for non-subscribers
- ✅ Featured channels

### 4. Recent Videos (30 points)
- ✅ Title optimization (length, structure)
- ✅ Description quality
- ✅ Tag usage across videos

**Total Score: 165 points possible**

## Output

The workflow generates a detailed report with:

- **Overall Score** (0-165 scale, normalized to 100)
- **Category Scores** for each area
- **Priority Actions** ranked by impact
- **Detailed Analysis** with specific recommendations
- **Status Indicators** (✅ Good, ⚠️ Needs Work, ❌ Critical)

## Score Guide

| Score | Status | Meaning |
|-------|--------|---------|
| 80-100 | ✅ Excellent | Channel is well-optimized |
| 60-79 | ⚠️ Good | Minor improvements needed |
| 40-59 | ⚠️ Needs Work | Several issues to address |
| 0-39 | ❌ Critical | Immediate action required |

## Requirements

- YouTube OAuth2 API credentials in n8n
- Channel must have at least 1 video (10+ recommended for better analysis)

## Setup

1. **Import Template**
   - Workflows → Import from File → `template.json`

2. **Configure Credentials**
   - Connect YouTube OAuth2 credentials to these nodes:
     - "Get Channel Details"
     - "Get Recent Videos"
     - "Get Video Details"

3. **Run Workflow**
   - Click "Execute Workflow"
   - Wait 5-10 seconds for completion
   - Check "Format Report" node output

## Sample Output

```
╔═══════════════════════════════════════════════════════════════════╗
║           YOUTUBE CHANNEL SETTINGS AUDIT REPORT                  ║
╚═══════════════════════════════════════════════════════════════════╝

Channel: Your Channel Name
Channel ID: UCxxxxxxxxxxxxxxxxxxxxxx
Audit Date: 1/25/2026, 8:30:45 PM

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 OVERALL SCORE: 78/100
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  🎨 Branding:      55/65
  ℹ️  Channel Info:  32/40
  ⚙️  Settings:      18/30
  🎬 Content:       23/30

⚠️  Good, but there's room for improvement.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 PRIORITY ACTIONS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. [HIGH] Add channel keywords (10-20)
   Impact: High - Discovery and SEO

2. [MEDIUM] Create channel trailer
   Impact: Medium - Convert visitors to subscribers

3. [MEDIUM] Add channel watermark
   Impact: Medium - Branding and subscriptions
...
```

## Understanding Results

### Priority Levels

- **CRITICAL**: Must fix immediately (missing profile picture, empty description)
- **HIGH**: Important for growth (keywords, SEO elements)
- **MEDIUM**: Good to have (watermark, trailer, featured channels)
- **LOW**: Nice improvements (minor optimizations)

### Common Issues

| Issue | Solution | Priority |
|-------|----------|----------|
| No profile picture | Upload 800x800px image | CRITICAL |
| No banner | Upload 2560x1440px banner | HIGH |
| Short description | Write 200-1000 character description with keywords | HIGH |
| No keywords | Add 10-20 relevant channel keywords | HIGH |
| Missing tags | Add 5-15 tags per video | MEDIUM |
| Short video descriptions | Write detailed descriptions (200+ chars) | MEDIUM |

## Using Results

### Immediate Actions

1. Review the **Priority Actions** section
2. Address CRITICAL and HIGH items first
3. Use the **AI-Powered Channel Optimizer** workflow to generate optimized content
4. Implement recommendations in YouTube Studio

### Long-term Strategy

1. Re-run audit monthly to track progress
2. Focus on one category per week
3. Update existing videos starting with top performers
4. Apply learnings to new uploads

## API Quota Usage

This workflow uses approximately **3 YouTube API quota units**:

- Channels.list (snippet, brandingSettings, etc.): 1 unit
- Search.list (recent videos): 100 units
- Videos.list (video details): 1 unit

**Total: ~102 units** (well under the 10,000 daily limit)

## Troubleshooting

| Error | Solution |
|-------|----------|
| "No videos found" | Upload at least 1 video, or check credentials |
| "Insufficient permissions" | Re-authorize YouTube OAuth2 credentials |
| "Quota exceeded" | Wait 24 hours or use a different Google Cloud project |
| Empty report | Check that all 3 HTTP nodes have valid credentials |

## Next Steps

After running the audit:

1. **Use AI-Powered Channel Optimizer**
   - Generates optimized descriptions, keywords, and tags
   - Based on your actual content and top performers
   - See: `AI-Powered YouTube Channel Optimizer` workflow

2. **Implement Changes**
   - Go to YouTube Studio → Settings
   - Update channel description and keywords
   - Add watermark if missing
   - Update video metadata for top 10 videos

3. **Monitor Results**
   - Re-run audit after 30 days
   - Track score improvements
   - Measure impact on views/subscribers

## Best Practices

- Run audit before launching major content campaigns
- Compare scores month-over-month
- Focus on your weakest category first
- Don't change titles on high-performing videos without testing
- Update descriptions and tags regularly

## Related Workflows

- **AI-Powered YouTube Channel Optimizer** - Generate optimized content
- **YouTube Channel Watermark Updater** - Fix watermark issues
- **YouTube Video Bulk Editor** (future) - Apply changes to multiple videos

## API Reference

- [YouTube Data API v3 - Channels](https://developers.google.com/youtube/v3/docs/channels)
- [YouTube Data API v3 - Search](https://developers.google.com/youtube/v3/docs/search)
- [YouTube Data API v3 - Videos](https://developers.google.com/youtube/v3/docs/videos)

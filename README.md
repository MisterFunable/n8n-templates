<div align="right">

[![n8n Creator](https://img.shields.io/badge/n8n-Creator-EA4B71?style=for-the-badge&logo=n8n&logoColor=white)](https://n8n.io/creators/misterfunable/)
[![Medium](https://img.shields.io/badge/Medium-12100E?style=for-the-badge&logo=medium&logoColor=white)](https://medium.com/@mister.funable)

</div>

# n8n Workflow Templates

A curated collection of n8n automation templates for social media management, YouTube optimization, and AI-powered research workflows.

## Features

- **Social Media Automation** - Cross-post between Instagram, X/Twitter, and YouTube
- **Analytics & Tracking** - Monitor Instagram metrics with flexible backend support
- **YouTube Management** - Channel optimization, translations, watermarks, and more
- **AI Integration** - Google Gemini, OpenAI, and Anthropic for intelligent automation
- **Trend Monitoring** - Automated trend detection and content resharing
- **Documentation Tools** - AI-powered ADR creation and web research
- **Multi-Backend Support** - Works with n8n Data Tables or Airtable

---

## Available Templates

### Social Media Cross-Posting

| Template | Description | Tested | n8n.io |
|----------|-------------|--------|--------|
| [Instagram to X](./Instagram%20to%20X) | Automatically cross-post your Instagram content to X (Twitter) | ✅ | - |
| [Instagram Auto Accept Collabs](./Instagram%20Auto%20Accept%20Collabs) | Automatically accept or decline collaboration invites on Instagram | ⏳ | - |
| [Upload from Instagram to YouTube](./Upload%20from%20Instagram%20To%20YouTube) | Download Instagram videos and upload to YouTube with optimized metadata | ✅ | [View](https://n8n.io/workflows/12773-batch-upload-instagram-reels-to-youtube-with-scheduled-delays/) |

### YouTube Management

| Template | Description | Tested | n8n.io |
|----------|-------------|--------|--------|
| [Auto-Translate YouTube Video Content](./Auto-Translate%20YouTube%20Video%20Content%20with%20Google%20Gemini%20AI) | Translate YouTube video titles and descriptions using Google Gemini AI | ✅ | [View](https://n8n.io/workflows/9543-auto-translate-youtube-video-content-with-google-gemini-ai/) |
| [Auto-Translate YouTube Video (Loop Lookup Ver)](./Auto-Translate%20YouTube%20Video%20(Loop%20Lookup%20Ver)) | Optimized translation workflow with loop-based processing | ✅ | - |
| [YouTube Channel Watermark Updater](./YouTube%20Channel%20Watermark%20Updater) | Set or update channel watermark (single or multi-account) | ✅ | - |
| [YouTube Channel Settings Audit](./YouTube%20Channel%20Settings%20Audit) | Audit channel optimization with algorithm-focused scoring (V1 & V2) | ⏳ | - |
| [AI-Powered YouTube Channel Optimizer](./AI-Powered%20YouTube%20Channel%20Optimizer) | AI-driven channel analysis and content recommendations using Gemini | ✅ | - |
| [YouTube Featured Channels Updater](./YouTube%20Featured%20Channels%20Updater) | Update featured channels section on YouTube home tab | ✅ | - |
| [YouTube Channel Subscriber](./YouTube%20Channel%20Subscriber) | Bulk subscribe to a list of channels with rate limiting | ✅ | - |

### Analytics, Tracking & Backup

| Template | Description | Tested | n8n.io |
|----------|-------------|--------|--------|
| [Instagram Account Information Tracker](./Instagram%20Account%20Information%20Tracker) | Track Instagram metrics with n8n Data Tables or Airtable backend | ⏳ | - |
| [Instagram Video Backup to Google Drive](./Instagram%20Video%20Backup%20to%20Google%20Drive) | Automated backup of Instagram videos with searchable JSON metadata catalog | ⏳ | - |

### Content Automation Systems

| Template | Description | Tested | n8n.io |
|----------|-------------|--------|--------|
| [Trend Reshare System](./Trend%20Reshare%20System) | Multi-workflow system: monitor trends, match to videos, post to X | ⏳ | - |

### AI Research & Documentation

| Template | Description | Tested | n8n.io |
|----------|-------------|--------|--------|
| [ADR Researcher](./ADR%20Researcher) | AI-powered engineering documentation assistant (OpenAI/Anthropic) | ⏳ | - |
| [Web Research Social Links Finder](./Web%20Research%20Social%20Links%20Finder) | Extract social media links from websites using Gemini AI | ⏳ | - |

### Error Workflows

| Template | Description | Tested | n8n.io |
|----------|-------------|--------|--------|
| [Error Workflows - Slack Notifier](./Error%20Workflows/Slack%20Notifier) | Send formatted Slack alerts when workflows fail | ⏳ | - |
| [Error Workflows - Discord Notifier](./Error%20Workflows/Discord%20Notifier) | Send formatted Discord alerts when workflows fail | ⏳ | - |

**Legend:** ✅ Tested & Working | ⏳ Needs Testing

> **Note on Testing Status:** Templates marked with ⏳ are structurally complete with full documentation but require end-to-end testing with live API credentials to verify all integrations work correctly. They should function as designed but haven't been fully validated in production environments yet.

---

## Template Highlights

**Trend Reshare System** - A sophisticated 4-workflow system that monitors Google Trends, syncs your YouTube videos to Airtable, matches trending topics to your content, and automatically posts to X with engagement optimization.

**Instagram Account Tracker** - Unique schema-as-code approach with JSON-driven table definitions. Choose between n8n Data Tables or Airtable backend without changing your workflow logic. Includes CSV import for fastest setup.

**ADR Researcher** - Interactive AI agent that creates Architecture Decision Records, runbooks, and technical documentation. Uses web search with source citations and maintains conversation memory for iterative refinement.

**YouTube Settings Audit V2** - Algorithm-focused channel analysis with visual HTML reports. Scores on 100-point scale with emphasis on upload consistency, engagement, CTR, and watch time patterns.

## Getting Started

### Quick Setup (3 Steps)

1. **Download** the `template.json` file from any template folder
2. **Import** into n8n: `Workflows` > `Import from File`
3. **Configure** credentials and settings (see template's README)

### For Multi-Workflow Systems

Some templates (like Trend Reshare System) require multiple workflows:

1. Import all numbered workflows in order (`01-`, `02-`, `03-`, etc.)
2. Set up the shared data store (Airtable or n8n Data Tables)
3. Configure credentials in each workflow
4. Test workflows sequentially before activating schedules

### Detailed Guides

Each template folder contains:
- `README.md` - Setup instructions and API references
- `template.json` - The main workflow file
- `template-v2.json` - Enhanced versions (if available)
- Additional docs for complex templates (QUICK-START, TROUBLESHOOTING, etc.)

## Required Credentials by Category

### Social Media APIs
- **Instagram (Meta Graph API)** - Bearer token (60-day expiration)
- **X/Twitter API v2** - OAuth 2.0 with PKCE
- **YouTube Data API v3** - Google OAuth 2.0

### AI Services
- **Google Gemini** - API key (free tier: 60 req/min)
- **OpenAI GPT-4o** - API key (pay-per-use)
- **Anthropic Claude** - API key (pay-per-use)

### Search & Data
- **SerpAPI** - API key (free tier: 100 searches/month)
- **Google Custom Search** - API key + Search Engine ID (100 searches/day)
- **Airtable** - API key (free tier: 1,000 records/base)

### Free Tier Summary
Most templates can run entirely on free tiers:
- Gemini AI: Effectively unlimited for normal usage
- YouTube API: 10,000 quota units/day
- Airtable: 1,000 records per base
- SerpAPI: 100 searches/month

---

## Related Articles

Explore my n8n guides on Medium for in-depth tutorials:

### [n8n Article Collection](https://medium.com/@mister.funable/list/n8n-fc9fe38c3afe)

#### Workflow Guides
- [n8n Nodes You Should Know (But Probably Don't)](https://medium.com/@mister.funable/n8n-nodes-you-should-know-but-probably-dont-06d61b77c457)
- [How to Publish Your n8n Template](https://medium.com/@mister.funable/how-to-publish-your-n8n-template-375832d8efd7)
- [Getting Instagram Posts with n8n (The Simple Way Part 1)](https://medium.com/@mister.funable/getting-instagram-posts-with-n8n-the-simple-way-part-1-c66cb5217054)

#### Local Setup and Infrastructure
- [Running n8n Locally with Ngrok](https://medium.com/@mister.funable/running-n8n-locally-with-ngrok-734af69e1530)
- [Backing Up n8n When the Data Lives in a Docker Volume](https://medium.com/@mister.funable/backing-up-n8n-when-the-data-lives-in-a-docker-volume-62e200d79172)
- [How (Not) to Work with Local Files in n8n](https://medium.com/@mister.funable/how-not-to-work-with-local-files-in-n8n-8c4545a3f2b2)

---

## Contributing & Testing

### Help Test Templates

Several templates are marked ⏳ and need end-to-end testing:
- Trend Reshare System
- Instagram Account Information Tracker
- Instagram Video Backup to Google Drive
- Instagram Auto Accept Collabs
- ADR Researcher
- Web Research Social Links Finder
- YouTube Channel Settings Audit
- Error Workflows (Slack & Discord)

If you test any of these and confirm they work, please open an issue or PR!

### For Developers

See [CLAUDE.md](./CLAUDE.md) for:
- Detailed architecture documentation
- n8n workflow patterns and best practices
- API quota considerations
- Multi-workflow system design
- Template quality standards

See [AGENTS.md](./AGENTS.md) for:
- AI agent instructions
- Template publishing guidelines
- n8n community standards

---

## License

Feel free to use and modify these templates for your own projects.

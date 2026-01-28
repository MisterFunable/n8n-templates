# YouTube Channel Watermark Updater

A simplified n8n workflow to set your YouTube channel's watermark image.

## Available Versions

- **`template.json`** - Single account version (this file)
- **`template-v2-multi-account.json`** - Multi-account version (update 7 channels at once)
  - See [README-MULTI-ACCOUNT.md](./README-MULTI-ACCOUNT.md) for setup instructions

## What It Does

Sets a watermark image that appears on all videos in your YouTube channel. The workflow:

1. Automatically detects your YouTube channel ID from your credentials
2. Downloads the watermark image from a URL
3. Uploads it to YouTube as your channel's watermark

## Requirements

### Google Cloud Setup
1. **Google Cloud Project** with YouTube Data API v3 enabled
2. **OAuth 2.0 Credentials** (Client ID and Client Secret)

### n8n Setup
- **YouTube OAuth2 API** credential

### Image Requirements
- **Max size:** 10MB
- **Formats:** PNG or JPEG
- **Recommended:** Transparent PNG, minimum 150x150px
- Appears in the top-right corner of videos

## Setup Instructions

1. **Import the Template**
   - In n8n: Workflows → Import from File
   - Select `template.json`

2. **Configure YouTube OAuth2 Credentials**
   - In n8n: Credentials → Add Credential → YouTube OAuth2 API
   - Enter Client ID and Client Secret from Google Cloud Console
   - Connect and authorize

3. **Connect Credentials**
   - Click "Get My Channel ID" node → Select your YouTube OAuth2 credentials
   - Click "Upload Watermark" node → Select the same credentials

4. **Optional: Change Watermark Image**
   - Edit the "Set Configuration" node
   - Update `watermarkUrl` to your image URL (default: `https://i.imgur.com/tpteEPD.png`)

## Usage

1. Connect your YouTube OAuth2 credentials to all 3 HTTP Request nodes
2. Click "Execute Workflow"
3. Success response: HTTP 200 or 204 from the final node

## Why the Split/Merge Pattern?

YouTube's Watermark API requires a **resumable upload protocol**:
1. **Initiate Upload**: POST request with metadata → returns upload URL (as JSON)
2. **Upload Image Data**: PUT binary data to that URL

**The problem:** When "Initiate Upload" makes the POST request, n8n's HTTP Request node outputs JSON response data, which overwrites the binary image data from the previous node.

**The solution:** Split the workflow after downloading the image:
- **Branch A**: Send metadata to get upload URL
- **Branch B**: Keep the binary data (using NoOp node)
- **Merge**: Combine both outputs so final node has both upload URL AND binary data

This pattern is necessary for YouTube's API and cannot be simplified further while maintaining functionality.

## Workflow Structure

```
Manual Trigger → Get Channel ID → Set Config → Download Image
                                                      ↓
                                    ┌─────────────────┴─────────────────┐
                                    ↓                                   ↓
                              Initiate Upload                     Keep Binary (NoOp)
                                    ↓                                   ↓
                                    └─────────────────┬─────────────────┘
                                                      ↓
                                                    Merge
                                                      ↓
                                              Upload Image Data
```

**8 functional nodes + 1 documentation note:**
- **Manual Trigger**: Start the workflow
- **Get My Channel ID**: Fetches your channel ID via YouTube API
- **Set Configuration**: Configure watermark URL and settings
- **Download Watermark**: Downloads the image as binary data
- **Initiate Upload**: Starts resumable upload, gets upload URL (returns JSON)
- **Keep Binary**: NoOp node that preserves the binary data from Download
- **Merge**: Combines upload URL (from Initiate) with binary data (from Keep Binary)
- **Upload Image Data**: Uploads binary data to the upload URL

## API Reference

- [YouTube Data API v3 - Channels: list](https://developers.google.com/youtube/v3/docs/channels/list)
- [YouTube Data API v3 - Watermarks: set](https://developers.google.com/youtube/v3/docs/watermarks/set)

**Quota Cost:** ~51 units (1 for channels.list + 50 for watermarks.set)

## Important Notes

### Upload Success vs. Active Watermark

**The workflow may complete successfully (HTTP 200/204) but the watermark might not appear as active immediately.**

Common reasons:
- YouTube's processing delay (can take several minutes)
- Cached data in YouTube Studio (refresh the page)
- Previous watermark needs to be manually unset first
- API quota limits reached

**To verify:** Go to YouTube Studio → Customization → Branding → Video watermark

If you don't see your watermark after 5-10 minutes, try:
1. Removing any existing watermark first
2. Running the workflow again
3. Checking YouTube Studio in an incognito window

## Troubleshooting

| Error | Solution |
|-------|----------|
| `imageFormatUnsupported` | Use PNG or JPEG format |
| `imageTooTall` / `imageTooWide` | Reduce image dimensions |
| `forbidden` | Check OAuth permissions and API enablement |
| `mediaBodyRequired` | Ensure image URL is valid and accessible |
| No channel found | Your Google account needs a YouTube channel |
| Success but watermark not visible | See "Upload Success vs. Active Watermark" above |

## Removing a Watermark

To remove your watermark, use this HTTP Request:

```
POST https://www.googleapis.com/youtube/v3/watermarks/unset?channelId=YOUR_CHANNEL_ID
```

With YouTube OAuth2 authentication.

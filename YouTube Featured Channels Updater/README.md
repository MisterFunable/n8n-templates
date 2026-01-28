# YouTube Featured Channels Updater

An n8n workflow to set and manage featured channels on your YouTube channel's home tab layout.

## Available Versions

- **`template.json`** - Generic version with placeholder channel IDs
- **`template-v2-personal.json`** - Personal version pre-configured for Mechenable account
  - Section title: "Cultured Corner 🍷"
  - Features: Jueguenable, Legoenable, Dollenable, Posenable, Statuenable
  - Credentials: YouTube Account Mechenable

## What It Does

Sets up a "Featured Channels" section as the first row on your YouTube channel's home tab with a predefined list of channels in a specific order. The workflow:

1. Automatically detects your YouTube channel from credentials
2. Checks if a Featured Channels section already exists
3. Updates the existing section or creates a new one
4. Places it at position 0 (first row on the home tab)
5. Verifies all channels are present in the correct order
6. Generates a detailed validation report

## Requirements

### Google Cloud Setup
1. **Google Cloud Project** with YouTube Data API v3 enabled
2. **OAuth 2.0 Credentials** (Client ID and Client Secret)

### n8n Setup
- **YouTube OAuth2 API** credential with `youtube.force-ssl` scope

## Setup Instructions

### 1. Import the Template
- In n8n: Workflows → Import from File
- Select `template.json`

### 2. Configure YouTube OAuth2 Credentials
- In n8n: Credentials → Add Credential → YouTube OAuth2 API
- Enter Client ID and Client Secret from Google Cloud Console
- Connect and authorize

### 3. Connect Credentials to All HTTP Request Nodes
Connect your credentials to these nodes:
- **Get My Channel**
- **Get Current Sections**
- **Update Section**
- **Insert Section**
- **Verify Sections**

### 4. Configure Featured Channels

Edit the **"Set Configuration"** node and update these fields:

| Field | Description | Default |
|-------|-------------|---------|
| `sectionTitle` | Title displayed above featured channels | "Featured Channels" |
| `featuredChannels` | Array of channel IDs (in display order) | Example IDs |
| `targetPosition` | Row position on home tab (0 = first) | 0 |

#### Example Configuration:

```javascript
// Featured channels array - channels appear in this exact order
[
  "UCX6OQ3DkcsbYNE6H8uQQuVA",  // MrBeast
  "UCBcRF18a7Qf58cCRy5xuWwQ",  // MKBHD
  "UC-lHJZR3Gqxm24_Vd_AJ5Yw"   // PewDiePie
]
```

## Finding Channel IDs

Channel IDs are 24-character strings starting with "UC".

### Method 1: From Channel URL
Navigate to the channel and look at the URL:
```
https://www.youtube.com/channel/UCX6OQ3DkcsbYNE6H8uQQuVA
                                ^^^^^^^^^^^^^^^^^^^^^^^^
                                This is the Channel ID
```

### Method 2: From Handle/Username
1. Go to `https://www.youtube.com/@username`
2. View Page Source (Ctrl+U or Cmd+U)
3. Search for `"channelId":"UC` 
4. Copy the 24-character ID

### Method 3: Using YouTube API
Use this workflow's "Get My Channel" node pattern with a different channel ID.

## Usage

1. Connect your YouTube OAuth2 credentials to all 5 HTTP Request nodes
2. Configure your featured channels list in "Set Configuration"
3. Click **"Execute Workflow"**
4. Review the final report for success/warnings

## Workflow Structure

```
Manual Trigger → Get My Channel → Set Configuration → Get Current Sections
                                                              ↓
                                                    Check Existing Section
                                                              ↓
                                                       Section Exists?
                                                       ↙           ↘
                                              Update Section    Insert Section
                                                       ↘           ↙
                                                      Merge Results
                                                              ↓
                                                      Verify Sections
                                                              ↓
                                                      Generate Report
```

**11 nodes total:**
- **Manual Trigger**: Start the workflow
- **Get My Channel**: Fetch your channel ID via YouTube API
- **Set Configuration**: Define featured channels and settings
- **Get Current Sections**: List all channel sections
- **Check Existing Section**: Find if Featured Channels section exists
- **Section Exists?**: Branch based on existence
- **Update Section**: PUT request to update existing section
- **Insert Section**: POST request to create new section
- **Merge Results**: Combine both branches
- **Verify Sections**: Re-fetch sections to confirm changes
- **Generate Report**: Validate and create summary

## Output Report

The workflow outputs a detailed validation report:

```json
{
  "status": "SUCCESS",
  "channel": {
    "id": "UC...",
    "title": "Your Channel Name"
  },
  "action": "UPDATED",
  "section": {
    "title": "Featured Channels",
    "targetPosition": 0,
    "actualPosition": 0
  },
  "validation": {
    "success": true,
    "sectionFound": true,
    "positionCorrect": true,
    "allChannelsPresent": true,
    "orderCorrect": true,
    "missingChannels": [],
    "extraChannels": []
  },
  "summary": [
    "All checks passed!",
    "Featured Channels section is at position 0 (first row)",
    "All 3 channels are present in the correct order"
  ]
}
```

## API Reference

- [YouTube Data API v3 - ChannelSections](https://developers.google.com/youtube/v3/docs/channelSections)
- [ChannelSections: list](https://developers.google.com/youtube/v3/docs/channelSections/list)
- [ChannelSections: insert](https://developers.google.com/youtube/v3/docs/channelSections/insert)
- [ChannelSections: update](https://developers.google.com/youtube/v3/docs/channelSections/update)

**Quota Cost:**
- `channelSections.list`: 1 unit (called twice)
- `channelSections.insert`: 50 units (if creating)
- `channelSections.update`: 50 units (if updating)
- `channels.list`: 1 unit

**Total:** ~53 units per run (update) or ~53 units (insert)

## Limitations

- **Maximum 10 sections** per channel (YouTube API limit)
- **Cannot include your own channel** in the featured channels list
- **Order is preserved** - channels appear exactly as specified in the array
- **Title required** for `multipleChannels` section type
- **Title max length**: 100 characters

## Troubleshooting

| Error | Solution |
|-------|----------|
| `channelSectionForbidden` | Check OAuth permissions and API enablement |
| `channelNotActive` | One of the featured channel IDs is invalid or suspended |
| `channelsDuplicated` | Remove duplicate channel IDs from your list |
| `channelsNeeded` | Ensure `featuredChannels` array is not empty |
| `maxChannelSectionExceeded` | Delete some existing sections (max 10 allowed) |
| `maxChannelsExceeded` | Reduce the number of featured channels |
| `ownChannelInChannels` | Remove your own channel ID from the list |
| `titleRequired` | The `sectionTitle` field cannot be empty |

## Removing Featured Channels Section

To remove the Featured Channels section entirely, use this HTTP Request:

```
DELETE https://www.googleapis.com/youtube/v3/channelSections?id=SECTION_ID
```

Get the `SECTION_ID` from the "Get Current Sections" node output.

## Personal Version (template-v2-personal.json)

Pre-configured for the "enable" channel family with "Cultured Corner 🍷" section title:

### Configuration
- **Section Title**: Cultured Corner 🍷
- **Position**: 0 (first row)
- **Featured Channels** (in order):
  1. Mechenable (`UC434hYDRQ_tzPUtSUAKNxwA`)
  2. Jueguenable (`UCEH0sxRdQsy-y6-I-7V0pvw`)
  3. Dollenable (`UCvF_vV9iGZowLL_SxmKJ7_Q`)
  4. Legoenable (`UCXhYzdDMIFpKWJgY2PRpfnQ`)
  5. Posenable (`UCG2lQGYhvB9MdKiPZK7_XKQ`)
  6. Statuenable (`UC306rH2OGwvmuI1E-I3SsHg`)

### Setup
1. Import `template-v2-personal.json` into n8n
2. Connect your YouTube OAuth2 credentials
3. **Remove YOUR channel** from the `featuredChannels` array (you can't feature yourself!)
4. Run the workflow

### Note on Own Channel Exclusion
YouTube API does not allow featuring your own channel. Before running, edit "Set Configuration" and remove whichever channel matches the account you're running from.

## Related Workflows

- **YouTube Channel Watermark Updater** - Set channel watermark
- **YouTube Channel Settings Audit** - Audit channel optimization
- **AI-Powered YouTube Channel Optimizer** - Get AI recommendations

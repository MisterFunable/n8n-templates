# Instagram Auto Accept Collabs

Automatically accepts (or declines) collaboration invites on your Instagram posts. When another creator invites you to collaborate on their post, this workflow processes the invite automatically based on your configuration.

> **New to Facebook API setup?** See [SETUP-GUIDE.md](./SETUP-GUIDE.md) for detailed step-by-step instructions with screenshots and troubleshooting.

## Important: Authentication Requirements

This workflow uses the **Instagram API with Facebook Login**, which is **different** from the standard Instagram Bearer Token used in other workflows.

| What You Might Have | What This Workflow Needs |
|---------------------|--------------------------|
| Instagram Bearer Token (`graph.instagram.com`) | Facebook Graph API OAuth2 (`graph.facebook.com`) |
| Simple API token | OAuth2 flow with Facebook Login |

### Why?

The collaboration_invites endpoint was added to the **Instagram API with Facebook Login** in December 2025. It requires:
- Your Instagram account to be linked to a Facebook Page
- Facebook Graph API credentials (not Instagram Bearer token)

---

## Quick Start

### Step 1: Verify Your Instagram is Linked to Facebook

1. Open Instagram app → Settings → Account → Sharing to other apps
2. Verify **Facebook** is connected
3. Your Instagram must be a **Business** or **Creator** account
4. It must be linked to a **Facebook Page** you manage

### Step 2: Create Facebook Developer App (if needed)

1. Go to [developers.facebook.com](https://developers.facebook.com/)
2. Create a new app → Select "Business" type
3. Add the **Facebook Login for Business** product

### Step 3: Configure Permissions

In your Facebook App → App Review → Permissions:

| Permission | Purpose |
|------------|---------|
| `instagram_basic` | Access Instagram account info and collaboration invites |
| `pages_show_list` | List Facebook Pages you manage |
| `pages_read_engagement` | Read Page data to find linked Instagram |

### Step 4: Create n8n Credential

In n8n:
1. Go to **Credentials** → **Add Credential**
2. Search for **"Facebook Graph API"**
3. Enter your App ID and App Secret (from Facebook App → Settings → Basic)
4. Complete the OAuth2 authorization flow
5. Make sure to grant all requested permissions

### Step 5: Import and Configure Workflow

1. Import `template.json` into n8n
2. Update all HTTP Request nodes to use your Facebook Graph API credential
3. Update the **Configuration** node with your Data Table ID

### Step 6: Create Data Table

From the "Check If Already Processed" node, create a Data Table with:

| Field | Type | Required |
|-------|------|----------|
| `mediaId` | String | Yes (Primary Key) |
| `inviterUsername` | String | No |
| `action` | String | No |
| `processedAt` | String | No |
| `success` | String | No |
| `response` | String | No |

---

## How It Works

```
[Schedule] → [Get Facebook Pages] → [Extract IG Account] → [Fetch Invites] → [Process] → [Accept/Decline]
```

1. **Get Facebook Pages** - Lists all Pages you manage with their linked Instagram accounts
2. **Extract Instagram Account ID** - Finds the Instagram Business Account ID
3. **Fetch Collaboration Invites** - Gets pending invites from Instagram API
4. **Process & Accept/Decline** - Based on your configuration

---

## Configuration Options

In the **Configuration** node:

| Setting | Description | Default |
|---------|-------------|---------|
| `autoAccept` | `true` = accept all, `false` = decline all | `true` |
| `filterUsernames` | Comma-separated whitelist (empty = no filter) | `""` |
| `dataTableId` | Your Data Table ID | `YOUR_DATA_TABLE_ID` |
| `waitBetweenRequests` | Seconds between API calls | `2` |

### Examples

**Accept all invites:**
```json
{ "autoAccept": true, "filterUsernames": "" }
```

**Only accept from specific users:**
```json
{ "autoAccept": true, "filterUsernames": "partner1,brandaccount,friend" }
```

**Decline all invites:**
```json
{ "autoAccept": false, "filterUsernames": "" }
```

---

## Troubleshooting

### "No Instagram Business Account found"

Your Instagram account is not properly linked to a Facebook Page:
1. Open Instagram → Settings → Account → Sharing to other apps → Facebook
2. Make sure you're connecting to a **Page** (not just your personal profile)
3. Your Instagram must be a Business or Creator account

### "Invalid OAuth access token"

You're using the wrong credential type:
- ❌ Instagram Bearer Token (won't work)
- ✅ Facebook Graph API OAuth2 (required)

### "username field is deprecated"

The workflow was using the old `/me` endpoint. Update to the latest template version which uses `/me/accounts` instead.

### "Permission denied" or "Missing permissions"

1. Go to Facebook App → App Review → Permissions
2. Ensure `instagram_basic`, `pages_show_list`, `pages_read_engagement` are approved
3. Re-authorize the OAuth2 flow in n8n

### No invites appearing

- Verify you actually have pending collaboration invites (check Instagram app)
- The API only returns **pending** invites (not accepted/declined ones)
- Collaboration invites work for Feed posts, Reels, and Carousels (not Stories)

---

## API Flow

The workflow makes these API calls:

1. **Get Pages with Instagram:**
   ```
   GET /me/accounts?fields=id,name,instagram_business_account
   ```

2. **Get Collaboration Invites:**
   ```
   GET /{ig-user-id}/collaboration_invites
   ```

3. **Accept/Decline Invite:**
   ```
   POST /{ig-user-id}/collaboration_invites
   Body: { "media_id": "...", "action": "accept" }
   ```

---

## Limitations

1. **Stories not supported** - Collaboration invites only work for Feed images, Reels, and Carousels
2. **5 collaborators max** - Instagram limits each post to 5 collaborators
3. **Facebook Login required** - Cannot use simple Instagram Bearer token
4. **Business/Creator account required** - Personal accounts don't have API access

---

## API Reference

- [Instagram Platform Changelog - Dec 3, 2025](https://developers.facebook.com/docs/instagram-platform/changelog/) - Accept/Decline Collaboration Invites announcement
- [Instagram API with Facebook Login](https://developers.facebook.com/docs/instagram-platform/instagram-api-with-facebook-login)
- [Facebook Graph API Reference](https://developers.facebook.com/docs/graph-api)

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.1 | Jan 2026 | Fixed authentication to use Facebook Graph API OAuth2 |
| 1.0 | Jan 2026 | Initial release |

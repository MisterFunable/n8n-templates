# Instagram Auto Accept Collabs - Setup Guide

Complete guide for setting up Facebook/Instagram credentials for this workflow.

---

## Prerequisites Checklist

Before starting, verify you have:

- [ ] Instagram **Business** or **Creator** account (not Personal)
- [ ] A **Facebook Page** you manage with Admin access
- [ ] Instagram account **linked to the Facebook Page** (not just your profile)
- [ ] Facebook Developer account

---

## Part 1: Verify Instagram Account Type

Your Instagram must be a **Business** or **Creator** account.

### Check Your Account Type

1. Open Instagram app
2. Go to **Settings** → **Account**
3. Look for **Switch to Professional Account** or see your current type

### Switch to Business/Creator (if needed)

1. Instagram → Settings → Account → **Switch to Professional Account**
2. Choose **Creator** or **Business**
3. Complete the setup

---

## Part 2: Link Instagram to Facebook Page

**This is the most important step.** The API requires Instagram to be linked to a Facebook Page, not just your personal Facebook profile.

### Option A: From Facebook Page (Recommended)

1. Go to your Facebook Page
2. Click **Settings** (or **Professional Dashboard** on new Pages)
3. In the left menu, find **Linked Accounts** or **Instagram**
4. Click **Connect Instagram Account**
5. Log in to Instagram when prompted
6. Authorize the connection

### Option B: From Instagram

1. Open Instagram → **Settings**
2. Go to **Account** → **Sharing to other apps** (or **Linked Accounts**)
3. Tap **Facebook**
4. **Important**: When prompted, select your **Facebook Page** (not your personal profile)
5. Complete the authorization

### Verify the Link

After linking, verify it worked:

1. Go to your Facebook Page → Settings → Instagram
2. You should see your Instagram account connected
3. Note: It may take a few minutes to sync

---

## Part 3: Create Facebook Developer App

### Step 1: Access Facebook Developers

1. Go to [developers.facebook.com](https://developers.facebook.com/)
2. Click **My Apps** (top right)
3. Click **Create App**

### Step 2: Create the App

1. Select **Other** for use case (or **Business** if available)
2. Select **Business** as app type
3. Enter app name (e.g., "n8n Instagram Automation")
4. Enter your email
5. Click **Create App**

### Step 3: Add Facebook Login Product

1. In your app dashboard, click **Add Product**
2. Find **Facebook Login** and click **Set Up**
3. Select **Web** as platform
4. Enter any URL for Site URL (e.g., `https://n8n.example.com`)
5. Click **Save**

### Step 4: Get App Credentials

1. Go to **Settings** → **Basic**
2. Copy your **App ID**
3. Click **Show** next to App Secret and copy it
4. Save these for later

---

## Part 4: Configure Permissions

### Required Permissions

| Permission | Purpose | Status |
|------------|---------|--------|
| `instagram_basic` | Access Instagram collaboration invites | Required |
| `pages_show_list` | List Facebook Pages you manage | Required |
| `pages_read_engagement` | Read Page data and linked Instagram | Required |

### Add Permissions to Your App

1. In your Facebook App, go to **App Review** → **Permissions and Features**
2. Find each permission listed above
3. Click **Request** for each one

**Note**: For development/testing, these work immediately for your own account. For production with other users, you'll need to submit for App Review.

### Enable Instagram Settings

1. Go to **Products** → **Facebook Login** → **Settings**
2. Enable **Login with the JavaScript SDK** (optional)
3. Add your n8n URL to **Valid OAuth Redirect URIs**:
   - For n8n Cloud: `https://app.n8n.cloud/rest/oauth2-credential/callback`
   - For self-hosted: `https://your-n8n-domain.com/rest/oauth2-credential/callback`

---

## Part 5: Test with Graph API Explorer

Before setting up n8n, verify everything works:

### Step 1: Open Graph API Explorer

1. Go to [developers.facebook.com/tools/explorer](https://developers.facebook.com/tools/explorer/)
2. Select your app from the dropdown

### Step 2: Generate Token with Permissions

1. Click **Generate Access Token**
2. Check these permissions:
   - `pages_show_list`
   - `pages_read_engagement`
   - `instagram_basic`
3. Click **Generate Access Token**
4. Authorize when prompted

### Step 3: Test the Query

Enter this query and click **Submit**:

```
/me/accounts?fields=id,name,instagram_business_account{id,username}
```

### Expected Response

You should see something like:

```json
{
  "data": [
    {
      "id": "123456789",
      "name": "My Facebook Page",
      "instagram_business_account": {
        "id": "17841400000000000",
        "username": "myinstagram"
      }
    }
  ]
}
```

### Troubleshooting Test Results

| Result | Problem | Solution |
|--------|---------|----------|
| Empty `data` array | No Pages found | Create a Facebook Page or check admin access |
| Page without `instagram_business_account` | Instagram not linked to Page | Follow Part 2 again |
| Error: "permission denied" | Missing permissions | Add permissions in Part 4 |
| Error: "invalid token" | Token expired or wrong app | Generate new token |

---

## Part 6: Create n8n Credential

### Step 1: Add Facebook Graph API Credential

1. In n8n, go to **Credentials**
2. Click **Add Credential**
3. Search for **"Facebook Graph API"**
4. Click to create

### Step 2: Enter App Details

| Field | Value |
|-------|-------|
| **App ID** | Your Facebook App ID (from Part 3) |
| **App Secret** | Your Facebook App Secret (from Part 3) |

### Step 3: Complete OAuth Flow

1. Click **Sign in with Facebook**
2. Log in with the Facebook account that has Page admin access
3. Grant all requested permissions
4. Click **Save**

### Step 4: Verify Credential

The credential should show as connected. If there's an error:
- Check your App ID and Secret are correct
- Verify the OAuth redirect URI is configured in your Facebook App
- Make sure your Facebook account has admin access to a Page with Instagram linked

---

## Part 7: Configure the Workflow

### Step 1: Import Workflow

1. Download `template.json`
2. In n8n, go to **Workflows** → **Import from File**
3. Select the template file

### Step 2: Update Credentials

Update these nodes to use your Facebook Graph API credential:
- **Get Facebook Pages**
- **Fetch Collaboration Invites**
- **Accept/Decline Invite**

### Step 3: Create Data Table

1. Open the **Check If Already Processed** node
2. Click to create a new Data Table
3. Add these fields:

| Field | Type | Primary |
|-------|------|---------|
| `mediaId` | String | Yes |
| `inviterUsername` | String | No |
| `action` | String | No |
| `processedAt` | String | No |
| `success` | String | No |
| `response` | String | No |

4. Save and copy the Data Table ID

### Step 4: Update Configuration

In the **Configuration** node, update:

```json
{
  "autoAccept": true,
  "filterUsernames": "",
  "dataTableId": "YOUR_DATA_TABLE_ID_HERE",
  "waitBetweenRequests": 2
}
```

### Step 5: Test the Workflow

1. Click **Test Workflow** (or use Manual Trigger)
2. Check each node's output:
   - **Get Facebook Pages** should show your Page with `instagram_business_account`
   - **Fetch Collaboration Invites** should work (may return empty if no pending invites)

---

## Common Issues

### "No Instagram Business Account found"

**Cause**: Instagram not linked to a Facebook Page

**Solution**:
1. Go to Facebook Page → Settings → Instagram
2. Connect your Instagram account
3. Wait a few minutes, then retry

### Empty response from `/me/accounts`

**Cause**: You don't have admin access to any Facebook Pages

**Solution**:
1. Create a Facebook Page, or
2. Get admin access to an existing Page

### "OAuthException: Invalid OAuth access token"

**Cause**: Token expired or wrong credential type

**Solution**:
1. Delete the credential in n8n
2. Create a new Facebook Graph API credential
3. Re-authorize with fresh permissions

### "User does not have sufficient permissions"

**Cause**: Missing required permissions

**Solution**:
1. Go to Graph API Explorer
2. Generate new token with all permissions checked
3. Or request permissions in App Review

---

## Required Facebook Page Roles

You need one of these roles on the Facebook Page:

| Role | Can Use This Workflow? |
|------|------------------------|
| Admin | ✅ Yes |
| Editor | ✅ Yes |
| Moderator | ❌ No |
| Advertiser | ❌ No |
| Analyst | ❌ No |

### Check Your Role

1. Go to your Facebook Page
2. Click **Settings** → **Page Roles** (or **Page Access** on new Pages)
3. Find your name and check your role

---

## Quick Reference

### API Endpoints Used

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/me/accounts` | GET | List Pages with linked Instagram |
| `/{ig-user-id}/collaboration_invites` | GET | Get pending invites |
| `/{ig-user-id}/collaboration_invites` | POST | Accept/decline invite |

### Required Permissions Summary

```
instagram_basic
pages_show_list
pages_read_engagement
```

### Token Scopes

When generating tokens in Graph API Explorer or n8n OAuth, ensure these scopes are included:
- `pages_show_list`
- `pages_read_engagement`
- `instagram_basic`

---

## Support Resources

- [Facebook Graph API Documentation](https://developers.facebook.com/docs/graph-api)
- [Instagram API with Facebook Login](https://developers.facebook.com/docs/instagram-platform/instagram-api-with-facebook-login)
- [Graph API Explorer](https://developers.facebook.com/tools/explorer/)
- [Facebook App Dashboard](https://developers.facebook.com/apps/)

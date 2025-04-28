# 🚀 LinkedIn and Instagram Auto Poster using Zapier + Google Sheets

This project automates posting text content to LinkedIn (and future Instagram) directly from a Google Sheet using Zapier, Webhooks, and LinkedIn APIs.

---

## 📋 Overview
- **Platforms**: LinkedIn (Instagram coming)
- **Automation Tool**: Zapier
- **Data Source**: Google Sheets
- **APIs Used**:
  - LinkedIn UGC Posts API (/v2/ugcPosts)
  - (Instagram API future scope)
- **Authentication**: OAuth 2.0 Bearer Token

---

## 🛠️ Setup Instructions

### 1. LinkedIn Developer Setup
- Create a LinkedIn Developer App.
- Add Products:
  - Sign In with LinkedIn (OpenID Connect) → to fetch Member ID
  - Share on LinkedIn → to post content
- Generate Access Token with `w_member_social` permission.
- Fetch Member ID via `/v2/userinfo` endpoint.

### 2. Google Sheet Setup
Create columns:

| text | image_url | date_to_post | platforms | post_action | post_status | ready_to_post |
|:---|:---|:---|:---|:---|:---|:---|
| Your post text | (Optional) | 2025-04-28 | LinkedIn | Publish | Pending | YES |

- `ready_to_post` auto-calculates using a formula based on today's date.

### 3. Zapier Zap Setup
- **Trigger**: New or Updated Spreadsheet Row (Google Sheets)
- **Filter**:
  - post_action = Publish
  - ready_to_post = YES
  - post_status = Publish
- **Path A (LinkedIn)**:
  - If platforms contains LinkedIn ➔ continue
  - **Webhook Action**: POST to LinkedIn API

- **Webhook POST Configuration**:

| Field | Value |
|:---|:---|
| URL | https://api.linkedin.com/v2/ugcPosts |
| Payload Type | JSON |
| Wrap Request In Array | No |
| Unflatten | Yes |

- **Body (Data)**:

```json
{
  "author": "urn:li:person:YOUR_MEMBER_ID",
  "lifecycleState": "PUBLISHED",
  "specificContent": {
    "com.linkedin.ugc.ShareContent": {
      "shareCommentary": {
        "text": [Row Text from Sheet]
      },
      "shareMediaCategory": "NONE"
    }
  },
  "visibility": {
    "com.linkedin.ugc.MemberNetworkVisibility": "PUBLIC"
  }
}

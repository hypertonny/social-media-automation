# 🚀 LinkedIn & Instagram Auto Poster using Zapier + Google Sheets

This project automates posting text content directly from a Google Sheet to LinkedIn (and Instagram in future) using Zapier, Webhooks, and LinkedIn APIs.

---

## 📋 Overview
- **Platforms**: LinkedIn (Instagram coming soon)
- **Automation Tool**: Zapier
- **Data Source**: Google Sheets
- **APIs Used**:
  - LinkedIn UGC Posts API (`/v2/ugcPosts`)
- **Authentication**: OAuth 2.0 Bearer Token

---

## 🛠️ Setup Instructions

### 1. LinkedIn Developer Setup
- Create a [LinkedIn Developer App](https://www.linkedin.com/developers/).
- Add products:
  - **Sign In with LinkedIn (OpenID Connect)** → to fetch Member ID
  - **Share on LinkedIn** → to post content
- Generate an **Access Token** with `w_member_social` permission.
- Fetch your **Member ID** using the `/v2/userinfo` endpoint.

---

### 2. Google Sheet Setup
Create a sheet with the following columns:

| text | image_url | date_to_post | platforms | post_action | post_status | ready_to_post |
|:---|:---|:---|:---|:---|:---|:---|
| Your post text | (Optional) | 2025-04-28 | LinkedIn | Publish | Pending | YES |

Use this formula to auto-calculate `ready_to_post`:

```excel
=IF(date_to_post_cell = TODAY(), "YES", "NO")
```

Replace `date_to_post_cell` with the actual cell reference.

---

### 3. Zapier Zap Setup

**Trigger**:  
- New or Updated Spreadsheet Row (Google Sheets)

**Filter**:  
- `post_action = Publish`
- `ready_to_post = YES`
- `post_status = Publish`

**Path A (LinkedIn)**:
- Condition: `platforms` contains "LinkedIn"
- Action: Webhook by Zapier (POST Request)

---

### 🛜 Webhook POST Configuration

- **Method**: POST
- **URL**: `https://api.linkedin.com/v2/ugcPosts`
- **Data Pass-Through**: False
- **Payload Type**: JSON
- **Unflatten**: No

**Body (Data)**:
```json
{
  "author": "urn:li:person:YOUR_MEMBER_ID",
  "lifecycleState": "PUBLISHED",
  "specificContent": {
    "com.linkedin.ugc.ShareContent": {
      "shareCommentary": {
        "text": "YOUR_POST_TEXT_FROM_SHEET"
      },
      "shareMediaCategory": "NONE"
    }
  },
  "visibility": {
    "com.linkedin.ugc.MemberNetworkVisibility": "PUBLIC"
  }
}
```

**Headers**:
| Key | Value |
|:---|:---|
| Authorization | Bearer YOUR_ACCESS_TOKEN |
| X-Restli-Protocol-Version | 2.0.0 |
| Content-Type | application/json |

✅ **Important**:  
- Ensure "Bearer" is included before the Access Token in the `Authorization` header.

---

## ✅ Final Result

Whenever a new row in your Google Sheet has:
- `ready_to_post = YES`
- `post_action = Publish`
- `post_status = Publish`

Zapier will automatically post the text on your LinkedIn profile! 🎯

After successful posting:
- `post_status` will update to **Posted** automatically.

---

## 📌 Notes

- 🔄 Access Tokens expire periodically. Refresh them when needed.
- 📸 Direct Instagram posting requires a **Business Instagram Account** connected to a **Facebook Page**.

---

## 🌟 Future Scope

- 📷 Posting images and other media.
- 🤖 Smart scheduling using AI predictions.
- 🛠️ Advanced error handling and retries.

---

## 🔥 License

This project is free to **use**, **clone**, and **modify** for personal or professional automation purposes.

---

# 🚀 LinkedIn Auto-Poster — n8n Workflow

Automatically post scheduled LinkedIn content (with images) from a Google Sheet — every 2 days, hands-free.

---

## 📌 What This Does

This n8n workflow reads your pre-written LinkedIn posts from a Google Sheet, picks the next unposted one, fetches the image, and publishes it to LinkedIn — then marks it as posted in the sheet. Runs automatically every 2 days via a cron schedule.

---

## 🔁 Workflow Overview

```
Schedule Trigger (every 2 days)
        ↓
Get row(s) in sheet       ← Reads all posts from Google Sheet
        ↓
Code in Python            ← Picks the first row where status ≠ "posted"
        ↓
HTTP Request              ← Fetches image URL → converts to binary
        ↓
Create a post             ← Posts to LinkedIn with text + image binary
        ↓
Update row in sheet       ← Marks that row as "posted" in the sheet
```

---

## 🧩 Node Breakdown

### 1. ⏰ Schedule Trigger
- Runs the workflow automatically **every 2 days**
- No manual intervention needed once published

### 2. 📊 Get Row(s) in Sheet
- Reads all rows from your connected **Google Sheet**
- Sheet contains columns like: `Post Content`, `Image URL`, `Status`

### 3. 🐍 Code in Python
- Loops through the fetched rows
- Selects the **first row** where `Status` is **not** `"posted"`
- Passes that row's data to the next node

### 4. 🌐 HTTP Request
- Takes the `Image URL` from the selected row
- Fetches the image and converts it to **binary format**
- Required for LinkedIn's media upload API

### 5. 💼 Create a Post (LinkedIn)
- Posts to LinkedIn using the official LinkedIn node
- Sends:
  - Post text/content
  - Image in binary format

### 6. 📝 Update Row in Sheet
- Finds the row that was just posted
- Updates its `Status` column to `"posted"`
- Prevents the same post from being published again

---

## 📋 Google Sheet Structure

Your sheet should follow this column layout:

| Post Content | Image URL | Status |
|---|---|---|
| Your LinkedIn post text here... | https://example.com/image.jpg | |
| Another post... | https://example.com/image2.jpg | posted |

- Leave `Status` blank for pending posts
- The workflow sets it to `posted` after publishing

---

## ⚙️ Setup Instructions

### Prerequisites
- [n8n](https://n8n.io/) instance (cloud or self-hosted)
- Google account with Sheets access
- LinkedIn account connected to n8n

### Steps

1. **Import the workflow** into your n8n instance
2. **Connect Google Sheets** credential in the `Get row(s)` and `Update row` nodes
3. **Connect LinkedIn** credential in the `Create a post` node
4. **Update the Sheet ID** in both Google Sheets nodes to point to your spreadsheet
5. **Fill your Google Sheet** with posts and image URLs
6. **Activate the workflow** — it will run every 2 days automatically

---

## 🔐 Required Credentials

| Service | Used In |
|---|---|
| Google Sheets OAuth2 | Get rows + Update row |
| LinkedIn OAuth2 | Create a post |

---

## 📁 Files in This Repo

```
├── linkedin_automation.json   # n8n workflow export
└── README.md                  # This file
```

> To import the workflow: open n8n → **Workflows** → **Import from file** → select `linkedin_automation.json`

---

## 💡 Tips

- Add as many rows as you want to your sheet — the workflow processes one post per run
- To pause posting, just deactivate the workflow in n8n
- You can change the schedule interval in the **Schedule Trigger** node (e.g., daily, weekly)
- Make sure image URLs are **publicly accessible** so the HTTP Request node can fetch them

---

## 🛠️ Built With

- [n8n](https://n8n.io/) — Workflow automation
- Google Sheets — Content management
- LinkedIn API — Publishing

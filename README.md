# 🚀 LinkedIn Auto-Poster — n8n Workflow

Automatically post scheduled LinkedIn content (with images) from a Google Sheet — every 2 days at 6:00 PM, hands-free.

---

## 📌 What This Does

This n8n workflow reads your pre-written LinkedIn posts from a Google Sheet (`Post Sheet`), picks the first row where `Posted?` is `no`, fetches the image from the URL, and publishes it to LinkedIn with the post text and hashtags — then marks that row as `yes` in the sheet. Runs automatically every 2 days at 6:00 PM via a cron schedule.

---

## 🔁 Workflow Overview

```
Schedule Trigger (every 2 days at 6:00 PM)
        ↓
Get row(s) in sheet       ← Reads all rows from "Post Sheet" tab
        ↓
Code in Python            ← Finds first row where Posted? == "no"
        ↓
HTTP Request              ← Fetches Image URL → converts to binary
        ↓
Create a post             ← Posts to LinkedIn (text + hashtags + image)
        ↓
Update row in sheet       ← Sets Posted? to "yes" using Post ID as key
```

---

## 🧩 Node Breakdown

### 1. ⏰ Schedule Trigger
- Fires automatically **every 2 days at 6:00 PM**
- No manual intervention needed once the workflow is active

### 2. 📊 Get Row(s) in Sheet
- Reads all rows from the **Post Sheet** tab in your Google Sheet
- Pulls all columns: `Post ID`, `Post Content`, `Hashtags`, `Image URL`, `Posted?`

### 3. 🐍 Code in Python
- Loops through all fetched rows
- Selects the **first row** where `Posted?` equals `"no"` (case-insensitive)
- Returns only that one row to the next node
- If no unposted rows exist, returns empty and the workflow stops

### 4. 🌐 HTTP Request
- Takes the `Image URL` from the selected row
- Fetches the image and returns it as **binary file data**
- Required for LinkedIn's image upload format

### 5. 💼 Create a Post (LinkedIn)
- Posts to LinkedIn using the official LinkedIn node
- Sends:
  - `Post Content` as the main text
  - `Hashtags` appended below the content
  - Image in binary format
  - Visibility set to **PUBLIC**

### 6. 📝 Update Row in Sheet
- Matches the row by **Post ID**
- Updates `Posted?` from `no` → `yes`
- Prevents the same post from ever being published again

---

## 📋 Google Sheet Structure

Sheet name: **Post Sheet**

| Post ID | Post Content | Hashtags | Image URL | Posted? |
|---|---|---|---|---|
| 1 | Your LinkedIn post text here... | #automation #n8n | https://example.com/image.jpg | no |
| 2 | Another post... | #linkedin #productivity | https://example.com/image2.jpg | no |

- `Posted?` must be `no` for pending posts — the workflow sets it to `yes` after publishing
- `Post ID` is used as the unique key to update the correct row
- Image URLs must be **publicly accessible**
- A ready-to-use template is included: `linkedin_post_template.xlsx`

---

## ⚙️ Setup Instructions

### Prerequisites
- [n8n](https://n8n.io/) instance (cloud or self-hosted)
- Google account with Sheets access
- LinkedIn account connected to n8n

### Steps

1. **Import the workflow** into your n8n instance
2. **Connect Google Sheets** credential in the `Get row(s) in sheet` and `Update row in sheet` nodes
3. **Connect LinkedIn** credential in the `Create a post` node
4. **Update the Sheet ID** in both Google Sheets nodes to point to your spreadsheet
5. **Set the correct Sheet tab** — make sure it points to `Post Sheet`
6. **Fill your Google Sheet** using the provided `linkedin_post_template.xlsx` as a base
7. **Activate the workflow** — it will run every 2 days at 6:00 PM automatically

---

## 🔐 Required Credentials

| Service | Used In |
|---|---|
| Google Sheets OAuth2 | Get row(s) in sheet + Update row in sheet |
| LinkedIn OAuth2 | Create a post |

---

## 📁 Files in This Repo

```
├── linkedin_automation.json       # n8n workflow export
├── linkedin_post_template.xlsx    # Google Sheet template with correct columns
└── README.md                      # This file
```

> To import the workflow: open n8n → **Workflows** → **Import from file** → select `linkedin_automation.json`  
> To use the sheet template: upload `linkedin_post_template.xlsx` to Google Drive → open with Google Sheets

---

## 💡 Tips

- The workflow posts **one item per run** — it always picks the next unposted row
- To pause posting, just deactivate the workflow in n8n
- To change the schedule, edit the **Schedule Trigger** node (e.g., every day, every 3 days)
- Make sure image URLs are publicly accessible — Google Drive links won't work unless shared publicly
- You can add as many rows as you want; the workflow will work through them in order

---

## 🛠️ Built With

- [n8n](https://n8n.io/) — Workflow automation
- Google Sheets — Content queue management
- LinkedIn API — Publishing

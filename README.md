# n8n Workflow Auto-Backup to Google Drive

Automatically backs up all your n8n workflows as `.json` files to Google Drive every 23 hours. Creates a timestamped folder on every run, uploads each workflow, and deletes outdated backup folders to keep your Drive clean. Includes a full error-handling pipeline that sends Gmail alerts if anything goes wrong.

---

![Workflow Preview](/assets/workflow.png)
![Workflow Preview](/assets/drive.png)

## Features

- **Scheduled backup** — Runs every 23 hours automatically
- **Timestamped folders** — Each backup gets a unique folder name (e.g. `n8n Backup 20-02-2026 6:00 PM`) to prevent collisions
- **Full workflow export** — Every workflow on your n8n instance is exported as a formatted `.json` file
- **Auto-cleanup** — Outdated backup folders are detected and deleted after each successful run
- **Error alerts** — A dedicated error handler catches any failure and sends a Gmail alert with the error message, failed node name, execution ID, timestamp, and a direct link to the execution log

---

## 🗂️ Workflow Structure

```
Schedule Trigger (every 23hrs)
  └── Generate timestamped folder name
        └── Create backup folder in Google Drive
              └── Fetch all workflows via n8n API
                    └── Loop through each workflow
                          ├── Convert workflow to .json file
                          │     └── Upload to backup folder
                          └── Get all existing n8n backup folders
                                └── Filter out current folder
                                      └── Delete outdated folders

Error Trigger (catches all failures)
  └── Extract error details
        └── Send Gmail alert
```

---

## Setup

### Prerequisites

- n8n instance (self-hosted or cloud)
- Google account with Drive and Gmail access
- n8n API key

### Step 1 — Import the workflow

1. Download `Backup_n8n_Workflows_to_Drive.json`
2. Open your n8n instance
3. Go to **Workflows** → click **Import**
4. Select the downloaded file

### Step 2 — Connect your credentials

| Node | Credential Required |
|---|---|
| Create Backup Folder | Google Drive OAuth2 |
| Get All Workflow Files | n8n API key |
| Upload to Drive Folder | Google Drive OAuth2 |
| Get Folders Prefix n8n Backup | Google Drive OAuth2 |
| Delete Outdated Folder | Google Drive OAuth2 |
| Send Email Alert | Gmail OAuth2 |

### Step 3 — Create your n8n API key

1. In n8n, go to **Settings**
2. Click **n8n API**
3. Click **Create API Key**
4. Copy and paste it into the `n8n-local` credential

### Step 4 — Configure the error alert email

In the `Extract Error Details` node, update this field to your email address:

```
alert_email: "your-email@gmail.com"
```

### Step 5 — Customize the Gmail error template

Open the `Send Email Alert` node and replace the placeholder with your own HTML email template.

### Step 6 — Activate

Toggle the workflow to **Active**. It will run automatically every 23 hours.

---

## Notes

- Backup folders are named using the pattern: `n8n Backup dd-MM-yyyy h:mm a`
- The cleanup step searches for all Drive folders containing `n8n backup` and deletes every folder except the one just created
- If you rename the folder prefix, update the search query in the `Get Folders Prefix n8n Backup` node accordingly
- The error workflow is self-referencing — it is set as the error workflow for itself via workflow settings

---


## File Structure

```
assets/
drive.png
workflow.png
workflow/
Backup_n8n_Workflows_to_Drive.json      # Import this into n8n
README.md
LICENSE
```


[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)
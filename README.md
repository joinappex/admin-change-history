# 🗂 Move Old Changes Script

This Python script automates the process of archiving old changes from a Google Sheet. It reads from a **"Recent Changes"** sheet, identifies rows older than 14 days based on a **"Change Timestamp"** column, and moves them to a **"Historical"** sheet. It also removes empty rows from the Recent Changes sheet to keep data compact.

---

## 📊 Example Spreadsheet Format

Your Google Spreadsheet must have a sheet named **"Recent Changes"** with the following columns:

| Product Name   | Version | Change Name | Change Details | Change Timestamp            | End Change Timestamp | Change Type  | Component | Platform | Link | Comments |
|----------------|---------|-------------|----------------|------------------------------|-----------------------|--------------|-----------|----------|------|----------|
| Product Name 1 | 112.12.12 | Testing     |                | 2025-05-01T17:34:00+03:00    | 2025-05-22            | Full Rollout | Marketing | All      |      |          |
| Product Name 2 |           | Deploying   | | 2025-03-27              |                       | Full Rollout | Marketing | All      |      |          |

✅ The `Change Timestamp` column must exist and be in one of these formats:
- ISO 8601: `2025-05-01T17:34:00+03:00` or `2025-05-01T17:34:00Z`
- US-style: `05/01/2025 17:34:00`

---

## 🛠 Script Behavior

- Moves rows from "Recent Changes" to "Historical" if the `Change Timestamp` is older than 14 days
- Keeps the header intact
- Removes gaps in "Recent Changes" by shifting non-empty rows upward

---

## 🖥 Local Usage

### 1. Create a `.env` file

```env
SERVICE_ACCOUNT_JSON=service-account-creds.json
SPREADSHEET_ID=your_spreadsheet_id_here
```

### 2. Install dependencies

```bash
pip install gspread google-auth python-dotenv
```

### 3. Run the script

```bash
python move_old_changes.py
```

---

## ☁️ GitHub Actions Automation

### 1. Add Secrets to GitHub

In your GitHub repo → Settings → Secrets and variables → Actions → Add the following:

- `SERVICE_ACCOUNT_JSON` – paste contents of your service-account-creds.json
- `SPREADSHEET_ID` – your sheet ID (from the Google Sheets URL)

### 2. Enable and Run the Workflow

If you've copied or forked this repo, GitHub disables Actions by default. To enable and run the workflow:

1. Go to the **"Actions"** tab of your repository.
2. You will see a banner asking if you want to enable workflows — click **“I understand…”** and **Enable**.
3. You will see a workflow named **"Move Old Sheet Rows"**.
4. Click into it, then press **"Run workflow"** (top-right dropdown) to trigger it manually.

The script will also run automatically every day if the schedule is enabled.

---


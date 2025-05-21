# Change History Sheet

This is an example model of a sheet that you can manually populate to keep track of changes that affect your business. They can either be app releases, backend relesaes, marketing changes (e.g. incrementing budget, changing visuals), outages, external changes, etc.

To keep things simple to use, it uses two sheets, a "Recent Changes" one, that only contains changes from the last 14 days and a "Historical" one.

You can look at an example template sheet [here](https://docs.google.com/spreadsheets/d/1ZBUeefx5cMomx53adNwjsrCVj5Rd7rBBp2OYAjDQYQo/edit?gid=0#gid=0)

# 🗂 Move "Recent Changes" -> "Historical" Script

This repo has a Python script that moves "old" rows from **"Recent Changes"** into the a **"Historical"** sheet.
It identifies old rows by looking at the **"Change Timestamp"** column and moving those older than 14 days.
It also removes empty rows from the Recent Changes sheet to keep data compact.

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

### 2. Create Workflow File

Create `.github/workflows/move-old-changes.yml`:

```yaml
name: Move Old Sheet Rows

on:
  schedule:
    - cron: '0 5 * * *'  # Runs daily at 5 AM UTC
  workflow_dispatch:

jobs:
  move-rows:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - uses: actions/setup-python@v5
        with:
          python-version: 3.9

      - name: Install dependencies
        run: |
          python -m venv venv
          source venv/bin/activate
          pip install gspread google-auth python-dotenv

      - name: Run script
        env:
          SERVICE_ACCOUNT_JSON: ${{ secrets.SERVICE_ACCOUNT_JSON }}
          SPREADSHEET_ID: ${{ secrets.SPREADSHEET_ID }}
        run: |
          echo "$SERVICE_ACCOUNT_JSON" > service-account-creds.json
          source venv/bin/activate
          python move_old_changes.py
```


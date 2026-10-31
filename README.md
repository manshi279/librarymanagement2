# LibraCore — College Library Management System

Premium dark-blue library dashboard for GitHub Pages. No Node.js or local server is required.

## Features
- Dashboard cards, availability ring, last-7-days circulation activity and recent transactions
- Student registration, editing, search, department/course fields and printable library ID cards
- Book catalogue with title, author, department, category, publisher, ISBN, quantity and shelf/rack
- Author/title/ISBN/ID search, department filter, add/edit/delete book records and stock tracking
- Issue and return register, issue/due/actual return dates, active loans, overdue detection and configurable per-day fine
- Student-wise transaction history, fine records, department stock report and CSV exports
- Local browser storage works immediately as a demo
- Optional Google Sheets data storage and daily Google Drive backup through Google Apps Script

## Project files
- `index.html` — frontend app for GitHub Pages
- `Code.gs` — Google Apps Script backend for Sheets and Drive backup
- `README.md` — setup guide

## Part A — Publish frontend on GitHub Pages
1. Create a GitHub repository, e.g. `college-library-management`.
2. Upload `index.html` to the repository root.
3. Open **Settings → Pages**.
4. Under Build and deployment choose **Deploy from a branch**, branch `main`, folder `/(root)`, then Save.
5. Wait for the published URL shown on the Pages settings page. The frontend runs in browser without Node.js.

## Part B — Connect Google Sheets
1. Open [Google Apps Script](https://script.google.com/) and create a **New project**.
2. Replace the default editor code with the contents of `Code.gs`.
3. Save the project.
4. Select function `setup` in the function dropdown and click **Run**. Approve the Google Sheets and Drive permissions. Open **Execution log** to find the database URL (or run `showDatabaseUrl`).
5. Select **Deploy → New deployment**.
6. Select type **Web app**.
7. Set **Execute as: Me**. Set **Who has access: Anyone** (or your organization option if all users are within a Google Workspace domain). Deploy and authorize if asked.
8. Copy the Web app URL ending in `/exec`.
9. In `index.html`, find this line near the top of the main script:
   `const API_URL = "";`
   Paste your URL between the quotes, for example:
   `const API_URL = "https://script.google.com/macros/s/YOUR_DEPLOYMENT_ID/exec";`
10. Commit the updated `index.html` to GitHub. Reload the GitHub Pages website. On first connection, the app reads the Google Sheets database. Changes are saved to Sheets; the browser also keeps a local cache.

**Important:** This is a lightweight trusted-team demo, not a hardened public multi-tenant service. The Apps Script endpoint uses JSONP to avoid browser cross-origin preflight. Anyone who obtains an “Anyone” endpoint URL may be able to read/write its data. Do not publish sensitive student information in a public repository. For real college deployment, restrict access to your Workspace domain or add proper authentication and role permissions.

## Part C — Enable daily Google Drive backups
1. In Apps Script, select `createDailyBackupTrigger`.
2. Click **Run** once and approve Drive permissions.
3. The script creates a `LibraCore Library Backups` folder in Drive and a timestamped spreadsheet copy. A daily trigger is scheduled around 2 AM in the script timezone.
4. To change the timezone, open **Project Settings** in Apps Script and set the correct timezone before running the trigger setup.

## Data storage notes
- If `API_URL` is blank, data is stored only in that browser's local storage. Use the same browser/device to see it again.
- If connected, Google Sheets is the shared data source and the browser keeps a local cache.
- The web app does not upload student photos; ID card photos are shown from the optional photo URL you enter.
- Use CSV export from Issue History or Reports for additional offline copies.

## Fine and stock rules
- Default fine is ₹10 per late day, editable when recording a return.
- Late days are calculated from the due date to the actual return date.
- A book's available copies = total quantity − active issue transactions.
- The app prevents issuing unavailable copies and prevents reducing quantity below the number of active issues.

## Suggested first test
1. Register a test student.
2. Add a book with quantity 2.
3. Issue one copy to that student.
4. Confirm available stock changes to 1.
5. Record a return date after the due date and verify fine calculation.
6. Refresh the page to verify local data, then test again with Apps Script connected.

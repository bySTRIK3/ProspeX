````markdown
# 📊 ProspeX Data Workflow

This document outlines the data pipeline and workflow for the **ProspeX Project**, which tracks, ranks, and analyzes baseball prospects using data from public sources (Fangraphs, Baseball America, MLB Pipeline, etc.).

---

## 🔄 Workflow Overview

| Component         | Role                                                    |
|------------------|---------------------------------------------------------|
| **GitHub**        | Version control for scripts, CSVs, changelogs           |
| **Google Sheets** | Editable frontend for QA, live entry, ID mapping        |
| **Database**      | Centralized data warehouse for analytics & querying     |

---

## 🔁 Workflow Diagram

```text
GitHub
  ↑↓         ↘
Sheets ←→ Scripts ←→ PostgreSQL/Snowflake
  ↑              ↘
Manual QA       Looker Studio / Dashboards
````

---

## ⚙️ Step-by-Step Integration

### 🔁 1. GitHub → Sheets Sync

Use a Google Apps Script to import CSVs directly from GitHub into Google Sheets for collaborative editing:

```javascript
function importCSVfromGitHub() {
  var fileURL = 'https://raw.githubusercontent.com/YourRepo/ProspeX/main/data/processed/master_prospect_rankings.csv';
  var csv = UrlFetchApp.fetch(fileURL).getContentText();
  var data = Utilities.parseCsv(csv);
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Master_Rankings");
  sheet.clearContents();
  sheet.getRange(1, 1, data.length, data[0].length).setValues(data);
}
```

---

### 🔁 2. Sheets → Database Sync

#### Python ETL Example

```python
import gspread
import pandas as pd
from sqlalchemy import create_engine

# Load Google Sheet
gc = gspread.service_account(filename='your_credentials.json')
sheet = gc.open("ProspeX_DB").worksheet("Master_Rankings")
data = pd.DataFrame(sheet.get_all_records())

# Upload to database
engine = create_engine('postgresql://user:pass@host/dbname')
data.to_sql('prospex_rankings', con=engine, if_exists='replace', index=False)
```

---

### 🔁 3. Database → GitHub

Use a scheduled script or GitHub Action to:

* Export database tables (e.g., `rank_trends`)
* Save them to `/data/archive/`
* Update `CHANGELOG.md`

```bash
# Example auto-commit
git add data/archive/rank_trends.csv
git commit -m "🔄 Weekly update: rank_trends.csv"
git push origin main
```

---

## 📁 Project Directory Reference

```plaintext
📁 ProspeX
├── 📁 data
│   ├── raw/
│   ├── cleaned/
│   ├── processed/
│   ├── archive/
│   └── export/
├── 📁 scripts
│   ├── etl/
│   └── scraping/
├── 📁 docs
│   └── changelog.md
├── README.md
└── .gitignore
```

---

## 🛠️ Tools Used

* **GitHub** – versioning, code, auto-sync
* **Google Sheets + Apps Script** – manual edits, lookup, sync
* **PostgreSQL / Snowflake** – data warehouse
* **Python + Pandas + SQLAlchemy** – ETL automation
* **Looker Studio / Sheets** – dashboards, reports

---

## 📅 Automation Ideas

* `GitHub Actions` to export CSVs from database weekly
* `Apps Script Triggers` to auto-fetch GitHub data
* `Python scheduler` to push updates to the database from Sheets

---

## ✅ Next Steps

* [ ] Set up Google Sheets with Apps Script sync
* [ ] Finalize DB schema (rankings, scouting, trends)
* [ ] Connect Looker Studio to SQL views
* [ ] Create automation scripts

---

```

Would you like this turned into a downloadable `.md` file or copied into your GitHub repo directly?
```

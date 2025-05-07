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

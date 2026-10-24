# 📖 Data Dictionary — `Rupesh_Kumar_IT_Ticket_Analysis.xlsx`

The workbook has 3 core data sheets, 22 analysis/pivot sheets (one per question), and a `Dashboard` sheet. This document covers the 3 core sheets, which feed every pivot table and chart in the workbook.

---

## Sheet: `Tickets` (raw data) — 97,498 rows × 10 columns

| # | Column | Type | Description |
|---|---|---|---|
| 1 | `ID Ticket` | Text | Unique ticket identifier |
| 2 | `Fecha` | Date | Date the ticket was raised |
| 3 | `Employee ID` | Integer | ID of the employee who raised the ticket |
| 4 | `Agent ID` | Integer | ID of the IT agent who handled the ticket (joins to `IT Agents`) |
| 5 | `Request Category` | Text | High-level category: `Login Access`, `System`, `Software`, `Hardware` |
| 6 | `Issue Type` | Text | `IT Error` or `IT Request` |
| 7 | `Severity` | Text | `0 - Unclassified`, `1 - Minor`, `2 - Normal`, `3 - Major`, `4 - Urgent` |
| 8 | `Priority` | Text | `0 - Unassigned`, `1 - Low`, `2 - Mid`, `3 - High` |
| 9 | `Resolution Time (Days)` | Integer | Days taken to resolve the ticket (0 = same-day) |
| 10 | `Satisfaction Rate` | Integer (1–5) | Employee satisfaction rating for the resolution |

---

## Sheet: `IT Agents` (reference table) — 50 rows × 8 columns

| # | Column | Type | Description |
|---|---|---|---|
| 1 | `Agent ID` | Integer | Unique agent identifier (joins to `Tickets`/`Cleaned Data`) |
| 2 | `Full Name` | Text | Agent's full name |
| 3 | `Email` | Text | Agent's corporate email address |
| 4 | `Year of Birth` | Integer | Birth year |
| 5 | `Month of Birth` | Integer | Birth month |
| 6 | `Day of Birth` | Integer | Birth day |
| 7 | `Email_Domain` | Text (derived) | Domain extracted from `Email` — all 50 agents share `fp20analytics` |
| 8 | `Age (As of 2026-07-31)` | Integer (derived) | Agent's current age, calculated from birth date |

---

## Sheet: `Cleaned Data` (analysis-ready) — 97,498 rows × 21 columns

Joins `Tickets` to `IT Agents` via `Agent ID` and adds derived fields used throughout the dashboard and pivot tables.

| # | Column | Type | Description |
|---|---|---|---|
| 1 | `ID Ticket` | Text | Same as `Tickets.ID Ticket` |
| 2 | `Date` | Date | Standardized accident/ticket date |
| 3 | `Employee ID` | Integer | Employee who raised the ticket |
| 4 | `Agent ID` | Integer | Handling agent's ID |
| 5 | `Request Category` | Text | Standardized category label |
| 6 | `Issue Type` | Text | `IT Error` / `IT Request` |
| 7 | `Severity` | Text | Standardized severity label (`0 - Unclassified` → `4 - Urgent`) |
| 8 | `Priority` | Text | Standardized priority label |
| 9 | `Resolution Time (Days)` | Integer | Days to resolve |
| 10 | `Satisfaction Rate` | Integer | 1–5 rating |
| 11 | `Agent Name` | Text (derived) | `INDEX`/`MATCH` lookup from `IT Agents` |
| 12 | `Agent Age` | Integer (derived) | Lookup from `IT Agents` |
| 13 | `Severity Rank` | Integer (derived) | Numeric map of Severity (0–4) used for correlation analysis |
| 14 | `Year` | Integer (derived) | Extracted from `Date` |
| 15 | `Month-Year` | Date (derived) | First-of-month date, used for monthly trend charts |
| 16 | `Quarter` | Integer (derived) | Calendar quarter (1–4) |
| 17 | `Day of Week` | Text (derived) | Mon–Sun |
| 18 | `Age Band` | Text (derived) | 5-year agent age bucket: `29-33`, `34-38`, `39-43`, `44-48`, `49-53`, `54-58` |
| 19 | `Email` | Text (derived) | Agent's email, joined in for reference |
| 20 | `Email_Domain` | Text (derived) | Extracted domain |
| 21 | `Email_Name` | Text (derived) | Name portion parsed from the email |

---

## Value distributions (full dataset, 97,498 tickets)

**Request Category**
- System: 39,002 (40.0%) · Login Access: 29,193 (29.9%) · Software: 19,570 (20.1%) · Hardware: 9,733 (10.0%)

**Issue Type**
- IT Request: 73,220 (75.1%) · IT Error: 24,278 (24.9%)

**Severity**
- Normal: 88,656 (90.9%) · Major: 4,836 (5.0%) · Urgent: 1,392 (1.4%) · Minor: 2,258 (2.3%) · Unclassified: 356 (0.4%)

**Priority**
- High: 35,549 (36.5%) · Unassigned: 29,410 (30.2%) · Mid: 16,845 (~17.3%) · Low: 16,694 (~15.9%)

**Agent Age Band**
- 29–33: 12 agents · 34–38: 7 agents · 39–43: 10 agents · 44–48: 13 agents · 49–53: 6 agents · 54–58: 2 agents

**Resolution Time by Category (avg. days)**
- Login Access: 0.31 · Software: 5.24 · System: 6.62 · Hardware: 7.63

**Yearly Ticket Volume**
- 2016: 13,051 · 2017: 14,915 · 2018: 18,954 · 2019: 21,490 · 2020: 29,088

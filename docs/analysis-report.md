# 📑 Full Analysis Report — IT Ticket Analysis

This is the complete question-by-question write-up behind the dashboard and headline findings in the main [README](../README.md). Every answer traces back to a formula, pivot table, or chart in `data/Rupesh_Kumar_IT_Ticket_Analysis.xlsx` (see the sheet named after each question).

---

## Part A — Objective Questions

### Q1. What is the total number of attributes present in the data?
**Answer:** The raw `Tickets` sheet has 10 attributes (ID Ticket, Fecha, Employee ID, Agent ID, Request Category, Issue Type, Severity, Priority, Resolution Time (Days), Satisfaction Rate). The `IT Agents` reference sheet has 6 attributes (Agent ID, Full Name, Email, Year/Month/Day of Birth). **Total: 16 attributes.**
**Approach:** Counted the header row of the `Tickets` and `IT Agents` sheets directly.

### Q2. Which columns have inconsistent or missing values, and what is the count of such values?
**Answer:** No blank/null cells exist in either sheet, but **4 columns** contained inconsistent category labels or malformed names, totaling **34,606 affected ticket rows** and **15 affected agent records**.
**Approach:** Used `COUNTIF`/`COUNTIFS` on each column to check for blanks, then reviewed distinct values in Severity, Priority, and Full Name via pivot tables, cross-checking Full Name against the Email column's `firstname.lastname` convention to detect naming inconsistencies. Corrected directly in `Cleaned Data` and `IT Agents`.

### Q3. What is the average daily ticket volume over time?
**Answer:** **53.37 tickets/day** on average across the full 2016–2020 period (97,498 tickets over 1,827 days).
**Formula:** `=ROUND(GETPIVOTDATA("ID Ticket",$A$11)/COUNTA(A12:$A$1838),2)`
**Chart:** "Ticket Volume Trend" line chart (`I Objective` sheet).

### Q4. What is the distribution of ticket categories?
**Answer:** System 39,002 (40.0%) · Login Access 29,193 (29.9%) · Software 19,570 (20.1%) · Hardware 9,733 (10.0%).
**Approach:** Pivot table — Request Category (rows) × Count of ID Ticket (values) — visualized as a pie chart.
**Insight:** System and Login Access together account for ~70% of all tickets.

### Q5. How many tickets has each agent handled?
**Answer:** Each of the 50 agents handled between **1,856 and 2,027 tickets**, averaging **1,950 tickets/agent** — a very even distribution.
**Approach:** Pivot table — Agent Name (rows) × Count of ID Ticket (values) — sorted descending, bar chart.

### Q6. How can you extract the domain from the email addresses in the IT Agents sheet?
**Answer:** All 50 agents share a single email domain: **fp20analytics**.
**Formula:** `=LEFT(RIGHT(C2,LEN(C2)-FIND("@",C2)),FIND(".",RIGHT(C2,LEN(C2)-FIND("@",C2)))-1)`

### Q7. How can you find the full name of an agent given their Agent ID?
**Answer:** `=INDEX('IT Agents'!$B:$B,MATCH(D2,'IT Agents'!$A:$A,0))` — implemented as the "Agent Name" column (K) in `Cleaned Data`. Example: Agent ID 4 → "Barraza Alberto".
**Approach:** `INDEX`/`MATCH` lookup applied to all 97,498 ticket rows.

### Q8. What is the count of each issue type?
**Answer:** IT Request: 73,220 (75.1%) · IT Error: 24,278 (24.9%).
**Insight:** Three out of four tickets are routine requests rather than error reports — most volume is provisioning/self-service work, not break-fix work.

### Q9. What is the average resolution time for tickets?
**Answer:** **4.55 days** overall — but this hides large variation by category: Hardware 7.63 days, System 6.62 days, Software 5.24 days, Login Access just 0.31 days.
**Formula:** `=AVERAGE('Cleaned Data'!I2:I97499)` for the overall figure; category breakdown via pivot table (Request Category × Average Resolution Time), column chart.

### Q10. How has the volume of tickets changed over time?
**Answer:** Ticket volume grew from **13,051 (2016)** to **29,088 (2020)** — a **122.9% increase** over five years, with growth accelerating in 2019–2020. 2020 Q4 (7,684 tickets) was the single busiest quarter on record.
**Approach:** Pivot table — Year (rows) × Quarter (columns) × Count of ID Ticket (values) — line chart.

### Q11. What is the average age of the IT agents?
**Answer:** **35.4 years** (workbook figure), with agents fairly evenly split across bands: 29–33 (12 agents), 34–38 (7), 39–43 (10), 44–48 (13), 49–53 (6), 54–58 (2).
**Formula:** `=AVERAGE('Cleaned Data'!$L:$L)`

### Q12. Is there a correlation between the severity of issues and the resolution time?
**Answer:** Correlation coefficient = **–0.0405** — essentially no linear relationship between severity and resolution time. "Urgent" tickets actually resolve fastest (2.00 days) while "Normal" tickets take longest (4.66 days) — severity tagging is not currently driving triage priority.
**Approach:** Severity mapped to a numeric "Severity Rank" (Unclassified=0, Minor=1, Normal=2, Major=3, Urgent=4) via `SWITCH`, then `=CORREL('Cleaned Data'!M2:M97499,'Cleaned Data'!I2:I97499)`.

### Q13. How many categorical columns are there in the data?
**Answer:** **13 categorical columns, 3 continuous/numeric columns** (out of 16 total across `Tickets` and `IT Agents`).
**Approach:** Categorical data describes a group/identity rather than a measurable quantity; continuous data is measurable and arithmetic (sum/average) is meaningful on it. ID-type numeric fields (Employee ID, Agent ID, Year) are treated as categorical since they are labels, not quantities.

---

## Part B — Subjective (Strategic) Questions

### Q1. If there is an investment, should it be used to hire more agents, improve training, or upgrade ticket-management software?
**Insight:** Satisfaction shows almost no sensitivity to resolution speed — even Hardware, the slowest category (7.63 days), scores the same ~4.10 satisfaction as instant-resolving Login Access. The same 50 agents absorbed a 123% ticket-volume increase (2016–2020) without resolution time degrading.
**Recommendation:** Prioritize a ticket-management software/diagnostic-tooling upgrade targeted at Hardware and System (the slowest, highest-effort categories). Since satisfaction isn't resolution-time-driven, training should focus on communication/service quality rather than raw speed. Hold agent headcount steady and reassess after the tooling upgrade.

### Q2. Which agents need additional training based on their performance metrics?
**Insight:** Most of the bottom 10 agents by satisfaction (Alberto Trejo, Nurio Zepeda, Elena Velez, Lorena Leon) also show above-average resolution times (5.2–5.5 days vs. the 4.55-day team average) — a speed-related training gap. Alfonso Barraza is the exception: his resolution time (5.00 days) is close to average, yet his satisfaction (3.04) is the lowest on the team — a non-speed issue.
**Recommendation:** Flag an agent for additional training if they consistently show higher-than-average resolution time **and** lower-than-average satisfaction.

### Q3. Do certain categories of requests have longer resolution times?
**Insight:** Yes — Hardware (7.63 days) and System (6.62 days) take far longer than Software (5.24 days) and especially Login Access (0.31 days). Within every category, "Unassigned"/"Low" priority tickets consistently take *longer* than "High" priority ones (e.g. Hardware: 9.84 days Unassigned vs. 5.48 days High) — confirming priority tagging, when applied, does drive faster handling.
**Recommendation:** Hardware and System's long resolution times likely reflect complex troubleshooting, need for specialized expertise, or process bottlenecks — worth a focused review.

### Q4. How effective are the current software tools in managing IT tickets?
**Insight:** Resolution time stayed essentially flat (4.52–4.59 days) across all five years while ticket volume grew 123% (13,051 → 29,088) with the **same 50-agent headcount**, and satisfaction actually improved (3.98 → 4.16) over the same period.
**Recommendation:** Current tools/process are effectively absorbing the growing ticket load — no urgent tooling replacement is justified on effectiveness grounds. However, flat resolution time under rising volume suggests the team is nearing capacity; monitor closely and revisit if growth continues at the same rate.

### Q5. How has IT support team performance changed over time (monthly/quarterly)?
**Insight:** Resolution time oscillates in a fairly narrow band (4.4–4.8 days) with normal quarter-to-quarter noise but no sustained upward/downward trend; satisfaction shows a gentle upward drift across the full period. No individual month or quarter stands out as an operational crisis point.
**Recommendation:** No corrective action needed for quarter-to-quarter volatility — it reflects normal variation. Continue monitoring to catch any future sustained trend early.

### Q6. If we invest more in tech, will it improve resolution times and satisfaction?
**Insight:** On the current trajectory (via `FORECAST.LINEAR`), resolution time is projected to stay essentially flat (~4.57 days) in both 2021 and 2022, while satisfaction keeps drifting upward on its own (~4.21 in 2021, ~4.25 in 2022) — satisfaction improvement does not require new tech spend, but resolution-time improvement will not happen organically.
**Recommendation:** If the goal is specifically to cut resolution time, tech investment is justified — the trend alone won't deliver it. If the goal is satisfaction, the current trajectory is already improving without further spend. Prioritize investment toward resolution-time reduction (e.g., automation for Hardware/System diagnostics).

### Q7. What are the key performance metrics for IT agents — do any need to be let go?
**Insight:** Key KPIs are Tickets Handled, Avg Resolution Time, and Avg Satisfaction. The 5 flagged agents (Alfonso Barraza 3.04, Alberto Trejo 3.59, Sandra Lujan 3.60, Nurio Zepeda 3.61, Elena Velez 3.62) all still handle 1,906–2,021 tickets, close to the 1,950 team average — they're underperforming on quality/speed, not on volume.
**Recommendation:** No firings are warranted — flagged agents are pulling a normal ticket load, making them coachable rather than replaceable. Put them through a structured training cycle and re-evaluate in 90 days before any staffing action.

### Q8. How do agent demographics impact satisfaction and ticket outcomes?
**Insight:** The dataset has no demographic fields for ticket-raising employees, only agent birth date — used Age Band (29–33, 34–38, 39–43, 44–48, 49–53, 54–58) as a proxy for experience. The **34–38 age band stands out negatively**: longest average resolution time (4.68 days) and lowest satisfaction (3.96).
**Recommendation:** Rebalance ticket routing to reduce load concentration on the 34–38 band, and investigate whether this group needs additional support tooling/staffing rather than assuming a skills gap — their *volume*, not competence, looks like the primary driver.

### Q9. What are the trends in ticket volume and satisfaction — any peak/stable periods?
**Insight:** Ticket volume is essentially flat across all seven days (13,778–14,080, under 2.2% spread) — Tuesday/Wednesday marginally busiest, Monday marginally quietest, but no meaningful weekday-vs-weekend pattern. This points to a 24/7, always-on support operation. Satisfaction is similarly stable (4.08–4.12) every day.
**Recommendation:** Since demand is stable rather than peaked, staffing/shift scheduling can be flat across the week — the operational priority is managing the steady long-term volume growth, not day-of-week peaks (since none exist).

### Q10. What metrics should be in the final dashboard for a comprehensive management view?
**Insight:** The dashboard leads with 4 headline KPIs (Total Tickets, Avg Resolution Time, Avg Satisfaction, Avg Tickets per Agent), followed by the six visuals most requested across the objective/subjective questions, plus Year/Severity/Category/Priority slicers connected to every chart and KPI tile.
**Recommendation:** All slicers are built and connected, so management can isolate a specific year or urgency level across a multi-week review without rebuilding the report each time.

---

## Additional dashboard-only findings (from the 9-visual stakeholder dashboard)

- **Seasonality:** Volume is fairly stable year-round (7,242–8,495/month), but Aug, Sep, Oct and Dec consistently exceed 8,200 tickets — 12–17% above the January low.
- **Severity tagging is nearly useless today:** 90.9% of tickets (88,656) are "Normal" severity; Major + Urgent combined are just 6.4%. 356 tickets remain "Unclassified."
- **Priority tagging has a large gap:** 36.5% High, 30.2% Unassigned — nearly a third of tickets have no priority at all.
- **Resolution-time distribution:** 25,071 tickets (>25% of volume) resolve same-day (Day 0); a secondary peak occurs at Days 5–7 (23,173 tickets), reflecting standard weekly SLA cycles and escalations.
- **Satisfaction by age band is non-linear:** the 54–58 band scores highest (4.39) while 34–38 scores lowest (3.96) — an 11% gap.

See `presentation/RupeshKumar_IT_Ticket_Analysis.pptx` for the full slide-by-slide version of these findings, including the complete 7-item strategic action plan.

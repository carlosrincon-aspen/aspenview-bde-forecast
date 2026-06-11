# AspenView — Rolling 12-Month Sales & Readiness Forecast
## User Guide

A live, browser-based forecasting platform that replaces the manual Excel workbook. It connects to Zoho CRM, lets you layer manual forecasting inputs on top of the CRM data, and keeps everyone (manager + team) looking at the same numbers in real time.

---

## 1. What this platform does

| Before (Excel) | Now (Platform) |
|---|---|
| Deals copied by hand from Zoho | Deals pulled automatically from Zoho |
| Each person had their own file | Everyone shares one live view (auto-synced) |
| Margin not tracked | Margin built in, feeds cash-flow |
| Static monthly snapshot | Rolling 3-month forecast that moves with one click |
| No reliability measure | Forecast-accuracy bands by horizon |

**Two kinds of data live together:**
- **From Zoho (automatic):** deal name, account/client, stage, owner (BDE), amount, probability, close date — refreshed every time you Sync.
- **Manual (you enter):** monthly headcount per deal, MRR Aureum, win-rate adjustments, margin overrides, the **Forecast** and **Super-Deal** flags, and the Super-Deal qualitative assessment.

The manual inputs are what turn raw CRM data into a real forecast.

---

## 2. Getting in

1. Open the platform URL in Chrome or Edge.
2. Click **Sign in with Google**.
3. Choose your **@aspenview.com** account. (Only @aspenview.com accounts are allowed.)
4. You land on the **Overview** tab.

**The header (top-right) shows three things:**
- **Sync · Live** (green) → your data is connected and shared. If it says *Saving…* it's pushing a change; *Live (updated by …)* means a teammate just made a change you received.
- **Zoho · Connected / Disconnected** → click it to connect or disconnect Zoho.
- **Your name** and **Log Out**.

> Everything you edit is shared. If the manager changes a number, you'll see it appear within seconds, and vice-versa.

---

## 3. Connecting Zoho (one-time per person, per session)

The Zoho token lasts about 1 hour, so you may reconnect occasionally.

1. In the header, click the **Zoho · Disconnected** pill.
2. You're sent to Zoho. Click **Accept** to authorize read access to Deals and Accounts.
3. You return to the platform; the pill turns green: **Zoho · Connected**.

### Pulling the deals
1. Go to the **Aureum Demand Forecast** tab.
2. Click **↻ Sync from Zoho** (top-right).
3. A summary appears: how many deals were pulled, how many matched the allowed stages, how many were skipped, and how many ghost/manual deals are preserved.
4. Click **Accept**. The deal list refreshes with the latest from Zoho.

**Which deals come in:** only deals in these stages (everything except Closed Lost):
`SQL - Solution Discovery · Stage 2 (WTW) · Commercial Design · Recruitment (Path 2.1) · P.S (Path 2.2) · Pricing · Paper Process / Contracting · Closed Won`

**What's preserved on re-sync:** your monthly headcount, MRR Aureum, win-rate edits, and the Forecast/Super flags stay attached to each deal. Only the Zoho fields (stage, amount, owner, etc.) get refreshed.

---

## 4. The tabs, one by one

### 4.1 Overview
Your executive snapshot.
- **Top KPI cards:** total deals, forecasted headcount (3 months), adjusted revenue (3 months), margin $ (3 months).
- **Anchor month picker (top-right):** sets the starting month of the rolling 3-month window. Move it forward by one each new month (see §6).
- **Rolling 3-Month Window table:** headcount, projected revenue, adjusted revenue, and margin $ for the anchor month and the two months after.
- **Forecast Reliability by Horizon:** the accuracy bands (M0 / M+1 / M+2). Until history builds up, these show the agreed defaults (80% ±5 / 75% ±10 / 30% ±20).

### 4.2 Aureum Demand Forecast — the master list
This is where everything starts. Every deal in Zoho (in the allowed stages) is one row, **grouped by account** so all deals of a client sit together.

**Columns explained:**
| Column | Source | Editable? |
|---|---|---|
| **Fcst** (checkbox) | Manual | ✅ Check to include this deal in the **3 mo. revenue** tab |
| **Super** (checkbox) | Manual | ✅ Check to flag it as a Super-Deal |
| **BDE** | Zoho owner (or inherited) | Read-only. `≈` = inferred from another deal in the same account; `⚠ none` = no owner anywhere in the account |
| **Status / Client / Deal / Stage / Loc / 1st Bill.** | Zoho | Read-only. Click the **deal name ↗** to open it directly in Zoho CRM |
| **Monthly columns (Jun 26 … Feb 27)** | Manual | ✅ Type the headcount you expect to be billable that month |
| **MRR Zoho ($)** | Zoho `Amount` | Read-only. Green background = it has a value and is being used in the math |
| **MRR Aureum ($)** | Manual | ✅ Your own MRR number. Used **only when MRR Zoho is empty/zero** |
| **Win %** | Zoho probability / manual | ✅ Adjust the win probability used in the forecast |
| **Margin %** | Margin Rules / manual | ✅ Override the margin for this specific deal |

**Search & filters (top):** type in the search box to filter by deal, client, or BDE. Use the **BDE / Stage / Status** dropdowns to multi-select (pick two BDEs at once, etc.). A number badge shows how many you've selected; "✕ Clear" resets a filter.

### 4.3 3 mo. revenue — the rolling forecast
Shows **only the deals you checked "Fcst"** in the Aureum tab. For each, across the 3-month window:
- **HC** (headcount that month)
- **Proj. Rev** = HC × MRR per head
- **Adj. Rev** = Proj. Rev × Win %
- **Margin $** = Adj. Rev × Margin %

The bottom totals and the three summary cards (3-mo Projected, Adjusted, Margin) roll everything up. Filter by BDE / Stage / Status, and move the **Anchor month** to slide the window.

> This is the number the company sees. It only contains deals you deliberately flagged — nothing leaks in by accident.

### 4.4 Forecast Accuracy
Measures how reliable the forecast has been — **not** a way to grade anyone.
1. The platform automatically snapshots the rolling forecast the first time it's opened each month.
2. When a month closes, click **Enter Actuals** and record the real headcount, adjusted revenue, and margin.
3. The bands recompute: for each horizon (current month, +1, +2) it compares past snapshots against actuals and shows **mean accuracy ± standard deviation**.
4. **Force Snapshot Now** lets you take one on demand.

### 4.5 Super-Deals
The Aureum-tier program assessment — mirrors the Excel Super-Deals tab. Each program (e.g. MDP-Zilliant, CGI, MGM) is one row with editable dropdowns:
- **Deal Confidence, Start Readiness, Readiness Gap, Speed Sensitivity, Ramp Duration, First Phase Timing, Phase Risk Concentration, Program Horizon, Deployment Map.**
- Color-coded dropdowns (green/amber/red) read at a glance.
- **Peak HC / Phase 1 / Activation / Notes:** edit with the ✎ button. Hover the ⓘ icon for program notes.
- **+ Add Program** to create a new one.
- Below, a **deal-level rollup** sums the financials of every deal you flagged "Super" in the Aureum tab.

### 4.6 Fulfillment View
Headcount by deal-type, split into Active Roles and Super-Deal Roles — for delivery/recruiting prioritization. Filter by BDE and search.

### 4.7 MRR Forecast 2026
Adjusted revenue by client across all 12 months. Read it by quarter by reading the relevant month columns together (e.g. Jan–Mar = Q1). Search by client.

### 4.8 Active Customers — Actuals
Only customers currently producing revenue (Active / Expanding / Aureum). Shows current headcount, MRR per head, and monthly $. Filter by BDE and status.

### 4.9 Margin Rules
The margin assumptions that feed cash-flow. A table of **Deal Type/Stage → default margin %**, fully editable. Margin for any deal resolves in this order:
1. **Per-deal override** (the Margin % you typed on the deal) →
2. **Super-Deal rule** (if flagged Super) →
3. **Stage rule** (matches the deal's stage) →
4. **Default** fallback.

Review these with Finance quarterly to keep cash-flow assumptions current.

### 4.10 Settings
- **Zoho integration:** status, field mapping, connect/sync/disconnect.
- **Data management:** **Export all data (JSON)** for backup, **Import** to restore, **Reset all data** to start fresh from the seed.
- **Daily workflow** reminder.

---

## 5. How the forecast actually moves (the data flow)

```
ZOHO  ──(Sync)──►  Aureum Demand Forecast
                         │
        you enter:  Monthly HC  +  MRR Aureum (if Zoho blank)  +  Win %  +  Margin %
        you check:  Forecast ☑   Super ☑
                         │
                         ▼
   Forecast ☑ deals  ──►  3 mo. revenue   (HC × MRR/head × Win% × Margin%)
   Super ☑ deals     ──►  Super-Deals rollup
                         │
                         ▼
        Overview KPIs + Forecast Accuracy bands + MRR Forecast
```

**The levers you pull:**
- **Monthly HC** → drives projected revenue and the rolling forecast.
- **MRR Zoho vs MRR Aureum** → the effective price per deal (Zoho wins if it has a value; otherwise your Aureum number).
- **Win %** → discounts the revenue by probability.
- **Margin %** → turns revenue into the margin number Finance needs.
- **Anchor month** → slides the 3-month window forward.
- **Forecast / Super checkboxes** → decide what shows up in the rolling forecast and the super-deal lane.

---

## 6. Recommended rhythm

| When | Do this | Where |
|---|---|---|
| **Each morning** | Click **↻ Sync from Zoho** to pull overnight changes | Aureum Demand Forecast |
| **Daily** | Update monthly HC, MRR Aureum, win % as deals progress; check **Fcst** on deals to forecast, **Super** on Aureum-tier | Aureum Demand Forecast |
| **Weekly** | Review the rolling 3-month numbers | 3 mo. revenue |
| **Monthly (1st)** | Move the **Anchor month** forward by one — the whole window rolls | Anchor picker (any tab) |
| **Monthly (after a month closes)** | **Enter Actuals** so the accuracy bands learn | Forecast Accuracy |
| **Quarterly** | Review **Margin Rules** and the Super-Deal program assessments with Finance | Margin Rules / Super-Deals |

---

## 7. Tips & troubleshooting

| Symptom | Fix |
|---|---|
| Revenue shows $0 | The deal has no MRR Zoho and no MRR Aureum. Enter an MRR Aureum value, or make sure the Amount is set in Zoho. |
| A deal isn't in 3 mo. revenue | Check the **Fcst** box on it in Aureum, and make sure it has headcount in the anchor window. |
| Sign-in says "not authorized" | You used a non-@aspenview.com account. Switch accounts. |
| Zoho sync fails (401) | The 1-hour token expired. Click the Zoho pill → reconnect. |
| Sync pulls deals but stage looks wrong | The stage name in Zoho doesn't match the allowed list — flag it so it can be added. |
| A deal has `⚠ none` for BDE | No deal in that whole account has an owner in Zoho. Assign an owner in Zoho, then re-sync. |
| Changes not showing on another computer | Confirm both show **Sync · Live**. Hard-refresh (Ctrl+Shift+R). |

---

## 8. Backup

Browser storage holds your data locally and Firestore keeps the shared copy. Still, export a backup periodically: **Settings → ⬇ Export all data (JSON)**. To restore: **Settings → ⬆ Import data (JSON)**.

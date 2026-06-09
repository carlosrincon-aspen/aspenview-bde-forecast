# AspenView — Rolling 12-Month Sales & Readiness Forecast
## Setup Guide

A single-file web dashboard that replaces the existing rolling 12-month Excel workbook with a Zoho-connected, browser-based forecasting tool for the BDE deal tracker.

**Estimated setup time:** 25–40 minutes · No coding required after the one-time OAuth setup.

---

## What you get

| Feature | Where it lives | Notes |
|---|---|---|
| Rolling 3-month revenue forecast | `3 mo. revenue` tab | Single **Anchor Month** picker advances the window each month |
| Margin % per deal | `Margin Rules` tab + per-deal override | Rule-based defaults; overrides win when set |
| Forecast accuracy bands (M0 / M+1 / M+2) | `Forecast Accuracy` tab | Auto-snapshots each month; bands recompute from history |
| Super-Deal lane | `Super-Deals` tab | Auto-classified at ≥ 50 total HC |
| Fulfillment view | `Fulfillment View` tab | HC by deal-type for recruiting prioritization |
| MRR view | `MRR Forecast 2026` tab | Adj. revenue grouped by client across 12 months |
| Active customers | `Active Customers — Actuals` tab | Filter view of Active / Expanding / Aureum |
| Zoho CRM sync | `Settings` tab | Pulls Deals via OAuth; one-click refresh |
| Google Sign-In | Login screen | Only `@aspenview.com` accounts allowed |

---

## Step 1 — Google OAuth setup (one-time, ~15 minutes)

This restricts login to `@aspenview.com` accounts.

### 1.1 Create the Google OAuth client

1. Go to https://console.cloud.google.com and sign in with your AspenView account.
2. Top dropdown → **New Project** → name it `AspenView BDE Forecast` → **Create**.
3. Sidebar → **APIs & Services → OAuth consent screen** → choose **Internal** → **Create**.
   - App name: `BDE Rolling Forecast`
   - Support email: your AspenView email
   - Save and continue through the remaining screens.
4. Sidebar → **APIs & Services → Credentials** → **+ Create Credentials → OAuth client ID**.
   - Application type: **Web application**
   - Name: `BDE Forecast Dashboard`
   - **Authorized JavaScript origins**: add your hosting URL (see Step 3) — for local testing also add `http://localhost:8000`
   - **Create**
5. Copy the **Client ID** (looks like `1234567890-abc….apps.googleusercontent.com`).

### 1.2 Paste it into the dashboard

Open `index.html` in a text editor and find:

```
data-client_id="REPLACE_WITH_GOOGLE_CLIENT_ID"
```

Replace with your Client ID. Save.

---

## Step 2 — Zoho CRM OAuth setup (one-time, ~10 minutes)

This wires the **↻ Sync from Zoho** button so deals come straight from Zoho instead of being entered by hand.

### 2.1 Create the Zoho OAuth app

1. Go to https://api-console.zoho.com and sign in with the AspenView Zoho admin account.
2. **Add Client** → **Self Client** is fine for testing, but for production use **Server-based Applications** or **JavaScript** (for the in-browser flow this dashboard uses).
3. Fill in:
   - Client Name: `AspenView BDE Forecast`
   - Homepage URL: your hosting URL (from Step 3)
   - **Authorized Redirect URIs**: same URL — the dashboard reads the token from the URL fragment
4. **Create** → copy the **Client ID**.

### 2.2 Paste it into the dashboard

In `index.html` find:

```
var ZOHO_CLIENT_ID  = 'REPLACE_WITH_ZOHO_CLIENT_ID';
```

Replace with the Zoho Client ID. Save.

### 2.3 Field mapping (already wired)

The sync expects these standard Zoho Deal fields:

| Zoho field | Dashboard field |
|---|---|
| `Deal_Name` | Deal Name |
| `Account_Name` | Client |
| `Stage` | Stage (normalized to SQL / Commercial Design / Recruitment / Pricing / Paper / Closed Won) |
| `Amount` | Pricing |
| `Probability` | Adjusted Win Rate |
| `Closing_Date` | First Billable / Activation Month |
| `Description` | Commercial Description (used as margin signal) |

If your Zoho instance uses custom field names, update the field list inside `syncZohoDeals()` in `index.html`.

---

## Step 3 — Host on GitHub Pages (one-time, ~10 minutes)

GitHub Pages gives you a free permanent URL that works with Google + Zoho OAuth.

1. https://github.com → **New repository** → name it `aspenview-bde-forecast` → **Public** → check "Add a README" → **Create**.
2. In the repo → **Add file → Upload files** → drag `index.html` → **Commit**.
3. **Settings → Pages** → Source: **Deploy from branch** → Branch: `main` / root → **Save**.
4. After 1–2 minutes your URL will be `https://<your-github-username>.github.io/aspenview-bde-forecast/`.
5. Go back to the Google OAuth client (Step 1.1, #4) and add this URL under **Authorized JavaScript origins**. Repeat for the Zoho OAuth app's redirect URI.

---

## Step 4 — Daily / Weekly / Monthly workflow

| Cadence | Action | Where |
|---|---|---|
| Every morning | Click **↻ Sync from Zoho** to pull overnight deal changes | `Aureum Demand Forecast` tab |
| Daily | Adjust monthly HC and pricing as deals progress; cells auto-save | `Aureum Demand Forecast` tab |
| Weekly | Sanity-check the 3-month window | `3 mo. revenue` tab |
| **Monthly (on the 1st)** | Move the **Anchor Month** forward by 1 — this rolls the window | Top-right Anchor picker on any tab |
| Monthly (after month closes) | **Enter Actuals** for the closed month so the accuracy bands learn | `Forecast Accuracy` tab |
| Quarterly | Review **Margin Rules** with Finance | `Margin Rules` tab |

---

## Forecast accuracy — how the bands work

Every month, on the first session of a new month, the dashboard takes a **snapshot** of the current rolling 3-month forecast (M0 / M+1 / M+2). When a month closes and actuals are entered, the dashboard compares each historical snapshot's prediction for that month against the actual, and aggregates per-horizon as:

> **Mean accuracy ± Standard deviation**

So after a few months of history, the **Overview** and **Forecast Accuracy** tabs show something like:

> **M0:** 82% ± 4 — n = 6 snapshots
> **M+1:** 74% ± 11 — n = 5 snapshots
> **M+2:** 33% ± 18 — n = 4 snapshots

Until history accumulates, the bands fall back to the targets defined for this project:
- M0 = 80% ± 5
- M+1 = 75% ± 10
- M+2 = 30% ± 20

**Not a performance metric.** The point is to hand Finance a credible *range* for cash-flow planning, not to grade the forecaster.

---

## Backup & restore

- **Export**: Settings → ⬇ Export all data (JSON) — saves deals, margin rules, snapshots, actuals.
- **Import**: Settings → ⬆ Import data (JSON) — restores from a backup.
- **Reset**: Settings → ⚠ Reset all data — wipes everything back to the original 78 seed deals.

Browser storage is local — back up weekly until Zoho sync is the primary source of truth.

---

## Troubleshooting

| Symptom | Fix |
|---|---|
| Google sign-in shows "unauthorized" | The OAuth client ID is missing, wrong, or not configured for the current URL. Re-check Step 1.2 and the Authorized JavaScript origins. |
| Zoho sync says "Connect Zoho first" | The OAuth token expired (1 hour TTL). Click the **Zoho · Disconnected** pill in the header to reconnect. |
| Zoho sync pulls deals but Stage is always "SQL" | Your Zoho Stage values don't match the normalization map. Edit `normalizeStage()` in `index.html` to match your stage names. |
| Margin % on a deal looks wrong | Margin Rules tab → check the rule for that deal's stage. Or override the % directly on the Aureum Demand Forecast tab. |
| Accuracy bands stay at the default 80/75/30% | You don't have actuals recorded yet. Forecast Accuracy tab → **Enter Actuals** for at least one closed month. |


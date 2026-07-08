# AspenView BDE Rolling 12-Month Sales & Readiness Forecast — Project Context

> **Use this file as the briefing for every new conversation.** Paste it at the start of a chat so Claude has full context. The Changelog at the bottom is the running registry of what we've built — append a new entry each session.

---

## 1. What this project is

A live, browser-based forecasting platform built for AspenView Technology Partners. Replaces a manual Excel workbook with a Zoho-CRM-integrated, multi-user dashboard for sales pipeline forecasting, accuracy tracking, and long-term financial projection. Designed primarily for Don Kelly (VP Sales, recovering from a stroke) and Michael Durham (Carlos's manager), with eventual visibility to Eric (CEO) and Finance.

This is Track 1 of a broader project (Track 2 = BDE report-card dashboard, not yet started).

---

## 2. Stakeholders

| Person | Role | Email | Relationship to project |
|---|---|---|---|
| **Carlos Rincon** | BDR Manager at AspenView | carlos.rincon@aspenview.com | Builder. Reports to Michael. Conversation in Spanish, deliverables in English. |
| **Michael Durham** | Carlos's manager | michael.durham@aspenview.com | Primary product owner. Reviews and approves before showing Don. |
| **Don Kelly** | VP Sales | don.kelly@aspenview.com | Primary end user. Post-stroke — UX must be intuitive, minimal clicks, plain language. Age 67. |
| **Juliana Castaneda** | Admin | juliana.castaneda@aspenview.com | Has admin access. |
| **Eric** | CEO | — | Sees Pipeline tab + Long-term Growth output. Investor-facing audience. |

These four emails — carlos, michael, don, juliana — are the **only** people with manager/admin role. Anyone else with an @aspenview.com email signs in as a BDE (read/edit but no admin access).

---

## 3. Where things live

| Resource | URL / Path |
|---|---|
| **Live URL** | https://carlosrincon-aspen.github.io/aspenview-bde-forecast/ |
| **Repo** | https://github.com/carlosrincon-aspen/aspenview-bde-forecast |
| **Local path** | `C:\Users\Usuario\Documents\Claude\Projects\AspenView-BDE-Forecast\index.html` |
| **Deploy** | `git commit + push` → GitHub Pages rebuilds in ~90s |
| **Custom domain (planned)** | `forecast.aspenview.com` — needs DNS access (not set up yet) |
| **EN guide** | `guide-en.html` (linked from header 📖 Guide button) |
| **ES guide** | `guia-es.html` |

---

## 4. Tech stack

- **Single-file HTML SPA** (~4000+ lines in `index.html`) — no build step, vanilla JS, no framework
- **Firebase Auth** (Google popup, restricted to `@aspenview.com`) — project `aspenview-bde-forecast-154b4`
- **Firestore** — shared state at `dashboard/state` (deals, snapshots, actuals, admin list, activity log, etc.)
- **Zoho CRM v3 REST API** — OAuth client `1000.IDIKQZQT1WD4JUK97IYAFVWKEFSAPC`. Currently READ scope; WRITE scope needed for Push-to-Zoho (pending admin enable)
- **Chart.js v4** (CDN) — for trend line charts
- **AspenView brand**: navy `#0A1928`, gold `#E8B84B`, Roboto font

### Zoho field mapping
| Zoho API name | Used as |
|---|---|
| `Deal_Name` | Deal name |
| `Account_Name` | Client |
| `Stage` | Stage (mapped to 5-phase model) |
| `Owner` | BDE |
| `Closing_Date` | First billable / closingDate |
| `Created_Time` | Deal age segmentation |
| `MRR` | MRR Zoho (NOT `Amount` — that field is often blank) |
| `Probability` | Win % |
| `Headcounts` | Total HC on the deal (NOT `Total_Headcount` — wrong field name) |
| `Target_Margin_Band` | Margin from Zoho |
| `Account_Status` | Status pill (joined from Accounts module) |
| `Description` | Commercial Description |

---

## 5. Stakeholder rules (always follow)

These were established through corrections during the build. Always honor them:

1. **No personal names in user-facing copy** — code comments fine, but UI text must not say "Don's", "Michael's", "Eric's" anywhere. People leave, the platform stays.
2. **Conversation in Spanish, deliverables in English** — Carlos chats in Spanish, but all files, UI, guides, docs go in English.
3. **No Carlos / no BDR references in deliverables** — this is Don/Michael's project; Carlos builds it but his name doesn't appear in the product.
4. **Plain language but not infantilized** — keep technical labels (M+0, M+1, M+2) but avoid jargon that requires context (no "rubber stamp", no "investor view" subtitles). Don and Michael are 67 — minimize clicks, but they're sophisticated.
5. **Zoho is the system of record** — never create deals locally, always redirect to Zoho. Push changes back via PATCH when WRITE scope is enabled.
6. **Build & Invoice, NOT Close** — Michael's framing for HC by month. Always reinforce: HC = when people start billing, not when the deal closes.

---

## 6. The 5-phase model (Michael's framing)

Every Zoho deal maps to exactly one phase:

| Phase | Zoho stages | Default forecast? | Color |
|---|---|---|---|
| **Phase 1 — SQL** | SQL - Solution Discovery, Stage 2 (WTW) | OFF | gray |
| **Phase 2 — Recruitment** | Recruitment (Path 2.1), P.S (Path 2.2), Commercial Design | ON | blue |
| **Phase 3 — Pricing** | Pricing | ON | gold |
| **Phase 4 — Legal Contracting** | Paper Process / Contracting | ON | green |
| **Phase 5 — Closed Won** | Closed Won | OFF (captured in cash flow) | aureum |

Phase filter pills at the top of Aureum Demand Forecast let users toggle which phases enter the forecast.

---

## 7. The 12 modules (current platform structure)

Topnav order. Tabs in **bold** are most-used.

| # | Module | Purpose |
|---|---|---|
| 1 | **Overview** | Daily dashboard. KPI cards + rolling 3-month window + accuracy bands + Lock button. |
| 2 | **Pipeline** | Investor-grade view. All deals by phase + size bands (Super/Large/Mid/Small) + deal age segmentation. |
| 3 | **Aureum Demand Forecast** | Master worksheet. Every deal as a row, editable. Phase filter at top. |
| 4 | **3 mo. revenue** | Deep-dive on next 3 months. Lock button. Raw vs Adjusted accuracy haircut toggle. |
| 5 | **12-Month Plan** | Mid-term (months 4-12). Per-deal HC ramp by month. |
| 6 | **Long-term Growth** | Months 13-24. Pick a monthly growth %, model projects forward. |
| 7 | **Playground** | Zoho-vs-Manual diff with per-deal Approve + Push-to-Zoho. |
| 8 | **Forecast Accuracy** | Reliability bands + Accuracy by BDE + Backfill + embedded Forecast Trend section. |
| 9 | **Super-Deals** | Qualitative assessment of 100+ HC programs across 9 dimensions. |
| 10 | **Active Customers — Actuals** | Customers billing today (Active/Expanding/Aureum/Closed Won). |
| 11 | **Cleanup** | Reconcile not-in-Zoho ghosts with Zoho candidates. Merge or Kill. |
| 12 | **Settings** | Stage Rules (Win%/Margin% defaults) + Zoho integration + Export/Import + Replay tour. |

**Plus**: ★ Admin Panel (header button, admins only) — Team / Activity / Errors sub-tabs.

---

## 8. Key concepts

### Lock & sign-off (Phase B feature)
Don commits a 3-month forecast by clicking **🔒 Lock {month} forecast**. The snapshot becomes immutable with `lockedBy` + `lockedAt`. Only locked snapshots count toward accuracy bands by default.

### Per-BDE accuracy
Each snapshot stores `bde` per deal. When actuals roll in for a month, the platform compares per-BDE forecasts against the aggregate actual (proportionally attributed to each BDE based on their forecast share). Per-BDE accuracy by horizon (M+0 / M+1 / M+2) appears in Forecast Accuracy → "Accuracy by BDE" table.

### Backfill historical accuracy
**📥 Backfill historical data** form in Forecast Accuracy. One row per (month, BDE) capturing what was forecast at start of that month, 1 month earlier, 2 months earlier, plus the actual. Used to seed accuracy bands without waiting 3+ months of natural cycles.

### Actuals
Entered manually via **Enter Actuals** when a month closes. Aggregate (whole-company) HC + Adj Revenue + Margin. Per-BDE attribution is proportional, not separately captured. Could be automated via QuickBooks/Stripe integration (Phase 3).

### Phase B+ — Pipeline tab
Investor-grade dashboard. KPI cards (Total Deals / HC / MRR / ARR) + Phase Breakdown table + Deal Breakdown by phase with size bands + Deal Age Segmentation (Healthy / Active / Slow / Critical).

### Phase C — Mid-term + Long-term
- **12-Month Plan**: months 4-12 with per-deal HC ramp
- **Long-term Growth**: months 13-24 with growth % dropdown (0.5% to 10%/month), aggregate only

### Forecast Trend (embedded in Forecast Accuracy)
Weekly auto-snapshots (Monday first-login) with 3 line charts: deals forecasted (count), total HC (3-mo window), total Adj. Revenue. Each chart shows solid Forecast line + dashed green Actual line (from Actuals + backfill). Click any point → modal with deal-level drill-down. Demo baseline of 6 synthetic points seeds the charts before real history accumulates.

### Audit log
Every user action logged to Firestore: HC edits, MRR/Win/Margin edits, Fcst/Super toggles, deal deletes, playground approves, anchor changes, sign-ins, Zoho syncs, Pushes to Zoho, admin grants. Capped at 200 events. Visible in Admin Panel → Activity.

### Onboarding tour
12-step walk-through, auto-triggers on first sign-in. Spotlights each tab (gold pulse) and shows a card below the topnav with an arrow pointing at the tab. **🎓 Take Tour** button in header to re-launch any time; **↻ Replay tour** card in Settings.

---

## 9. Open / pending items

### Blocking (depends on others)
- ⏳ **Zoho admin to grant `ZohoCRM.modules.Deals.WRITE`** to OAuth client `1000.IDIKQZQT1WD4JUK97IYAFVWKEFSAPC`. Until then, the Playground "Push approved to Zoho" button shows a clear error message. Carlos or Michael needs to coordinate.

### Setup
- ⏳ **Custom domain `forecast.aspenview.com`** — needs DNS access to aspenview.com (same admin who set up worldcup.aspenview.com). Add CNAME `forecast` → `carlosrincon-aspen.github.io`, then set in repo Settings → Pages.

### Phase 2 (post current scope)
- Audit-log capture for: Stage Rules edits, Super-Deals dropdown edits, Cleanup merge/kill, Actuals entry
- Per-BDE actuals input (today actuals are aggregate-only; per-BDE attribution is proportional)
- Track 2: **BDE report-card dashboard** mirroring the BDR Vantage tool (not yet started)
- Pricing-stage margin from Matt's Vantage pricing module (separate integration)

### Phase 3 (future)
- Billing/finance system integration for automatic Actuals (QuickBooks, Stripe — TBD which AspenView uses)
- Automate Long-term Growth baseline picker
- Mobile responsive layout polish

---

## 10. How to navigate the codebase

`index.html` is the entire app. Key sections by line range:

| Lines | Section |
|---|---|
| 1–190 | `<style>` block + Firebase/Chart.js script tags |
| ~280–390 | Constants: USERS, TABS, ALL_MONTHS, STAGES, PHASES |
| ~400–580 | Storage helpers (get/set per data key) + isAdmin / logActivity / timeAgo |
| ~580–730 | Onboarding tour: ONBOARDING_STEPS, ensureOnboardingOverlay, render/position/skip/end |
| ~730–820 | Firebase init + Firestore sync + Auth |
| ~820–900 | Login / logout |
| ~900–1100 | Zoho integration (connect, sync, patch, fields) |
| ~1100–1400 | Migrations + helpers (effectivePricing, pricePerHead, projectedRevenue, etc.) |
| ~1400–1500 | Snapshots: ensureMonthlySnapshot, lockForecast, computeAccuracyBands |
| ~1500–1550 | TopNav + renderTab routing |
| ~1550–1640 | renderOverview |
| ~1640–1900 | renderPipelineOverview |
| ~1900–2150 | renderAureumGrid |
| ~2160–2300 | render3MoRevenue |
| ~2310–2500 | renderMidtermForecast + renderLongtermGrowth |
| ~2520–2670 | renderForecastAccuracy + Backfill modal |
| ~2670–3180 | Forecast Trend section + charts + drill-down modal |
| ~3180–3320 | renderSuperDeals |
| ~3320–3380 | renderActiveCustomers |
| ~3380–3450 | renderMarginRules + stageRulesSectionHtml (now embedded in Settings) |
| ~3500–3700 | renderPlayground + pushApprovedToZoho |
| ~3700–3900 | renderAdminPanel |
| ~3900–4100 | renderCleanup + renderSettings |

### Working with this codebase
- **No build step**: edit `index.html`, `git push`, GitHub Pages rebuilds in ~90s
- **Always validate JS syntax** before pushing: `node -e "..."` extract-and-eval script
- **Firestore-synced keys**: see `FB_SYNCED_KEYS` near top. Adding new shared state? Add the key here.
- **Local-only keys**: migration flags (`seed_merged_v7`, `inexcel_flagged_v11`, etc.) and `onboarding_completed_v1` are NOT in `FB_SYNCED_KEYS`
- **Activity log**: any meaningful user action should call `logActivity(source, action, details)`

---

## 11. Recent visual / UX preferences

- Banners use gold left-border (`border-left: 4px solid var(--gold)`) with `#fff8e1` or `#fdf6e6` backgrounds for callouts
- KPI cards on Pipeline use navy gradient `linear-gradient(135deg,#0A1928,#1c3250)`
- Phase pills use existing colors: gray (Phase 1), blue (Phase 2), gold (Phase 3), green (Phase 4), aureum (Phase 5)
- Tour spotlight is gold pulse (1.6s) on `.topnav-btn.tour-spotlight`
- Tables: navy header, sticky in long lists, alternating row backgrounds `#f9fbfd` / white

---

## 12. Changelog (newest first)

Append a new entry per session. Format: `YYYY-MM-DD — session summary` followed by bullets of what changed.

### 2026-07-06 — Fix: conflict-guard false positive on login
The PM-10 conflict guard was too sensitive: the app's **automatic login-time writes** (daily backup `forecast_versions`, weekly `trend_snapshots`, `deal_count_history`) called `save()`→`fbSchedulePush()` and armed `_pendingLocalEdits=true`. So when a teammate (Don) saved, Carlos got a **false conflict banner right after opening the app** — even without editing — and Reload re-triggered it (housekeeping runs again → loop). Fix: `save()` now passes the key to `fbSchedulePush(k)`, which only arms the guard when `k` is a genuine data edit — a `NON_EDIT_KEYS` set (trend_snapshots, forecast_versions, deal_count_history, last_zoho_sync, error_log, activity_log, known_users) is excluded (still pushes, just doesn't count as an unsaved edit). Real deal/PMFF/margin edits still arm it correctly. JS validated; `version.txt`→`-conflict-guard-fp-fix`.
NOTE (2026-07-06): GitHub Pages `deploy` job hit transient "Deployment failed, try again later" + a stuck queued run after ~13 rapid deploys; fixed by a clean empty-commit redeploy. Lesson: batch changes, don't push in rapid succession.

### 2026-07-01 (PM-12) — Fix Zoho pagination >2000 (DISCRETE_PAGINATION_LIMIT_EXCEEDED)
Error Log showed repeated Zoho 400s: `DISCRETE_PAGINATION_LIMIT_EXCEEDED · limit 2000` on `/Accounts?...&per_page=200&page=11` (Don's syncs). Cause: Zoho only allows the first 2000 records via classic `&page=N`; AspenView has >2000 **Accounts**, so page 11 (~2200) is rejected. **Impact was benign:** deals still synced fine (<2000 deals, separate call); only the Account_Status "Status pill" join for accounts past 2000 didn't refresh (deals kept prior status — no manual data lost). Mostly log noise. **Fix:** `fetchAllZohoDeals` + `fetchAllZohoAccounts` now use **cursor pagination** — prefer `info.next_page_token` (works past 2000); if Zoho ever omits it, fall back to `&page` but stop cleanly at 2000 instead of erroring. Safety stop at 20k records. JS validated; `version.txt`→`-zoho-cursor-pagination`.

### 2026-07-01 (PM-11) — One-click Health Check (in-app self-test)
Carlos wanted a button he (admin/creator) can click from Horizon to verify everything we hardened. Built **`runSelfTest()` + `openSelfTestModal()`**, button **"🩺 Run health check"** in **Admin → Recovery & data**. Read-only, changes nothing. Stays honest by **introspecting the real deployed code** (`fn.toString()`) + live state, so if anyone ever removes a preserved field it FAILS. 10 checks: (1) Zoho Sync preserves every manual override (asserts each `p.<field>` in the live sync), (2) ghost→Zoho reconcile keeps overrides, (3) opening the app never mutates deals (autoApplySeedOnce has no setDeals), (4) Export covers all FB_SYNCED_KEYS, (5) Import symmetric + reversible, (6) daily/version backup captures every forecast field, (7) conflict guard wired, (8) all core actions callable (`typeof window[fn]`), (9) restore points exist, (10) live data snapshot. Modal shows PASS/NOTE/FAIL per check + overall verdict + Re-run. Verified all checks pass against current code. `version.txt`→`-health-check`.

### 2026-07-01 (PM-10) — Concurrency conflict guard (Risk 1 mitigation)
Closes the biggest silent-data-loss window without the full per-deal-write refactor. **Found while auditing:** the existing `onSnapshot` blindly applied a teammate's remote state over local (`FB_SYNCED_KEYS.forEach(save(k,data[k]))`) — so a teammate's save could **wipe your in-progress unsaved edits**. Fix:
- Track **`_pendingLocalEdits`** (set in `fbSchedulePush` on a genuine local edit; cleared in `fbPushNow` on save success).
- In `onSnapshot`, if a teammate's change arrives **while `_pendingLocalEdits` is true**: do NOT overwrite local, **cancel the pending push** (so it can't auto-overwrite theirs either), and show a red **conflict banner** — "⚠ {who} just saved while you have unsaved edits" with **↻ Reload (take theirs)** / **Keep mine (override)**. `_conflictActive` blocks auto-push until the user chooses (`conflictReload` = reload; `conflictKeepMine` = push local over theirs + log).
- When there are NO local edits, remote changes still apply live as before (keeps everyone fresh → kills the stale-load case).
- Remaining (unchanged): a truly simultaneous sub-second double-save can still last-write-win; full fix = per-deal Firestore writes. JS validated; `version.txt`→`-conflict-guard`.

### 2026-07-01 (PM-9) — Deals & Accounts table is collapsible
Carlos: click the title to minimize/expand — not always fixed open. `togglePhaseTable()` stores `aureum_phase_table_open` (local, NOT synced — per-browser view pref, survives reloads). **Default collapsed**; the collapsed header shows a caret (▸/▾) + a compact summary ("N deals · M accounts — click to expand") so it's still useful shut. Body (table + Recruitment-or-higher line) renders only when open. `version.txt`→`-phase-table-collapsible`.

### 2026-07-01 (PM-8) — Deals & Accounts table: no white-hover on TOTAL + respects Forecast-only
Two Carlos fixes on the new Aureum phase table:
- **TOTAL row no longer flashes white on hover** (recurring Carlos gripe). Root cause: global CSS `tr:hover td { background:#fafbfc }` (line 109) paints the cell over the row bg. Fix = give the TOTAL `<tr>` the existing **`pl-total`** class, which has `tr.pl-total:hover td { background:transparent }` (line 111) so the navy row bg shows through. Same pattern used for the Pipeline/Aureum total bars.
- **Table now respects the "Forecast only" toggle:** `phaseDealsAccountsTableHtml(deals, f.forecastOnly)` — when on, counts only forecast deals (inForecast, Closed Won excluded), same rule as the grid; header shows a "Forecast only" / "all deals" badge. Don can forecast several deals per account and each still adds MRR/HC separately, so % stays share-of-deals. JS validated; `version.txt`→`-aureum-phase-table-hover-fcst`.

### 2026-07-01 (PM-7) — Don's ask: Deals & Accounts by Phase table (on Aureum, NOT Pipeline)
Don: "how many accounts do we have at each stage vs deals? We have 64 deals at Recruitment or higher — but how many accounts? Deal count can be misleading." We didn't have account (unique-client) counts anywhere. **Carlos's call: put it on the Aureum tab, NOT Pipeline — Pipeline is the investor view of the whole pipeline and must stay untouched (no fcst mixing).** (First draft mistakenly edited Pipeline; fully reverted.)
- New **`phaseDealsAccountsTableHtml(deals)`** rendered on Aureum, right under the phase-filter pills (easy to read, above the grid): per phase → **# Deals · % of Deals · # Accounts (unique clients)** + a TOTAL row + a highlighted **"Recruitment or higher (Phase 2+): N deals across M accounts"** line (Don's exact example). Counts the full phased set (Closed Lost / unmapped excluded), independent of the phase-filter pills / forecast-only toggle so it's always the full picture. Accounts normalized by lowercased client name. JS validated; `version.txt`→`-aureum-deals-accounts`. Pipeline tab untouched.

### 2026-07-01 (PM-6) — Mike-call B + D + F (Overview quarter view, Summary→Financial Model, deal-count tracking)
Closed the rest of the Mike-call punch-list. JS validated. `version.txt`→`-overview-quarter-financialmodel`.
- **(D) Summary → "Financial Model".** Renamed the tab and **moved it to the FAR RIGHT** of the tab row (after Settings) — `TABS` reordered, id kept `summary` (no routing changes). Heading now "Financial Model — Forecast Summary Statistics"; subtitle states **all forecast numbers are locked (read-only); only the PMFF is editable** (already enforced by `canEditPmff` — PMFF is the only input; the rest are computed).
- **(B) Overview reworked (Michael's asks):**
  - **Deal counts now partition by billing-start month and reconcile:** Next 3 Months + Rest of {yr} + {yr+1} + {yr+2} (+ unscheduled) = total forecast. Added **2027 & 2028 KPI cards** (2028 shows 0) + a one-line reconciliation. (Old "active-in-window" counts double-counted; now each deal is bucketed by its first billing month.)
  - **Average vs Total made consistent/explicit:** lead row relabeled **"Average MRR / month"** (gross ÷ months) and the total row **"Total Projected MRR"** (sum over the period); card note spells out "Average MRR/month is a monthly average; every other row is a period total." Resolves Michael's "is 684 a total or an average?" FY26 totals reconcile (3-mo + remainder); the average legitimately differs (averaging over more months).
  - **Current Quarter view (replaces the rolling window):** the "Rolling 3-Month Window" card is now **"Current Quarter · Q{n} {yyyy}"** month-by-month (Jul/Aug/Sep) — the quarter we're actually IN, independent of the anchor (rolling lost visibility of the current month). "3-mo Total" → "Q{n} Total". Also added a **Current Quarter column** in front of the main summary calendar table. For the sales "win the quarter" tracking.
- **(F) Deal-count drift is now traceable:** new synced key `deal_count_history` (last 30), written on every Zoho sync via `recordDealCount()`; the Overview "Total Deals" card shows **"▲/▼ N since last sync"**. Answers Michael's "we keep losing deals" — a drop = deals went Closed Lost or were removed in Zoho (Pipeline tab already itemizes excluded deals).

### 2026-07-01 (PM-5) — A closed: Lockdown vs. Current view
Closes item A. New **`lockdownVsCurrentCardHtml()`** on the Forecast Accuracy tab (right under the accuracy bands): takes the anchor month's locked snapshot (else the most recent locked), rebuilds a current snapshot for the same month via `buildPerDealSnapshot`, and shows **Locked vs Current** for Deals · HC (3-mo) · Adjusted revenue (3-mo) · Contribution margin $ (3-mo) with a **Better/(Worse)** delta, plus a deal-level list of what was **ADDED / REMOVED / CHANGED** since the lock (HC + Adj deltas). Header labels the lockdown ("Locked {month} by X on …") — the per-month lockdown version Michael wanted. Empty state guides to lock in Aureum. Accuracy is still measured against the locked snapshot; this is the human-readable drift view. JS validated; `version.txt`→`-lockdown-vs-current`.

### 2026-07-01 (PM-4) — Lockdown model: Lock month → push to Zoho in ONE step (item A core)
Built Michael's forecast-lifecycle core. **⚠ Writes to the real Zoho CRM — must be tested live (Zoho auth can't run headless); test on ONE deal before a full push.** JS validated. Deployed, commit pending, `version.txt`→`-lockdown-push`.
- **New `lockAndPushForecast()`** — the single Aureum action bar button **"🔒 Lock {month} → Zoho"** (replaces the separate "Official lock" + "Push to Zoho"). In one step it: saves a reversible version → locks the month (silent) → pushes to Zoho. Requires an active Zoho connection (else blocks with a clear message — Michael: "won't accept a lockdown unless it pushes"). Shows a confirm with a per-deal preview + count first.
- **What gets pushed (Carlos's spec):** only the fields Don adjusts by hand in Aureum that **DIFFER** from Zoho, across the **Forecast-only** set (`inForecast`, not Closed Won). Mapping via new `forecastPushDiff(d)`: **Fcst close date→`Closing_Date`** (the field that burned Don — was NEVER pushed before), **Fcst HC→`Headcounts`**, **Fcst MRR (HC×$/head)→`MRR`**, **Fcst ARR→`ARR`**, **Win%→`Probability`** (manual override only), **Fcst contribution margin→`Target_Margin_Band`** (only when Don set an actual/forecast margin, not a stage default). `$/head` has no standalone Zoho field → carried by MRR/ARR. On success, the Zoho-side values on the live deal are updated so the diff clears and a later Sync won't "revert".
- Old `pushApprovedToZoho` (Playground approve-gated diff push) **kept** as the admin tool; `aureumLockForecast`/`aureumPushToZoho` kept as thin aliases → `lockAndPushForecast`. `lockForecast` gained a `silent` param.
- **Still to do on A:** the tracked **lockdown-version-per-month** label + a **lockdown-vs-current** view (accuracy is already measured against locked snapshots; this is the UI to compare). Overview Current-Quarter view + Summary→Financial Model still pending (batch after).

### 2026-07-01 (PM-3) — UX consolidation: one Lock home, recovery tools tucked into Admin (nothing deleted)
Carlos: "consolidate so it doesn't confuse, but don't delete anything useful." Reorganized/relabeled — **zero functions removed** (all still callable; buttons just relocated). JS validated.
- **Lock is now a SINGLE action, in the Aureum action bar.** The duplicate 🔒 Lock buttons on **Overview** and **3 mo. revenue** became **read-only status pills** ("🔒 {month} locked" / "🔓 not locked — lock it in Aureum"). Also removed the duplicate "🔒 Lock current forecast" from the Forecast Accuracy toolbar. `lockForecast` is no longer wired to any loose button — only via the Aureum `aureumLockForecast`. (The lock+push-to-Zoho **one-step** merge is the next A build — needs push-semantics design.)
- **Recovery/power tools moved to Admin Panel → new "Recovery & data" sub-tab:** ⟲ Full rebuild from log · ↩ Reconstruct (typed cells only) · 🔎 Audit log — plus a Backups & versions summary. They were cluttering Forecast Accuracy. The **per-snapshot ↩ Recover** stays on each Historical Snapshots row (contextual); Export/Import stays in Settings.
- **Auto-backups relabeled** in the Versions modal: "🔒 auto safety backup — never changes your forecast" (kills Don's fear that the automatic daily update was overwriting his work — it's purely additive; the real overwrite was the now-fixed Zoho sync).
- Left intact: Save version, Versions, Aureum Lock + Push to Zoho (single place), Playground push (admin diff), all recovery functions.

### 2026-07-01 (PM-2) — Mike-call punch-list · batch 1: Pipeline TCV + en-US number format + Monthly MRR label
From Carlos↔Michael call. **Michael has the final word on Horizon; internal guardrail = map & save ALL manual adjustments (drives the lockdown/version work).** This batch = the unblocked, low-risk items; the rest of the punch-list is tracked below. JS validated (`node --check`); column counts reconcile (Pipeline 7 cols).

**1) TCV column added to Pipeline.** Juliana confirmed the Zoho Deals field is displayed as **TCV** with API name **`TCV`** — `ZOHO_TCV_FIELD` was already `'TCV'`, so `d.tcv` already syncs. Surfaced it: a **Total Pipeline TCV** KPI card + a **TCV** column in the Phase Breakdown table, the collapsed phase summary line, and the Deal Breakdown (size-band subtotals + per-deal + summary rows). Shows `—` when a deal has no TCV; **populates after the next Zoho sync**. Rationale (Michael): ARR = MRR×12 assumes 12 months, but contracts can be 3/6/18 mo, so TCV can be higher/lower than ARR. Coordinate the pipeline-report definition with Julie/Alejandra.

**2) Number formatting → en-US everywhere.** All 29 no-arg `.toLocaleString()` calls now use `'en-US'`, so thousands use a **comma** (2,600) not a locale period (2.600). Michael flagged Pipeline total headcount showing "2.6"; the browser locale (Spanish) was the cause. Fixes it app-wide (English deliverables / US audience).

**3) Active Customers — Actuals:** the "Monthly $" column is relabeled **"Monthly MRR $"** (it's monthly MRR, per Michael).

**Mike-call punch-list — still to do (Michael's asks):**
- **(A) Forecast lifecycle / lockdown (core):** push to Zoho ONLY at the monthly **lockdown**, in ONE step (Claude refuses a lockdown unless it also pushes); update Zoho **once a month**. A tracked **lockdown version per month** ("July forecast, locked June 30") is what accuracy is measured against; add a **lockdown-vs-current** view. Between lockdowns, save versions freely (many/day) — dates, margins, HC, close dates all saved — **without** pushing. Every save MUST persist every changed field (dates+margins were not saving — *now covered by the schemaV3 capture fix*). **Don wants to drop the automatic daily update** (fears it overrides his work) — Michael: double-edged → **decision pending** (touches `ensureDailyBackupVersion`). Procedure: Carlos emails Don (CC Michael) to lock the recovered dates as the July-made-in-June forecast; nothing was ever locked down yet.
- **(B) Overview:** add out-year columns (2027 / 2028; 2028=0) so the 63-in-forecast reconciles (43 next-3-mo + 13 rest-26 + ~7 in 27); resolve **Average vs Total** inconsistency (is the 3-mo first column the 3-mo total or the monthly avg? label says "Average MRR") and verify the Full-Year-2026 line math; confirm 26/27 = strictly NEW business (2028 = +1.6% growth). Replace **"Rolling three-month window" with "Current Quarter" month-by-month** (Jul/Aug/Sep — rolling loses visibility of the current month); relabel "3-month total" → "Q3 total", roll to Q4 after Q3. Add a **Current Quarter** summary column in front of the 3-mo total (for the sales "win the quarter" tracking, separate from John Scott's financial forecast).
- **(D) Summary → "Financial Model":** rename (it's the CFO/John Scott financial forecast), **move to far right** of the tab row (right of Settings), and **lock the forecast numbers** — only **PMFF** is editable there (CFO input only).
- **(E) Pipeline:** TCV done ↑; number format done ↑.
- **(F) Concern:** deal count dropping (322→317 in the week) — investigate why deals disappear.

### 2026-07-01 (PM) — Backup/restore integrity: protect ALL manually-entered data (Aureum + Summary)
Carlos asked to guarantee that **no path can lose any manually-entered data**, that a Zoho Sync keeps everything (hand-set dates, margins, etc.), and that **backups protect absolutely everything including the Summary (PMFF) and Aureum manual inputs**. Full read-audit of every deal-write/rebuild path; closed 5 gaps. JS validated (`node --check`). **KEY LESSON: the daily backup and the JSON export were NOT complete — they silently dropped Summary PMFF + Super-Deal assessments; and two merge/reconcile paths dropped per-deal overrides.**

**1) Export is now a FULL-fidelity backup.** `exportData()` dropped `pmff` (Summary/CFO input), `super_programs` (Super-Deal assessments + notes), the access roster and every settings/override key — the downloaded JSON was NOT a complete backup. It now iterates **all** `FB_SYNCED_KEYS` (each key at top level → backward-compatible), so any current or future synced key is included automatically. Filename → `aspenview-bde-forecast-FULL-YYYY-MM-DD.json`; logs the key count.

**2) Import restores the WHOLE platform, safely.** `importData()` restored only 5 keys. It now restores every exported key; **data keys overwrite**, **roster keys (`known_users`/`viewers`/`pmff_editors`/`admin_extra`) UNION-merge** so restoring an old backup never revokes anyone's access. Gated by `canEdit()`, and it auto-saves a reversible safety version first (re-appended even if the backup carried its own `forecast_versions`).

**3) Daily/working-version backup now covers Summary + Super-Deals.** `_captureForecastItems` added `marginOverride`; each version + the daily auto-backup now also store **`pmff` + `superPrograms` + `marginRules`** (`schemaV` bumped 2→3). `restoreForecastVersion` restores them **only from schemaV≥3** versions (an older draft never wipes current Summary/Super data). Restore confirm notes when Summary/Super are included.

**4) `mergeSeedData` no longer drops ghost overrides.** When a hand-edited not-in-Zoho deal reconciled into its Zoho record, only mrr/HC/flags carried — `dollarPerHead`, `winRateAdj`, `forecastMargin`, `actualMargin`, `marginOverride`, `growthPct`, `forecastClosingDate`, `totalHC` were LOST. Now filled from the ghost when the Zoho record lacks them (Zoho-linked deal wins on a real conflict).

**5) Hand-set Zoho-mirror fields survive the sync (Hueco 4).** `status`, `firstBillableMonth`, `activationMonth`, `commercialDescription` used to be "Zoho wins" — a manual edit in the deal modal was reverted on the next sync. Added a per-field `d._manual` marker set on modal Save (and carried across the sync) so a hand-set value wins; **untouched deals still track Zoho.** `forecastClosingDate` was already preserved.

**Deploy:** commits pushed to `main`; `version.txt` bumped to fire the refresh banner. **To do:** Don/Michael hard-refresh once; optional deeper fix = per-deal Firestore writes for the sub-second concurrency race (unchanged from AM).

### 2026-07-01 — Eric feedback deliverable · Overview/Summary rework · data-loss root-cause fix + full integrity audit
Big session, driven by Eric's (CEO) feedback via Michael. All live on GitHub Pages (commits through `9436135`). **KEY LESSON: the Zoho sync was silently reverting Don's manual per-deal overrides on every sync — any deal-rebuild path MUST carry over every user field.** Highlights:

**1) Consolidated response to Eric.** Combined all of Eric's feedback — 2 emails + 2 handwritten PDFs (Part 1 = Overview labels & calcs; Part 2 = Summary output) — into one numbered checklist with status + where-to-see-it. Deliverable in 3 formats (`ERIC_RESPONSE.md` / `.html` / `.pdf`, brand-styled; PDF rendered via headless Chrome). 18 items done/answered; **2 open, each attributed to Eric with owners Eric & Michael:** (a) timing of the Existing/New split & Months 13-24 ("maybe 2027"), (b) definition of **Gross Margin** (his email quote included). Sent to Michael to forward.

**2) Overview rebuilt (Michael's asks).** Four auto-updating deal-count KPIs (Total Deals·Pipeline / In Forecast [Fcst-checked, Closed Won excluded] / Next 3 Months / Rest of 2026). One **calendar table** — columns `3-Month · Remainder of 2026 · Full Year 2026 · Full Year 2027 · Full Year 2028 (top-down)`, all labelled **Total** — that **leads with Average MRR** (monthly), then Projected Revenue, Adjusted Revenue (×win), **Contribution Margin Dollars** (×win×margin), Forecast Headcount. Removed the "×" formula annotations (Michael). Retired the standalone "Rolling Months 4-12" block. Full Year 2026 = 3-Month + Remainder (reconciles).

**3) Summary Contribution % bug — FIXED.** It divided win-adjusted contribution by GROSS MRR (≈ margin × win ≈ 14%, which Michael flagged as impossible). Now divides by the **win-adjusted MRR** → reads as the true blended margin (~25-40%). Same fix in the By-month view. Also: **PMFF is entered only in the By-month view**; the By-horizon view totals it up read-only (no double entry).

**4) Method consistency (Michael: "same format throughout").** 12-Month Plan now uses the **per-deal win×margin** method like Overview/Summary/Aureum (retired the flat 15% accuracy / 35% margin haircut). Removed the now-orphaned months-4-12 margin/accuracy controls from Settings (kept default $/head). **"Forecast only" now means `inForecast` AND not Closed Won everywhere** — the Aureum filter was counting Closed-Won Fcst deals (showed 67) while Overview/export showed 63; now all reconcile at 63.

**5) UI polish.** Aureum **bottom summary band frozen** while scrolling (stacked sticky offsets, no overlap). Pipeline colored total/size-band bars no longer flash white on hover. Export dialogs use an **in-app English modal** (no OS-language "Aceptar/Cancelar"). "Contribution Margin Dollars", "Total" labels, MRR/ARR $/head throughout. Tour + `guide-en.html` refreshed to the new vocabulary and the PMFF month→total flow.

**6) Admin Panel counts fixed.** "Team members" / "Active today" now come from the reconstructed roster (known_users + activity log), not the last-write-wins known_users doc (which read 1 / 0 when several people had signed in). Added a text filter to the live + full activity views.

**7) DATA-LOSS ROOT CAUSE FIXED + recovery.** The Zoho sync (`syncZohoDeals`) rebuilt each deal from a fresh literal that **omitted Don's manual overrides** — `forecastClosingDate`, `dollarPerHead`, `forecastMargin`, `actualMargin`, `growthPct` — so every "Sync from Zoho" reverted them (the confirm message even falsely claimed they were preserved). Don's forecast close dates were wiped this way. **Fixed:** the sync now carries them over; message corrected. **Recovery:** Don's dates were still in `activity_archive` (57 `Edit forecastClosingDate` events) → new one-click **"↩ Restore forecast close dates from audit"** button (Admin → Activity) re-applies the latest date per deal (auto-saves a reversible version first, fuzzy name match). Forecast versions now **capture + restore** forecastClosingDate & forecastMargin, and restore is **schema-guarded** (`schemaV`) so restoring an OLD version never wipes fields it didn't capture. `exportData` now includes activity_log + forecast_versions.

**8) Full data-integrity audit (Carlos asked to verify every scenario).** Confirmed via git diff that this session's UI/calc edits wrote **zero** deal data. New-computer/fresh-browser login is safe (`autoApplySeedOnce` is a hard no-op; the destructive `purgeSeededHcOnce`/`migrateStageRules` are **never called**; only trend snapshots + daily backup run on load). Zoho connect touches no deals; sync matches by `zohoId` and always preserves `monthlyHC` + (now) all overrides. Every field setter mutates in place; all destructive paths (revert, reset, import, delete) are manual + confirmed. **PMFF (John) only written by `updatePmff`.** Residual risk = the whole state is ONE Firestore doc with last-write-wins, so two people editing in the same sub-second window could overwrite each other (rare, and recoverable via audit log + daily backups; true fix = per-deal writes).

**9) Concurrency safety net + auto-update.** Live **editing-presence banner** — when someone edits, others see "👥 X is editing Horizon right now — coordinate before editing the same deals" (stored in `dashboard/presence`, one key per user via `merge:true`, isolated from the state doc, **no new Firestore rule needed**). **Auto-update check:** the app polls `version.txt` (every 3 min + on focus); when a new deploy lands it shows a "🔄 New version — Refresh" banner so nobody keeps working in a stale tab (a stale tab running the old sync was what wiped Don's data). **Bump `version.txt` on every deploy** for the banner to fire.

**Open / to do:** (a) Eric to decide the 2 open items (Existing/New timing, Gross Margin definition + a source for nonbillable cost of sales); (b) Don & Michael must **hard-refresh once** to leave the stale (pre-fix) tab; (c) run the close-date recovery button and verify against what Don had; (d) optional deeper fix for the sub-second concurrency race (per-deal Firestore writes).

### 2026-06-29 — Zoho push enabled · roles & invite-only · export integrity · Eric's Summary tab
Huge session. All live on GitHub Pages (commits through `4298668`). Highlights:

**1) Zoho Push-to-Zoho is LIVE.** The OAuth client `1000.IDIKQZQT1WD4JUK97IYAFVWKEFSAPC` ("AspenView BDE Forecast" app, US DC) now grants `Deals.READ + Deals.WRITE + Accounts.READ`. Carlos re-authorized (Disconnect→Connect→Accept) and a real Playground push succeeded ("1 deal updated"). Token is per-user/per-browser — Michael & Don must each re-auth before they push. The big push of Don's forecast still waits on Don/Michael sign-off.

**2) Permanent audit log.** Every `logActivity` event now also mirrors to a standalone, uncapped Firestore collection **`activity_archive`** (the in-doc log stays capped at 200 for the recovery tools). "📜 Load full history" in Admin → Activity reads it. ⚠ Requires a Firestore security rule for `activity_archive` (same condition as `dashboard`) — Carlos published it.

**3) Roles + invite-only access.**
- New **Observer** (view-only) role: browse + export, but can't edit/lock/approve/push. Enforced at the data layer (`save()` blocks `VIEWER_PROTECTED_KEYS` writes for non-editors) + read-only CSS on in-table inputs.
- **Invite-only:** only code-defined managers + granted admins + granted observers can sign in (`isInvited`); any other @aspenview.com is denied (`denyAccess` after Firestore loads; managers hardcoded so never locked out). There is no open BDE-editor anymore — to edit, you're admin/manager.
- **Admin → Team** now shows the FULL roster (managers + granted + observers + activity-log actors), not just signed-in; ROLE column dropped (Access shows ADMIN/OBSERVER/Editor); Observers KPI card; admins/observers managed with Grant/Make/Revoke buttons (mutually exclusive). John Scott + Eric Berg set as **observers**.

**4) Export integrity (Finance-grade).** Single source of truth `aureumTotals()` feeds BOTH the grid totals and the Excel/Sheets export → can't drift. Export is **forecast-only always** (Michael's call) + a **top-down 2028 column** (2027 × growth %). Labels per Eric: **Contribution Margin**, **MRR $/head**, **Contribution $**. **Adjusted HC shows 1 decimal** (weighted math reconciles by hand). A scope reminder + **"✓ Verify export = table"** button reconcile to the dollar and **block** export on any mismatch. Aureum summary: gold-framed 26/27/28 totals (both sides); the HC row shows headcount totals (not $).

**5) Eric's "Summary" tab (EAB template).** New tab: Forecast Summary Statistics. **Existing / New / Total** accounts × **3 horizons** (3-mo, 4-12, 13-24) × **Forecast / PMFF / B(w)** × **4 metrics** (Headcount, MRR, Contribution $, Contribution %). Forecast = monthly averages (so a sum never reads as a headcount); 13-24 = 4-12 avg grown one year top-down. **PMFF** = Prior-Month Financial Forecast (CFO input, typed in); **B(w)** = Better/(Worse) = Forecast − PMFF (color-coded). **Total auto-sums Existing + New** (Contribution % weighted). Existing/New classified by client relationship (Active/Expanding/Closed-Won client = existing). **PMFF-editor capability:** specific observers (CFO/CEO = John, Eric) can edit ONLY the PMFF cells (`canEditPmff`, `pmff_editors` list, `body.pmff-allowed input.pmff-input` CSS, `save()` lets them write only `pmff`); grant via Admin → Team "+ Summary edit".

**6) Overview label clarity (Eric's calc question).** The "$4.2M (4-12) seems too small" was NOT a bug — it's the 9-month TOTAL and 679 is cumulative HC-months, not a headcount. Relabeled both; Projected/Adj Revenue now say "MRR".

**7) Guide + tour.** Guide EN refreshed for Horizon (roles, invite-only, export forecast-only + 2028, Summary §6.2, backups). Tour is role-aware (observers skip editor-only tabs) and has new Export + Summary steps.

**Open / to decide with Michael & Eric (live, point-by-point):** monthly-average vs period-total in Summary · official "existing vs new" definition · timing (Eric: Existing/New split + 13-24 in 2027) · whether Contribution is win-adjusted · **uploading** the CFO's PMFF as a file (today typed in; by-month Forecast view is now built via the By horizon/By month toggle). New synced keys this session: `viewers`, `pmff`, `pmff_editors`.

### 2026-06-26 — Eric/John change-set, a data-loss incident + recovery, and hardening
Big day. All live on GitHub Pages (commits through `e893763`). Order of events:

**1) Data-loss incident + recovery (most important).** Don built a full forecast (6:40–7:27am, locked 7:27) and it got **wiped**. Root cause: the `purgeSeededHcOnce` "forget the spreadsheet" migration was **auto-run on load gated by a per-browser localStorage flag**, so a fresh browser/device re-ran it and cleared the live `deals` build in Firestore for everyone. Recovery built and used:
- `recoverForecastFromSnapshot(monthKey)` — restores the 3-month build from a snapshot (the 7:27 lock survived in `snapshots`). Button on each Forecast-Accuracy snapshot row.
- `reconstructFromActivityLog()` — replays typed `Edit HC` / `Edit Fcst HC` from the activity log (all months).
- `rebuildFromActivityLog()` (the **⟲ Full rebuild from log** button) — replays the WHOLE session: every ⇶ Spread (via `computeSpreadPlan`, refactored out of `applySpread`) + typed cells, then pins the exact 3-month window. **Fuzzy (normalized) deal-name matching** so renamed deals match. The log is authoritative — the snapshot pin does NOT overwrite a replayed spread (the FIFA bug).
- **Spread start clamp:** many deals carry placeholder close dates (Jan 2030); close+1 sent spreads to 2030 (off-screen → missing 2027). Both the rebuild and the Spread modal now clamp the start to `[anchor … anchor+24]`, else the anchor. This restored 2026/2027 (2027 jumped $1.6M → ~$3.3M).
- `openForecastAuditModal()` — **🔎 Audit log** viewer (every forecast action, oldest-first, copyable) so Carlos could see exactly what Don did, deal by deal.
- ⚠ **No server-side recovery available:** Firebase project `aspenview-bde-forecast-154b4` is on the **Spark (free) plan** → no Firestore PITR / backups. The activity log caps at 200 events. So the in-app rebuild is the ceiling; consider upgrading to **Blaze + enable PITR** as the real safety net.

**2) Anti-data-loss hardening (so it never happens again).** `autoApplySeedOnce` neutralized to a no-op; `purgeSeededHcOnce` / `migrateStageRules` no longer auto-run; removed the one-click "Reset all deals to Zoho" mass-wipe button; `resetAllData` now requires typing RESET + auto-saves a version first; **`ensureDailyBackupVersion()`** auto-saves a daily restore point on login (keeps last 10). **Nothing mutates deals on load anymore.**

**3) Per-user view state.** `active_phases` removed from `FB_SYNCED_KEYS` — the phase filter is now **local per browser**, so two people on different PCs filter without changing each other's screen. (Search / multi-select / tab / scroll were already local.) `togglePhaseActive` no longer logs.

**4) Eric/John meeting change-set (additive only — no data touched):**
- **Forecast Margin** — editable `Fcst MG` column per deal (placeholder = stage target); `getMarginFor` = actual → forecast → target, so it drives the adjusted MRR / contribution.
- **Adjusted-by-month rows** at the bottom: Adj. Headcount (×win), Adj. MRR (×win), Adj. Margin $ / contribution (×win×forecast-margin), across Y1/Y2 + year totals. Gross rows relabelled (HC / mo, MRR / mo). Bottom summary rows made **non-sticky** (`sumrow` class, no hover flash, subtle separators) after the sticky `totals-row` versions stacked/overlapped.
- **CGI breakout vs All Other** in the weighted summary card (own HC/MRR/win/margin; OneMain stays in All Other).
- **Editable Forecast Close Date** in the close-date cell (`forecastClosingDate`, type=date) to fix erroneous Zoho dates (2030); Zoho date shown as reference; does NOT change the Spread. (Done as an editable cell, not a separate column, to avoid grid churn — split later if Michael wants two columns.)
- **Export for Finance/John:** `⬇ Download Excel` → formatted **.xls** (HTML-table flavour: bold navy header, shaded TOTAL rows, right-aligned numbers; opens in Excel and, via Drive upload, Google Sheets with formatting). `📋 Copy for Google Sheets` → TSV to clipboard, paste into a blank Sheet (plain values). Both READ-ONLY via `_aureumExportMatrix()` (deal rows + monthly build + gross & adjusted summary). ⚠ Export functions are top-level so they use a local `_yd` helper (the grid's `yearDollars` is scoped inside `renderAureumGrid`).
- **Header:** tool name **"Horizon"** enlarged to 21px.

**Open / to confirm:** Don was validating the recovered forecast (some spreads where he hand-changed the start month may land a month off — quick manual fix). Forecast-margin/adjusted/CGI/close-date all pending Don+Michael review. Carlos stepped out; will adjust on return.

### 2026-06-25 — Don + Michael live-review change-set (the 20-point punch list)
Implemented from the minute-by-minute transcript of the live review. Backup: `index.backup-2026-06-25.html`. JS validated; Aureum column counts reconcile (MRR/month row = 38 + N1). All pending **live verification** by Carlos after push (Firebase Google auth can't run headless).
- **The "jumping" bug (the #1 complaint) — FIXED.** Every cell edit re-rendered `mainContent` and reset the grid's own `.scroll-x` scroll (→ "jumps to top") and page scroll. `renderTab()` now captures & restores both scroll positions; `selectTab()` resets to top on a real tab switch (`_suppressScrollRestore`). Affected everyone, not just Don.
- **Stage Win % / Margin % (Don's numbers).** Win: SQL 10 · Stage 2 30 · Commercial Design 50 · P.S 50 · Recruitment 75 · Pricing 75 · Legal/Paper 95 · Closed Won 100 · Super-Deal 35 · Default 30. Margin: all 35, **P.S/Path 45**, Super-Deal 25. Updated `DEFAULT_MARGIN_RULES` + `STAGES`, plus a one-time `migrateStageRules20260625()` so the live (Firestore) state adopts them (won't clobber later manual edits).
- **Weighted financial model (Michael's long discussion).** Deal rows stay **PURE** (a 3-person deal is 3, never 1.5). New **Probability-weighted summary** card on Aureum: Pure forecast vs **Weighted (×stage win%)** vs **Contribution (×margin)** per year + probability-weighted HC. Same weighted/contribution shown for Y1 in the sticky bottom bar. Finance (John) takes pure or weighted; never edits per deal. Helpers added to the Aureum totals loop (`wHC`, `y1w/y2w/y3w`, `y1c/y2c/y3c`).
- **MRR by month.** New "MRR / month (forecast)" totals row under the headcount totals (Σ HC × $/head per month).
- **Spread start month** now defaults to **close date + 1 month** (`dealCloseMonthKey` + conservative cash-flow rule). Falls back to anchor if no usable close date.
- **Close Date column.** Renamed "Start Date" → **Close Date**; displayed snapped to the **30th** of its month (`fmtDate30`, **visual only** — Zoho value unchanged, per decision). Billing start is set by Spread.
- **Fcst HC default = Zoho HC** shown as the field placeholder; Spread already seeds from Zoho HC.
- **Y3 growth relabeled** to "MRR growth" (toolbar + column header).
- **"Forget the spreadsheet" — purge.** One-time `purgeSeededHcOnce()` clears every deal's seeded monthly build (the 257 / 50 / blue super-deal), resetting `totalHC` to the Zoho HC. Forecast now starts 100% from Zoho; Don rebuilds with Spread.
- **Close-date push counter.** On each Zoho sync, when a deal's `Closing_Date` moves later we bump `closeDatePushes` and log `closeDateHistory`. Shown as a ↗N badge on the Aureum Close Date cell (amber, **red at 3+**), and a new **"Deals Pushing"** card on Forecast Accuracy (by BDE, sorted).
- **Reconciliation by deal** (new card on Forecast Accuracy). For the most recent **locked** month: deals forecast that did **NOT** close at the TOP, then the ones that closed, then **deals with a Zoho close date that month that weren't forecasted** (to chase RevMax/BDEs). Summary footer counts.
- **Active Customers — Actuals** now **grouped by client** with a subtotal per client (HC + monthly $) + grand total (Don: "select Hexcel, see the total").
- **Column color scheme** (Michael): editable inputs get a faint gold tint, read-only/Zoho inputs grey, outputs white; legend updated.
- ⚠ **To verify live after push:** (1) Fcst MRR column already recalcs from Fcst HC × Fcst $/head — confirm Don sees it move (the "MRR" he watched is the *Zoho* MRR, which correctly stays fixed); (2) Y2 (2027) totals populate once HC is added (was empty in the demo only because no 2027 HC existed yet); (3) session persistence before lock (already live-saved to Firestore); (4) duplicate/"Pulse" Closed-Won deals = Zoho data hygiene, not a tool bug.

**Deployed to GitHub Pages + follow-up refinements (same session, live, commits up to `4c4e46a`).** Sent Don + Michael the link to review. Iterations after Carlos test-drove the live build:
- **Win % now defaults to the STAGE rule, not Zoho Probability.** The sync no longer auto-populates `winRateAdj` from Zoho's probability (that always overrode the stage). `winRateAdj` is kept only as an explicit per-deal override; `zoho_winRateAdj` still captured for the Playground diff. So Win % reads from Settings stage rules as Don wanted.
- **Comprehensive purge + manual reset button.** First-load auto-purge raced with Firestore and left old-file HC in place. Rewrote `purgeSeededHcOnce` (flag `purge_oldfile_v2`) to clear monthly build, Fcst HC override, $/head, win%, margins, growth% — **keeping `inForecast` (Don's Fcst picks)**. Added deterministic **Settings → "Forget the Spreadsheet — Reset Deals to Zoho"** button (`resetDealsToZoho`) — the reliable path; run it once. Result: Fcst HC = Zoho HC, Win % = stage, Fcst $/head = MRR ÷ Zoho HC, Fcst MRR = real Zoho MRR.
- **Spread start follows each deal's close date.** `dealCloseMonthKey` now parses ISO, `"MON'YY"` and general dates; the Spread dropdown is built around the deal's timeline so the default (close + 1 mo) is always selectable — no longer falls back to the current month.
- **Year-total headers** relabeled **"YYYY Total MRR"** (Michael: "show MR for each of the year headings").
- **Fcst $/head now drives the forecast live.** `updateDealField` re-renders (scroll preserved), so editing $/head / win% / margin / growth immediately recomputes **Fcst MRR, year totals, MRR/month row, weighted summary**. **ARR column is now Fcst ARR = Fcst MRR × 12** (Carlos/Michael: "the ARR needs to change by the forecast"). The "MRR" column stays the Zoho reference.
- **Active Customers** changed from subtotal rows to **collapsible per-client groups** (▸/▾, collapsed by default) with a **# Deals** column + HC + Monthly $ on the client line; Expand-all/Collapse-all (`activeExpanded` / `toggleActiveClient` / `setAllActiveExpanded`).
- **Playground phantom-diff fix.** It compared Zoho HC vs the monthly-build *sum* (0 after a reset → every deal showed a −N diff). Now compares Zoho HC vs the **Fcst HC total**, so a deal equal to Zoho shows no diff and only real forecast changes appear.
- ⚠ Carlos's standing reminder to Don/Michael: **on open, hard-refresh (Ctrl+Shift+R) and Sync to Zoho on the Aureum tab first**; run the Settings reset-to-Zoho once.

### 2026-06-24 (PM) — Tool named "Horizon" + official brand palette
- **Tool name: "Horizon"** (Carlos/Michael had no name; Aureum is taken — Horizon fits the AspenView navigation family: Vantage / Compass / Horizon, and "view → horizon"). Set in `<title>`, header, and login. Alternatives floated: Summit, Meridian. NOTE: the main tab is still labelled "Aureum Demand Forecast" — the word "Aureum" there is the aureum-tier (super-deal) concept, not the product; rename later if it causes confusion.
- **Official brand palette** applied from `AspenView Color Codes.pdf` via CSS vars (propagates to all var-based theming): `--navy #0C1A28` (Dark Blue), `--navy2 #1B3244` (Pre-Dark), `--gold #FEC219` (Aureum) + `--gold2 #D4AF37` (alt), `--brand-blue #008BC9` (Main Blue) + `--brand-blue2 #00AFE6`, plus light/baby/greyish blues. Wordmark "VIEW" now Main Blue. ⚠ Some accents are hardcoded hex (phase pills, banner backgrounds) and weren't swapped — tune later if needed. If `--gold #FEC219` reads too bright, switch to `#D4AF37`.

### 2026-06-24 — Michael 1:1 change-set (multi-year Aureum, lock/push at bottom, $/head split, branding)
Implemented from the Carlos↔Michael meeting (scope: this tool only):
- **Multi-year on one Aureum tab** (calendar year): deal details → **Y1 months (anchor→Dec)** → **Y1 $ total** → **Y2 months (Jan–Dec)** → **Y2 $ total** → **Y3 growth %** (per-deal, editable, default % in the toolbar — manager-only) → **Y3 $ total**. Year totals = Σ HC × $/head; Y3 = Y2 × (1+growth%). Spread works across Y1+Y2 (24-mo). Helpers `getDefaultGrowthPct/effectiveGrowthPct`, `yearDollars`; new synced key `default_growth_pct`.
- **$/head split** (Michael's revert idea): **Zoho $/head** (read-only, MRR÷Zoho HC — original never overwritten) + **Fcst $/head** (editable, green when confirmed). Zoho HC also read-only. `zohoPricePerHead()` added.
- **Bottom sticky action bar** on Aureum: bottom-line totals (Fcst HC + Y1/Y2/Y3 $) + **🔒 Lock {anchor}** + **↗ Push changes to Zoho**, so the flow (select Fcst → adjust → see totals → lock → push) stays on one screen. `aureumLockForecast()` locks then prompts to push; `aureumPushToZoho()` requires lock first (routes to existing push; needs Zoho WRITE scope, still pending).
- **Branding**: wordmark "VIEW" now blue (`--brand-blue #5B9BD5`, ⚠ confirm exact hex with Alejandra/Laura), trees moved to the RIGHT of the text in header + login.
- Column counts reconcile (43 with a July anchor = 37 + 6 Y1 months); JS validated. Other tabs (3 mo/12-Month/Overview) left as-is per Michael ("don't worry about other tabs yet").
- ⚠ Pending from the meeting (not yet done): Overview multi-year summary (26/27/28), tool name, logo polish ("lollipops"), real "push changed deals" once WRITE scope lands.

### 2026-06-22 (PM) — Table restructure + rolling quarter + auto Super-Deals + Zoho-vs-Don
Carlos change-set after the morning review:
- **Column order across all forecast tables** (Aureum, 3 mo. revenue, 12-Month Plan, Super-Deal rollup): now lead with **Client → Deal** (both frozen-left via new `.freeze-1` / `.freeze-2` CSS).
- **Aureum & 3 mo. cleanup**: removed **Status, Loc, TCV** columns (Status filter dropdowns removed too).
- **Deal Summary reordered**: `Start Date · Contract · Zoho HC · Total HC · $/head · MRR · Fcst MRR · Cov % · ARR · Win % · TM % · AM %`. Total HC now sits before the dollar columns. "Start" → **Start Date**. Target % / Actual % → **TM %** / **AM %** (Target Margin / Actual Margin, with tooltips).
- **Spread is its own column** (⇶) at the start of the Headcount Build group, right before month 1 — pulled out of the frozen Deal cell.
- **Rolling quarter, automatic.** `ALL_MONTHS` is now generated dynamically from the real current month; default anchor = **month after the current one** (`defaultAnchorMonth`), and any saved anchor at/before the default rolls forward on its own. Today (June) → forecasts **Jul/Aug/Sep**. Managers can still pick a later anchor; it sticks.
- **Super-Deals auto-derived** from live 100+ HC deals (Michael's rule). Don's 7 static seed programs retired (`SUPER_PROGRAMS_SEED = []`). New `reconcileSuperPrograms()` groups flagged deals by client, computes Peak HC / Phase 1 / Activation, and preserves per-client qualitative assessments + notes across syncs. "+ Add Program" / Delete removed; edit modal now only sets activation override + notes.
- **Zoho-vs-Don comparison.** `pricePerHead()` now uses **MRR ÷ Zoho HC** as the basis (was MRR ÷ own built HC), so if Zoho maps 200 HC and Don forecasts 100, his **Fcst MRR** comes out to half. New Aureum columns **Zoho HC**, **Fcst MRR** (Total HC × $/head) and **Cov %** (Fcst ÷ Zoho MRR) make the gap explicit; this also makes 3 mo. / 12-month revenue respond to how much HC Don actually commits.
- Backup saved: `index.backup-2026-06-22.html`. JS validated (inline script compiles); column counts reconcile (Aureum 33 / 3 mo 20 / 12-Month 16).
- ⚠ To verify live: Zoho `Headcounts` populates `zoho_totalHC` for the Zoho HC column; clear the saved anchor if a stale one is pinned.

**Follow-up refinements same session (live, commits up to `4bfd9de`):**
- Labels: `Contract` → **Contract Terms** (value shows "N mo"); **TM % / AM %** → **Target MG / Actual MG**.
- **Cov % column removed** (confused the read; comparison now read directly as Zoho HC vs Fcst HC and MRR vs Fcst MRR).
- **Fcst HC is now an editable, independent field** (renamed from Total HC): Don types the total he forecasts; it is NOT tied to the monthly cells. The **⇶ Spread** modal defaults to that total and distributes it across the phases/months. `updateForecastHC()` only stores `d.totalHC`. (Editing a month cell directly still re-syncs totalHC to the sum — the one "anchored" path.)
- **Super-Deal rule fixed**: `dealHcForRollup()` / `isSuperDeal()` now take **`max(zoho_totalHC, monthly build, totalHC)`** instead of Zoho-first — so a deal Don forecasts at 100+ counts even when Zoho mapped fewer (e.g. 257 vs 50). ⚠ This also feeds Pipeline HC totals / size bands (uses the larger of mapped vs forecast). Carlos OK'd keeping it shared.
- Super-Deals tab: client cell now **links to the largest deal in Zoho**; **SUB-TOTAL row is sticky** (`totals-row`).
- Super-Deals **Phase 1** = first wave (first build month) of the **linked/representative deal** (the client's largest, the one the link opens), not the sum across all the client's deals. Peak HC stays the per-client total.
- Aureum: removed the redundant **SUPER pill under the deal name** (there's a Super column).
- **Closed Won (phase 5) excluded from the forecast** — 3 mo. revenue and 12-Month Plan now drop it (captured in cash flow). Forecast inclusion stays driven by the per-deal **Fcst** checkbox (Carlos chose manual control over auto-by-phase); the phase pills remain an Aureum view filter, so the Aureum phase count and the 3 mo. count intentionally differ.
- **Single source of truth for forecast totals** (`forecastDeals()` + `forecastTotals()`). The forecast set is defined once (Fcst flag, no Closed Won); Overview's 3-month window and 4–12 block draw from `forecastTotals()`, and 3 mo. revenue + 12-Month Plan base their rows on `forecastDeals()`. So Overview ≡ 3 mo. (months 1–3) and Overview 4–12 ≡ 12-Month Plan, by construction — no more duplicated math that can drift. Caveats that legitimately make them differ: 3 mo./12-Month view filters (BDE/Stage/search), the 3 mo. accuracy-haircut toggle, and Aureum being the full input superset (filter it to "Forecast only" to reconcile).
- **Overview rolling window aligned with the 3 mo. revenue tab.** `renderOverview()` summed ALL deals; now it filters to `inForecast && phase !== 5` (Closed Won) for both the 3-month window and the Months 4–12 block, so the Overview totals match the 3 mo. tab (they previously drifted higher). The 3 mo. tab's own BDE/Stage view filters can still make it lower than the Overview.
- **Ownership & handoff runbook** added: `OWNERSHIP_AND_HANDOFF.md` — how to make the tool a company-owned asset that survives the builder leaving. Key findings: app is already multi-admin (low risk); real risks are **GitHub repo on a personal account** (→ create AspenView org + transfer) and **Zoho OAuth app under the builder's personal Zoho** (→ re-register under a company Zoho admin; implicit flow, no secrets to move). Firebase is on a company Workspace (OK — just ensure ≥2 owners + backups). Includes a deploy runbook, data backup/restore, in-app admin management, and an "if the builder leaves" checklist.
- **Actuals from Zoho (Option A).** `buildZohoActuals()` / `pullActualsFromZoho()` derive month-by-month actuals from **Closed Won deals grouped by Owner** → real **per-BDE actuals** (no more proportional-only proxy). A Closed Won deal is attributed to its **start month**, valued at MRR/HC. Runs **quietly on login and after every Zoho sync** (`ensureZohoActuals`), plus a manual **"↻ Pull actuals from Zoho"** button in Forecast Accuracy. **Non-destructive**: months entered manually are never overwritten (source tag ZOHO/MANUAL shown). Per-BDE accuracy now uses the real by-owner actual when present, else the proportional proxy. ⚠ Proxy caveat: this is deal MRR at start month, NOT finance-system billed revenue (that's still the Phase 3 QuickBooks/Stripe integration). A true scheduled pull (no one opening the app) would need a backend Cloud Function — deferred (Option B).
- **$/head is now a confirmable rate** (Option 1). `pricePerHead()`: confirmed override → **MRR ÷ Zoho HC only when Zoho carries headcount** → Settings default. Removed the circular `MRR ÷ built/Fcst HC` step that inflated the rate when Zoho mapped fewer heads than Don forecasts. Aureum $/head cell turns **green when Don has confirmed a rate**; blank = suggested (tooltip says which source). Note: Fcst MRR can still legitimately exceed Zoho MRR when Don forecasts more heads than Zoho mapped — that's a real signal, not a bug.

### 2026-06-22 — Don+Michael review change-set (100% Zoho)
After the live demo to Don & Michael:
- **Excel retired — 100% Zoho.** Removed the Cleanup tab, the In-Excel column/filter, the MRR Aureum (dual-MRR) column, the Overview reconciliation card, and the Excel seed (one-time migration purges non-Zoho ghosts). `buildSeedIndex()` now returns `{}`.
- **Deal Summary from Zoho** in Aureum: Start date, Contract (months), MRR, ARR, TCV, Total HC — new configurable field constants `ZOHO_ARR_FIELD / ZOHO_TCV_FIELD / ZOHO_CONTRACT_FIELD / ZOHO_START_FIELD / ZOHO_NRR_FIELD` (⚠ API names guessed — Carlos to verify; sync degrades gracefully if wrong). Win % = Zoho `Probability`. Margin is calculated (NOT in Zoho).
- **$/head**: per-deal override → MRR÷HC → global default (Settings → Forecast Assumptions).
- **Target vs Actual margin**: Target = Stage Rules table (read-only in grid, edit in Settings); Actual = per-deal, blank until priced/closed.
- **Headcount Spread tool** (⇶ per row): total HC + start + first wave + #phases + months/phase → auto-fills the monthly build.
- **Frozen Deal column** (sticky-left) so it stays visible scrolling sideways.
- **3 mo. revenue**: added Total Billable HC to the summary big numbers.
- **Week-over-week forecast changes** card in Forecast Accuracy (added / removed / HC+rev changed, from weekly snapshots).
- **Access reversed**: Don + Juliana now FULL (admin + governance). `canGovern()` = `isAdmin()`. Don restored to manager role.
- **Demo trend data removed** — starts clean; `purgeDemoTrendSnapshots()` strips any seeded demo points.
- Decisions: $/head = MRR÷HC (no Zoho field); margin 30%/accuracy 15% defaults; tool name TBD; domain forecast.aspenview.com pending.
- Behind the scenes (separate, with John Jacobs): load Statements of Work for closed-won → real HC/$-per-head/margin → push to Zoho. Not in the tool yet.

### 2026-06-19 (PM) — Michael review change-set (pre-Don final, Monday)
Implemented Michael's post-demo feedback, to the letter, for the Monday final review with Don:
- **Single input model**: `Aureum Demand Forecast` is now the only input and rolls a full 12 months from the anchor (new `rollingMonths()` helper). `3 mo. revenue` and `12-Month Plan` are read-only OUTPUTS of it (removed the duplicate HC inputs from 12-Month Plan).
- **Aureum grouped headers**: two-row header — **Deal Summary** (MRR Zoho, MRR Aureum, Win %, Margin %, **+ Total HC**) and **Headcount Build — Headcount by Month**. Months 1–3 tinted "precise"; gold divider at month 3→4. Fixed a pre-existing off-by-one in the TOTALS row colspan.
- **Super-Deal is automatic** at **100+ total headcount** (was a manual 50-HC checkbox) — the Super column is now a read-only indicator. `SUPER_DEAL_HC_THRESHOLD = 100`, `isSuperDeal()` derives from `dealHcForRollup()`.
- **Phase filter** moved to the left of the phase pills and made prominent.
- **Months 4–12 assumptions** (admin-only, team-synced): standard margin **30%** default, accuracy **15%** default — editable in **Settings → Forecast Assumptions**. Dollars for 4–12 = HC × $/head (`pricePerHead`, i.e. MRR ÷ HC).
- **Overview**: added **Deal Count Reconciliation** (total / in Zoho / not-in-Zoho ghosts → Cleanup) and a **Rolling Months 4–12** block with its own accuracy % and standard margin.
- **Forecast Coverage by Phase** report on the Pipeline tab: pipeline vs forecasted deals/HC/MRR + coverage % per phase (Recruitment / Pricing / Legal).
- **Reliability bands governance**: bands stay computed from locked snapshots + actuals, but only managers can pin an override (acc & ±) per horizon (`accuracy_overrides`). BDEs cannot move them.
- **Long-term Growth → renamed "Rolling Months 13–24"**; growth % & baseline are now manager-only controls.
- **MRR clarity**: MRR Aureum cell highlights when it differs from Zoho MRR (the 21k-vs-40k case → reconcile in Playground). Fixed stale "Zoho.Amount" copy (MRR field is primary, Amount is fallback) in the sync comment, deal modal, and Settings field-map.
- Decisions locked with Carlos: $/HC = MRR ÷ Headcounts (division) until a literal Zoho field is found; margin 30% / accuracy 15% defaults, both editable; reconciliation = Zoho + ghosts.

### 2026-06-19 — Onboarding tour + guide refresh + Project Context doc
- Added `🎓 Take Tour` button in the header next to `📖 Guide` to launch the tour any time.
- Redesigned the tour overlay so the topnav stays visible. The active tab pulses with a gold spotlight; the explanation card is positioned below the topnav with a white triangle arrow pointing up at the highlighted tab. Arrow shifts horizontally with each step.
- Rewrote EN + ES user guides for the current 12-tab structure: added 5-phase model section, Build & Invoice framing, walk-through for Pipeline / 12-Month Plan / Long-term Growth, Lock & per-BDE accuracy, Backfill, embedded Forecast Trend, Admin Panel as header button, Guided tour replay instructions. Removed references to deleted tabs.
- Created this `PROJECT_CONTEXT.md` as the canonical briefing doc.

### 2026-06-18 — Tour, dashboards, dual-line trend charts, plain copy
- Added the guided 12-step onboarding tour that auto-triggers on first sign-in.
- Added the **Pipeline** tab — investor-grade dashboard with phase breakdown, deal-breakdown-by-size-band, and deal-age segmentation.
- Added the **12-Month Plan** and **Long-term Growth** tabs to round out Michael's full vision: months 1-3 deep dive (3 mo. revenue) → 4-12 per-deal ramp → 13-24 aggregate growth.
- Added **Lock & sign-off** mechanic (Phase B): `🔒 Lock {month} forecast` button creates an immutable snapshot. Per-BDE accuracy bands (M+0/M+1/M+2). Raw vs Adjusted accuracy haircut toggle on 3 mo. revenue.
- Added **Backfill historical accuracy** form so the accuracy table populates without waiting 2-3 real cycles.
- Added the **Phase A — 5-phase nomenclature** + phase filter pills on Aureum + phase column in tables.
- Added **Build & Invoice — NOT Close** banner on Aureum, 3 mo. revenue, 12-Month Plan, and Long-term Growth.
- Consolidated topnav from 16 → 12 tabs: removed Forecast Trend (now embedded in Accuracy), Fulfillment View, MRR Forecast 2026. Moved Stage Rules into Settings.
- Stripped personal names from all user-facing copy (no Don/Michael/Eric in UI text).
- Forecast Trend charts now show two lines: solid Forecast + dashed green Actual (from Actuals + backfill).
- Moved Commercial Design from Phase 1 (SQL) to Phase 2 (Recruitment) per Michael's confirmation.

### 2026-06-17 — Admin Panel, Forecast Trend, Audit log
- Added **Admin Panel** (header button, admins-only): Team management with Grant/Revoke admin from UI, Activity stream, Error log.
- Added **Forecast Trend** tab (later merged into Accuracy in Phase A consolidation): weekly auto-snapshots, 3 line charts, KPI deltas, click-to-drill-down modal.
- Wired audit-log capture for every user adjustment (HC, MRR, Win%, Margin%, Fcst/Super toggles, deletes, playground approves, anchor change).
- Locked admin list to four emails: carlos.rincon, michael.durham, don.kelly, juliana.castaneda.

### 2026-06-16 — Pre-Michael fixes
- Fixed Zoho headcount field name: `Total_Headcount` → `Headcounts` (was returning empty).
- Active Customers — Actuals now falls back to Zoho `Headcounts` when no monthly HC is planned. Added `·z` legend marker.

### 2026-06-11 (approx) — Michael 1:1 feedback batch
- In-Excel indicator column + filter.
- Win% / Margin% moved to the LEFT of monthly columns.
- Sticky table header.
- Inline ✕ delete for ghost rows.
- "+ New Deal" redirects to Zoho.
- **Stage Rules** (formerly Margin Rules) — per-stage Win% + Margin%.
- Zoho `Target_Margin_Band` → deal margin (green cell when from Zoho).
- ⚠ missing margin badge on Commercial Design+ deals with no margin.
- **Playground** tab: Zoho-vs-Manual diff + per-deal Approve + Push-to-Zoho (real PATCH, requires WRITE scope).
- **Cleanup** tab: reconcile not-in-Zoho ghosts with Zoho candidates per account.
- MRR Zoho reads `MRR` field (not `Amount`).
- Account_Status synced from Zoho Accounts module.

### 2026-06-10 (approx) — Initial build
- HTML SPA scaffold with AspenView theme + Google login.
- Excel seed: 78 deals from Don's APR'26 workbook (sheet1 + sheet2 overlay + sheet6 super flags).
- Zoho OAuth + sync with pagination, owner backfill, smart name matching.
- Firebase Auth + Firestore shared state.
- Dual-MRR model (Zoho + Aureum).
- Super-Deals qualitative table (9 dropdowns × 7 seeded programs).
- Forecast Accuracy with monthly auto-snapshots and reliability bands.
- Search + multi-select filters across all deal modules.
- Branded EN + ES user guides (initial version).

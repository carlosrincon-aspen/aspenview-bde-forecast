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

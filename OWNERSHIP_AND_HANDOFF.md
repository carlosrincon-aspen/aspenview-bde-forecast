# AspenView BDE Forecast — Ownership & Handoff Runbook

> **Purpose:** make this tool a *company-owned* asset that survives any single person leaving
> (including its original builder). The application itself is already multi-admin; the real risk
> is **infrastructure ownership** tied to personal accounts. This document lists every asset, who
> must own it, and the exact steps to make it communal.
>
> **Audience:** AspenView admins (Michael Durham, Don Kelly, Juliana Castaneda) + IT/Ops.
> Keep this file in the repo and a copy of the credential inventory in the company password manager.

---

## 1. TL;DR — what must happen so nothing is lost

| # | Asset | Today | Risk if builder leaves | Action | Priority |
|---|-------|-------|------------------------|--------|----------|
| 1 | **GitHub repo + hosting** | Personal account `carlosrincon-aspen/aspenview-bde-forecast` | Code **and** live site (GitHub Pages) lost if the account is closed | Create an **AspenView GitHub Organization**, transfer the repo into it, add ≥2 company owners | 🔴 High |
| 2 | **Zoho OAuth app** | Registered under the builder's personal Zoho account (`ZOHO_CLIENT_ID 1000.IDIKQZQT…`) | CRM sync (deals + actuals) breaks if that Zoho user is deactivated | Re-register the OAuth app under a **company Zoho admin** account; update the client ID in the code | 🔴 High |
| 3 | **Firebase / Google project** (`aspenview-bde-forecast-154b4`) — Auth + Firestore (all the data) | Company Google Workspace account ✅ | Low, but must not depend on one person | Confirm **≥2 company owners** in IAM; set up periodic data export | 🟡 Medium |
| 4 | **Custom domain** `forecast.aspenview.com` | Company DNS (corporate domain) | Low | Add the CNAME and set it in GitHub Pages | 🟢 Low |
| 5 | **In-app admins** | 4 emails + grant/revoke from Admin Panel | Low (already multi-admin) | Remove the departing person; confirm the others can grant/revoke | 🟢 Low |

**If you only do two things:** #1 (GitHub org + transfer) and #2 (Zoho app under a company account). Those are the only assets currently tied to a personal account in a way that can take the tool down.

---

## 2. Asset inventory (details)

| Asset | Identifier / location | Owner target |
|-------|----------------------|--------------|
| Source code + hosting | GitHub repo, GitHub Pages build (~90s after push) | AspenView GitHub Org |
| Live URL | https://carlosrincon-aspen.github.io/aspenview-bde-forecast/ (→ will change to org URL / custom domain) | Company |
| Auth + database | Firebase project `aspenview-bde-forecast-154b4` (Google Auth restricted to `@aspenview.com`, Firestore at `dashboard/state`) | Company Workspace (✅ already) |
| CRM integration | Zoho CRM v3 REST, OAuth **implicit grant** (`response_type=token`). Client ID in `index.html` (`ZOHO_CLIENT_ID`). **No client secret / refresh token is stored anywhere** — tokens are per-user, short-lived, in `localStorage`. | Company Zoho admin |
| Custom domain | `forecast.aspenview.com` (DNS on the corporate domain) | Company |
| Briefing / docs | `Bde.md` (project context), this file | Company |

> **Security note:** the Firebase web config and the Zoho client ID that appear in `index.html` are **not secrets** — they are public client identifiers (normal for a browser app). There is **no password, client secret, or private key in the repository.** Access is gated by Google sign-in (`@aspenview.com` only) and Firestore security rules.

---

## 3. Action 1 — GitHub: make the repo company-owned 🔴

**Goal:** the repo and the live site no longer depend on a personal GitHub account.

1. **Create a GitHub Organization** for AspenView (github.com → *Your organizations* → *New organization*). Use a company email and add at least **two** company members as **Owners** (e.g., an IT lead + Michael). Name suggestion: `aspenview` or `aspenview-tech`.
2. **Transfer the repo** into the org: repo → *Settings* → *General* → *Danger Zone* → *Transfer ownership* → enter the org name. (All history, branches, and issues move with it.)
3. **Re-enable GitHub Pages** on the transferred repo: *Settings* → *Pages* → Source = `main` branch, root. The new URL becomes `https://<org>.github.io/aspenview-bde-forecast/`.
4. **Add company collaborators/owners** with admin rights so more than one person can deploy.
5. **Update the local clone's remote** (whoever maintains it):
   ```bash
   git remote set-url origin https://github.com/<org>/aspenview-bde-forecast.git
   ```
6. If the **custom domain** is used (Action 4), the public URL stays `forecast.aspenview.com` regardless of the org name — so end users see no change.
7. **Firebase Auth authorized domains:** in the Firebase console (Action 3), add the new Pages domain / custom domain to *Authentication → Settings → Authorized domains* so Google sign-in keeps working.

> Optional but recommended: keep the original personal repo as a read-only archive (or delete it after confirming the org copy + Pages work), and update any bookmarks.

---

## 4. Action 2 — Zoho OAuth under a company account 🔴

**Goal:** the CRM connection doesn't die if the builder's Zoho user is deactivated.

The app uses the **implicit (token) flow**, so there is nothing secret to move — only the **OAuth app registration** needs to live under a company account.

1. A **Zoho admin (company account)** goes to **https://api-console.zoho.com** and creates a new client — type **"JavaScript / Single Page Application"** (implicit flow).
2. Set the **Authorized Redirect URI / Homepage** to the live URL (e.g., `https://forecast.aspenview.com/` and/or `https://<org>.github.io/aspenview-bde-forecast/`). The app's redirect is built from `ZOHO_REDIRECT` in `index.html` — make them match.
3. Request scope **`ZohoCRM.modules.ALL`** (or at least `ZohoCRM.modules.Deals.READ`; add `ZohoCRM.modules.Deals.WRITE` to enable Push-to-Zoho — see note below).
4. Copy the **new Client ID** and replace `ZOHO_CLIENT_ID` in `index.html`, then commit + push.
5. Each admin clicks **Connect Zoho** in the app once to grant access (per-user token; no shared secret).

> **Pending since build:** the WRITE scope (`ZohoCRM.modules.Deals.WRITE`) was never granted, so **Push-to-Zoho** in the Playground tab is inactive. Re-registering under the company account is the right moment to add WRITE if you want that feature.

---

## 5. Action 3 — Firebase: confirm company ownership + backups 🟡

The project is already on a company Workspace account — good. Just make it resilient:

1. **IAM owners:** Firebase Console → *Project settings* → *Users and permissions* (or Google Cloud Console → *IAM*). Confirm **at least two** `@aspenview.com` accounts have the **Owner** role (e.g., Michael + IT). Remove sole dependence on one person.
2. **Billing:** if the project is on a paid plan, confirm the billing account is company-owned (not a personal card).
3. **Data backups (important — this is where all the live data lives):**
   - *Quick / manual:* in the app, **Settings → Export** downloads the full state as JSON. Do this monthly (and before any big change). **Import** restores it.
   - *Robust:* enable scheduled **Firestore exports** to a Cloud Storage bucket (Firebase Console → Firestore → *Import/Export*, or `gcloud firestore export`). Set a weekly schedule.
4. **Auth authorized domains:** keep the live domain(s) listed (see Action 1, step 7).

---

## 6. Action 4 — Custom domain 🟢

1. In corporate DNS for `aspenview.com`, add a **CNAME**: `forecast` → `<org>.github.io` (or `carlosrincon-aspen.github.io` until the transfer).
2. In the repo: *Settings* → *Pages* → *Custom domain* → `forecast.aspenview.com`; enable *Enforce HTTPS*.
3. Add `forecast.aspenview.com` to Firebase **Authorized domains** and to the Zoho **Redirect URI**.

---

## 7. In-app administration (no code needed)

The tool is already multi-admin — admins can run it day to day without a developer:

- **Admins today:** carlos.rincon, michael.durham, don.kelly, juliana.castaneda. Anyone with an `@aspenview.com` email signs in as a BDE (read/edit, no admin).
- **Grant/revoke admin:** header **★ Admin Panel → Team** → grant or revoke admin from the UI. Use this to **remove a departing person** and add new admins.
- **Adjust assumptions:** Settings → Stage Rules (Win %/Margin %), Forecast Assumptions ($/head default, months-4–12 margin/accuracy), Zoho integration, Export/Import.
- **Activity & errors:** Admin Panel → Activity / Errors (audit log).

> When the builder leaves: an admin opens Admin Panel → Team and **revokes** their access. Nothing else in the app breaks — the other admins retain full control.

---

## 8. Deployment runbook (for whoever maintains the code)

No build step. It's a single `index.html` (vanilla JS) served by GitHub Pages.

1. Edit `index.html`.
2. **Validate JS before pushing** (catches syntax errors):
   ```bash
   node -e 'const fs=require("fs");const h=fs.readFileSync("index.html","utf8");const re=/<script\b([^>]*)>([\s\S]*?)<\/script>/gi;let m,ok=0,n=0;while((m=re.exec(h))){if(/\bsrc\s*=/.test(m[1]))continue;n++;try{new Function(m[2]);ok++;}catch(e){console.log("SYNTAX ERROR:",e.message);}}console.log(n+" inline script(s), "+ok+" OK");'
   ```
3. `git commit` + `git push origin main`. GitHub Pages rebuilds in ~90 seconds.
4. Hard-refresh the browser (`Ctrl+Shift+R`) to bypass cache.
5. `Bde.md` is the canonical project briefing — append a Changelog entry each session. This repo is fully workable with **Claude Code** (paste `Bde.md` to onboard the assistant).

---

## 9. Credential / access inventory (store in the company password manager — NOT in the repo)

There are **no secrets in the code**. What to record centrally so access is never lost:

- GitHub Org owner accounts (who has admin on the org/repo).
- Google/Firebase project owner accounts (`aspenview-bde-forecast-154b4`).
- Zoho admin account that owns the OAuth app + the **Client ID** + redirect URI.
- DNS provider login for `aspenview.com`.
- This runbook's location.

---

## 10. "If the builder leaves tomorrow" — minimum checklist

- [ ] Repo transferred to the AspenView GitHub Org (Action 1) **or** at least ≥2 company owners added as collaborators with admin.
- [ ] GitHub Pages re-enabled and the live URL confirmed working.
- [ ] Zoho OAuth app re-registered under a company Zoho admin; `ZOHO_CLIENT_ID` updated and pushed (Action 2).
- [ ] Firebase project shows ≥2 company **Owners**; a recent data export saved (Action 3).
- [ ] Custom domain `forecast.aspenview.com` live (Action 4) so the URL never depends on a personal username.
- [ ] Departing person's admin access revoked in Admin Panel → Team.
- [ ] Credential inventory (section 9) saved in the company password manager.

Once these are checked, the tool is a fully communal AspenView asset — any admin can run it, and any maintainer with org access can deploy changes.

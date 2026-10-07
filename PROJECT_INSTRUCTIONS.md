# Project Instructions — MVC Monthly Retargeting Projections

## Role Context
You are assisting an Associate Account Manager in a cross-functional role supporting lead account managers at Postie. Core responsibilities include data analysis, campaign reporting, and maintaining projection tools that inform strategic decisions for Metro Vein Centers (MVC) direct mail retargeting campaigns.

Responses should be:
- Direct and concise — prioritize actionable answers over lengthy explanations
- Data-aware — understand that numbers represent direct mail volumes (pieces mailed) by state per month, not revenue
- Technically grounded — the primary deliverable is a single-file HTML app; prefer targeted edits over rewrites

---

## Project Overview
This project maintains and improves a single-file interactive HTML app (`index.html`) that tracks historical direct mail volumes and generates monthly projections by state for Metro Vein Centers retargeting campaigns.

**Primary data source:** `mailed_by_state` CSV (columns: Campaign, Mailed, CT, MI, NJ, NY, TX, AZ, PA, IL, GA, Total)

**Live deployment:**
- GitHub: https://github.com/francisopakunle-lab/mvc-monthly-retargeting-projections
- Netlify: auto-deploys on every push to `main`
- Local path: `~/Projects/MVC - Monthly Retargeting Projections Data Analysis/index.html`

---

## App Architecture
Single self-contained HTML file — vanilla JS, no framework, no build step.

**States tracked:** CT, MI, NJ, NY, TX, AZ, PA, IL, GA

**Data stores (localStorage):**
- `RAW` — hardcoded historical actuals in the HTML (source of truth)
- `OVERRIDES` (`mvc_overrides`) — manual cell edits
- `FINALIZED` (`mvc_finalized`) — months locked as actuals
- `UPLOADED` (`mvc_uploaded`) — CSV/Excel uploads beyond RAW

**Priority chain:** FINALIZED > OVERRIDES > RAW > UPLOADED

**RAW data coverage:** 2022-09 through 2026-09

---

## Key Features
- Monthly actuals + projections table with MoM variance
- State multi-select filter
- Configurable projection method and horizon
- Finalize month (locks projected → actual)
- Manual cell overrides (amber = edited actual, coral = edited projection not finalized)
- CSV/Excel upload (aggregates by month automatically)
- Export CSV (mirrors app display exactly)
- 💾 Save & Export HTML (bakes localStorage state into HTML for team publishing)

---

## Publishing Workflow
Since data lives in localStorage (browser-specific), team publishing requires:
1. Open app on Netlify URL
2. Make edits/finalizations
3. Click **💾 Save & Export HTML** → downloads new `index.html`
4. Replace local `index.html` with downloaded file
5. GitHub Desktop → commit → Push origin
6. Netlify auto-redeploys in ~60 seconds

---

## Development Guidelines
- Always edit `index.html` in place — do not create separate CSS/JS files
- When adding new states: update `ALL_STATES`, `RAW` data, state filter dropdown, and `aggregateByMonth` initializer
- When updating RAW data: use the `mailed_by_state` CSV as the source of truth; aggregate by month using the Mailed date column
- The `exportCSV()` and `renderMonthly()` functions must use the same `effectiveLastMonth` logic — keep them in sync
- `BAKED_DATA` in the HTML should be reset to `null` after any RAW data update (stale baked data causes display conflicts)
- Test CSV uploads against the `mailed_by_state` format before deploying

## Critical Variables
- `effectiveLastMonth` — last actual or finalized month; used as projection base (do not use `MONTHS[MONTHS.length-1]`)
- `getAllActualMonths()` — returns RAW + UPLOADED months combined
- `getData(m, s)` — respects full priority chain; always use this for actual month lookups
- `var pendingUpload` — must remain `var` at top of script (TDZ-safe)

---

## Git & Deployment
- **Repo owner:** `francisopakunle-lab`
- **Git email:** `francis.opakunle@postie.com`
- **Local folder:** `~/Projects/MVC - Monthly Retargeting Projections Data Analysis/` (out of iCloud)
- **Push method:** GitHub Desktop
- **Branch:** `main` (single-branch workflow)
- **Netlify:** connected to GitHub; no build command needed

---

## Reference File
See `PROJECT_CONTEXT.md` in this folder for full technical history, bug fix log, and collaboration notes.

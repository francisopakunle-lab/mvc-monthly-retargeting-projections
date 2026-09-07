# MVC Monthly Retargeting Projections — Project Context

## What This Project Is
A single-file interactive HTML data app (`index.html`) for Metro Vein Centers that tracks and projects monthly direct mail retargeting volume by state. It cross-synthesizes historical mailing data to generate forward-looking projections for the team.

---

## Live Deployment
- **GitHub Repo:** https://github.com/francisopakunle-lab/mvc-monthly-retargeting-projections
- **Netlify URL:** (auto-deploys on every push to `main`)
- **Local file path:** `~/Projects/MVC - Monthly Retargeting Projections Data Analysis/index.html`
- **GitHub account:** `francisopakunle-lab`

---

## App Architecture
Single self-contained HTML file — no framework, no build step, no dependencies except SheetJS (CDN) for Excel upload support.

### Data Stores (all persisted via localStorage)
| Store | Key | Purpose |
|---|---|---|
| `RAW` | hardcoded in HTML | Historical actuals (source of truth) |
| `OVERRIDES` | `mvc_overrides` | Manual cell edits |
| `FINALIZED` | `mvc_finalized` | Months locked as actuals |
| `UPLOADED` | `mvc_uploaded` | CSV/Excel uploads beyond RAW |

**Priority chain:** `FINALIZED > OVERRIDES > RAW > UPLOADED`

### States Tracked
`CT, MI, NJ, NY, TX, AZ, PA, IL`  
IL was added in the latest update (September 2026). Historical IL values are 0 until late 2024; meaningful IL volume starts 2026-04.

### RAW Data Coverage
- Earliest month: `2022-09`
- Latest actual month in RAW: `2026-07`
- Primary source CSV: `mailed_by_state` (59-row format with columns: Campaign, Mailed, CT, MI, NJ, NY, TX, AZ, PA, IL, Total)

---

## Key Features
1. **Monthly table** — actuals and projections side by side with MoM variance
2. **State filter** — multi-select dropdown to view any combination of states
3. **Projection methods** — configurable (avg, trend, etc.) over N months
4. **Finalize month** — locks a projected month as an actual (stored in FINALIZED)
5. **Manual overrides** — click any cell to edit; amber highlight = edited actual, coral/orange highlight = edited projection not yet finalized
6. **📥 Upload Actuals** — drag-and-drop CSV or Excel upload; aggregates by month automatically
7. **Export CSV** — exports all actuals + projections matching the app display exactly
8. **💾 Save & Export HTML** — bakes current localStorage state (overrides, finalizations, uploads) into a new `index.html` for team sharing via GitHub push

---

## Publishing Workflow (for team data sharing)
Because data edits live in localStorage (browser-specific), this workflow is required to share state across the team:

1. Open the app on the **Netlify URL**
2. Make edits, uploads, finalizations
3. Click **💾 Save & Export HTML** — downloads a new `index.html`
4. Replace `~/Projects/MVC - Monthly Retargeting Projections Data Analysis/index.html` with the downloaded file
5. Open **GitHub Desktop** → commit → **Push origin**
6. Netlify auto-redeploys in ~60 seconds

---

## Technical Decisions & Key Fixes Made

### CSV Upload Pipeline
- `parseCSV()` normalizes line endings (Windows/Unix/Mac) and strips BOM
- `extractMonth()` handles ISO (`2022-09-20`), M/D/YYYY, and M/D/YY (2-digit year → +2000)
- `aggregateByMonth()` uses named column headers first; falls back to positional parsing if Campaign column commas shift indices
- File input uses `<label>` wrapping `<input type="file">` to bypass browser security blocks on programmatic `.click()`

### Critical Bug Fixes (resolved)
- `lastActualMonth` ReferenceError (was named `effectiveLastMonth`) — crashed page load and blocked all event listeners
- `pendingUpload` Temporal Dead Zone — changed from `let` to `var` at top of script
- Modal CSS cascade conflict — `.modal-overlay { display:flex }` overrode `.hidden { display:none }`; fixed by using `style.display` directly
- CSV export used `MONTHS[MONTHS.length-1]` (RAW-only) as projection base; fixed to use `effectiveLastMonth` (matches display logic)

### BAKED_DATA System
The `exportHTML()` function injects current localStorage state into a placeholder in the HTML:
```javascript
var BAKED_DATA = null; /* BAKED_DATA_PLACEHOLDER */
```
On page load, if `BAKED_DATA` is not null, it restores overrides/finalizations/uploads to localStorage so all team members see the same data.

---

## Git / Deployment Setup
- **Git initialized** at `~/Projects/MVC - Monthly Retargeting Projections Data Analysis/`
- **Folder location:** Local (out of iCloud — moved to avoid git lock file conflicts)
- **Git user:** `francisopakunle-lab` / `francis.opakunle@postie.com`
- **Push method:** GitHub Desktop (GUI) or Terminal
- **Netlify:** Connected to GitHub repo; auto-deploys on every push to `main`
- **No build command needed** — Netlify serves the raw HTML file directly

---

## Team Collaboration Notes
- Small team; single-branch (`main`) workflow is sufficient
- Best practice: **Fetch → Pull before starting work; Push when done**
- Merge conflicts are possible if two people edit simultaneously — communicate before editing
- To add a collaborator: GitHub repo Settings → Access → Add people (grant Write access)
- New team members clone via GitHub Desktop: File → Clone Repository

---

## File Structure
```
~/Projects/MVC - Monthly Retargeting Projections Data Analysis/
├── index.html          ← The entire app (single file)
├── .gitignore          ← Excludes .DS_Store, *.csv, *.xlsx
└── PROJECT_CONTEXT.md  ← This file
```

---

## Upcoming / Potential Next Steps
- Add more states as MVC expands to new markets
- Consider moving data storage off localStorage (e.g., a lightweight backend or Google Sheets API) for true real-time multi-user sync
- Monthly update workflow: upload new `mailed_by_state` CSV → verify → Save & Export HTML → push

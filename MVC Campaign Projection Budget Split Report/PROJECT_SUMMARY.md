# MVC Campaign Projection Budget Split Report — Project Summary

## What this is
A single-file HTML tool ("MVC Campaign Split & Spend") for Metro Vein Centers. It ingests a monthly "State Projections" CSV (columns include `Month`, `Type`, `Total`), lets the user pick a month, and splits that month's total mail volume across 4 campaigns — English Retargeting, Spanish Retargeting, English Multi Touch, Spanish Multi Touch — calculating spend for each based on a per-piece mailing rate.

## File & hosting locations
- **Local file:** `MVC_Campaign_Split.html`, in the connected project folder "MVC Campaign Projection Budget Split Report."
- **GitHub repo:** `francisopakunle-lab/mvc-monthly-retargeting-projections`, branch `main`. The file lives in the `MVC Campaign Projection Budget Split Report/` folder in that repo.
  - **Important:** the repo also has an unrelated `index.html` at its root, belonging to a different project (a "MVC State Projections" tool with baked-in CSV data). Do not touch it.
- **Live site:** `mvc-campaignsplits-1c57f9.netlify.app`, connected to the GitHub repo for continuous deployment (auto-publish is ON — pushes to `main` go live automatically). A `_redirects` file in the report folder routes the root URL to `MVC_Campaign_Split.html` so the tool loads directly.

## Established workflow
1. Claude edits the local file and shows a summary of changes.
2. User reviews locally (opens the file directly, tests with a CSV).
3. Only after explicit user go-ahead does Claude push to GitHub `main` (via GitHub's web upload flow, since there's no local git repo connected) — **Claude always asks permission before pushing**, per the user's explicit request. The user has also pushed manually themselves at times.
4. Netlify auto-deploys whatever lands on `main`; Claude can check deploy status/logs directly via browser tools.

## Core calculation logic (current/final state)
- **Pricing table** (`PRICE_TABLE`), 4 mailing tiers, includes the $0.06 data fee:
  - 500 → $0.7497
  - 1,000 → $0.7354
  - 2,500 → $0.7289
  - 5,000 → $0.7326
- For each campaign: `raw weight = tier × sends`; volume is split proportionally across the 4 campaigns so they sum to the CSV's grand total for the selected month.
- **Volume** and **Volume / Send** are each rounded independently to the nearest whole number (not forced to be exact multiples of each other). This was a deliberate final decision — earlier iterations tried forcing `Volume = Sends × Volume/Send` exactly, but that caused Volume to jump in coarse increments (multiples of Sends), so it was reverted to independent rounding for finer granularity.
- **Spend = Volume × $/Piece**, calculated from the independently-rounded Volume.
- **Volume (Proj Total)** — a separate, reference-only column. Uses the largest-remainder (Hamilton) apportionment method on the true unrounded volumes so the 4 campaigns' figures always sum to exactly the CSV's true monthly total. This column never feeds into Spend.
- Hover tooltips on Volume cells show the true unrounded figure.
- A footnote under the table explains the rounding behavior in plain language.

## Other features
- **Settings persistence:** Tier, Frequency, and Sends selections for all 4 campaigns are saved to `localStorage` and automatically restored when a new CSV is uploaded (previously these reset to defaults on every upload, which caused a confusing mismatch once).
- **Platform License ID row:** added below the totals row, a dropdown (currently one option: `006PQ00000WEAI1YAP`), persisted via `localStorage`, and included in the CSV export as a labeled row.
- **CSV export** mirrors the on-screen table: Campaign, Tier, Frequency, Sends, Volume/Send, Volume, Volume (Proj Total), $/Piece, Spend, plus the Platform License ID.

## Key decisions / history worth knowing
- The original $/piece values were updated once (from an older rate card to the current 4 values above).
- There was extended back-and-forth on how Volume, Volume/Send, and Spend should round and reconcile — several options were tried (strict row-level consistency vs. exact-total matching vs. independent rounding) before landing on the current approach: Volume and Volume/Send round independently, Spend derives from Volume, and a separate reference column guarantees the total always ties to the CSV.
- Netlify was originally a manual "drag and drop" deploy with no connection to GitHub — this was diagnosed and fixed by linking the Netlify site to the GitHub repo for continuous deployment.

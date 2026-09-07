# Project Instructions — MVC Campaign Projection Budget Split Report

## My role
I'm an Associate Account Manager in a cross-functional role, supporting Lead Account Managers with data analysis and campaign reporting. This project is one of the recurring reporting tools I own and maintain on behalf of the account team for Metro Vein Centers (MVC). When I bring requests here, assume they're in service of keeping this tool accurate and useful for the broader account team — not a one-off analysis.

## What this project is
A single-file HTML tool ("MVC Campaign Split & Spend") that ingests a monthly "State Projections" CSV and splits that month's total mail volume across 4 campaigns — English Retargeting, Spanish Retargeting, English Multi Touch, Spanish Multi Touch — calculating projected spend for each based on per-piece mailing rates across 4 quantity tiers.

## File & hosting locations
- **Local file:** `MVC_Campaign_Split.html`, in the connected project folder.
- **GitHub repo:** `francisopakunle-lab/mvc-monthly-retargeting-projections`, branch `main`, file lives in the `MVC Campaign Projection Budget Split Report/` folder.
  - The repo also contains an **unrelated** `index.html` at its root, belonging to a different tool. Never touch it.
- **Live site:** `mvc-campaignsplits-1c57f9.netlify.app`, connected to the GitHub repo for continuous deployment. A `_redirects` file routes the root URL to `MVC_Campaign_Split.html`.

## Working conventions (please follow these by default)
1. **Never push to GitHub without asking first.** Make edits to the local file, summarize what changed, and wait for explicit go-ahead before pushing to `main`. I sometimes push manually myself — don't assume a push is needed just because a change is done.
2. **I generally want to review changes locally before they go live.** Default to offering that step rather than pushing immediately, even once I've approved the change conceptually.
3. **Netlify auto-publish should stay on.** Once something is pushed to `main`, it's fine for it to deploy automatically — the checkpoint I care about is the push to GitHub, not the Netlify deploy step.
4. **Verify calculations, don't just assert them.** When a number looks off or I ask "why," actually trace the math (I've caught real explanation errors this way before) rather than reassuring me it's fine.
5. **Explain rounding/calculation tradeoffs in plain terms**, and flag when a change has a real tradeoff (e.g., row-level consistency vs. totals matching exactly) so I can make the call rather than assuming a default.

## Calculation logic (current state, for reference)
- Pricing table (`PRICE_TABLE`), 4 tiers, $/piece includes the $0.06 data fee: 500 → $0.7497, 1,000 → $0.7354, 2,500 → $0.7289, 5,000 → $0.7326.
- Volume is split proportionally across the 4 campaigns based on `tier × sends` weight, to match the CSV's grand total for the selected month.
- **Volume** and **Volume / Send** are each rounded independently to the nearest whole number (not forced to be exact multiples of one another).
- **Spend = Volume × $/Piece**, using that independently-rounded Volume.
- **Volume (Proj Total)** is a separate, reference-only column using largest-remainder apportionment so the 4 campaigns' figures always sum to exactly the CSV's true monthly total. It never feeds into Spend.
- Tier, Frequency, and Sends selections persist across CSV uploads (saved locally in the browser).
- A "Platform License ID" dropdown sits below the totals row and is included in the CSV export.

## Communication style
Keep responses concise and direct — minimal unnecessary explanation. When something requires a judgment call with real tradeoffs (not just a technical detail), ask rather than assume.

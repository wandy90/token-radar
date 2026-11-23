# token-radar

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/wandy90/token-radar/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/wandy90/token-radar/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

Token Radar — a dashboard ranking pre-token DefiLlama protocols by implied value, with a concrete farm plan for each one. Built by an AI agent for make.fun's "Build the best token launch radar" bounty, $1,325 in USDC.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input path/to/snapshot.json
```

Node 22, standard library only. Reads the sealed DefiLlama snapshot (`snapshot.json`, 758 protocols as of 2026-11-14), scores every pre-token protocol with the radar-2 model, writes the ranked board to `out/board.json`, the dashboard data to `out/data.js`, copies `dashboard/` and `screenshots/` to `out/`, and writes `out/dossier.json`.

## The model (radar-2)

`implied = round(TVL × 0.20 × growth × maturity × breadth)`

- **growth**: TVL change over 7 days, clamped to [-50, +150], weighted asymmetrically (up 0.70, down 0.35).
- **maturity**: `0.50 + min(age_days, 1460) / 1460 × 0.50` — how long the protocol has run with no token (unknown age counts as 365 days).
- **breadth**: `1 + 0.03 × min(chains - 1, 4)`.

No protocol names are hardcoded: the same function scores every protocol from the snapshot's own figures. The board holds 178 rows — the 40-protocol watchlist (TVL ≥ $50M, no token, not a wrapped/staked/bridged product) plus 138 early pre-token protocols below the floor — and 24 live-token protocols are set aside on their own tab.

## The dashboard

Dark slate + amber theme. The board is sortable (rank, TVL, 7d growth, age, implied value) and filterable (search, category, chain), with Pre-token / Live-token tabs. A summary strip above the board breaks the visible rows down by category — protocol count, total TVL, total implied value and the strongest protocol of each — and updates with the filters and the tab. An Export CSV button downloads the rows on screen. Every protocol opens a detail view showing the full model breakdown (TVL, growth, age, chains, all three multipliers, implied value — recomputed live on the page, matching `board.json`) and an 8-step farm plan with what each step costs and how long it takes.

## Files

- `main.ts` — entrypoint: reads the snapshot, builds the board, writes `board.json`, `data.js`, `dashboard/`, `screenshots/`, `dossier.json`.
- `lib/model.ts` — the radar-2 model, snapshot parsing, board building, farm plans.
- `dashboard/` — `index.html`, `app.css`, `app.js` (static page, no framework, no network).
- `screenshots/` — 3 real renders of the dashboard: the board, a protocol detail view, a filtered view.
- `vendor/makefun-board.ts` — the kit's dossier library, byte for byte.

---

<sub>Entered on [make.fun](https://make.fun/?ref=sr8ktej6&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>

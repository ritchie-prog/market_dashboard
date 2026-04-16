# Market Dashboard

Static stock dashboard with daily auto-refresh via GitHub Actions (Yahoo Finance), hosted on GitHub Pages.

**Live site:** https://ritchie-prog.github.io/market_dashboard/

## How to use the dashboard

The dashboard has two panes: a **ticker list** on the left and a **TradingView chart** on the right.

### Layout

- **📅 MACRO EVENTS** (top of left pane) — hover to view high-importance U.S. macro events for the next 7 days.
- **📋 COLUMN GUIDE** — hover for short definitions of Grade, ATRx, and 1M-VARS.
- **Group headers** (Indices, S&P Style ETFs, Sel Sectors, EW Sectors, Industries, Countries) — click to scroll to that group. The Industries header also shows a sector color legend.

### Selecting a ticker

- **Click any row** to load that ticker in the TradingView chart.
- **↑ / ↓ arrow keys** move through tickers across all groups in order.
- The active row is highlighted; the chart auto-scrolls it into view.

### Columns

| Column | Meaning |
| --- | --- |
| Ticker | Symbol; color-coded by sector in the Industries group. |
| Grade | ABC rating — **A** = EMA10 > EMA20 > SMA50 uptrend, **B** = mixed, **C** = downtrend. |
| Daily | Today's % change. |
| Intra | Intraday % change. |
| 5D / 20D | Trailing 5- and 20-day % change. |
| ATR% | Average True Range as a % of price (volatility). |
| ATRx | Distance from SMA50 in ATR units. |
| 1M-VARS | Volatility-Adjusted Relative Strength vs SPY over last 21 days (0–100%). |
| VARS | Sparkline of the VARS series. |
| LETF | Linked leveraged ETFs — green = long, red = short. **Click the ETF symbol** to load its chart. |

Daily / Intra / 5D / 20D cells include a colored bar (green = positive, red = negative) sized relative to the group's range.

### Sorting

- **Click a column header** to sort that group by that column (ascending → descending → reset on the 3rd click).
- Sorting is per-group; each group keeps its own sort state.
- Sorted columns are highlighted in blue.

### Data freshness

Data refreshes daily via GitHub Actions after U.S. market close (16:30 ET, Mon–Fri). Source: Yahoo Finance.

## Build data locally

```bash
cd market-dashboard
pip install -r requirements.txt
python scripts/build_data.py --out-dir data
```

This generates: `data/snapshot.json`, `data/events.json`, `data/meta.json`, and `data/charts/*.png`.

To preview locally: open `index.html` in a browser, or serve the project root with a static server (e.g. `python -m http.server 8000`) and visit `http://localhost:8000`.

## Deploy to GitHub Pages

1. Create a new GitHub repository and push this directory’s contents to it (or push as the repo root).
2. **Before first deploy** you need initial data. Either:
   - **Recommended:** In the repo go to **Actions** → “Refresh dashboard data” → **Run workflow**. When it finishes, it will commit `data/` to the repo.
   - Or run locally: `python scripts/build_data.py --out-dir data`, then `git add data/`, commit and push.
3. In the repo **Settings → Pages**:
   - Set Source to **GitHub Actions** (or “Deploy from a branch”).
   - If using a branch: choose branch `main` and folder `/ (root)`.
4. The workflow runs daily at 16:30 US Eastern to refresh data; you can also run it manually from **Actions**.

Site URL: `https://<your-username>.github.io/<repo-name>/`

## Project structure

```
market-dashboard/
├── .github/workflows/refresh_data.yml   # Daily data refresh
├── scripts/build_data.py                # Fetches data, outputs JSON + charts
├── data/                                # Generated (commit for Pages)
│   ├── snapshot.json
│   ├── events.json
│   ├── meta.json
│   └── charts/*.png
├── index.html                            # Static frontend
├── requirements.txt
└── README.md
```

Data: Yahoo Finance (yfinance), economic calendar (investpy). Charts: TradingView embed.

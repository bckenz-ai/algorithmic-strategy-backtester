# Algorithmic Strategy Backtester

> A from-scratch backtesting engine for quantitative trading strategies — built to evaluate signal quality with institutional-grade rigor and transaction cost realism.

---

## Overview

This project implements a complete backtesting framework for a **50/200-day SMA crossover strategy** applied to the EUR/USD forex pair (2020–2026). Rather than relying on off-the-shelf backtesting libraries for core logic, the signal generation, return computation, and performance reporting are implemented directly in Pandas and NumPy to keep the methodology transparent and auditable.

The notebook walks through every decision — from data acquisition to look-ahead bias prevention to transaction cost friction — with inline commentary explaining *why* each step is done the way it is, not just *how*.

---

## Strategy Logic

**Signal:** Long when the 50-day SMA is above the 200-day SMA; flat (no position) otherwise. A classic trend-following signal.

**Entry/Exit:**
- Enter long when SMA50 crosses above SMA200 (golden cross)
- Exit when SMA50 crosses back below SMA200 (death cross)

**Look-ahead bias prevention:** All position columns are `.shift(1)`-ed before multiplying against daily returns. This ensures only yesterday's signal is used to determine today's position — a critical correctness requirement that most naive implementations get wrong.

---

## Performance Metrics

The notebook computes the following at the end of the backtest period:

| Metric | Description |
|---|---|
| **Total Return** | Compounded log return over the full period |
| **Sharpe Ratio** | Annualized, benchmarked against the ECB Euro Short-Term Rate (€STR) |
| **Maximum Drawdown** | Peak-to-trough decline in the equity curve |
| **Win Rate** | Percentage of closed trades with positive return |
| **Number of Trades** | Total trade windows opened over the backtest period |

Returns are computed as **log returns** throughout (`np.log(close / close.shift(1))`), then exponentiated for final equity curve values. This ensures mathematically consistent compounding across the full period.

---

## Transaction Costs

A flat **0.1% cost per trade** (entry + exit) is subtracted from strategy returns on days where position changes occur. This simulates realistic spread/commission friction and prevents the backtest from overstating edge on a high-turnover signal.

---

## Visualizations

- **Equity Curve Comparison** — Strategy cumulative return vs. buy-and-hold benchmark, plotted as growth of $1 over the full period

---

## Stack

- `Python`
- `yfinance` — historical OHLCV data
- `Pandas` — signal generation, position management, return computation
- `NumPy` — log return math, equity curve construction
- `Matplotlib` — equity curve visualization
- `Backtrader` — (imported; available for extension)

---

## File Structure

```
algorithmic-strategy-backtester/
│
└── Algorithmic_Strategy_Backtester.ipynb   # Full backtesting notebook
```

---

## How to Run

**1. Clone the repo**
```bash
git clone https://github.com/bckenz-ai/algorithmic-strategy-backtester.git
cd algorithmic-strategy-backtester
```

**2. Install dependencies**
```bash
pip install yfinance pandas numpy matplotlib backtrader
```

**3. Open the notebook**
```bash
jupyter notebook Algorithmic_Strategy_Backtester.ipynb
```

Run all cells top-to-bottom. Data is pulled live from Yahoo Finance on execution.

---

## Key Design Decisions

**Why log returns?**
Log returns are time-additive and produce accurate compounding over multi-year periods. Simple returns compound incorrectly when summed. All return math in this project uses `np.log(P_t / P_{t-1})`.

**Why `.shift(1)` on the position column?**
Without the shift, today's signal would be used to trade today — an impossible feat in live trading that inflates backtest performance. The shift ensures the signal is always one day stale relative to the trade it generates.

**Why the ECB €STR as the risk-free rate?**
EUR/USD is a euro-denominated strategy from the long side. The Euro Short-Term Rate is the appropriate risk-free benchmark for eurozone instruments, analogous to using the Fed Funds rate for USD strategies.

**Why flat transaction costs instead of a spread model?**
Simplicity and reproducibility. A 0.1% flat cost is a conservative but tractable approximation for retail forex execution costs. It's easy to adjust and reason about without requiring a live broker connection.

---

## Related Projects

- [Stock Portfolio Risk Dashboard](https://github.com/bckenz-ai/stock-portfolio-risk-dashboard) — Sharpe, MDD, and VaR for equity portfolios via Streamlit
- [Forex Dashboard](https://github.com/Forex-DataViz/forex-dashboard) — Automated USD-PHP macro data pipeline and visualization

---

## Author

**Bryant Kenzo P. Carolino**
B.S. Data Science and Analytics — University of Santo Tomas, Manila

[LinkedIn](https://linkedin.com/in/kenzo-carolino) &nbsp;·&nbsp; [Email](mailto:bc.kenz@gmail.com)

# Algorithmic Strategy Backtester
[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/bckenz-ai/algorithmic-strategy-backtester/blob/main/Algorithmic_Strategy_Backtester.ipynb)
> A from-scratch backtesting engine for a 50/200-day SMA crossover strategy on EUR/USD. Built for signal transparency, look-ahead bias prevention, and honest performance analysis.

---

## Overview

This project implements a complete backtesting pipeline applied to the **EUR/USD forex pair (2020–2026)**. Signal generation, return computation, transaction cost modeling, and performance reporting are all built directly in Pandas and NumPy.

Each metric is interpreted against real institutional benchmarks, and there's an honest conclusion as to *why* the strategy behaved the way it did given the market environment it was tested in.

---

## Strategy Logic

**Signal:** Long when SMA50 > SMA200 (golden cross); flat otherwise. A classic trend-following signal that bets on sustained directional momentum.

| Condition | Action |
|---|---|
| SMA50 crosses above SMA200 | Enter long |
| SMA50 crosses back below SMA200 | Exit to flat |

**Look-ahead bias prevention:** Position columns are `.shift(1)`-ed before multiplying against daily returns, ensuring only yesterday's signal determines today's trade — a correctness requirement most naive implementations skip.

**Transaction costs:** A flat 0.1% cost is subtracted from strategy returns on every day a position change occurs (entry or exit), simulating realistic spread/commission friction.

---

## Results

Backtest period: **January 2020 – December 2025** | Asset: **EUR/USD**

| Metric | Strategy | Benchmark (Buy-and-Hold) |
|---|---|---|
| Total Return | -7.357% | -4.477% |
| Sharpe Ratio | -0.596 | — |
| Maximum Drawdown | -23.744% | — |
| Win Rate | 33.33% (2/6 trades) | — |

---

## Metric Interpretations

### Total Return: -7.357%
The strategy underperforms simple buy-and-hold by **2.88%**. It not only failed to protect against EUR/USD's overall decline — it amplified losses through mistimed entries and transaction costs.

### Sharpe Ratio: -0.596
A negative Sharpe Ratio means the strategy generated returns *worse* than the ECB Euro Short-Term Rate (€STR) of 1.933% per year. The risk assumed produced no compensating reward. An investor would have been better off in a risk-free instrument.

### Maximum Drawdown: -23.744%
The equity curve fell 23.744% from its peak before recovering. On a $1,000 starting portfolio, the balance drops to $762.56 — and recovering to breakeven requires a ~31.1% gain, not just 23.744%. Most institutional desks impose a 10–20% MDD hard stop; **this strategy would have been shut down in a professional setting.**

### Win Rate: 33.33%
2 profitable trades out of 6. Importantly, 6 trades is statistically meaningless — a minimum of ~30 trades is required before any win rate can be attributed to the strategy itself rather than variance. No reliable conclusions can be drawn from this sample.

---

## Conclusion

The 50/200 SMA crossover is a **trend-following strategy** — it only works in markets with clear, sustained directional movement. EUR/USD from 2020 to 2026 was choppy and range-bound, with no persistent uptrend. Every time the golden cross fired a long signal, price reversed shortly after, generating **whipsaw losses** rather than trend-riding profits.

The strategy wasn't broken — it was applied to the wrong market regime. Testing it on a genuinely trending asset would likely produce very different results.

---

## Methodology Notes

**Why log returns?**
Log returns are time-additive and compound correctly over multi-year periods. Simple returns do not. All return math uses `np.log(P_t / P_{t-1})`, with final equity values recovered via `np.exp(cumsum)`.

**Why `.shift(1)` on position?**
Without the shift, today's signal would trade today — impossible in live execution and a common source of look-ahead bias that silently inflates backtest results. The shift enforces a one-day lag between signal and trade.

**Why the ECB €STR as risk-free rate?**
EUR/USD is a euro-denominated long position. The Euro Short-Term Rate is the correct risk-free benchmark for eurozone instruments, analogous to using the Fed Funds rate for USD strategies.

---

## Stack

`Python` `yfinance` `Pandas` `NumPy` `Matplotlib` `Backtrader`

---

## File Structure

```
algorithmic-strategy-backtester/
│
└── Algorithmic_Strategy_Backtester.ipynb   # Full backtesting notebook
```

---

## How to Run

```bash
git clone https://github.com/bckenz-ai/algorithmic-strategy-backtester.git
cd algorithmic-strategy-backtester
pip install yfinance pandas numpy matplotlib backtrader
jupyter notebook Algorithmic_Strategy_Backtester.ipynb
```

Run all cells top-to-bottom. Data is pulled live from Yahoo Finance on execution.

---

## References

European Central Bank. (2021, April 7). *Euro short-term rate (€STR)*. https://www.ecb.europa.eu/stats/financial_markets_and_interest_rates/euro_short-term_rate/html/index.en.html

TradeZella. (2026, April 14). *Drawdown Management: How to Survive and Recover from Trading Drawdowns*. https://www.tradezella.com/blog/drawdown-management

Yahoo Finance. (2025). *Yahoo Finance – Business Finance, Stock Market, Quotes, News*. https://finance.yahoo.com/

---

## Related Projects

- [Stock Portfolio Risk Dashboard](https://github.com/bckenz-ai/stock-portfolio-risk-dashboard) — Sharpe, MDD, and VaR for equity portfolios via Streamlit
- [Forex Dashboard](https://github.com/Forex-DataViz/forex-dashboard) — Automated USD-PHP macro data pipeline and visualization

---

## Author

**Bryant Kenzo P. Carolino**
B.S. Data Science and Analytics — University of Santo Tomas, Manila

[LinkedIn](https://linkedin.com/in/kenzo-carolino) &nbsp;·&nbsp; [Email](mailto:bc.kenz@gmail.com)

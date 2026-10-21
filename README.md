# Does an HMM Regime Filter Beat Simple Volatility Rules?

![Python](https://img.shields.io/badge/python-3.10%2B-blue)
![Notebook](https://img.shields.io/badge/notebook-Jupyter-orange)

---
## Result
A Python walk-forward test of whether a Hidden Markov Model (HMM) regime filter improves rule-based S&P 500 strategies beyond what a simple VIX or volatility rule already achieves. Every day's regime comes from a model fitted only on earlier data.
---
> **Mostly no.** The HMM filter helped the breakout strategy but did not consistently beat a simple 20-day volatility rule. No strategy-filter pair passes a Deflated Sharpe test (best DSR **0.76** against the usual **0.95** bar)

Sharpe ratio (max drawdown), net of 5 bps costs, `March 2000` to Aug 2026. Best Sharpe per row in bold.

| Strategy  | No filter        | VIX < 20          | 20-day vol rule   | HMM               |
|-----------|:----------------:|:-----------------:|:-----------------:|:-----------------:|
| Breakout  | 0.19 (-34%)      | -0.16 (-35%)      | 0.17 (-33%)       | **0.31 (-28%)**   |
| Bollinger | **0.32 (-31%)**  | 0.15 (-26%)       | **0.32 (-16%)**   | 0.02 (-36%)       |
| Trend     | 0.29 (-41%)      | 0.37 (-27%)       | **0.57 (-26%)**   | 0.31 (-39%)       |
| RSI       | **0.19 (-32%)**  | -0.18 (-29%)      | 0.17 (-17%)       | -0.26 (-30%)      |

---

## Method

| Component | Details |
|-----------|---------|
| **Data** | S&P 500 and VIX from Yahoo Finance (`yfinance`), 2000 to Aug 2026 |
| **HMM** | Gaussian, K = 5 states, fitted on `log_vix` and 60/20/5-day returns. K was chosen by a rule set before looking at results |
| **No look-ahead** | 8-year initial window, refitted every 252 days on an expanding window. Regimes use forward-filtered probabilities, not `predict` |
| **Strategies** | Breakout, Bollinger, Trend (50/200) and RSI, each long/short/flat |
| **Filters** | A filter switches a strategy off in stress: VIX above 20, volatility above its past 75th percentile, or HMM stress probability of 0.5 or more |
| **Backtest** | Positions shifted one day, 5 bps cost per trade. Metrics: Sharpe, drawdown, PSR and DSR (N = 16) |

---

## Limitations

- 2025-26 data is a true unopened holdout set.
- Price index only (no dividends), cash earns 0%, one asset.
- K = 5 depends on judgement thresholds. A stricter rule would pick K = 3.

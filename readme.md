# Crypto Statistical Arbitrage via Cointegration and Regime Filtering

> Mean-reversion statistical arbitrage on cointegrated crypto baskets — walk-forward validation, two-tier regime filtering, bucketed position sizing, and inverse-volatility risk parity with **Net Sharpe 1.76 out-of-sample**.

---

## Overview

This project implements an end-to-end quantitative research pipeline for **crypto statistical arbitrage** based on cointegration. Rather than trading single pairs, the strategy discovers 2–4 asset baskets with a stable cointegrating relationship, constructs an Ornstein-Uhlenbeck mean-reversion signal on each spread, and filters signals through a two-tier regime framework before deploying into a diversified portfolio.

The entire pipeline is validated via a **walk-forward backtest** to prevent overfitting, and the final strategy is evaluated on a fully held-out 20% test set the model never touches during calibration.

---

## Results

### Out-of-Sample (20% held-out test set — ~220 days)

| Metric | Gross | Net (20bps) |
|---|---|---|
| Sharpe Ratio | 1.953 | **1.764** |
| Cumulative Return | +6.50% | +5.61% |
| Max Drawdown | — | **-1.38%** |
| Win Rate | — | **72.2%** |
| Cost Drag (Sharpe) | — | 0.190 |
| Annual Turnover | — | 7.05× |
| Entry Events | — | 9 (~15 annualised) |


> **Important caveat:** The OOS Sharpe of 1.76 is based on only 9 round-trip trades (~220 days). This is not yet statistically meaningful — several dozen independent trades at minimum would be needed before the Sharpe ratio becomes a reliable indicator of true edge.

---

## Pipeline

```
Data Acquisition          Basket Discovery          Walk-Forward Backtest
──────────────────        ────────────────          ─────────────────────
Binance daily OHLCV  →    Johansen cointegration →  365-day train windows
23 crypto assets          (rank = 1 filter)         60-day test windows
Mar 2023–Mar 2026         Quality filters:          8 folds total
18 retained after           ADF stationarity        Parameter calibration
quality filter              Variance ratio          per fold (hl_window
80/20 train/test            Half-life 2–15 days       scaled to median HL)
                            Rolling HL stability
                            Spread predictability
                          Dynamic rolling weights
                          (re-estimated monthly)
                               ▼
Portfolio Evaluation      Basket Selection          Signal Generation
────────────────────      ────────────────          ─────────────────
4 baskets deployed    ←   Composite score:      ←   OU mean-reversion
Bucketed positions        mean_SR × consistency     signal on dynamic
  flat / 0.5 / 1.0        × log1p(n_folds)         spread
Risk parity weights       Filter: ≥2 folds,         Two-tier regime:
  inverse-vol, 30d        mean_SR > 0               Hard: vol spike,
20bps tcost on            Greedy diversification:     ADF breakdown
  signal changes only     max 2 baskets/asset       Soft: continuous
Market neutrality                                     score weighting
  analysis (OLS)
```

---

## License

MIT License — free to use, modify, and distribute with attribution.
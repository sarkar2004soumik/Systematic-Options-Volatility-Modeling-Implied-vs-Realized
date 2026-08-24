# Systematic-Options-Volatility-Modeling-Implied-vs-Realized
# Systematic Options Volatility Modeling: Implied vs Realized Volatility in the Indian Options Market

## Overview

A Black-Scholes-based options analytics and research pipeline: option pricing, all five
standard Greeks, a numerical implied-volatility solver, realized-volatility estimation
(close-to-close and Parkinson high-low estimators), implied-vs-realized volatility
comparison, IV skew/term-structure visualization, and systematic long/short ATM straddle
strategy backtesting with realistic transaction costs.

Built with explicit, honest treatment of options' asymmetric risk: short-volatility
strategies are never reported by average P&L alone without also showing the tail risk.

## Research Question

*Does the spread between an option's implied volatility and the volatility subsequently
realized by the underlying carry useful information for a systematic options trading
strategy, after realistic transaction costs — and how does that translate into the
asymmetric risk of long vs. short volatility positions?*

## Objectives

See `report/academic_report.md` Section 7 for the full 8-point objective list (realized-vol
estimation, correct Black-Scholes/Greeks implementation, IV solver, IV-vs-RV comparison,
skew/term-structure visualization, straddle backtesting, honest asymmetric-risk reporting,
explicit mode separation).

## Key Features

- **Correct, tested Black-Scholes + Greeks**: verified against put-call parity, finite-
  difference checks, and IV-solver round-trips (13 passing tests).
- **Three explicit, clearly separated data modes**: research/demo (synthetic), historical
  backtesting (user CSV), and live (yfinance) — with the live mode's real limitation (options
  snapshot only, no history, unreliable for NSE tickers) stated directly rather than glossed
  over.
- **Honest short-volatility risk reporting**: short straddle P&L is never shown as only an
  average — the uncapped tail risk is always available and the per-trade P&L distribution is
  plotted so the asymmetry is visible.
- **Realistic strategy backtest**: proper time-decay-aware contract matching across entry and
  exit dates (the same conceptual contract is tracked as its time-to-expiry decreases),
  transaction costs on both legs of every trade.

## Mathematical Background

Black-Scholes-Merton pricing, the five Greeks (Delta, Gamma, Vega, Theta, Rho), and a
Brent's-method implied-volatility solver. Full formulas in `report/academic_report.md`
Section 13.

## Dataset

- **Research/demo mode** (default, used for the results committed in this repo): a documented
  synthetic underlying (GBM with known true volatility) and a synthetic daily option-chain
  history with a mean-reverting ATM IV level and a realistic volatility skew, repriced via
  Black-Scholes. See `src/data_collection.py::generate_synthetic_option_chain_history`.
- **Historical CSV mode**: bring your own historical option-chain CSV; schema documented in
  `data/sample_option_chain_schema.csv`.
- **Live mode**: `yfinance` for underlying price history (works for NSE tickers, e.g.
  `RELIANCE.NS`) and, separately, a current-day options snapshot (US tickers only, e.g.
  `AAPL` — NSE options are not reliably available via `yfinance`).

## Results

From the last synthetic-data pipeline run (`results/project2_summary.json`): the realized-
volatility estimator recovered the known synthetic GBM volatility closely (true 24.0% vs.
estimated 20d-window mean ≈22.7%); the mean IV-minus-subsequent-realized-vol spread was
small and positive (≈+0.1 percentage points) in this sample, consistent with a modest
volatility risk premium; 18 straddle round-trips were backtested, with the short straddle
showing a higher win rate and positive average P&L but a materially worse worst-case trade
than the long straddle — see `figures/06_strategy_pnl.png` for the visible asymmetry. These
are synthetic-data results; re-run with `MODE = "live"` or `"csv"` in Colab for real-market
figures.

## Performance Metrics

Trade count, total/average P&L, win rate, profit factor, best/worst trade, average entry IV
— see `src/strategy_backtest.py::summarize_strategy_trades`.

## Visualizations

Realized volatility (multi-window), implied vs. subsequent realized volatility, IV skew
snapshot, IV term structure, option-value-vs-spot curves (payoff-diagram style), and
strategy per-trade P&L bar charts — see `figures/`.

## Limitations

See `report/academic_report.md` Section 21 (synthetic-data approximation, live-mode
snapshot-only limitation for options, simplified flat-percentage transaction cost, display-
capped short-vol loss for charting, no margin modeling, flat risk-free-rate assumption).

## Future Improvements

Margin-aware position sizing, strangle/calendar-spread/delta-neutral strategy variants, a
genuine historical Indian options dataset pipeline (e.g. NSE bhavcopy archives), full IV
surface modeling (SVI/SABR), event-driven volatility jump modeling.

## Technologies Used

Python, NumPy, SciPy, pandas, matplotlib, seaborn, yfinance, pytest, Jupyter/Google Colab.

## References

- Black, F., & Scholes, M. (1973). *The Pricing of Options and Corporate Liabilities.*
  Journal of Political Economy, 81(3), 637–654.
- Merton, R. C. (1973). *Theory of Rational Option Pricing.* Bell Journal of Economics.
- Parkinson, M. (1980). *The Extreme Value Method for Estimating the Variance of the Rate of
  Return.* Journal of Business, 53(1), 61–65.
- Hull, J. C. *Options, Futures, and Other Derivatives.* Pearson.
- Sinclair, E. (2013). *Volatility Trading.* Wiley.



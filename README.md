# Portfolio Optimization: From Efficient Frontier to Real World Constraints

A production ready mean variance portfolio optimization framework built on
`CVXPY`, taken from a textbook Markowitz formulation all the way to a
constrained optimizer suitable for real allocation decisions.

## Motivation

Basic Markowitz optimization is a convex optimization exercise; running it
on a real desk is a constraint engineering exercise. This notebook builds
both halves: the mean variance theory, and the practical machinery
(covariance regularization, position limits, sector caps, turnover
control) that keeps the optimizer's output usable rather than a set of
theoretically optimal but untradeable weights.

## What's inside

1. **Data acquisition.** Pulling real historical price data for a universe
   of Indian (NSE listed) sector ETFs via `yfinance`, spanning 6 GICS style
   sectors over a full 3 year window.
2. **Statistical estimation.** Computing expected returns and the sample
   covariance matrix from historical data.
3. **Covariance matrix regularization.** Tikhonov (ridge style)
   regularization to keep the covariance matrix well conditioned and the
   optimizer numerically stable.
4. **Core portfolio optimization.** The Markowitz mean variance program
   `max μᵀw − λ·wᵀΣw` subject to budget and long only constraints, solved
   with `CVXPY` in min variance, target return, and risk aversion
   formulations.
5. **Efficient frontier construction.** Tracing the full risk return
   trade off curve.
6. **Production optimizer with constraints.** Extending the base program
   with per position weight limits and sector exposure caps.
7. **Constraint cost analysis.** Quantifying the Sharpe ratio cost (or
   benefit) of imposing realistic constraints versus the unconstrained
   optimum, including a feasibility check: a position cap is only solvable
   when the number of assets times the cap reaches 100%.
8. **Turnover constrained rebalancing.** Limiting how far a rebalance can
   move from the current portfolio, to control transaction costs.
9. **Final production portfolio.** A fully constrained solve (position
   limits, sector limits, regularized covariance) with allocation and
   efficient frontier visualizations.

## Key result

The unconstrained Markowitz optimum concentrates into just 2 of the 6
assets, driven by noisy sample mean return estimates. Every feasible
position cap tested (20% and above; anything tighter is mathematically
infeasible for a 6 asset universe) actually improves the Sharpe ratio over
the unconstrained case, by roughly 8% to 12%, rather than costing Sharpe
the way it does in a larger asset universe. The lesson: with mean variance
optimization, a moderate diversification constraint can act as an implicit
regularizer against an overfit, noise driven "optimum," and how expensive
or beneficial a constraint looks depends heavily on how concentrated the
unconstrained solution already is.

## Tech stack

`numpy`, `pandas`, `cvxpy`, `matplotlib`, `seaborn`, `yfinance`

## Repository structure

```
.
├── portfolio-optimizer.ipynb   Full notebook: theory, data, and code
└── README.md
```

## Running it

```bash
pip install numpy pandas cvxpy matplotlib seaborn yfinance
jupyter notebook portfolio-optimizer.ipynb
```

The notebook tries a live `yfinance` fetch first, and falls back to an
embedded real price snapshot bundled in the notebook if that fetch fails,
so it runs end to end even without internet access.

## References

- Markowitz, H. (1952). *Portfolio Selection.* Journal of Finance.
- Boyd, S. and Vandenberghe, L. (2004). *Convex Optimization.*
- Ledoit, O. and Wolf, M. (2004). *Honey, I Shrunk the Sample Covariance
  Matrix.* Journal of Portfolio Management.

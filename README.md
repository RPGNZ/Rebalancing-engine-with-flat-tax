# Portfolio Rebalancing Engine with French Flat Tax (aka PFU - Prélèvement Forfaitaire Unique)

A realistic and transparent portfolio rebalancing engine designed to model the impact of the French flat tax (PFU) on realized capital gains during portfolio reallocation.

This project demonstrates why naïve rebalancing implementations are financially inconsistent once taxation is introduced, and proposes an iterative convergence approach to properly account for tax drag during portfolio transitions.

---

## Overview

Traditional portfolio rebalancing algorithms generally assume frictionless markets:

- no transaction costs
- no taxation
- infinite liquidity
- instantaneous capital reallocation

In practice, taxable accounts introduce a recursive dependency:

1. Rebalancing generates sales
2. Sales generate realized gains
3. Realized gains generate taxes
4. Taxes reduce available capital
5. Reduced capital changes the target allocation
6. The new target allocation modifies required sales

This notebook implements an iterative solution to resolve this dependency consistently.

---

## Features

- Multi-asset portfolio allocation
- Arbitrary target weights
- Monthly DCA (Dollar Cost Averaging) contributions
- French Flat Tax (PFU = 31.4%) on realized gains
- Iterative convergence engine
- Explicit realized PnL tracking
- Cost basis support
- Simple and interpretable validation scenarios
- Fully reproducible research notebook

---

## Rebalancing Methodology

The engine follows the workflow below:

1. Compute current portfolio market value
2. Inject optional cash contribution (DCA)
3. Compute target allocation
4. Detect required buy/sell operations
5. Compute realized gains from sales
6. Apply flat tax on positive realized gains
7. Reduce available capital by tax amount
8. Recompute target allocation
9. Iterate until convergence

This iterative process ensures that:

- taxes are correctly reflected in available capital
- target allocations remain internally consistent
- recursive tax effects are properly resolved

---

## Why an Iterative Solver?

A one-pass rebalance implementation is generally incorrect in taxable accounts.

A naïve approach such as:

```python
units_after = (weights * net_value_after_tax) / prices
```

implicitly changes the required sales after taxes have already been computed.

This creates a recursive dependency between:

- sales
- taxes
- net portfolio value
- target allocation

The notebook resolves this dependency through iterative convergence.

---

## Example Use Cases

The notebook includes several validation scenarios designed to verify both numerical stability and financial consistency.

1. Simple Two-Asset Rebalance

Validates that:

- outperforming assets are partially sold
- taxes reduce final portfolio value
- post-rebalance weights match expected allocations

---

2. Zero-Tax Scenario

Validates that:

- rebalancing still occurs
- taxes remain exactly zero when no gains are realized
- portfolio value remains unchanged

---

3. DCA-Assisted Rebalancing

Demonstrates how monthly cash injections naturally reduce taxable sales and improve tax efficiency.

---

4. Convergence Stability Test

Stress-tests the iterative solver on a larger multi-asset portfolio and validates numerical convergence behavior.

---

## Example Output

The engine returns:

- updated portfolio units
- realized PnL
- taxes paid
- convergence iteration count
- final portfolio value
- detailed rebalance summary

Example summary table:

| Asset | Units Before | Units After | Realized PnL | Tax Paid |
| --- | --- | --- | --- | --- |
| BTC | 0.20 | 0.17 | 1800 | 565.2 |
| GLD | 10.0 | 16.3 | 0 | 0 |

---

## Technical Notes

This implementation assumes:

- French Flat Tax (PFU)
- taxation only on positive realized gains
- average cost basis methodology
- no transaction fees
- no slippage

---

## Disclaimer

This project is provided for educational and research purposes only.

It does not constitute financial, tax, or investment advice.

Tax regulations may vary depending on jurisdiction and account type.

# KPI Definitions

This document defines the main reporting terms used in the Power BI model.

| KPI | Definition | Purpose |
| --- | --- | --- |
| Actual MWh | Reported actual production for the period. | Measures delivered operational performance. |
| Budget MWh | Budgeted production for the period. | Provides the performance baseline. |
| Deviation MWh | Actual MWh minus Budget MWh. | Shows absolute under- or over-performance. |
| Deviation % | Deviation MWh divided by Budget MWh. | Shows relative performance versus budget. |
| Normalised MWh | Production normalized for wind index effects. | Separates weather impact from residual performance. |
| Wind Impact MWh | Actual MWh minus Normalised MWh. | Estimates the production impact linked to wind conditions. |
| Wind-adjusted Deviation MWh | Normalised MWh minus Budget MWh. | Shows the residual gap after wind normalization. |
| Operational Portfolio | Assets retained for operating performance review. | Excludes zero-ownership or non-operational anomalies. |
| Investigation Priority | Asset-level follow-up flag based on underperformance scale and driver logic. | Helps management focus review effort. |
| Data Quality Flag | Flag for missing or non-comparable data. | Makes reporting confidence visible. |

## DAX Measures

See [dax_measures.md](dax_measures.md) for the public DAX measure library used to explain the selected report pages.

## Interpretation Notes

- A negative `Deviation MWh` indicates underperformance versus budget.
- `Wind Impact MWh` is used to separate external wind conditions from remaining operational or residual effects.
- `Investigation Priority` is not a final business decision. It is a triage flag for management follow-up.
- `Data Quality Flag` is shown explicitly so that reporting users can see where interpretation requires caution.

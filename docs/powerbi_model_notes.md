# Power BI Model Notes

The public portfolio v0 does not include the PBIX/PBIP file because the report layout and navigation are still being refined. This document summarizes the current model structure used in the selected report pages.

See [dax_measures.md](dax_measures.md) for the public DAX measure library.

## Tables

- `clean_data`: monthly asset-level reporting table
- `task2_asset_summary`: asset-level summary table for follow-up and prioritization
- `Variance Step`: small disconnected table used for the variance bridge

## Measure Groups

- Core performance: actual, budget, deviation, deviation percentage, asset count.
- Variance attribution: wind impact, residual gap, variance bridge.
- Reconciliation: total rows, operational rows, excluded rows, retained flagged rows.
- Data quality: availability missing rows/assets, wind-data missing assets, flagged assets, production certainty.
- Asset follow-up: asset-level actual, budget, variance, variance percentage, residual gap, and compact SVG indicators.

## Current Report Pages

- Board Review: management view for budget-vs-actual performance, variance bridge, trend, market deviation, and key takeaways.
- Asset Detail: follow-up view for ranking underperforming assets by absolute and relative variance.
- Reconciliation Appendix: traceability view for clean-to-portfolio rows, MWh reconciliation, excluded records, and retained caveats.
- Data Quality Appendix: production certainty, missingness, asset-level caveats, and data quality flags.

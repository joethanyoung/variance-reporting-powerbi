# DAX Validation

Validation date: 2026-06-08

This note documents how the public DAX measure library was checked before publishing the portfolio v0.

## Validation Method

The DAX documentation was checked against three evidence sources:

1. Processed table schemas:
   - `data/processed/clean_data.csv`
   - `data/processed/task2_asset_summary.csv`
2. Current Power BI semantic model export:
   - `powerbi/test.SemanticModel/definition/tables/*.tmdl`
3. Selected report screenshots:
   - Board Review
   - Asset Detail
   - Reconciliation Appendix
   - Data Quality Appendix

Static checks performed:

- Every documented table/column reference was checked against the processed CSV headers.
- Every documented measure dependency was checked against either the public DAX library or the current TMDL-exported measures.
- Every measure currently exported from TMDL was checked for coverage in `docs/dax_measures.md`.

## Validation Result

| Check | Result |
| --- | --- |
| Processed tables checked | `clean_data`, `task2_asset_summary` |
| Current TMDL-exported measures | 20 |
| Public documented DAX definitions | 33 |
| Bad table/column references | 0 |
| Bad measure dependencies | 0 |
| TMDL-exported measures missing from docs | 0 |

## Evidence Boundary

The public repo does not include the PBIX/PBIP file. For that reason, the DAX library separates evidence status:

- Measures exported in the current TMDL source are treated as verified current-model measures.
- Helper measures such as `Deviation %`, `Wind Impact MWh`, and `Residual Gap MWh` are documented to make the reporting logic explicit.
- Asset-level SVG measures are visual-layer reconstructions used to explain the table indicators shown in screenshots.

Before any future PBIX/PBIP release, the helper and visual-layer measures should be checked directly inside Power BI Desktop for final naming, formatting, and image URL categorization.


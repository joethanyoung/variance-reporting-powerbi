# DAX Measures

This document records the public DAX layer used to explain the selected Power BI report pages. The PBIX/PBIP file is not included in this portfolio v0, so the measures below provide reviewable evidence of the Power BI logic behind the screenshots.

Evidence status is separated deliberately:

- **Verified in current TMDL export**: measures present in the exported Power BI semantic model source.
- **Documented helper measure**: explicit DAX added here to make the report logic reviewable, even where the current public repo does not include the PBIX/PBIP model file.
- **Visual-layer reconstruction**: DAX pattern documenting the table visual logic shown in screenshots; final naming/formatting should be checked in Power BI before any later PBIX release.

The measures are grouped by reporting purpose: core performance KPIs, variance attribution, data quality, and asset-level follow-up.

## Core Performance Measures

Verified in current TMDL export unless marked otherwise.

```DAX
Budget MWh =
SUM(clean_data[budget_mwh])

Actual MWh =
SUM(clean_data[actual_mwh])

Deviation MWh =
[Actual MWh] - [Budget MWh]
```

Documented helper measure:

```DAX
Deviation % =
DIVIDE([Deviation MWh], [Budget MWh])
```

Verified in current TMDL export:

```DAX
Asset Count =
DISTINCTCOUNT(clean_data[asset_id])

Deviation Color =
IF([Deviation MWh] >= 0, "#2E7D32", "#C62828")
```

## Variance Attribution

Documented helper measures:

```DAX
Wind Impact MWh =
SUM(clean_data[wind_impact_mwh])

Residual Gap MWh =
SUM(clean_data[wind_adjusted_deviation_mwh])
```

The variance bridge uses a small disconnected table. The table and measure are verified in the current TMDL export; the exported measure uses direct column sums for wind and residual steps.

```DAX
Variance Step =
DATATABLE(
    "Step", STRING,
    "Sort", INTEGER,
    {
        {"Budget", 1},
        {"Wind Impact", 2},
        {"Residual Gap", 3}
    }
)
```

```DAX
Variance Bridge MWh =
SWITCH(
    SELECTEDVALUE('Variance Step'[Step]),
    "Budget", [Budget MWh],
    "Wind Impact", SUM(clean_data[wind_impact_mwh]),
    "Residual Gap", SUM(clean_data[wind_adjusted_deviation_mwh])
)
```

## Reconciliation and Data Quality Measures

Verified in current TMDL export.

```DAX
Total Rows =
COUNTROWS(clean_data)

Operational Rows =
CALCULATE(
    COUNTROWS(clean_data),
    clean_data[in_operational_portfolio] = TRUE()
)

Excluded Rows =
CALCULATE(
    COUNTROWS(clean_data),
    clean_data[in_operational_portfolio] = FALSE()
)

Flagged Rows =
CALCULATE(
    COUNTROWS(clean_data),
    clean_data[exclusion_reason] <> "ok"
)

Flagged Retained Rows =
CALCULATE(
    COUNTROWS(clean_data),
    clean_data[exclusion_reason] <> "ok",
    clean_data[in_operational_portfolio] = TRUE()
)

Availability Missing Rows =
CALCULATE(
    COUNTROWS(clean_data),
    clean_data[exclusion_reason] = "availability_missing"
)
```

```DAX
Production Rows =
COUNTROWS(clean_data)

Final Production Rows =
CALCULATE(
    COUNTROWS(clean_data),
    clean_data[production_certainty] = "Final"
)

Preliminary Production Rows =
CALCULATE(
    COUNTROWS(clean_data),
    clean_data[production_certainty] = "Preliminary"
)

Estimated Production Rows =
CALCULATE(
    COUNTROWS(clean_data),
    clean_data[production_certainty] = "Estimated"
)
```

## Asset Summary Measures

Verified in current TMDL export.

```DAX
Asset Count Summary =
DISTINCTCOUNT(task2_asset_summary[asset_id])

Availability Missing Assets =
CALCULATE(
    DISTINCTCOUNT(task2_asset_summary[asset_id]),
    task2_asset_summary[availability_missing_months] > 0
)

Wind Data Missing Assets =
COALESCE(
    CALCULATE(
        DISTINCTCOUNT(task2_asset_summary[asset_id]),
        task2_asset_summary[wind_data_missing_months] > 0
    ),
    0
)

Flagged Assets =
CALCULATE(
    DISTINCTCOUNT(task2_asset_summary[asset_id]),
    task2_asset_summary[data_quality_flag] <> "ok"
)
```

## Asset-Level Follow-Up Measures

These documented helper measures support the asset-detail page, where assets are sorted worst-first and compared by absolute and relative variance. The underlying columns are present in `task2_asset_summary.csv`; the exact PBIX measure names should be verified before any later PBIX release.

```DAX
Asset AC MWh =
SUM(task2_asset_summary[actual_mwh])

Asset Budget MWh =
SUM(task2_asset_summary[budget_mwh])

Asset Var MWh =
[Asset AC MWh] - [Asset Budget MWh]

Asset Var % =
DIVIDE([Asset Var MWh], [Asset Budget MWh])

Asset Residual MWh =
SUM(task2_asset_summary[wind_adjusted_deviation_mwh])
```

## Asset-Level Visual Measures

The report uses compact visual measures to make absolute and relative underperformance easier to scan in a table. These are visual-layer reconstructions for the public documentation, not source data fields.

```DAX
Asset Max Abs Var MWh =
MAXX(
    ALLSELECTED(task2_asset_summary[asset_id]),
    ABS([Asset Var MWh])
)

Asset Delta Bar SVG =
VAR Width = 120
VAR Height = 18
VAR Bottom = Height - 1
VAR Mid = 60
VAR ValueWidth =
    INT(
        DIVIDE(
            ABS([Asset Var MWh]),
            [Asset Max Abs Var MWh]
        ) * 55
    )
VAR X =
    IF([Asset Var MWh] >= 0, Mid, Mid - ValueWidth)
VAR FillColor =
    IF([Asset Var MWh] >= 0, "%232E7D32", "%23EF6C73")
RETURN
    "data:image/svg+xml;utf8," &
    "<svg xmlns='http://www.w3.org/2000/svg' width='" & Width & "' height='" & Height & "'>" &
    "<line x1='" & Mid & "' y1='1' x2='" & Mid & "' y2='" & Bottom & "' stroke='%23999999' stroke-width='1'/>" &
    "<rect x='" & X & "' y='4' width='" & ValueWidth & "' height='10' fill='" & FillColor & "'/>" &
    "</svg>"
```

```DAX
Asset Max Abs Var % =
MAXX(
    ALLSELECTED(task2_asset_summary[asset_id]),
    ABS([Asset Var %])
)

Asset Delta % Pin SVG =
VAR Width = 120
VAR Height = 18
VAR Bottom = Height - 1
VAR Mid = 60
VAR Offset =
    INT(
        DIVIDE(
            [Asset Var %],
            [Asset Max Abs Var %]
        ) * 55
    )
VAR PinX = Mid + Offset
RETURN
    "data:image/svg+xml;utf8," &
    "<svg xmlns='http://www.w3.org/2000/svg' width='" & Width & "' height='" & Height & "'>" &
    "<line x1='" & Mid & "' y1='1' x2='" & Mid & "' y2='" & Bottom & "' stroke='%23999999' stroke-width='1'/>" &
    "<line x1='" & PinX & "' y1='3' x2='" & PinX & "' y2='15' stroke='%23EF6C73' stroke-width='2'/>" &
    "<circle cx='" & PinX & "' cy='9' r='2' fill='%23EF6C73'/>" &
    "</svg>"
```

## Notes

- The `clean_data` measures support the board review and reconciliation pages.
- The `task2_asset_summary` measures support asset-level follow-up and data quality pages.
- The SVG measures are used to improve table scanability and are part of the report presentation layer.
- The current public repo does not claim these measures are a final production semantic model.
- Final naming and formatting should be checked in Power BI before a later PBIX release.

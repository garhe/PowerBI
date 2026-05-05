# Retention Metric Definitions

This report shows product activity and retention at two time grains:

1. Weekly view: based on a 7-day calendar week
2. Monthly view: based on a calendar month

A CCID is considered active when it appears in the source data during the selected week or month.

Retention measures how many previously active CCIDs returned in the next period. It is based on CCID overlap between two periods, not just whether the total count stayed the same.

## Weekly Metrics

### CCID in 7-day
The number of distinct active CCIDs in a calendar week.

### Retention in 7-day
The share of prior-week active CCIDs that were also active in the current week.

Formula:  
Retention = Retained CCIDs / Prior Week Active CCIDs

### CCID WoW
Week-over-week change in active CCID count, comparing the current week with the immediately previous week.

Formula:  
(Current Week CCID - Previous Week CCID) / Previous Week CCID

This is shown as a percentage.

### Retention WoW
Week-over-week change in weekly retention, comparing the current week with the immediately previous week.

Formula:  
Current Week Retention - Previous Week Retention

This is shown in percentage points (`pp`), not percent growth.

### CCID Wo4W
Change in active CCID count compared with 4 weeks earlier.

Formula:  
(Current Week CCID - CCID 4 Weeks Ago) / CCID 4 Weeks Ago

This is shown as a percentage.

### Retention Wo4W
Change in weekly retention compared with 4 weeks earlier.

Formula:  
Current Week Retention - Retention 4 Weeks Ago

This is shown in percentage points (`pp`).

## Monthly Metrics

### CCID in monthly
The number of distinct active CCIDs in a calendar month.

### Retention in monthly
The share of prior-month active CCIDs that were also active in the current month.

Formula:  
Retention = Retained CCIDs / Prior Month Active CCIDs

### CCID MoM
Month-over-month change in active CCID count, comparing the current month with the immediately previous month.

Formula:  
(Current Month CCID - Previous Month CCID) / Previous Month CCID

This is shown as a percentage.

### Retention MoM
Month-over-month change in monthly retention, comparing the current month with the immediately previous month.

Formula:  
Current Month Retention - Previous Month Retention

This is shown in percentage points (`pp`).

### CCID vs Last June
Change in monthly active CCID count compared with the latest June baseline.

Baseline rule:  
Each month is compared with the most recent June at or before that reporting cycle.

Examples:
1. Jan 2026 to Jun 2026 compare with Jun 2025
2. Jul 2026 to May 2027 compare with Jun 2026

This is typically shown as a percentage.

### Retention vs Last June
Change in monthly retention compared with the latest June baseline.

Formula:  
Current Month Retention - Latest June Retention

This is shown in percentage points (`pp`).

## Important Interpretation Notes

### Retention is based on overlap, not total count
Two weeks can have the same CCID count but different retention if the underlying active CCIDs changed.

### Percent vs percentage points
Count-based change metrics such as `CCID WoW`, `CCID Wo4W`, and `CCID MoM` are shown as percentages.  
Retention change metrics such as `Retention WoW`, `Retention Wo4W`, `Retention MoM`, and `Retention vs Last June` are shown as percentage points.

Example:
If retention moves from 70% to 73%, that is:
1. `+3.0 pp` change in retention
2. not `+3%`

## DAX Implementation Notes

### Use Date table for period shifts
Use Date-table time intelligence for prior-period logic instead of filtering fact-table week or month columns.

Examples:
1. Last Week = `CALCULATE([Active CCID], DATEADD('Date'[Date], -7, DAY))`
2. Last Month = `CALCULATE([Active CCID], DATEADD('Date'[Date], -1, MONTH))`

This keeps results aligned with report period selections and avoids blank or mismatched prior-period values.

### Preserve product filter context
For set-based metrics (`Retained`, `New`, `Churned`, `Prior Active`), remove only period columns from Date context, not all Date filters.

Examples:
1. Weekly set logic: `REMOVEFILTERS('Date'[WeekIndex], 'Date'[WeekEndFriday])`
2. Monthly set logic: `REMOVEFILTERS('Date'[MonthIndex], 'Date'[MonthEnd])`

This preserves `Speech L1/L2` context while shifting only time grain.

### WoW and MoM should reference shared prior-period measures
Define percent change metrics from base measures to avoid denominator drift across visuals.

Examples:
1. `CCID WoW % = DIVIDE([Active CCID] - [Last Week CCID], [Last Week CCID])`
2. `CCID MoM % = DIVIDE([Active CCID] - [Last Month CCID], [Last Month CCID])`

### Formatting note for conditional colors
In rule-based conditional formatting for percent measures, use rule type `Number` (not `Percent`) for thresholds such as 0, -1, and 1.

## Short Version for Tooltip / Info Box

### CCID
Distinct active CCIDs in the selected week or month.

### Retention
Percent of prior-period active CCIDs that returned in the current period.

### WoW / MoM
Change versus the immediately previous week or month.

### Wo4W
Change versus 4 weeks earlier.

### vs Last June
Change versus the latest June baseline for the applicable reporting cycle.
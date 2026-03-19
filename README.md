# TiDB PR Review Report

This repository organizes the completed review artifacts for merged TiDB PRs in the recent two-year window, focused on `DDL / add index / infoschema / dxf / metadata`.

## Scope

- Review scope: merged PRs on `master` from the last two years.
- Method: fast relevance triage, stricter worthy triage, then worktree-based deep review at the merged commit.
- Filter: keep only unresolved `Blocker` / `High` findings, and drop items already fixed later.
- Verification note: this dataset is based on static review only. No tests were run as part of the review workflow.

## Current Snapshot

- Worthy-reviewed PRs: `334 / 334`
- Total deep-review result files: `354`
- Unresolved findings kept after later-fix filtering: `69`
- Severity split: `10 Blocker`, `59 High`

## Layout

- `reports/`
  - Final grouped report
  - Condensed unresolved finding summary
- `charts/`
  - Trend chart for unresolved findings by merge month (`png`, `svg`, `csv`)
- `data/unresolved-high-blocker.json`
  - Structured machine-readable dataset for unresolved `Blocker` / `High`
- `data/review-results/`
  - Raw per-PR review result JSON files
- `data/meta/`
  - Review-pipeline summary metadata

## Entry Points

- Start with `reports/final-grouped-report.md` for the grouped Chinese report.
- Use `reports/unresolved-high-blocker-summary.md` for the flat unresolved finding list.
- Use `charts/unresolved-high-blocker-trend.png` for the monthly trend chart.
- Use `data/unresolved-high-blocker.json` if you want to post-process the findings.

## Notes

- This repository currently contains the completed review output for the recent two-year window only.
- The earlier `3y-to-2y` extension window was not yet materialized here.

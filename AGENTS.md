# AGENTS.md

This repository packages review artifacts and summary reports for TiDB merged PR analysis.

## Purpose

- Preserve completed review outputs in a reusable repository.
- Keep raw per-PR findings, derived summaries, and charts together.
- Support future extensions without rewriting historical snapshots.

## Repository Rules

1. Treat `data/review-results/` as raw evidence.
   Do not rewrite existing per-PR JSON unless the source review result itself is known to be wrong and you can explain why.
2. Prefer additive updates over destructive edits.
   If a new review window or a new aggregation pass is added, place it in clearly named new files or directories instead of overwriting the current snapshot.
3. Keep summaries derived from raw data.
   If you change a report or chart, make sure it still matches the JSON source data in `data/`.
4. Do not invent findings.
   This repository is for organizing existing review output, not for adding unsupported conclusions.
5. No secrets or credentials.
   Keep the repository safe to push to GitHub.

## Layout Expectations

- `reports/`
  Final human-readable reports.
- `charts/`
  Derived visualizations and export-friendly chart data such as CSV.
- `data/unresolved-high-blocker.json`
  Canonical structured summary for unresolved high-severity findings.
- `data/review-results/`
  Raw per-PR review result files.
- `data/meta/`
  Pipeline metadata such as triage and batch summaries.

## Change Guidance

- For documentation-only adjustments, keep edits small and localized.
- When adding a new time window, use a new top-level file or subdirectory name that includes the window, for example `reports/3y-to-2y-...`.
- If you generate a new derived artifact, document its role briefly in `README.md`.
- Prefer Markdown, JSON, CSV, and static images over tool-specific binary formats.

## Validation

- For repository-organization changes, verify file placement and `git status`.
- For derived summaries/charts, spot-check counts against the corresponding JSON source.
- No test suite is required for normal artifact-packaging updates.

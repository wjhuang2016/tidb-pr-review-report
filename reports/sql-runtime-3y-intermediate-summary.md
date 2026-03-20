# SQL Runtime 3Y Intermediate Snapshot

This snapshot preserves the intermediate triage output for the broader three-year `sql_runtime` expansion.

## Scope

- Window: `2023-03-17 .. 2026-03-21`
- Focus preset: `planner / executor / expression / session / sessionctx / statistics / kv / store / parser / table / privilege`
- Method so far:
  - local candidate shrinking
  - first-stage `gpt-5.4` relevance triage
  - second-stage `gpt-5.4` worthy triage
- Not done yet:
  - no `gpt-5.2` worktree deep review has started for this branch of work
  - no unresolved `Blocker / High` report exists yet for this scope

## Funnel

- Merged commits in window: `7728`
- Pass1 candidates: `4684`
- Pass2 candidate rows: `3656`
- Unique PRs after deduplication: `3654`
  - duplicate PRs in candidate rows: `#57343`, `#51497`
- Relevance triage:
  - `2581` `deep_review`
  - `1073` `skip`
- Worthy triage:
  - `1206` `deep_review`
  - `1375` `skip`

## Synced Artifacts

- PR-level first-stage decisions:
  - `data/sql-runtime-3y/triage-results/`
- PR-level second-stage decisions:
  - `data/sql-runtime-3y/worthy-results/`
- Candidate and queue metadata:
  - `data/meta/sql-runtime-3y/`

## Notes

- Previously reviewed DDL/meta-overlap PRs were not subtracted before running `worthy`; this snapshot follows the latest user instruction.
- This is an intermediate triage snapshot, not a final review report.

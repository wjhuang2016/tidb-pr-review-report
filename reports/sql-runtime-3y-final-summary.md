# SQL Runtime 3Y Final Summary

This snapshot completes the broader three-year `sql_runtime` review line.

## Final Numbers

- Worthy deep-review queue: `1206 / 1206` reviewed
- `has_high_or_blocker`: `146` PRs
- Unresolved findings kept after later-fix filtering: `158` findings across `146` PRs
- Severity split: `32` Blocker, `126` High
- Confidence split: `135` High, `23` Medium

## Concentration

- `planner`: `86` PRs, `93` findings
- `executor`: `75` PRs, `84` findings
- `expression`: `29` PRs, `31` findings
- `parser`: `26` PRs, `29` findings
- `other`: `20` PRs, `21` findings
- `session`: `20` PRs, `23` findings
- `sessionctx`: `20` PRs, `24` findings
- `store`: `14` PRs, `14` findings

## Time Shape

- `2024-Q3`: `21` PRs, `25` findings
- `2024-Q4`: `19` PRs, `21` findings
- `2024-Q1`: `17` PRs, `17` findings
- `2025-Q2`: `17` PRs, `18` findings
- `2024-Q2`: `15` PRs, `15` findings
- `2025-Q3`: `15` PRs, `16` findings

## Blocker PRs

- `#48010` types: fix the behavior of casting json string to integers (#48010)
- `#49421` parser, planner: fix embedded setOprStmt will be seen as SetOprSelectList item and lost its orderBy and Limit (#49421)
- `#49756` expression: pass const bool to all calls for expression.ConstItem (#49756)
- `#51329` planner: remove the limitation that predicates can't be pushed through `Projection` on `TableDual` (#51329)
- `#51546` planner: choose to generate optimal plans without plan cache rather than generating suboptimal plans and caching it when generating MVIndex plans (#51546)
- `#51901` util: skip always false DNF (#51901)
- `#51403` statistics: concurrency init stats (#51403)
- `#53395` planner: fix the wrong result caused by `year_col cmp out-of-range-uint` (#53395)
- `#53646` planner: fix small regression caused by #53094 (#53646)
- `#52780` expression: JSON_SCHEMA_VALID() (#52780)
- `#55185` planner: use code-gen to generate CloneForPlanCache method for Insert/Update/Delete (#55185)
- `#55631` planner: Removed check for prepared stmt during partition pruning (#55631)
- `#56682` executor: ignore foreign key error in `UPDATE/INSERT/DELETE ignore` (#56682)
- `#57260` planner: optimize the performance of PointPlan for Instance Plan Cache (#57260)
- `#57699` planner: provide a system view information_schema.tidb_plan_cache to allow users to see info in the cache (#57699)
- `#58033` executor: run idxlookup workers in a pool (#58033)
- `#58962` planner: remove redundant branches in the OR list (#58962)
- `#58945` privilege/privileges:  refactor the data struct for user privilege data (#58945)
- `#60048` table: use writable columns to avoid columns not match when add column DDL exec parallel with update DML (#60048)
- `#61187` planner: Handle NullEQ <=> in range columns partition pruning (#61187)
- `#61249` expression: deduplicate the args of IN function (#61249)
- `#61942` planner: support `explain [analyze] <plan_digest>` for `explain explore` (#61942)
- `#62175` codec: should also return the value even with error because the error may be ignored in the future (#62175)
- `#58401` planner/core: update missing virtual columns in `update` and `insert` (#58401)
- `#63059` planner: handle firstrow columns in aggregation predicate pushdown (#63059)
- `#63757` planner: fix nested IN expression rewriter bug (#63757)
- `#65084` executor: nonclustered partitioned table could miss updates due to duplicate _tidb_rowid from EXCHANGE PARTITION (#65084)
- `#65264` planner: derivePathStatsAndTryHeuristics can keep more MPP task (#65264)
- `#65544` planner: fix EXISTS subquery on cluster tables causing Column ID -1 (#65544)
- `#65799` planner, core: implement partial order TopN attach2Task and partial order flow (#65799)
- `#66031` parser,ast: support column masking grammar (#66031)

## Artifacts

- Structured dataset: `unresolved-high-blocker.json`
- Flat summary: `unresolved-high-blocker-summary.md`
- Deep-review summary: `deep-review-summary.json`

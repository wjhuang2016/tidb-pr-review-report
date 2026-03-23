# SQL Runtime 3Y Unresolved Blocker/High Findings

- Generated at: 2026-03-23T14:25:28.678241+08:00
- Window: `2023-03-17 .. 2026-03-21`
- Focus preset: `planner / executor / expression / session / sessionctx / statistics / kv / store / parser / table / privilege`
- Method: `gpt-5.4` relevance triage -> `gpt-5.4` worthy triage -> worktree-based `gpt-5.2` deep review at merged commit
- Verification note: no tests were run; this is static review only

## Funnel

- Merged commits in window: `7728`
- Pass1 candidates: `4684`
- Pass2 candidate rows: `3656`
- Unique PRs after deduplication: `3654`
- Relevance hits: `2581`
- Worthy hits: `1206`
- Deep review completed: `1206 / 1206`

## Final Counts

- `no_high_or_blocker`: `1060` PRs
- `has_high_or_blocker`: `146` PRs
- Unresolved findings after later-fix filtering: `158` findings across `146` PRs
- Severity split: `32` Blocker, `126` High
- PR split: `31` PRs with Blocker, `115` high-only PRs
- All kept findings have `later_fixed=false`: `True`
- PRs with multiple kept findings: `12`

## Module Families

- `planner`: `86` PRs, `93` findings
- `executor`: `75` PRs, `84` findings
- `expression`: `29` PRs, `31` findings
- `parser`: `26` PRs, `29` findings
- `other`: `20` PRs, `21` findings
- `session`: `20` PRs, `23` findings
- `sessionctx`: `20` PRs, `24` findings
- `store`: `14` PRs, `14` findings
- `table`: `11` PRs, `11` findings
- `privilege`: `10` PRs, `11` findings
- `statistics`: `10` PRs, `11` findings
- `kv`: `5` PRs, `5` findings

## Time Distribution

- `2023`: `11` PRs, `13` findings
- `2024`: `72` PRs, `78` findings
- `2025`: `48` PRs, `51` findings
- `2026`: `15` PRs, `16` findings

Peak quarters by PR count:
- `2024-Q3`: `21` PRs, `25` findings
- `2024-Q4`: `19` PRs, `21` findings
- `2024-Q1`: `17` PRs, `17` findings
- `2025-Q2`: `17` PRs, `18` findings
- `2024-Q2`: `15` PRs, `15` findings
- `2025-Q3`: `15` PRs, `16` findings

Peak months by PR count:
- `2024-03`: `8` PRs, `8` findings
- `2024-08`: `8` PRs, `8` findings
- `2024-11`: `8` PRs, `8` findings
- `2025-09`: `8` PRs, `8` findings
- `2024-05`: `7` PRs, `7` findings
- `2024-09`: `7` PRs, `10` findings
- `2024-06`: `6` PRs, `6` findings
- `2024-07`: `6` PRs, `7` findings
- `2024-12`: `6` PRs, `8` findings
- `2025-04`: `6` PRs, `6` findings

## Blocker PRs

- `#48010` `2023-10-26` types: fix the behavior of casting json string to integers (#48010)
- `#49421` `2023-12-15` parser, planner: fix embedded setOprStmt will be seen as SetOprSelectList item and lost its orderBy and Limit (#49421)
- `#49756` `2023-12-25` expression: pass const bool to all calls for expression.ConstItem (#49756)
- `#51329` `2024-02-28` planner: remove the limitation that predicates can't be pushed through `Projection` on `TableDual` (#51329)
- `#51546` `2024-03-07` planner: choose to generate optimal plans without plan cache rather than generating suboptimal plans and caching it when generating MVIndex plans (#51546)
- `#51901` `2024-03-21` util: skip always false DNF (#51901)
- `#51403` `2024-03-28` statistics: concurrency init stats (#51403)
- `#53395` `2024-05-20` planner: fix the wrong result caused by `year_col cmp out-of-range-uint` (#53395)
- `#53646` `2024-05-29` planner: fix small regression caused by #53094 (#53646)
- `#52780` `2024-06-03` expression: JSON_SCHEMA_VALID() (#52780)
- `#55185` `2024-08-06` planner: use code-gen to generate CloneForPlanCache method for Insert/Update/Delete (#55185)
- `#55631` `2024-09-20` planner: Removed check for prepared stmt during partition pruning (#55631)
- `#56682` `2024-10-28` executor: ignore foreign key error in `UPDATE/INSERT/DELETE ignore` (#56682)
- `#57260` `2024-11-11` planner: optimize the performance of PointPlan for Instance Plan Cache (#57260)
- `#57699` `2024-11-27` planner: provide a system view information_schema.tidb_plan_cache to allow users to see info in the cache (#57699)
- `#58033` `2024-12-13` executor: run idxlookup workers in a pool (#58033)
- `#58962` `2025-01-23` planner: remove redundant branches in the OR list (#58962)
- `#58945` `2025-02-11` privilege/privileges:  refactor the data struct for user privilege data (#58945)
- `#60048` `2025-03-26` table: use writable columns to avoid columns not match when add column DDL exec parallel with update DML (#60048)
- `#61187` `2025-05-21` planner: Handle NullEQ <=> in range columns partition pruning (#61187)
- `#61249` `2025-05-31` expression: deduplicate the args of IN function (#61249)
- `#61942` `2025-06-24` planner: support `explain [analyze] <plan_digest>` for `explain explore` (#61942)
- `#62175` `2025-07-03` codec: should also return the value even with error because the error may be ignored in the future (#62175)
- `#58401` `2025-07-17` planner/core: update missing virtual columns in `update` and `insert` (#58401)
- `#63059` `2025-09-03` planner: handle firstrow columns in aggregation predicate pushdown (#63059)
- `#63757` `2025-09-26` planner: fix nested IN expression rewriter bug (#63757)
- `#65084` `2025-12-18` executor: nonclustered partitioned table could miss updates due to duplicate _tidb_rowid from EXCHANGE PARTITION (#65084)
- `#65264` `2025-12-26` planner: derivePathStatsAndTryHeuristics can keep more MPP task (#65264)
- `#65544` `2026-02-02` planner: fix EXISTS subquery on cluster tables causing Column ID -1 (#65544)
- `#65799` `2026-02-04` planner, core: implement partial order TopN attach2Task and partial order flow (#65799)
- `#66031` `2026-02-27` parser,ast: support column masking grammar (#66031)

## Flat Findings

- PR `#48010` `2023-10-26` `Blocker` `High`: types: fix the behavior of casting json string to integers (#48010)
  Modules: pkg/types (ConvertJSONToInt string casting), pkg/types/datum.go (Datum.ConvertTo for uint/int targets), pkg/expression (CAST/CONVERT semantics baseline), pkg/executor / sessionctx flags (AllowNegativeToUnsigned gating in write statements), tests/integrationtest (expression/json cast coverage)
  What: JSON-string -> UNSIGNED integer conversion ignores `AllowNegativeToUnsigned` in write contexts and can silently wrap negatives to huge values (bypassing strict-mode overflow handling) because the JSON-string branch returns a negative `int64` that is later cast to `uint64` without range/flag checks.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-48010.json

- PR `#49421` `2023-12-15` `Blocker` `High`: parser, planner: fix embedded setOprStmt will be seen as SetOprSelectList item and lost its orderBy and Limit (#49421)
  Modules: executor, parser, planner
  What: Planner regression: `buildSetOpr()` breaks INTERSECT-precedence grouping when encountering a `*ast.SetOprSelectList` that has embedded `OrderBy/Limit`, which can cause the INTERSECT operator to be misrouted into the UNION/EXCEPT stage and executed as UNION (distinct) instead of INTERSECT.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-49421.json

- PR `#49756` `2023-12-25` `Blocker` `High`: expression: pass const bool to all calls for expression.ConstItem (#49756)
  Modules: pkg/expression, pkg/expression/aggregation
  What: `APPROX_PERCENTILE` percent constness was loosened to `ConstItem(false)` in TypeInfer, allowing parameter-dependent percent values under prepared plan cache. Because percent range validation only happens during TypeInfer (not on cached-plan reuse), later executions with different `?` can bypass the range check and feed invalid `percent` into executor percentile selection, potentially causing a panic (`percent <= 0`) or wrong semantics (`percent > 100`).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-49756.json

- PR `#51329` `2024-02-28` `Blocker` `High`: planner: remove the limitation that predicates can't be pushed through `Projection` on `TableDual` (#51329)
  Modules: planner
  What: Predicate pushdown through `Projection` on `TableDual` can change SQL semantics for mutable/non-deterministic projection expressions by evaluating them twice (once for the pushed-down filter, once for the projection output), so the returned values can violate the original predicate and/or side effects can execute extra times.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-51329.json

- PR `#51546` `2024-03-07` `Blocker` `High`: planner: choose to generate optimal plans without plan cache rather than generating suboptimal plans and caching it when generating MVIndex plans (#51546)
  Modules: expression, planner
  What: Planner can panic when extracting JSON array elements for MVIndex planning because `IsImmutableFunc` is too permissive (treats `*Column` etc as “immutable”), leading to `EvalJSON(..., chunk.Row{})` on row-dependent expressions.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-51546.json

- PR `#51901` `2024-03-21` `Blocker` `High`: util: skip always false DNF (#51901)
  Modules: pkg/util/ranger, pkg/executor
  What: Prepared plan cache unsoundness: skipping empty-range DNF items can prune parameter-dependent OR branches from `AccessCondition` without setting `SkipPlanCache`, so a cached plan may later return incomplete/incorrect results when parameters change.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-51901.json

- PR `#51403` `2024-03-28` `Blocker` `High`: statistics: concurrency init stats (#51403)
  Modules: statistics, config
  What: `performance.enable-stats-cache-mem-quota=false` selects a map-backed stats cache that is not safe for concurrent writes; with `performance.concurrently-init-stats=true` (default in this commit), init-stats spawns multiple goroutines that call `StatsCache.Put` concurrently, which can trigger `fatal error: concurrent map writes` during bootstrap.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-51403.json

- PR `#53395` `2024-05-20` `Blocker` `High`: planner: fix the wrong result caused by `year_col cmp out-of-range-uint` (#53395)
  Modules: pkg/util/ranger, pkg/planner/core
  What: YEAR range building change can make `YEAR_col >= out-of-range-uint64` return wrong results: the new cap-to-`MaxInt64` avoids overflow but, for `>=`, leads to a clamped YEAR constant (2155) with no operator tightening, yielding range `[2155,+inf)` while the executor comparison against the real unsigned constant is always false.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-53395.json

- PR `#53646` `2024-05-29` `Blocker` `High`: planner: fix small regression caused by #53094 (#53646)
  Modules: planner
  What: PointGet plan-cache hit path skips `checkPreparedPriv` (privilege + table-lock checks) once a cached PointGet executor exists (`stmt.PointGet.Executor != nil`), enabling prepared EXECUTE to proceed without runtime privilege/table-lock enforcement on cache hits.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-53646.json

- PR `#52780` `2024-06-03` `Blocker` `High`: expression: JSON_SCHEMA_VALID() (#52780)
  Modules: executor, expression, parser, planner, sessionctx
  What: `JSON_SCHEMA_VALID()` introduces a dependency (`github.com/qri-io/jsonschema`) whose default behavior can perform external schema resolution via `$ref` (HTTP/file) and uses a package-global mutable registry implemented with unguarded maps. When invoked from SQL, this enables SSRF/local file access attempts and creates a realistic risk of TiDB panics/data races under concurrent use; the function also uses `context.Background()` so any external fetch cannot be canceled/timed out.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-52780.json

- PR `#55185` `2024-08-06` `Blocker` `High`: planner: use code-gen to generate CloneForPlanCache method for Insert/Update/Delete (#55185)
  Modules: expression, planner
  What: `(*expression.Assignment).Clone()` can panic when cloning lazy-error `ON DUPLICATE` assignments because it unconditionally calls `a.Expr.Clone()` even though `Expr` is allowed to be nil when `LazyErr` is set (deferred `ErrSubqueryMoreThan1Row` path). This breaks `Insert.CloneForPlanCache` (and any other use of `util.CloneAssignments`) under instance plan cache.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-55185.json

- PR `#55631` `2024-09-20` `Blocker` `High`: planner: Removed check for prepared stmt during partition pruning (#55631)
  Modules: pkg/planner/core/rule_partition_processor.go, pkg/planner/core/find_best_task.go
  What: Prepared plan-cache correctness regression: removing the plan-cache-aware const check allows `PartitionPruning` to evaluate parameter markers during prepared-plan building, so global index scans can get a constant `_tidb_pid IN (...)` partition filter derived from the *first* execution’s parameter values and then cache that plan, yielding wrong results for later executions with different parameter values.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-55631.json

- PR `#55631` `2024-09-20` `Blocker` `High`: planner: Removed check for prepared stmt during partition pruning (#55631)
  Modules: pkg/planner/core/rule_partition_processor.go, pkg/planner/core/find_best_task.go
  What: Crash regression risk: removing the const-expression guard means `extractDataForPrune` can attempt to `EvalInt` an expression that still contains column references using `chunk.Row{}` (nil chunk), which can panic.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-55631.json

- PR `#56682` `2024-10-28` `Blocker` `High`: executor: ignore foreign key error in `UPDATE/INSERT/DELETE ignore` (#56682)
  Modules: executor, planner
  What: Referred-FK (parent-side) UPDATE/ON-DUP-UPDATE is checked against the wrong row image in IGNORE mode, so 1451 violations can be missed and applied (no warning), breaking FK integrity.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-56682.json

- PR `#57260` `2024-11-11` `Blocker` `High`: planner: optimize the performance of PointPlan for Instance Plan Cache (#57260)
  Modules: planner
  What: `FastClonePointGetForPlanCache` clears `Handle` and does not copy `IndexValues`, but rebuild only restores parameter-driven slots; point-get plans with any literal key component can lose required key values on instance plan cache hits, causing panics or wrong results.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-57260.json

- PR `#57699` `2024-11-27` `Blocker` `High`: planner: provide a system view information_schema.tidb_plan_cache to allow users to see info in the cache (#57699)
  Modules: executor, planner, sessionctx
  What: `information_schema.tidb_plan_cache` exposes cached SQL text and parameter values for all users without privilege/visibility controls (no `PROCESS` gating or per-user filtering), enabling cross-user information disclosure.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-57699.json

- PR `#58033` `2024-12-13` `Blocker` `High`: executor: run idxlookup workers in a pool (#58033)
  Modules: executor
  What: Deadlock risk when index lookup concurrency is 1: the index scan runs as a task in the same worker pool whose `MaxWorkers` is also 1, so table lookup tasks can’t run until the index scan finishes. If the index scan produces more than the `resultCh` buffer (default 50) it blocks on `resultCh` while `Next()` is blocked on `task.doneCh`, causing an indefinite hang.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-58033.json

- PR `#58962` `2025-01-23` `Blocker` `High`: planner: remove redundant branches in the OR list (#58962)
  Modules: planner
  What: `removeRedundantORBranch` removes “duplicates” in OR lists using `string(expr.HashCode())` as the sole identity key. `HashCode()` is not a full identity for expressions with differing `RetType` (notably `CAST`, and also constants/collation), so this can incorrectly drop non-equivalent OR branches and change SQL semantics.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-58962.json

- PR `#58945` `2025-02-11` `Blocker` `High`: privilege/privileges:  refactor the data struct for user privilege data (#58945)
  Modules: pkg/privilege/privileges, pkg/domain
  What: `Handle.UpdateAll()` stores a fully-loaded privilege cache without initializing `globalVars` and without enforcing deterministic per-user ordering for tables that use first-match resolution (`global_priv`, `tables_priv`, `columns_priv`), so privilege/auth matching can select the wrong host-pattern row after `UpdateAll()`.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-58945.json

- PR `#60048` `2025-03-26` `Blocker` `High`: table: use writable columns to avoid columns not match when add column DDL exec parallel with update DML (#60048)
  Modules: table
  What: Potential panic regression for partitioned tables: switching `partitionedTable`’s eval buffer from `Cols()` to `WritableCols()` makes the in-memory row layout incompatible with INSERT rows that include `_tidb_rowid` during ADD COLUMN `StateWriteOnly`. The `_tidb_rowid` datum can be written into the new write-only column slot during `locatePartition`, and for some types this triggers a runtime panic in `chunk.MutRow.SetDatum` due to too-short backing buffers.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-60048.json

- PR `#61187` `2025-05-21` `Blocker` `High`: planner: Handle NullEQ <=> in range columns partition pruning (#61187)
  Modules: pkg/planner/core/rule_partition_processor.go, pkg/planner/core/casetest/partition/partition_pruner_test.go
  What: Partition pruning can panic on `const <=> col` (NullEQ) because operand-swapping calls `opposite()` with `ast.NullEQ`, but `opposite()` doesn’t handle `ast.NullEQ`.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-61187.json

- PR `#61249` `2025-05-31` `Blocker` `High`: expression: deduplicate the args of IN function (#61249)
  Modules: pkg/expression/builtin_other.go
  What: `IN` integer-arg dedup can drop semantically distinct unsigned constants that collide in `int64` value with signed negatives (e.g. `18446744073709551615` vs `-1`), causing `IN`/`NOT IN` to return incorrect results (values explicitly present in the list can be treated as absent).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-61249.json

- PR `#61942` `2025-06-24` `Blocker` `High`: planner: support `explain [analyze] <plan_digest>` for `explain explore` (#61942)
  Modules: pkg/parser, pkg/planner/core
  What: SQL injection / privilege bypass via new `EXPLAIN <plan_digest>` path: `planDigest` (from `stringLit`) is interpolated directly into internal SQL executed through a system session, allowing crafted digests to alter the internal statement-summary query.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-61942.json

- PR `#62175` `2025-07-03` `Blocker` `High`: codec: should also return the value even with error because the error may be ignored in the future (#62175)
  Modules: pkg/util/codec, pkg/executor/aggregate, tests/integrationtest
  What: `HashGroupKey` now returns non-nil `buf` on decimal/time encode error (to survive when the error is downgraded), but it still returns immediately on the *first* row error. Because `GetGroupKey` may downgrade truncation errors to warnings/ignored via `ErrCtx`, HashAgg can proceed with a partially-filled `groupKey` (rows after the first error remain empty/missing suffix), leading to incorrect grouping/deduplication for chunks with more than one row.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-62175.json

- PR `#58401` `2025-07-17` `Blocker` `High`: planner/core: update missing virtual columns in `update` and `insert` (#58401)
  Modules: pkg/planner/core, pkg/executor
  What: Blocker: `resolveGeneratedColumns` can make `InsertExec.OnDuplicate` non-empty even when the SQL has no `ON DUPLICATE KEY UPDATE` (via `ON UPDATE` columns), causing normal INSERT/INSERT IGNORE to take the ON DUPLICATE execution path and potentially suppress duplicate-key errors (no-op update).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-58401.json

- PR `#63059` `2025-09-03` `Blocker` `High`: planner: handle firstrow columns in aggregation predicate pushdown (#63059)
  Modules: planner
  What: Unsound aggregation predicate pushdown for `FIRST_ROW` output columns: predicates on `FIRST_ROW`/`ANY_VALUE` outputs can be pushed below aggregation, filtering rows before computing other aggregates and changing their results.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-63059.json

- PR `#63757` `2025-09-26` `Blocker` `High`: planner: fix nested IN expression rewriter bug (#63757)
  Modules: planner
  What: The fix only forces scalar-context rewriting for single-element non-subquery `IN` lists. Nested `IN`-subquery expressions used as a value still break when the outer `IN` list has more than one element: the inner `IN (subquery)` is rewritten with `asScalar=false` and produces no scalar expression on `ctxStack`, but `Leave` still calls `inToExpression`, which can index `ctxStack` with a negative offset and panic.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-63757.json

- PR `#65084` `2025-12-18` `Blocker` `High`: executor: nonclustered partitioned table could miss updates due to duplicate _tidb_rowid from EXCHANGE PARTITION (#65084)
  Modules: pkg/executor
  What: Partitioned nonclustered tables: change disables the “skip multiple changes on same row” gate globally, so duplicate handles no longer suppress repeat updates; this can cause the same physical row to be updated multiple times (e.g., UPDATE JOIN duplicate matches) with stale `oldData`, risking wrong results and index inconsistency.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-65084.json

- PR `#65264` `2025-12-26` `Blocker` `High`: planner: derivePathStatsAndTryHeuristics can keep more MPP task (#65264)
  Modules: pkg/planner/core
  What: `derivePathStatsAndTryHeuristics` can append a nil TiFlash access path when MPP is enforced but the table has no TiFlash access path, leading to planner panics when later code iterates `ds.PossibleAccessPaths`.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-65264.json

- PR `#65544` `2026-02-02` `Blocker` `High`: planner: fix EXISTS subquery on cluster tables causing Column ID -1 (#65544)
  Modules: pkg/planner/core, pkg/planner/core/operator/logicalop, pkg/planner/core/operator/physicalop, tests/integrationtest/planner/core/issuetest
  What: Cluster tables can now have `HandleCols == nil`, but the planner still records this nil handle in `handleHelper` as a `[]util.HandleCols{nil}` entry. `LogicalLock`/`PhysicalLock`/`SelectLockExec` assume handle entries are non-nil, so `SELECT ... FOR UPDATE/SHARE` on cluster tables can panic.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-65544.json

- PR `#65799` `2026-02-04` `Blocker` `High`: planner, core: implement partial order TopN attach2Task and partial order flow (#65799)
  Modules: planner, sessionctx
  What: Partial-order TopN plan can produce wrong query results because the planner pushes a normal cop `Limit(count=Count+Offset)` on a prefix-index-ordered stream without any protocol/executor mechanism to include the extra rows needed for correctness within the boundary prefix.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-65799.json

- PR `#66031` `2026-02-27` `Blocker` `High`: parser,ast: support column masking grammar (#66031)
  Modules: pkg/parser, pkg/parser/ast, pkg/planner/core, pkg/executor
  What: New masking policy statements become parseable, but are not handled by planner/executor, so they can silently succeed without applying any masking (DDL) or return empty results (SHOW).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-66031.json

- PR `#43582` `2023-05-10` `High` `High`: privilege, server: support LDAP authentication (#43582)
  Modules: server, privilege, session, sessionctx/variable, parser, executor
  What: LDAP SASL auth method (`saslAuthMethod`) is read without synchronization during authentication, but written under lock via sysvar updates; this is a data race.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-43582.json

- PR `#47063` `2023-10-23` `High` `High`: *: unified the cancel/kill logic in tidb (#47063)
  Modules: executor, expression, session, sessionctx, statistics, store
  What: BRIE kill monitoring uses `==` to compare the error returned by `SQLKiller.HandleSignal()` against `exeerrors.ErrQueryInterrupted`, but `HandleSignal()` returns a stack-wrapped error, so the comparison is always false and BRIE tasks won’t be canceled on kill.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-47063.json

- PR `#47764` `2023-10-23` `High` `High`: executor: optimize (left outer) (anti) semi join which has no other condition (#47764)
  Modules: pkg/executor
  What: New `hashRowContainer.GetOneMatchedRow` can panic on hash collisions when the underlying `RowContainer` has not spilled, due to dereferencing `c.chkBuf` even when it is nil.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-47764.json

- PR `#47946` `2023-10-25` `High` `High`: types: fix issue that we can insert negative value to unsinged float column sometimes (#47946)
  Modules: pkg/executor, pkg/expression
  What: `CAST(string AS REAL/FLOAT(M,D))` in expression layer no longer downgrades overflow/out-of-range errors to warnings (per StmtCtx flags) after the `ProduceFloatWithSpecifiedTp` refactor, causing SELECT/CAST to return errors where TiDB expects warning+clipped result.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-47946.json

- PR `#48010` `2023-10-26` `High` `High`: types: fix the behavior of casting json string to integers (#48010)
  Modules: pkg/types (ConvertJSONToInt string casting), pkg/types/datum.go (Datum.ConvertTo for uint/int targets), pkg/expression (CAST/CONVERT semantics baseline), pkg/executor / sessionctx flags (AllowNegativeToUnsigned gating in write statements), tests/integrationtest (expression/json cast coverage)
  What: Untrimmed sign detection in JSON-string->int conversion breaks negative values with leading whitespace, routing them through unsigned parsing and producing 0/overflow instead of the correct negative/wrapped result.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-48010.json

- PR `#48504` `2023-11-16` `High` `High`: parser/executor: admin show/set bdr role (#48504)
  Modules: executor, parser, planner
  What: `ADMIN SHOW BDR ROLE` is misclassified as non-read-only, so it is blocked in cluster read-only modes.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-48504.json

- PR `#48504` `2023-11-16` `High` `High`: parser/executor: admin show/set bdr role (#48504)
  Modules: executor, parser, planner
  What: `ADMIN SHOW BDR ROLE` executor is not retry-safe: it appends rows inside a retryable `RunInNewTxn` closure, risking duplicated output on internal txn retry.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-48504.json

- PR `#46632` `2023-11-30` `High` `High`: *: support serialization and deserialization of aggregate function for spill (#46632)
  Modules: pkg/executor/aggfuncs, pkg/util/serialization
  What: Spill serialization for `JSON_ARRAYAGG`/`JSON_OBJECTAGG` cannot handle SQL NULL (represented as `nil`), because the `interface{}` serializer/deserializer has no `nil` encoding. This makes spill paths for these aggregates crash/fail when NULLs are present.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-46632.json

- PR `#49676` `2023-12-25` `High` `High`: expression: refactor like and ilike cache logic (#49676)
  Modules: pkg/expression
  What: Regression risk: switching `buildStringParam`/`buildIntParam` const-detection to `ConstItem(false)` makes ParamMarker-based params “const”, which in vectorized `ILIKE` causes in-place lowercasing of the pattern string. Because ParamMarker values are fetched from `PlanCacheParams` and strings/datums are zero-copy, this can mutate `EXECUTE ... USING @var` session user-variable values (and/or shared parameter bytes) as a side effect of evaluation.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-49676.json

- PR `#47514` `2023-12-28` `High` `High`: executor: fix pointGet and batchPointGet for global index (#47514)
  Modules: pkg/executor, pkg/planner/core
  What: `Point_Get` on a global index learns the real partition physical ID only after reading the index value, but txn_scope validation runs only once (in `Open`) against the plan’s `partInfo` / logical table ID. It never re-validates txn_scope against the decoded partition ID used for the row-key read.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-47514.json

- PR `#49852` `2024-01-05` `High` `High`: planner, statistics: support estimation for mv index access path (#49852)
  Modules: pkg/planner/cardinality, pkg/planner/core, pkg/statistics
  What: `expBackoffEstimation` regression: index-stats fallback can leave `selectivity` unset (0) on successful `GetRowCountByIndexRanges`, causing exponential-backoff selectivity/rowcount to collapse to 0.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-49852.json

- PR `#48872` `2024-01-09` `High` `Medium`: executor: refine sort spill (#48872)
  Modules: pkg/executor/sortexec, pkg/util/chunk
  What: Spill-to-disk failures can be missed due to async error recording + unconditional `spillTriggered` transition, allowing external sort to treat partially-spilled partitions as complete and potentially return truncated/incorrect results without error.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-48872.json

- PR `#50341` `2024-01-17` `High` `High`: importinto: executor part of import from select (#50341)
  Modules: pkg/executor/import_into.go, pkg/executor/importer/import.go, pkg/executor/importer/table_import.go, pkg/executor/importer/engine_process.go, pkg/executor/importer/chunk_process.go
  What: `IMPORT INTO ... FROM SELECT` does not pin a single statement timestamp across parallel encoding workers, so `NOW()/CURRENT_TIMESTAMP`-based defaults / generated columns / `SET` clause expressions can evaluate to different times within the same statement when `WITH thread > 1`.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-50341.json

- PR `#50050` `2024-01-22` `High` `High`: statistics/handle/usage: modify the `SessionIndexUsageCollector` to implement new index usage design (#50050)
  Modules: statistics, session, sessionctx, executor, planner
  What: `collector.globalCollector.SpawnSession()` fails to propagate the global `closeCh` to `sessionCollector`, so `SendDeltaSync()` cannot observe closure and may block forever once buffers fill after the worker stops (notably via `SessionIndexUsageCollector.Flush()` on session close).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-50050.json

- PR `#50768` `2024-01-29` `High` `High`: hint: refactor PlanHints (#50768)
  Modules: planner
  What: Skew distinct-aggregation rewrite no longer propagates the full aggregation hint set to both newly created `LogicalAggregation` nodes, so aggregation hints can be partially dropped/ignored when the rewrite triggers.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-50768.json

- PR `#50852` `2024-02-01` `High` `High`: importinto: support import from stale read (#50852)
  Modules: executor, planner
  What: `tidb_snapshot` write prohibition for `IMPORT INTO` is only enforced on the no-result executor path; `IMPORT INTO ... FROM 'path'` returns job-info rows and appears to bypass the `tidb_snapshot` guard, allowing writes in history-read mode.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-50852.json

- PR `#49074` `2024-02-04` `High` `High`: executor: Support spill of parallel hash aggregate (#49074)
  Modules: executor, sessionctx
  What: Parallel hash-agg spill temp files may leak on early `Close()` (query cancel/timeout) because `Close()` can run concurrently with the fetcher goroutine and closes only spill files already registered in `spillHelper`; registration happens later in `fetchChildData` defer, and there is no guaranteed post-registration cleanup.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-49074.json

- PR `#51113` `2024-02-21` `High` `High`: util: Adjust binary collate behavior to do pattern matching in bytes (#51113)
  Modules: executor, privilege
  What: Privilege wildcard matching for `mysql.db.DB` (utf8mb4) is changed to byte-wise (`DoMatchBinary`), so `_` matches 1 byte instead of 1 UTF-8 character; this can cause incorrect privilege DB-pattern matches for multibyte database names.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-51113.json

- PR `#51538` `2024-03-14` `High` `High`: sessionctx,executor: support div_precision_increment sysvar (#51538)
  Modules: executor, expression, planner, sessionctx, store
  What: Plan cache correctness hole: `div_precision_increment` affects DECIMAL division/AVG type inference and rounding, but is not part of the plan cache key and `SET div_precision_increment` does not invalidate the session plan cache. Cached plans can therefore produce stale precision/rounding after the sysvar changes.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-51538.json

- PR `#51770` `2024-03-15` `High` `High`: txn: enable pipelined dml by hint (#51770)
  Modules: pkg/planner, pkg/session, pkg/sessionctx/variable
  What: `SET_VAR(tidb_dml_type=bulk)` becomes hint-updatable but can leak into the next statement’s txn-mode selection because hint restores happen after `PrepareTxnCtx`, potentially disabling pessimistic-auto-commit for a subsequent statement.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-51770.json

- PR `#51652` `2024-03-16` `High` `Medium`: txn: save RPCs for Pipelined-DML (#51652)
  Modules: executor, kv, session, store, table
  What: Potential semantic gap: pipelined DML switches lazy dup-key existence checks to `MemBuffer.GetLocal`, which ignores flushed (remote-buffer) mutations; this can miss duplicate-key detection across multiple flush generations in large/chunked inserts.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-51652.json

- PR `#51497` `2024-03-20` `High` `High`: *: Revert "add super privilege check for some admin commands (#51497)" (#51938)
  Modules: planner
  What: Privilege bypass: `ADMIN PLUGINS ENABLE/DISABLE` no longer requires `SUPER` because `buildAdmin` returns before appending the `mysql.SuperPriv` visitInfo, and the executor doesn’t enforce privileges either.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-51497.json

- PR `#50747` `2024-03-27` `High` `High`: executor: Support parallel sort spill (#50747)
  Modules: pkg/executor/sortexec, pkg/util/chunk, pkg/executor/aggregate
  What: Parallel sort spill triggered during result generation adjusts memory tracking as if input chunks were released, but keeps `sortedRowsIters` (and thus `chunk.Row` → `*Chunk`) alive until executor teardown, defeating spill’s purpose and potentially bypassing memory-quota enforcement.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-50747.json

- PR `#52451` `2024-04-11` `High` `Medium`: expression: `BuildContext` read location from `EvalContext` instead of `SessionVars` (#52451)
  Modules: expression, planner, session, table
  What: `SET_VAR(time_zone=...)` can diverge `SessionVars.TimeZone` from `EvalContext.Location()` (StmtCtx TypeCtx) during a statement; this commit moves some timezone-sensitive behavior (CURRENT_TIMESTAMP generation + TIMESTAMP default conversions) to use `EvalContext.Location()`, so those behaviors may ignore the hinted `time_zone` within the statement.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-52451.json

- PR `#52716` `2024-04-24` `High` `Medium`: expression: remove `InInsertOrUpdate` in `BuildExpression` (#52716)
  Modules: pkg/expression/context, pkg/expression/contextsession, pkg/table
  What: `CastValue/CastColumnValue` now always applies `handleZeroDatetime()` and returns its strict-mode errors **before** `tc.HandleTruncate()` and `forceIgnoreTruncate` can downgrade/ignore them. This can make SELECT/read paths (which intentionally set `TruncateAsWarning` and call `CastValue(..., forceIgnoreTruncate=true)`) unexpectedly fail with hard errors for zero/invalid date/datetime/timestamp casts.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-52716.json

- PR `#52971` `2024-05-07` `High` `Medium`: *: global index support `index_merge` and `mem_index_merge` (#52971)
  Modules: executor, kv, planner
  What: `IndexMergeReaderExecutor.Open` ignores `IndexRangesToKVRanges` errors for global-index partial plans, risking a nil-pointer panic.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-52971.json

- PR `#52511` `2024-05-11` `High` `High`: point-get: calculate the row level checksum on the real time to avoid incorrect checksum caused by schema change (#52511)
  Modules: executor, expression, tablecodec
  What: `fillRowChecksum` includes virtual generated (not stored) columns in its “missing column” default-fill path, which is incompatible with the row-level checksum algorithm (write path skips these columns). For `VIRTUAL NOT NULL` generated columns (supported by TiDB), `table.GetColOriginDefaultValue` can error or return a non-null placeholder, causing `tidb_row_checksum()` to fail or produce an inconsistent checksum.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-52511.json

- PR `#51191` `2024-05-13` `High` `High`: expression: fix charset conversion warning and error behavior (#51191)
  Modules: pkg/expression
  What: `from_binary`’s new “warn instead of error” mode for explicit CAST is not preserved across expression pushdown, so TiDB vs TiKV can diverge on invalid-byte conversions.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-51191.json

- PR `#53265` `2024-05-16` `High` `High`: session: set sql security of `tidb_mdl_view` to 'invoker' (#53265)
  Modules: pkg/session, pkg/infoschema/test/clustertablestest
  What: Security fix may not apply to existing clusters: the bootstrap/upgrade path does not force-replace `mysql.tidb_mdl_view` for clusters already at bootstrap version >=197, so adding `SQL SECURITY INVOKER` in the bootstrap DDL can be a no-op for those upgrades.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-53265.json

- PR `#53039` `2024-05-28` `High` `High`: br: add more options for br sql client (#53039)
  Modules: executor, parser
  What: `BRIEOptionEncryptionKeyFile` restores as `ENCRYPTION_KEY_FILE` but the parser only accepts `ENCRYPTION_KEYFILE`, making restored/showed BRIE SQL invalid.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-53039.json

- PR `#53974` `2024-06-18` `High` `High`: *: remove `ExtraPidCol` and replace it with `ExtraPhysTblIDCol` (#53974)
  Modules: executor, expression, parser, planner, store
  What: High-risk off-by-one in unistore coprocessor IndexScan: `closure_exec.go` may append `ExtraPhysTblID` (tid from key) even when `DecodeIndexKV` already produced a pid value for global indexes. This can shift the last-column values across rows and cause wrong partition handles, leading to incorrect reads/locks/results for multi-row global index scans on EmbedUnistore.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-53974.json

- PR `#54083` `2024-06-18` `High` `High`: planner: fix the issue that statement-level hints in sub-queries of Insert/Replace can not take effect (#54083)
  Modules: pkg/util/hint, pkg/session/test, pkg/bindinfo
  What: Conflicting MEMORY_QUOTA hints on `INSERT/REPLACE` (one after INSERT/REPLACE and one inside the SELECT subquery) can now be applied inconsistently with warnings: the code warns that the SELECT-side hint is ignored as conflicting/duplicated, but the extracted hint list plus ParseStmtHints “last one wins” logic makes the SELECT-side hint take effect.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-54083.json

- PR `#54163` `2024-06-25` `High` `Medium`: planner: remove the same projection when to InjectExtraProjection (#54163)
  Modules: pkg/planner/core/rule_inject_extra_projection.go
  What: `InjectProjBelowAgg` now removes “same projection” by reusing an existing projection column whenever expressions are structurally equal (`Expression.Equal`). This can unintentionally coalesce multiple invocations of mutable/side-effect scalar functions across agg args vs GROUP BY / ORDER BY items, changing evaluation count and observable side effects.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-54163.json

- PR `#53931` `2024-06-27` `High` `High`: rowcodec: E2E checksum introduce bytes-level checksum mechanism (#53931)
  Modules: executor, store, table, tablecodec
  What: `rowcodec.Encoder.Encode` stops returning any encoded bytes when `encodeRowCols` reports errors, breaking the EncodeRow contract and creating a risk of persisting empty row values when errors are downgraded/ignored by `StmtCtx.HandleError`.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-53931.json

- PR `#54179` `2024-06-27` `High` `High`: planner: The length function could not be substitute when collation of mapped column is utfxxx_bin (#54179)
  Modules: expression, planner
  What: Outer-join constant propagation (`PropConstOverOuterJoin`) bypasses the new constant-propagation safety flag, so the PR’s fix to prevent substituting constants into `length(col)` under PAD SPACE collations can still be bypassed in join-condition propagation, reintroducing the same class of incorrect simplification (wrong SQL semantics) for outer-join predicate propagation.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-54179.json

- PR `#53494` `2024-07-03` `High` `High`: *: implement auth plugin support in the extension framework (#53494)
  Modules: executor, parser, privilege
  What: Extension auth plugin authorization is not enforced in `RequestVerificationWithUser` / definer-privilege paths (e.g., SECURITY DEFINER view execution), allowing operations to proceed under SQL grants without plugin `VerifyPrivilege`/`VerifyDynamicPrivilege` checks (including a potential self-bypass when the definer is also the current session user).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-53494.json

- PR `#54588` `2024-07-12` `High` `High`: planner: add more test cases for Plan.Clone (#54588)
  Modules: planner, executor
  What: `CopTask.physPlanPartInfo` becoming a pointer introduces a nil-propagation bug in the admin index-lookup plan builder: `buildPhysicalIndexLookUpReader` constructs a `CopTask` without `physPlanPartInfo`, leading to `PhysicalIndexLookUpReader.PlanPartInfo=nil`, which can error during executor build for global indexes under dynamic partition pruning.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-54588.json

- PR `#54527` `2024-07-18` `High` `High`: server: add protocol support for lazy cursor fetch (#54527)
  Modules: executor, session, sessionctx
  What: Lazy-cursor path drops `TryDetach`’s `detached` flag on error, so `finishStmt`/detach errors can be swallowed and the server may incorrectly fall back to eager cursor execution on an RS that the session layer says is no longer usable.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-54527.json

- PR `#54527` `2024-07-18` `High` `High`: server: add protocol support for lazy cursor fetch (#54527)
  Modules: executor, session, sessionctx
  What: `execStmtResult.TryDetach` leaks a cursor handle when `finishStmt` fails (creates a cursor, but doesn’t close it on error), which can pin `MinStartTS`/GC and leak cursor-tracker entries.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-54527.json

- PR `#54553` `2024-07-23` `High` `High`: *: support tidb_redact_log for explain (#54553)
  Modules: executor, expression, parser, planner, table
  What: `EXPLAIN` redaction is bypassed for window function descriptors: `PhysicalWindow` formats `WindowFuncDesc` with `RedactLogDisable`, so window-function literal arguments are not redacted when `tidb_redact_log` is enabled.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-54553.json

- PR `#54800` `2024-07-23` `High` `High`: executor: support detaching the `IndexReader` and `IndexLookUp` (#54800)
  Modules: executor
  What: IndexLookUp detachment is effectively a no-op: `indexLookUpExecutorContext.Detach()` returns `iluCtx` instead of `newCtx`, so session-bound contexts (DistSQL/Ranger/Expr/BuildPB) are not converted to static contexts for the detached executor.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-54800.json

- PR `#54902` `2024-07-30` `High` `High`: *: remove limiting process id for auto analyze (#54902)
  Modules: executor, planner, statistics
  What: `execAnalyzeStmt` recovers panics but does not convert them into an error or rethrow; because returns are unnamed, a panic results in `error == nil`, causing auto-analyze to be counted as success and hiding real failures.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-54902.json

- PR `#55023` `2024-08-08` `High` `Medium`: server: add tidb_enable_shared_lock_promotion to support for share lock upgrade (#55023)
  Modules: executor, parser, planner, sessionctx
  What: `tidb_enable_shared_lock_promotion` enables `SELECT ... FOR SHARE` to acquire pessimistic locks, but the planner does not classify promoted `FOR SHARE` as `forUpdateRead`. This can skip planner-side “current read” safety behaviors (latest-index validation and explicit-txn isolation-read/TiFlash filtering) even though executor-side snapshot selection for `PhysicalLock` uses `GetStmtForUpdateTS()`.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-55023.json

- PR `#55259` `2024-08-09` `High` `High`: parser: support GLOBAL IndexOption (#55259)
  Modules: executor, parser
  What: `GLOBAL` specified via column options (`PRIMARY KEY GLOBAL` / `UNIQUE KEY GLOBAL`) is not wrapped as a TiDB special comment during `RestoreTiDBSpecialComment` restore, unlike `IndexOption.Global`. This makes “translate TiDB feature syntax to special comment” incomplete for the column-option syntax.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-55259.json

- PR `#55463` `2024-08-19` `High` `High`: planner/core: pushdown TiFlash store type check to ColumnToProto (#55463)
  Modules: executor, planner, table
  What: TiFlash `PartitionTableScan` PB construction fails to set `mysql.GeneratedColumnFlag` for virtual generated columns after the store-type check was pushed down into `util.ColumnToProto`, because `BuildPartitionTableScanFromInfos` hardcodes `isTiFlashStore=false`. This can break TiFlash placeholder-column behavior on the dynamic partition prune path.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-55463.json

- PR `#55418` `2024-08-20` `High` `High`: executor: fix query hang when sorted column is constant (#55418)
  Modules: pkg/executor/sortexec
  What: Sort/TopN key construction skips constant BY items without also filtering/re-aligning `keyCmpFuncs` and `Desc` flags, so comparisons can use the wrong compare func / sort direction whenever a constant ORDER BY item is not trailing.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-55418.json

- PR `#55550` `2024-08-22` `High` `High`: *: Vector Data types and Functions (#55550)
  Modules: executor, expression, kv, parser, planner, table
  What: VectorFloat32 implementations of `MAX/MIN` (`maxMin4VectorFloat32`) and `FIRST_ROW` (`firstRow4VectorFloat32`) do not implement spill serialization/deserialization. In hash-aggregation spill mode this will produce malformed spill chunks and/or panic during restore (nil partial result slices), causing query failure or wrong results.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-55550.json

- PR `#55408` `2024-08-29` `High` `High`: copr: optimize the balance implement for balanceBatchCopTask (#55408)
  Modules: pkg/store/copr
  What: `slices.SortFunc` comparator for `tasksForPartitions` is not a strict weak ordering when partitions have no tasks: it returns `-1` for both `(empty, empty)` and the reverse, which violates the sort contract and can cause undefined behavior and potential CPU blow-ups when many partitions have empty ranges.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-55408.json

- PR `#55634` `2024-08-29` `High` `High`: *: move annotated attributes of AST node to resolve.Context (#55634)
  Modules: executor, expression, parser, planner, session
  What: High risk of unbounded `resolve.Context.tableNames` growth (memory leak) for prepared statements: planner code adds new `*ast.TableName` alias clones into the shared `ResolveCtx` map during planning (`ExtractTableList(asName=true)` and `updatableTableListResolver`). Since `PlanCacheStmt.ResolveCtx` is persisted and reused across executions, repeated optimizations can retain an ever-growing number of cloned table-name objects.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-55634.json

- PR `#55691` `2024-09-02` `High` `High`: planner: introduce hashEquals for expression.Column/collationInfo/fieldType (#55691)
  Modules: expression, parser, planner
  What: `expression.Column`’s new `Hash64`/`Equals` are inconsistent for `RetType`: hashing uses strict `FieldType.Hash64`, but equality uses relaxed `FieldType.Equal`, so two columns can compare equal while producing different hashes.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-55691.json

- PR `#54804` `2024-09-05` `High` `High`: pkg/parser: support SWITCH_GROUP syntax for runaway watch (#54804)
  Modules: executor, parser, session
  What: `SHOW CREATE RESOURCE GROUP` / I_S output renders `SWITCH_GROUP(<name>)` without identifier quoting, so valid group names that require backticks produce invalid SQL.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-54804.json

- PR `#54804` `2024-09-05` `High` `Medium`: pkg/parser: support SWITCH_GROUP syntax for runaway watch (#54804)
  Modules: executor, parser, session
  What: Bootstrap adds new columns to runaway watch system tables, but writes use `INSERT ... VALUES` positional inserts; this risks mixed-version rolling upgrade breakage (old binaries inserting fewer values after schema upgrade).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-54804.json

- PR `#55793` `2024-09-09` `High` `High`: planner: introduce hashEquals interface for expression.Expression (#55793)
  Modules: expression, planner
  What: `Column.Hash64`/`Column.Equals` can violate the HashEquals contract (equal objects must hash the same) because `Equals` uses `FieldType.Equal` (partial comparison) while `Hash64` uses `FieldType.Hash64` (full-field hashing).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-55793.json

- PR `#55147` `2024-09-24` `High` `High`: runaway: Add processed_keys and request_ru to check (#55147)
  Modules: executor, parser, session, store
  What: System-table schema upgrade gap: adds `rule` column to runaway tables without bumping bootstrap version, so clusters already at `version214` on the parent commit won’t auto-upgrade and will be left with a schema that the new runtime SQL expects.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-55147.json

- PR `#56115` `2024-09-25` `High` `High`: *: AnalyzeV2 supports processing special global indexes (#56115)
  Modules: executor, planner, statistics
  What: AnalyzeV2: `ANALYZE TABLE ... INDEX` with an empty index list becomes a no-op when all public indexes are special global indexes.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-56115.json

- PR `#56115` `2024-09-25` `High` `High`: *: AnalyzeV2 supports processing special global indexes (#56115)
  Modules: executor, planner, statistics
  What: AnalyzeV2: index-only analyze of special global indexes writes index stats without advancing stats_meta.snapshot, undermining snapshot-based concurrent-analyze correctness (stale overwrites possible).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-56115.json

- PR `#56409` `2024-09-30` `High` `Medium`: *: support the vector index (#56409)
  Modules: executor, parser, planner, sessionctx, store
  What: `SyncTableSchemaToTiFlash` treats any HTTP response as success (no `StatusCode`/body check), so schema-sync failures can be silently ignored and the vector-index DDL flow can proceed into an invalid state.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-56409.json

- PR `#56082` `2024-10-09` `High` `High`: *: Drop partition DDL handling for overlapping partitions during State Changes (#56082)
  Modules: planner, table
  What: TiFlash MPP dynamic partition pruning incorrectly overrides an empty pruned-partition set by selecting a real partition to scan (including the explicitly requested partition name), which can expose rows from partitions that are supposed to be non-readable during `DROP PARTITION` state changes.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-56082.json

- PR `#56204` `2024-10-10` `High` `High`: planner: support vector index in planner (#56204)
  Modules: pkg/planner/core, pkg/planner/property, pkg/planner/util, pkg/expression, pkg/executor, pkg/store/mockstore
  What: Vector-index planning truncates `LIMIT+OFFSET` into `uint32` TopK without bounds checks, so large limits can overflow and cause wrong results when ANN pushdown is used.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-56204.json

- PR `#55950` `2024-10-22` `High` `High`: executor: support hash join spill (#55950)
  Modules: pkg/executor/join, pkg/util/chunk
  What: Restore-mode build errors can be dropped: `startProbeJoinWorkers` consumes `buildFinished` error and returns without notifying `joinResultCh`, so the main `Next()` loop may hang or return incomplete results without error.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-55950.json

- PR `#56850` `2024-10-29` `High` `High`: cardinality: remove the unnecessary limitation that the "exponential backoff" entry stats can't use iteself for estimation (#56850)
  Modules: pkg/planner/cardinality, pkg/planner/core/casetest, tests/integrationtest/t/planner/core
  What: `expBackoffEstimation()` can append a zero selectivity when using index-stats fallback, because it breaks on successful `GetRowCountByIndexRanges()` before assigning `selectivity`. This PR removes the self-index skip, increasing the likelihood of hitting exactly this “success-break-without-selectivity” path when only the current index can provide the fallback estimate.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-56850.json

- PR `#57053` `2024-11-13` `High` `High`: executor: support left outer semi join for hash join v2 (#57053)
  Modules: pkg/executor/join, pkg/expression, pkg/planner/core, pkg/util/queue
  What: `VecEvalBool` can incorrectly propagate `nulls=true` (NULL result) for a row that should be `false` when an earlier `EQ` condition from `[not] in (subq)` is NULL but a later non-IN predicate is NULL (treated as false by `EvalBool`). HashJoinV2 LeftOuterSemiJoin relies on `VecEvalBool`’s `nulls` to produce the marker column, so this can yield NULL marker instead of 0 (false) in some `IN` subquery shapes.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-57053.json

- PR `#57473` `2024-11-19` `High` `High`: planner: ONLY_FULL_GROUP_BY sql_mode was not working with VIEWs (#57473)
  Modules: planner
  What: `ONLY_FULL_GROUP_BY` fix is incomplete for VIEWs with table aliases: infoschema fallback uses `FieldName.TblName` (alias) instead of `OrigTblName`, so aliased tables inside views can still evade the check.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-57473.json

- PR `#57282` `2024-11-20` `High` `High`: *: bump client-go with some regression tests (#57282)
  Modules: executor, kv, session, store
  What: Snapshot iterators from ART MemDB are not consistently closed in executor mem-buffer scans (`iterTxnMemBuffer` and per-range iteration in `txnMemBufferIter`), which can leak client-go snapshot bookkeeping and prevent freeing/reuse of old-version nodes, causing memory growth/OOM after bumping `client-go`.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-57282.json

- PR `#57525` `2024-11-20` `High` `High`: Revert "util: fix codec for negative zero (#57343)" (#57525)
  Modules: pkg/util/codec, pkg/expression, tests/integrationtest/expression
  What: Reverting the negative-zero-preserving float codec reintroduces an incorrect query result (documented in `pingcap/tidb#41878`) by collapsing `-0.0` to `+0.0` in `codec.EncodeFloat`, which is used for coprocessor constant encoding.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-57343.json

- PR `#57430` `2024-11-21` `High` `High`: planner/core: fix a wrong privilege check for CTE & UPDATE statement (#57430)
  Modules: pkg/planner/core
  What: `PlanBuilder.nameMapCTE` is a single mutable field and not scoped/stacked; nested `WITH` inside a CTE definition can overwrite it, so `buildUpdate` may fail to recognize the UPDATE statement's own CTE table names and wrongly add `mysql.SelectPriv` visitInfo entries for them again.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-57430.json

- PR `#57042` `2024-11-29` `High` `High`: *: avoid notify privilege update for all users (#57042)
  Modules: executor, privilege, session
  What: Potential unauthenticated DoS/perf regression: `MatchIdentity` calls `ensureActiveUser` on client-supplied usernames, and `ensureActiveUser` unconditionally loads the full role graph and records the (even non-existent) username into `activeUsers`. Over time, `activeUsers` can grow without bound, and periodic privilege refresh (`UpdateAll`) reloads based on this expanding set, amplifying CPU/IO/memory usage.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-57042.json

- PR `#57937` `2024-12-04` `High` `High`: *: activate txn for query on infoschema tables (#57937)
  Modules: executor
  What: `MemTableReaderExec.Open` gates txn activation on `txn.Valid()`, which is false for lazy/pending txns; this likely makes the GC-blocking/txn-activation change ineffective in the common case.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-57937.json

- PR `#57937` `2024-12-04` `High` `Medium`: *: activate txn for query on infoschema tables (#57937)
  Modules: executor
  What: `memtableRetriever` uses a domain snapshot infoschema in-txn without re-attaching session-local infoschema overlays (e.g., local temporary tables), potentially making INFORMATION_SCHEMA results wrong inside explicit transactions.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-57937.json

- PR `#57808` `2024-12-05` `High` `High`: parser: add traffic capture/replay statements in the parser (#57808)
  Modules: pkg/parser, pkg/parser/ast
  What: `TRAFFIC REPLAY`’s `READ_ONLY` option restores as `READONLY`, but the grammar/lexer require `READ_ONLY`, so restored SQL is not accepted by the parser.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-57808.json

- PR `#58365` `2024-12-19` `High` `High`: planner: quickly get total count from index/column (#58365)
  Modules: planner
  What: `getTotalRowCount` now prioritizes the first matching index’s `TotalRowCount` and skips checking columns, which can return the wrong “table row count” when the matched index is a multi-valued index (MVIndex), skewing NDV scaling and causing optimizer misestimation/plan instability.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-58365.json

- PR `#58536` `2024-12-25` `High` `High`: planner: add cascades portal for later optimization. (#58536)
  Modules: pkg/planner/core, pkg/planner
  What: `USE_CASCADES()` statement hint is silently ignored when selecting the planner path; only `SessionVars.EnableCascadesPlanner` is consulted.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-58536.json

- PR `#58470` `2024-12-31` `High` `High`: planner: add memo group merge logic. (#58470)
  Modules: pkg/planner/cascades/memo
  What: `Memo.mergeGroup` mutates `src.hash2ParentGroupExpr` while iterating it, so some parent GEs can be skipped and never updated to point from `src` to `dst`.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-58470.json

- PR `#58470` `2024-12-31` `High` `High`: planner: add memo group merge logic. (#58470)
  Modules: pkg/planner/cascades/memo
  What: `Group.mergeTo` can stop iteration early because it deletes the current list element while iterating, so not all source `GroupExpression`s are migrated/merged before the source group is cleared.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-58470.json

- PR `#58657` `2025-01-07` `High` `High`: statistics: refactor stats meta handling to use DeltaUpdate for multi-table support (#58657)
  Modules: pkg/statistics/handle/ddl, pkg/statistics/handle/storage, pkg/statistics/handle/usage
  What: `UpdateStatsMeta` negative-delta batching changes the INSERT path for missing `mysql.stats_meta` rows: it inserts the decrement magnitude into the unsigned `count` column, which can create an incorrect positive row count when the target meta record is absent (regression vs previous `count=0` insert-on-missing behavior).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-58657.json

- PR `#58899` `2025-01-14` `High` `High`: json: add json fuzz test for `json_extract` function and fix several bugs found by it (#58899)
  Modules: pkg/types (JSON binary / JSON path), tests/integrationtest (executor/expression json results)
  What: `BinaryJSON.marshalFloat64To` exponent cleanup uses indices based on `len(floatBuf)` but applies them to the full `buf` without `floatPos` offset, and truncates `buf` to `[:n-1]`. For floats marshaled into a non-empty buffer (arrays/objects), this can read/modify/truncate the wrong region and corrupt the JSON output.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-58899.json

- PR `#59756` `2025-02-26` `High` `High`: executor: 'alter user' requires 'CREATE USER' privilege in most cases (#59756)
  Modules: executor
  What: Privilege bypass remains: current-user can alter COMMENT/ATTRIBUTE/RESOURCE GROUP without CREATE USER by combining them with a password change, because `alterPassword` ignores those options.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-59756.json

- PR `#59756` `2025-02-26` `High` `High`: executor: 'alter user' requires 'CREATE USER' privilege in most cases (#59756)
  Modules: executor
  What: CURRENT_USER resolution is now conditional on “password-only”; non-password `ALTER USER CURRENT_USER ...` leaves `Username/Hostname` empty and can operate on `''@''` or fail incorrectly.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-59756.json

- PR `#59487` `2025-02-27` `High` `High`: privilege, domain: reduce the memory jitter of privilege reload activity for 2M users (#59487)
  Modules: pkg/domain, pkg/privilege/privileges
  What: `UpdateAllActive`/`Update` incremental privilege reload can still materialize the entire `mysql.user` table when the target user set is empty or >1024, because `decodeUserTableRow` ignores `userList` while SQL filtering is disabled in that case.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-59487.json

- PR `#55792` `2025-03-12` `High` `High`: charset,collation: implement `GB18030` and 2 collations (#55792)
  Modules: expression, parser
  What: `encodingGB18030.MbLen` can panic on short inputs: it indexes `bs[2]`/`bs[3]` when the second byte looks like a 4-byte sequence prefix, without ensuring at least 4 bytes are available.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-55792.json

- PR `#60313` `2025-03-31` `High` `High`: privilege: fix `create temporary tables` privileges related bugs (#60313)
  Modules: executor, parser, planner, privilege
  What: After this commit, global `CREATE TEMPORARY TABLES` makes databases visible (`globalDBVisible`), but `SHOW TABLE STATUS` (and `SHOW INDEX`) still use `mysql.AllPrivMask` for per-table filtering, so `CreateTMPTablePriv` is treated as sufficient and leaks base-table/index metadata to users who only have `CREATE TEMPORARY TABLES`.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-60313.json

- PR `#57417` `2025-04-01` `High` `Medium`: planner: utilize equivalence to eliminate unnecessary exchangers (#57417)
  Modules: pkg/planner/core, pkg/planner/property, pkg/planner/funcdep, pkg/executor
  What: `NeedMPPExchangeByEquivalence` relaxes hash-exchanger enforcement to treat “current hash keys ⊆ required keys (up to equivalence)” as satisfying a required MPP hash partition. For multi-key shuffle joins, this can let two inputs independently satisfy the same required partitioning with different single-key subsets/permutations, risking misaligned partition routing and incorrect join results.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-57417.json

- PR `#59009` `2025-04-03` `High` `High`: ddl: Implement TableMode feature (#59009)
  Modules: executor, planner
  What: TableMode access enforcement can be bypassed via views: the new planner check only validates tables recorded in the preprocessor ResolveContext, but view expansion parses/builds the view’s internal SELECT without preprocessing it, so protected base tables referenced inside views are not checked and can still be read/written through the view.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-59009.json

- PR `#58490` `2025-04-07` `High` `High`: statistics: speed up the backgroud stats update worker (#58490)
  Modules: pkg/session/bootstrap.go, pkg/statistics/handle/bootstrap.go, pkg/statistics/handle/cache/statscache.go, pkg/statistics/handle/ddl/subscriber.go, pkg/statistics/handle/storage/gc.go, pkg/statistics/handle/storage/save.go, pkg/statistics/handle/storage/stats_read_writer.go, pkg/statistics/handle/storage/update.go, pkg/statistics/handle/types/interfaces.go, pkg/statistics/table.go
  What: FlashbackCluster stats refresh can become incomplete: `storage.UpdateStatsVersion()` doesn’t update `last_stats_histograms_version`, so the new background stats update worker may reuse cached column/index stats and skip reloading histograms after `FLASHBACK CLUSTER`.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-58490.json

- PR `#60522` `2025-04-15` `High` `Medium`: planner: generalize vector index to columnar index (#60522)
  Modules: planner
  What: `plan_to_pb` drops the historical `TopK != math.MaxUint32` guard and may push down an ANN/vector query with `TopK==MaxUint32`, regressing the safety behavior added in #57307 (ref #54245).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-60522.json

- PR `#60623` `2025-04-21` `High` `High`: planner: modernize some package (#60623)
  Modules: pkg/planner/cardinality
  What: `convertRangeFromExpectedCnt()` ascending-branch regression: switching to `for i = range ranges` makes the existing `if i == len(ranges)` guard ineffective for non-empty ranges, changing behavior when `expectedCnt` cannot be satisfied by the provided `rangeCounts`.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-60623.json

- PR `#60675` `2025-04-21` `High` `Medium`: planner: Fix issue #60556: Simplify Range Intersection Logic (#60675)
  Modules: pkg/util/ranger, pkg/planner/core/casetest/index
  What: Potential wrong empty-intersection when intersecting unequal-length bounds involving explicit `NULL`: new padding uses `MinNotNull` as “-∞”, which can make a shorter prefix-bound compare *greater* than a longer bound whose next component is `NULL`, causing `Range.IntersectRange()` to treat overlapping ranges as disjoint.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-60675.json

- PR `#60592` `2025-05-21` `High` `High`: planner: add variable control for index join build v2 and default them on (#60592)
  Modules: executor, planner, sessionctx
  What: Under the new default (`@@tidb_opt_index_join_build_v2=ON`), index-join enumeration uses `tryToEnumerateIndexJoin`, which still filters candidates with `NO_INDEX_*` hints even when conflicting forced `INL_*` hints exist. This can suppress forced index-join plans in conflict scenarios, contradicting the documented priority and the v1 behavior where force hints override filter hints.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-60592.json

- PR `#57845` `2025-05-26` `High` `High`: expression: Timestamp literal with time zone offset (#57845)
  Modules: pkg/expression, pkg/types, pkg/planner/core
  What: Plan-cache correctness risk: TIMESTAMP literals with explicit `±HH:MM` offsets are converted into the session time zone at expression build time and stored as a constant builtin value, but plan cache key only captures the current time zone offset (not the full zone identity/rules). Cached plans can therefore be reused across different `@@time_zone` settings with the same current offset, yielding wrong literal values (especially with DST-capable named zones).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-57845.json

- PR `#61280` `2025-05-30` `High` `High`: planner: Make SET_VAR query hints restore the original session variable values. (#61280)
  Modules: planner, sessionctx
  What: With the new “first-wins” restore map, an earlier `SET_VAR` application that fails validation can permanently occupy the restore entry with an invalid/empty old value, preventing later correct old-value capture for the same sysvar (e.g., when a binding’s `SET_VAR` succeeds). On the next statement, restore can fail and leave the session variable stuck at the hint-applied value, leaking across statements.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-61280.json

- PR `#61478` `2025-06-06` `High` `High`: planner: outer join pruning for constants (#61478)
  Modules: pkg/planner/core/casetest/plan_test.go, pkg/planner/core/rule/util/misc.go, pkg/planner/core/rule_join_elimination.go, tests/integrationtest/r/planner/core/rule_constant_propagation.result
  What: Outer-join pruning under DISTINCT/dup-agnostic aggregation becomes unsafe for mutable/side-effect SELECT expressions: the new condition (`aggCols` merely “not from inner table”) allows eliminating outer joins even when DISTINCT keys are projection columns like `UUID()`/`RAND()`, which are evaluated per-row and can change with join-induced duplication.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-61478.json

- PR `#61533` `2025-06-16` `High` `High`: types/time: invalid timestamp during DST spring forward fixed to next valid ts instead of zero date. (#61533)
  Modules: executor
  What: `types.Time.Convert` DST-adjustment returns `Time{FromGoTime(...)}`, which drops the TIMESTAMP type/fsp bits; the adjusted result becomes a DATETIME-typed `types.Time` even though the target type is TIMESTAMP.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-61533.json

- PR `#61778` `2025-06-18` `High` `High`: planner: support "explain explore analyze" when exploring new plans offline (#61778)
  Modules: executor, parser, planner, sessionctx
  What: `EXPLAIN EXPLORE ANALYZE` is supported by the parser, but `ExplainStmt.Restore()` emits an unsupported keyword order (`EXPLAIN ANALYZE EXPLORE ...`), making restored SQL unparsable.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-61778.json

- PR `#61778` `2025-06-18` `High` `Medium`: planner: support "explain explore analyze" when exploring new plans offline (#61778)
  Modules: executor, parser, planner, sessionctx
  What: `StatementContext.GetResultRowsCount()` can panic if `stmtctx.PlanIDFunc` isn’t initialized, because it calls a nil global function variable without checking.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-61778.json

- PR `#61752` `2025-06-24` `High` `High`: dxfservice: create store for SYSTEM keyspace (#61752)
  Modules: pkg/kv, pkg/session, pkg/store
  What: SYSTEM/user store initialization builds TiKV URLs incorrectly when `cfg.Path` already contains query params: it always appends `?keyspaceName=...` (instead of merging into existing query), which can break parsing (notably `disableGC`) and crash during startup in nextgen keyspace mode.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-61752.json

- PR `#61687` `2025-06-30` `High` `High`: planner: extend order ratio to joins (#61687)
  Modules: planner, sessionctx
  What: `tidb_opt_ordering_index_selectivity_ratio < 0` (documented as disable) can still add a positive ordering penalty to IndexJoin outer ExpectedCnt when `estimatedRowCount > outerRowCount`, due to multiplying before `max()` (`max(0, (outer-estimated)*orderRatio)`) causing a sign flip.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-61687.json

- PR `#62570` `2025-07-25` `High` `Medium`: bootstrap: use reserved ID for system schemas/tables in nextgen (#62570)
  Modules: session
  What: Nextgen bootstrap can create duplicate system schemas/tables (same names, different IDs) on an already-initialized store, because nextgen bootstrap uses fixed reserved IDs but meta create paths only check existence by ID (not by name), and infoschema overwrites duplicates by name.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-62570.json

- PR `#62607` `2025-07-30` `High` `High`: domain: ignore DDL session when calculating the min startTs (#62607)
  Modules: pkg/domain/infosync, pkg/executor/staticrecordset, pkg/session, pkg/server
  What: `ReportMinStartTS` ignores entire sessions when `StmtCtx.IsDDLJobInQueue` is true, which also ignores active cursor snapshot `StartTS` on that connection; this can let GC safepoint advance past an in-use cursor snapshot if the same connection runs a long DDL while a cursor remains open.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-62607.json

- PR `#61634` `2025-08-18` `High` `High`: *: add the new SEMv2 config and implement most of the functions (#61634)
  Modules: executor, expression, planner, privilege, session, sessionctx
  What: SEMv2 read-only sysvar enforcement is case-sensitive and can be bypassed: `SetExecutor` lowercases the var name for sysvar resolution but checks `semv2.IsReadOnlyVariable` using the raw (case-preserving) name.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-61634.json

- PR `#61634` `2025-08-18` `High` `Medium`: *: add the new SEMv2 config and implement most of the functions (#61634)
  Modules: executor, expression, planner, privilege, session, sessionctx
  What: `PlanBuilder.checkSEMStmt` can dereference a nil privilege manager under SEMv2, leading to a panic when restricted SQL is encountered in sessions that intentionally disable privilege checking.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-61634.json

- PR `#63072` `2025-08-20` `High` `Medium`: expression: fix unexpected modification of shared return type of cast expr (#63072)
  Modules: expression, parser
  What: `ColumnSubstituteImpl` deep-copies and reassigns `ScalarFunction.RetType` for substituted CAST arguments, but does not keep the builtin signature return-type pointer (`builtinFunc.getRetTp()` / embedded `baseBuiltinFunc.tp`) in sync. This breaks the documented implicit invariant (`ScalarFunc.RetType == ScalarFunc.builtinFunc.RetType`) and can cause later type propagation (e.g. `PropagateType` for CAST-to-REAL) to update only `sf.RetType` while runtime evaluation/rounding still uses the builtin signature’s `tp`.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-63072.json

- PR `#63227` `2025-08-29` `High` `High`: planner: fix select lock don't have Hash64 and Equals methods itself in next-gen mode. (#63227)
  Modules: pkg/parser, pkg/planner
  What: `BaseLogicalPlan.Equals` guard was not updated for `*LogicalLock` (while `Hash64` was), so generated `LogicalLock.Equals` can trigger an internal-check panic when equality is needed in cascades/memo.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-63227.json

- PR `#63319` `2025-09-08` `High` `High`: distsql: Add `SelectResultIter` to read rows from cop-task in the iterator way (#63319)
  Modules: executor
  What: `SelectResultIter` drops `selectResp` between cop responses without releasing the per-response memory accounting (`selectRespSize`), causing a memTracker leak/overcount across streamed responses.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-63319.json

- PR `#63416` `2025-09-10` `High` `High`: planner: Exists subquery to join variable (#63416)
  Modules: planner, sessionctx
  What: `tidb_opt_enable_semi_join_rewrite` enables `PreferRewriteSemiJoin` on `AntiSemiJoin`/`AntiLeftOuterSemiJoin` (NOT EXISTS) joins, but the rewrite rule never clears/handles these join types; the leftover prefer-bit is then misclassified as an unsupported join-method hint under MPP and blocks TiFlash/MPP join enumeration for anti semi joins.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-63416.json

- PR `#63443` `2025-09-11` `High` `High`: planner: re-enable the plan cache generator and move Delete/Inset/Update into physicalop (#63443)
  Modules: pkg/executor, pkg/planner/core, pkg/planner/core/operator/physicalop, pkg/planner/core/generator/plan_cache
  What: `PhysicalIndexMergeReader.CloneForPlanCache` can panic by flattening a nil `TablePlan` (nil-deref in `FlattenPushDownPlan`), despite `PhysicalIndexMergeReader` having code paths where `TablePlan` may be nil.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-63443.json

- PR `#62852` `2025-09-22` `High` `High`: txn: follower read only affect read-only statements (#62852)
  Modules: pkg/sessionctx/variable, pkg/sessiontxn/isolation
  What: Txn-bound snapshot `ReplicaRead` state can persist across statements, allowing follower reads during later non-read-only statements that use `txn.Get()` (e.g. `INSERT ... VALUES` duplicate-key checks) after a prior read-only point-get in the same explicit transaction.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-62852.json

- PR `#63670` `2025-09-24` `High` `Medium`: coprocessor: handle paging result even if not enabled (#63670)
  Modules: pkg/store/copr
  What: Copr cache hit can defeat the new “page-by-response-range” behavior: when paging is disabled but storage returns `Response.Range`, subsequent cache-hit responses may omit `Range`, causing TiDB to take the non-paging path and potentially return only the first cached page.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-63670.json

- PR `#62694` `2025-09-30` `High` `High`: planner, executor: support access path `keep order` with `IN` conditions using merge sort (#62694)
  Modules: executor, planner
  What: `IndexReaderExecutor` merge-sort path issues 1 cop request per `kv.KeyRange` (not per range group), so `PropMatchedNeedMergeSort` can explode request/stream count for multi-column `IN` ranges well beyond the planner’s 2048 group guard (risking severe runtime overhead / potential DoS).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-62694.json

- PR `#63746` `2025-10-10` `High` `High`: executor: finish INDEX_LOOKUP_PUSHDOWN execution part (#63746)
  Modules: executor, planner, store
  What: INDEX_LOOKUP_PUSHDOWN completed-row tasks retain decoded row chunks after the underlying coprocessor SelectResult/RowIter has been closed/advanced, but they do not perform task-level memory accounting, causing significant statement memory under-counting (and potential OOM / quota bypass).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-63746.json

- PR `#64297` `2025-11-17` `High` `High`: privileges,planner: implement checks for `RESTRICTED_USER_ADMIN` for granting privileges and roles (#64297)
  Modules: pkg/planner/core, pkg/privilege/privileges
  What: `GRANT ROLE` does not enforce `RESTRICTED_USER_ADMIN` when the *grantee user* is restricted, allowing role assignment to restricted accounts without the required dynamic privilege.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-64297.json

- PR `#64443` `2025-12-15` `High` `High`: stats: Correctly set stats version after running ANALYZE (#64443)
  Modules: pkg/statistics/handle/autoanalyze, pkg/statistics/handle/storage
  What: `DROP STATS` may fail to reset cached table-level `StatsVer` to `Version0` when lazy loading skips creating any in-memory column/index stats objects, because `TableStatsFromStorage` now requires `hasStats` to perform the reset.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-64443.json

- PR `#65049` `2025-12-17` `High` `High`: ddl: sync affinity group for pd and support `show affinity` (#65049)
  Modules: pkg/executor, pkg/parser, pkg/planner/core
  What: `SHOW AFFINITY` ignores privilege/visibility checks and can disclose metadata for tables in databases the current user should not see.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-65049.json

- PR `#64160` `2025-12-19` `High` `High`: sem, privilege: fix the issue that some privileges cannot be limited (#64160)
  Modules: planner, privilege
  What: SEMv2 restricted-privilege gating via `RESTRICTED_PRIV_ADMIN` is bypassable with `GRANT ALL PRIVILEGES` / `REVOKE ALL PRIVILEGES`: the code only checks whether the literal "ALL PRIVILEGES" token is restricted, and never enforces `RESTRICTED_PRIV_ADMIN` based on the expanded privilege set that includes restricted static privileges (e.g. `DROP`, `FILE`).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-64160.json

- PR `#65509` `2026-01-14` `High` `High`: planner: support cluster-level binding reload command (#65509)
  Modules: executor, parser, planner
  What: Potential panic in `SHOW [GLOBAL] BINDINGS` (and related SHOW) when binding cache returns nil entries: nil is checked only after sorting, but `sort.Slice` comparator dereferences binding fields.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-65509.json

- PR `#65749` `2026-01-23` `High` `Medium`: planner: fix outer-join-to-anti-semi rewrite for nested outer joins (#65749)
  Modules: planner
  What: Scenario2 now uses FD-derived `NotNullCols` as a proxy for “inner column is NOT NULL”, but FD not-null can be inferred from null-rejecting join predicates inside an `innerChild` outer join even when the column can still be NULL in `innerChild` output. This can incorrectly enable outer-join→anti-semi rewrites and change SQL semantics.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-65749.json

- PR `#65620` `2026-01-24` `High` `High`: planner, meta: code preparation for the `_tidb_commit_ts` new hidden column (#65620)
  Modules: executor, expression, planner
  What: `_tidb_commit_ts` is treated as a globally reserved pseudo column name in planner/executor prep code via name-based blocking and pruning, but the reservation is enforced primarily at query-planning time (and by name), not by column ID or DDL-level prevention. This can break backward compatibility for any user tables that legitimately have a real column named `_tidb_commit_ts` (queries error out or the column is silently pruned/ignored).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-65620.json

- PR `#65696` `2026-01-27` `High` `High`: planner: support straight_join() hint in CTEs and subqueries (#65696)
  Modules: pkg/planner/core, pkg/util/hint, tests/integrationtest
  What: `PlanBuilder.inStraightJoin` is no longer reset for SELECT blocks without STRAIGHT_JOIN, so once an outer SELECT enables STRAIGHT_JOIN (keyword or `/*+ straight_join() */`), nested SELECTs inherit the flag and have join-reorder disabled even if they did not specify STRAIGHT_JOIN.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-65696.json

- PR `#65857` `2026-01-29` `High` `Medium`: statistics: refactor out of range percent calculations (#65857)
  Modules: planner, statistics
  What: Potential NaN regression in out-of-range estimation: extracting overlap-percent math into helpers that use built-in `min/max` can propagate NaNs into `OutOfRangeRowCount` results when `histWidth` becomes NaN (e.g., `histL==histR==±Inf`) or range endpoints are NaN; the pre-refactor code largely avoided computing percents under NaN bounds via overlap-condition short-circuiting.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-65857.json

- PR `#65799` `2026-02-04` `High` `High`: planner, core: implement partial order TopN attach2Task and partial order flow (#65799)
  Modules: planner, sessionctx
  What: `tidb_opt_partial_ordered_index_for_topn` changes from BOOL to ENUM and drops support for legacy `ON/OFF/0/1` values, breaking sysvar/hint compatibility across versions.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-65799.json

- PR `#66015` `2026-02-05` `High` `High`: planner: simplify outer join other conditions (#66015)
  Modules: pkg/planner/core/operator/logicalop
  What: Outer join `OtherConditions` simplification can incorrectly eliminate evaluation of mutable/side-effect expressions due to constant short-circuit rewrites that ignore left-to-right eval order.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-66015.json

- PR `#65752` `2026-02-11` `High` `High`: *: implement shared lock for foreign key (#65752)
  Modules: executor, sessionctx, store
  What: With `tidb_foreign_key_check_in_shared_lock=ON`, FK checks acquire shared pessimistic locks on referenced keys; later statements in the same pessimistic transaction that need exclusive locks on those keys can fail because shared→exclusive lock upgrade is explicitly unsupported in client-go.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-65752.json

- PR `#66217` `2026-02-14` `High` `Medium`: planner: fix IndexJoin with Aggregation correctness issue (#66217)
  Modules: planner
  What: The new IndexJoin+Aggregation correctness guard can incorrectly allow unsafe plans when GROUP BY items are expressions: it checks inner join keys against *columns referenced inside* GROUP BY expressions, not against the actual grouping key(s), so aggregation groups can still be split across IndexJoin batches and yield wrong results.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-66217.json

- PR `#66701` `2026-03-06` `High` `Medium`: importinto: introduce a param to control conflict resolution (#66701)
  Modules: executor/importinto
  What: `on_duplicate_key='error'` (default) can fail late in the global-sort write+ingest phase with no rollback, potentially leaving the target table partially imported and secondary indexes inconsistent because data and index KV groups are ingested in separate, unordered subtasks and conflict-resolution steps are skipped on failure.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-66701.json

- PR `#66869` `2026-03-11` `High` `Medium`: planner: preserve outer refs in window subqueries (#66869)
  Modules: pkg/planner/core
  What: New window-subquery auxiliary-field discovery rewrites subquery expressions in a way that can execute uncorrelated scalar/EXISTS subqueries during planning, potentially causing double execution/side effects and semantic changes.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-66869.json

- PR `#66156` `2026-03-13` `High` `High`: planner: support index join for embedded inner-join patterns (#66156)
  Modules: executor, planner
  What: Data race / potential panic: enabling embedded inner-join patterns for index join causes concurrent inner-worker executor building to invoke the general `executorBuilder.build` (for the non-lookup child of an inner `PhysicalHashJoin`), which performs unsynchronized slice appends to shared `StmtCtx.TableIDs/IndexNames`.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-66156.json

- PR `#66996` `2026-03-18` `High` `High`: planner: enable index-only MV IndexMerge only for single covered partial path (optimizer part) (#66996)
  Modules: executor, planner
  What: Plain `EXPLAIN` is made to output an index-only MV IndexMerge plan by constructing `PhysicalIndexMergeReader` with `TablePlan=nil`, but this plan is explicitly “not executable yet” and is blocked from being built/executed outside plain EXPLAIN. This creates an intentional divergence where `EXPLAIN` (non-analyze) can show a plan shape that the executor cannot run, and which will differ from actual execution and `EXPLAIN ANALYZE`.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-3y-sql-runtime/results/pr-66996.json


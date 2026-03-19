# Unresolved Blocker/High Findings

- Generated at: 2026-03-19T14:32:36.917813+08:00
- Worthy deep-review queue: 334 PRs, fully reviewed
- Unresolved findings: 69 total
- Blocker: 10
- High: 59
- All findings are within the worthy-approved scope: True

## Blocker

- PR #40634: ddl: enable modify column on partitioned table (#40634)
  Severity: Blocker; Confidence: High; Modules: pkg/ddl
  What: `MODIFY COLUMN` on partitioned tables is enabled, but partitioning-column safety checks don’t account for `PARTITION BY KEY`: lossy conversions on KEY-partitioning columns (e.g., shrinking string length, reducing time/datetime FSP, or other conversions that can change value under non-strict SQL mode) can change the hashed partition key while the DDL reorg rewrites rows in-place without moving them across physical partitions. This can leave rows stored in the “wrong” partition, causing incorrect results under partition pruning/point-get.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-40634.json

- PR #53277: ddl: Reorganize partition supporting global index (#53277)
  Severity: Blocker; Confidence: High; Modules: pkg/ddl, pkg/table/tables
  What: `findNextNonTouchedPartitionID` has a loop whose DroppingDefinitions membership check is evaluated only once (the `notFoundErr` condition is never recomputed). If the immediate next partition is a dropped partition, the loop will keep advancing to the end and return 0, skipping remaining non-touched partitions. This can leave rebuilt global indexes incomplete after `REORGANIZE PARTITION` when dropped partitions are in the middle of `pi.Definitions`.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-53277.json

- PR #55129: domain,infoschema: avoid v1/v2 switch when loading snapshot infoschema (#55129)
  Severity: Blocker; Confidence: High; Modules: pkg/domain, pkg/infoschema, pkg/meta, pkg/sessionctx/variable
  What: Snapshot infoschema full-load can build infoschema v1 while `tidb_schema_cache_size>0`, but schema/table fetching is still optimized for v2 and only loads “must-load” table infos; v1 builder then omits most tables, yielding an incomplete snapshot infoschema.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-55129.json

- PR #55847: infoschema, meta: fix wrong auto id after `rename table` (#55847)
  Severity: Blocker; Confidence: Medium; Modules: pkg/infoschema, pkg/meta, pkg/meta/autoid
  What: `Allocator.Transfer` + `AutoIDAccessor.CopyTo` migrates and switches autoid ownership to the new schema during infoschema diff apply, but cross-schema rename explicitly allows concurrent DML; in a multi-node mixed-schema window this can split allocations across old/new meta keys and can non-monotonically overwrite the target autoid, risking duplicate auto-increment IDs or wrong base.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-55847.json

- PR #56786: ddl: Corrected index management during REORGANIZE PARTITION (#56786)
  Severity: Blocker; Confidence: High; Modules: pkg/ddl, pkg/meta/model, pkg/table/tables
  What: `PartitionInfo.DDLChangedIndex` is not cleared on successful `ActionReorganizePartition` completion. Because it is persisted in table meta and later reused by both `tables.newPartitionedTable()` (to decide which indexes exist on dropping/adding partitions) and rollback conversion (to decide which indexes to drop), stale entries can break subsequent REORGANIZE PARTITION executions and can even cause rollback to drop an index that pre-existed the current DDL.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-56786.json

- PR #57770: ddl/ingest: remove backend context manager and refactor code (#57770)
  Severity: Blocker; Confidence: High; Modules: pkg/ddl, pkg/ddl/ingest, pkg/lightning/backend/local
  What: `IngestIfQuotaExceeded` can import+reset engines while writers are concurrently appending, because flush locks are released before `unsafeImportAndResetAllEngines()`. This can race with local backend import/reset and lead to data loss or corruption in add-index ingest.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-57770.json

- PR #59407: ddl: split partition tables subtasks like normal tables (#59407)
  Severity: Blocker; Confidence: High; Modules: pkg/ddl
  What: `generatePlanForPhysicalTable` can silently return partial/empty subtask plans by swallowing planning-time errors (notably `allocNewTS` failures and potentially `getTableRange` errors). With this PR, partitioned tables now use this function, so an intermittent PD/GetTS or snapshot-range error can lead to an incomplete add-index backfill without the framework noticing.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-59407.json

- PR #61200: ddl, disttask: import into integrate table mode (#61200)
  Severity: Blocker; Confidence: High; Modules: pkg/ddl, pkg/disttask/importinto, pkg/planner/core, pkg/disttask/framework/storage
  What: `IMPORT INTO` classic-kernel submission can leave a table permanently in `TableModeImport` if task creation/commit fails after the mode switch, because `ddl.AlterTableMode` is not transactional with `TaskManager.WithNewTxn` and there is no rollback/compensation on error.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-61200.json

- PR #63465: ddl: make some `MODIFY COLUMN` skip row reorg (#63465)
  Severity: Blocker; Confidence: High; Modules: pkg/ddl, pkg/meta/model, pkg/table, pkg/tablecodec
  What: Rollback cleanup for skip-row-reorg `MODIFY COLUMN` can clear an existing `NOT NULL` constraint when only `ChangingFieldType` was used as an in-progress marker (i.e., when NULLability wasn’t being changed).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-63465.json

- PR #63965: ddl: some code refactor for lossy ddl optimization (#63965)
  Severity: Blocker; Confidence: High; Modules: pkg/ddl, pkg/meta/model, pkg/table
  What: Cancel/rollback path for `ActionModifyColumn` assumes refactored `ModifyColumnType` enum values, but does not handle the legacy `mysql.TypeNull` (6) value used by older jobs, which can leave a job stuck in `JobStateCancelling`.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-63965.json

## High

- PR #52873: pkg/infoschema: revert cache policy ref (#52873)
  Severity: High; Confidence: High; Modules: pkg/infoschema
  What: `ALTER/REPLACE PLACEMENT POLICY` may not refresh partition-level placement bundles when the altered policy is referenced only by partitions (table-level `PlacementPolicyRef` is nil), leaving partition bundles stale in infoschema.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-52873.json

- PR #53217: ddl: sync schema version using watch, notify sessions on owner node by job id (#53217)
  Severity: High; Confidence: Medium; Modules: pkg/ddl, pkg/ddl/syncer
  What: `SyncJobSchemaVerLoop` now performs a full-prefix etcd `Get`/`Watch` over `/tidb/ddl/all_schema_by_job_versions/` and caches all job-version keys, but the code only deletes these keys for `JobStateSynced` jobs. Terminal rollback/cancel paths (and any deletion failures) can leave per-job keys behind indefinitely, making the watched keyspace and cache unbounded over time and risking MDL schema-sync stalls or resource blowups.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-53217.json

- PR #53301: *: keep in-memory resident for model.TableInfo with special attributes (#53301)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/infoschema
  What: `infoschema` v2 `ListTablesWithSpecialAttribute` can miss special-attribute tables after cross-database rename because it deduplicates resident entries using only `tableID`, while resident entries are keyed/ordered by `(dbName, tableID, schemaVersion)`, allowing a tomb entry for the old DB to suppress the live entry for the new DB.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-53301.json

- PR #53544: ddl: Reorganize Partition failure/rollback tests and fixes (#53544)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/meta/model
  What: Backward-compatibility break in persisted metadata: changing `PartitionInfo.NewPartitionIDs` from an untagged field (JSON key `NewPartitionIDs`) to a tagged snake_case key (`new_partition_ids`) prevents new code from decoding legacy metadata during in-flight truncate-partition DDL, and can bypass truncating-partition write blocking.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-53544.json

- PR #53548: ddl: decouple job scheduler from 'ddl' and make it run/exit as owner changes (#53548)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/owner
  What: FLASHBACK CLUSTER: `splitRegionsByKeyRanges` retries `SplitRegions` in a tight infinite loop without checking context cancellation. After this change, it is invoked with the scheduler/worker context (canceled on owner change), so an owner change/shutdown during StageWriteOnly can spin/hang and block scheduler exit.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-53548.json

- PR #53821: infoschema: make build bundle faster for infoschema v2 (#53821)
  Severity: High; Confidence: High; Modules: pkg/infoschema/infoschema_v2.go, infoschema v2 placement bundles
  What: Full placement-bundle rebuild in infoschema v2 can miss some placement-policy tables after cross-database `RENAME TABLE`, due to `ListTablesWithSpecialAttribute` iterating `tableInfoResident` ordered by dbName and deduping only consecutive `tableID`s (with tomb handling after dedup). In certain key-order adjacency cases, the old-db tomb record can cause the new-db live record to be skipped, leaving `ruleBundleMap` incomplete.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-53821.json

- PR #54008: ddl: clean ingest temp data dir in background loop (#54008)
  Severity: High; Confidence: Medium; Modules: pkg/ddl/ddl.go, pkg/ddl/index.go, pkg/ddl/job_table.go, pkg/ddl/ingest/backend_mgr.go, pkg/ddl/ingest/env.go
  What: Potential regression: stale ingest temp dirs are no longer cleaned just-in-time before the ingest disk pre-check, because cleanup moved to a background loop that first runs only after a 1-minute tick. This creates a window where `PreCheckUsage()` can fail due to stale-dir disk bloat and the add-index job can be cancelled (non-retryable) even though the new cleanup loop would have freed space soon.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-54008.json

- PR #54185: *: optimize the speed of full load (#54185)
  Severity: High; Confidence: High; Modules: pkg/domain, pkg/infoschema, pkg/meta, pkg/parser/model, pkg/structure
  What: Schema-cache full-load fast path uses regex + partial unescaping for table names; JSON `\uXXXX` escapes (e.g., from `<`, `>`, `&`) are not decoded, so `TableName2ID` keys can be wrong and infoschema v2 name lookups can fail.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-54185.json

- PR #54804: pkg/parser: support SWITCH_GROUP syntax for runaway watch (#54804)
  Severity: High; Confidence: High; Modules: pkg/parser, pkg/ddl, pkg/meta/model, pkg/resourcegroup/runaway, pkg/session, pkg/executor
  What: Bootstrap schema upgrade adds `switch_group_name` to runaway watch system tables, but inserts rely on `INSERT ... VALUES` without column lists; during rolling upgrades, older TiDB binaries will start failing inserts after any newer node upgrades the tables.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-54804.json

- PR #55025: ddl: integrate fast create table into normal general DDL workflow (#55025)
  Severity: High; Confidence: High; Modules: pkg/ddl
  What: Merged `ActionCreateTables` jobs drop shared `InvolvingSchemaInfo` (e.g. placement policy refs), allowing the DDL scheduler to run conflicting policy DDL concurrently and potentially commit tables with dangling placement policy references.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-55025.json

- PR #55049: ddl: record get owner TS and compare it before runReorgJob quit (#55049)
  Severity: High; Confidence: High; Modules: pkg/ddl/ddl.go, pkg/ddl/job_scheduler.go, pkg/ddl/reorg.go
  What: Owner-generation stamp uses `time.Now().Unix()` (seconds), so rapid owner re-elections can reuse the same `ownerTS`, defeating the stale-result filter in `runReorgJob()`.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-55049.json

- PR #55264: *: Require GLOBAL IndexOption for creating Global Index (#55264)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/ddl/index.go, pkg/ddl/executor.go, pkg/parser/model/ddl.go, pkg/ddl/partition.go, pkg/util/dbterror, pkg/errno
  What: Forward-compat regression for in-flight DDL jobs: old queued add-index / add-primary-key jobs that relied on the legacy job-arg `global` boolean may be executed with `IndexInfo.Global=false` after this commit, because the worker no longer decodes/uses the legacy `global` flag and instead derives `IndexInfo.Global` solely from `IndexOption.Global`.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-55264.json

- PR #55891: ddl, planner: avoid running queries on the non-Public state table (#55891)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/infoschema, pkg/planner/core, pkg/executor, pkg/meta
  What: Implicit foreign-key cascade DML is likely broken by the new “non-Public table => ErrTableNotExists” planner gate: the PR/comment states cascades should still modify `WriteOnly/DeleteOnly` tables, but FK cascades are executed via internal SQL planning that now rejects those tables.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-55891.json

- PR #56020: resource_control: support set resource limit for background tasks (#56020)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/meta/model, pkg/executor, pkg/parser
  What: `BACKGROUND=NULL` / `BACKGROUND=()` is not honored: DDL code always re-initializes `ResourceGroupSettings.Background` to a non-nil empty struct even when the AST indicates “clear background settings”.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-56020.json

- PR #56130: ddl: args v2 for add/drop/rename index, include PK/vector-index (#56130)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/meta/model
  What: JobVersion1 finished add-index args lose the global-index flag: encoder uses `IndexArg.Global` instead of `IndexArg.IsGlobal`, so global indexes are treated as non-global in delete-range/GC paths.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-56130.json

- PR #56194: ddl: args v2 for lock/unlock/repair table and recover table/schema (#56194)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/executor, pkg/infoschema, pkg/meta, pkg/meta/model
  What: `JobVersion1` lock/unlock table DDL jobs are not backward-compatible across upgrade: this commit changes the persisted JSON field names for the lock/unlock args (CamelCase → snake_case tags), so older in-flight jobs may decode to empty args and then either never finish (lock) or finish without unlocking (unlock).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-56194.json

- PR #56409: *: support the vector index (#56409)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/infoschema, pkg/meta/model, pkg/domain/infosync, pkg/store/helper, pkg/parser
  What: Vector index DDL can wait indefinitely because TiFlash schema-sync and progress polling treat certain failures as success (non-2xx HTTP responses and empty `information_schema.tiflash_indexes` result sets).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-56409.json

- PR #56630: ddl: add `lease not found` and `deadline exceed` to retryable errors (#56630)
  Severity: High; Confidence: Medium; Modules: pkg/ddl, pkg/owner, pkg/util/dbterror, pkg/disttask/framework/taskexecutor, pkg/lightning/common, tests/realtikvtest
  What: Dist add-index disttask now retries nearly all non-`*terror.Error` errors (unknown errors default to retryable), which can keep subtasks in running state indefinitely (no retry budget) and leave DDL jobs stuck waiting for the global task on persistent infra/logic errors.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-56630.json

- PR #56702: ddl: support dropping multiple foreign keys (#56702)
  Severity: High; Confidence: High; Modules: pkg/ddl, tests/integrationtest/ddl
  What: Multi-schema `DROP FOREIGN KEY` subjobs are not rollback-safe: a later subjob failure (e.g., duplicate `DROP FOREIGN KEY` on the same name, or mixed DDL where a later operation fails) can leave earlier FK drops applied even though the overall `ALTER TABLE` fails.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-56702.json

- PR #56864: ddl: enable the ddl notifier by default (#56864)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/domain, pkg/meta, pkg/session
  What: Potential nil-store panic introduced by enabling notifier publish unconditionally: `asyncNotifyEvent` now always calls `notifier.PubSchemeChangeToStore` using `jobCtx.eventPublishStore`, but `EventPublishStore` remains optional in `ddl.NewDDL` options and the only non-nil check is `intest.Assert` (no-op in release builds).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-56864.json

- PR #56963: ddl: mitigate the issue that multiple owner might exist during force to be owner (#56963)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/owner, pkg/session, pkg/domain
  What: `ForceToBeOwner` swallows errors after retries (always returns nil), so upgrade-mode startup can proceed even when “force to be owner” did not succeed.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-56963.json

- PR #57017: *: let TempIndex support encode/decode partitionID flag (#57017)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/ddl/ingest, pkg/kv, pkg/table/tables, pkg/tablecodec
  What: TempIndexValue encoding change (global unique index delete marker adds 'p'+partitionID bytes) is not forward-compatible: pre-#57017 TiDB cannot decode new-format temp-index values, risking rolling upgrade/downgrade failures when temp-index is active.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-57017.json

- PR #57336: ddl: support modify the related reorg config by SQL (#57336)
  Severity: High; Confidence: High; Modules: pkg/executor/operate_ddl_jobs.go, pkg/meta/model/job.go, pkg/planner/core/planbuilder.go, pkg/planner/core/common_plans.go, pkg/ddl/ddl.go
  What: `ADMIN ALTER DDL JOBS` incorrectly mutates `Job.AdminOperator`, which is also used as the pause/resume ownership guard (system vs end user). This breaks the DDL job pause/resume state-machine invariant, especially for system-paused jobs during upgrading.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-57336.json

- PR #57392: ddl: add COMMENTS column to DDL jobs and enhance job reorg meta handling (#57392)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/meta/model, pkg/executor, pkg/planner/core
  What: `ADMIN SHOW DDL JOBS` `COMMENTS` column is mis-associated with rows for `ActionMultiSchemaChange` jobs due to column-12 append order, causing comments to shift between the parent job row and subjob rows.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-57392.json

- PR #57468: ddl: dynamically adjusting the concurrency and batch size of reorganization job (#57468)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/meta/model, pkg/disttask/operator, pkg/executor
  What: Shrinking txn-backfill concurrency can cancel workers in a way that drops in-flight task results (including errors/panics), so reorg/add-index may continue and complete even though some backfill tasks failed.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-57468.json

- PR #57766: *: support cancel query like 'select * from information_schema.tables' (#57766)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/domain, pkg/executor, pkg/infoschema, pkg/meta, pkg/planner/core
  What: `listTablesForEachSchema` incorrectly returns `errors.Trace(err)` (nil) when `ctx.Err()!=nil`, swallowing cancellation and allowing information_schema scans to “succeed” under cancellation.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-57766.json

- PR #57813: ddl: check context done in isReorgRunnable function (#57813)
  Severity: High; Confidence: High; Modules: pkg/ddl
  What: `backfillWorker.handleBackfillTask` checks reorg runnability using `d.ctx` (ddl/owner lifetime context) rather than the per-job step context that is canceled on job pause/cancel. Since backfill txn operations use `context.Background()`, this prevents timely interruption of long-running txn backfill loops on `ADMIN CANCEL/PAUSE`, and can delay scheduler/worker shutdown that waits on backfill workers.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-57813.json

- PR #57945: ddl: handle context done after sending DDL jobs (#57945)
  Severity: High; Confidence: High; Modules: pkg/ddl/ddl.go, pkg/ddl/executor.go, pkg/ddl/job_submitter.go, pkg/ddl/job_submitter_test.go
  What: Possible `ddl.Stop()` hang: `DoDDLJobWrapper` can return on DDL context cancel before receiving the job submission result, but `JobSubmitter.NotifyResult` uses blocking sends on unbuffered `ResultCh`; if cancellation happens while the submitter is still going to `NotifyResult`, it can block forever (no receiver), preventing `d.wg.Wait()` in `ddl.close()` from completing.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-57945.json

- PR #58062: domain,infoschema: make infoschema activity block GC safepoint advancing (#58062)
  Severity: High; Confidence: High; Modules: pkg/infoschema, pkg/domain/infosync, pkg/domain
  What: `InfoCache.GetAndResetRecentInfoSchemaTS()` uses a non-atomic `Load()+Store()` reset, so concurrent `infoschemaV2.keepAlive()` updates can be lost, undermining the GC-safepoint pinning this change introduces.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-58062.json

- PR #58990: dxf: support modify app param on scheduler part (#58990)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/disttask/framework/proto, pkg/disttask/framework/scheduler, pkg/disttask/importinto, pkg/disttask/example
  What: `TaskTypeExample` scheduler lacks an explicit `ModifyMeta` implementation; due to `BaseScheduler` embedding/forwarding `Extension`, calling `ModifyMeta` during task modification can recurse into itself and crash.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-58990.json

- PR #59009: ddl: Implement TableMode feature (#59009)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/infoschema, pkg/meta/model, pkg/util/dbutil, pkg/planner/core
  What: TableMode protection can be bypassed for `ALTER TABLE ... ADD FOREIGN KEY` when `foreign_key_checks=0`, allowing schema changes (and possibly auto-created indexes) on tables in Import/Restore mode.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-59009.json

- PR #59165: ddl: reuse backend for DXF subtasks of same step (#59165)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/ddl/ingest, pkg/lightning/backend/local
  What: DXF local-sort read-index step can leak open engines on early errors: with backend reuse and `BackendCtx.Close()` no longer closing the backend, a `buildLocalStorePipeline` failure after `Register` returns without engine cleanup, leaving engines open in the shared `*local.Backend` and breaking subsequent retries/subtasks (Pebble lock/open-engine failures, potential step wedging).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-59165.json

- PR #59343: ddl: modify param at runtime for add-index on DXF local sort (#59343)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/meta/model, pkg/disttask/framework/taskexecutor, pkg/disttask/operator, pkg/resourcemanager/pool/workerpool, pkg/executor
  What: DXF runtime concurrency modification does not update the StepExecutor’s stored `GetResource()` when applied during a running subtask. Because `readIndexStepExecutor.RunSubtask` re-reads concurrency from `GetResource().CPU.Capacity()` at each subtask start, a successful mid-subtask concurrency decrease can be lost for later subtasks, potentially rebuilding pipelines with stale (higher) concurrency and violating resource/slot assumptions.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-59343.json

- PR #59966: globalsort: write sorted kv meta to external storage (#59966)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/lightning/backend/external, pkg/disttask/framework/proto, pkg/disttask/framework/scheduler
  What: Global-sort add-index subtask meta format becomes backward-incompatible (ExternalPath + external storage), but disttask scheduling has no version/capability gating, so mixed-version clusters can dispatch these subtasks to older executors that can’t read required meta.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-59966.json

- PR #60395: disttask: distribute merge sort subtasks to all available nodes (#60395)
  Severity: High; Confidence: Medium; Modules: pkg/ddl, pkg/lightning/backend/external
  What: `external.DivideMergeSortDataFiles` is introduced/used to distribute merge-sort subtasks by node count, but its remainder batching can exceed the documented `MergeSortFileCountStep` cap, making its `maxTargetFilesPerSubtask := max(16, mergeConc)` overlap-budget check unsound. For some `mergeConc>16` + large-file-count inputs, this can produce merge-sort subtasks whose internal merge fanout (and thus downstream overlap/reader pressure) exceeds the intended `MergeSortOverlapThreshold` budget, risking global-sort add-index instability.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-60395.json

- PR #60403: ddl: Fix ddl owner doesn't use etcd keyspace prefix (#60403)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/keyspace, pkg/store, pkg/util/etcd
  What: Keyspace (API V2) rolling upgrade split-brain risk: DDL owner election keyspace prefix change makes old and new versions campaign on different etcd paths, allowing multiple concurrent DDL owners.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-60403.json

- PR #60828: ddl: update row count periodically when running reorg job (#60828)
  Severity: High; Confidence: High; Modules: pkg/ddl
  What: Pause handling can leave reorg work running after a job becomes `JobStatePaused`: because reorg now returns periodic timeouts while preserving `jobCtx.stepCtx`, but the `JobStatePausing/Paused` fast-path returns without canceling that long-lived context, and the scheduler exits on paused (final state), the background reorg goroutine/workers can continue after a successful pause.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-60828.json

- PR #61865: ddl: refresh TiFlash PlacementRules periodically (#61865)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/domain/infosync
  What: Periodic placement-rule “repair” can override user TiFlash replica changes due to stale job args: it submits `ActionSetTiFlashReplica` using snapshot Count/Labels, and when that job executes later it rewrites `TiFlashReplica` metadata even if a prior queued user DDL already changed/disabled TiFlash replica.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-61865.json

- PR #62014: ddl: send result chunks to writers after scan task complete (#62014)
  Severity: High; Confidence: Medium; Modules: pkg/ddl, pkg/ddl/ingest, pkg/lightning/backend/local, tests/realtikvtest/addindextest3
  What: Potential large memory spike/OOM risk: table scan now buffers all result chunks for a PD-region-sized `TableScanTask` before sending to writers, removing the prior streaming/backpressure behavior.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-62014.json

- PR #62120: ddl/dxf: modify ingest param at runtime for add-index with global sort (#62120)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/ddl/ingest, pkg/disttask/framework, pkg/lightning/backend/local, pkg/lightning/backend/external
  What: Runtime concurrency updates for global-sort add-index don’t reliably carry over to external-engine creation for later subtasks because `engineManager.closeEngine` uses a stale `WorkerConcurrency` copy (separate from the updated `local.Backend`), risking resource oversubscription/stalls after downscaling.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-62120.json

- PR #62190: ddl: add retry for updateSelfVersion (#62190)
  Severity: High; Confidence: High; Modules: pkg/ddl/schemaver, pkg/ddl/util, pkg/infoschema/issyncer, tests/realtikvtest/ddltest
  What: High risk of permanent hang / restart-safety bypass when MDL is disabled: UpdateSelfVersion now retries etcd Put “unlimited” using a non-cancelable context (via callers passing context.Background) and a lease captured once. If the etcd session/lease becomes invalid (e.g., prolonged etcd outage > TTL or domain close), the Put can never succeed and Reload can block indefinitely, preventing SyncLoop from handling syncer.Done() to stop validator and restart the syncer.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-62190.json

- PR #62477: ddl: skip waiting version synced without MDL for multi-schema-change (#62477)
  Severity: High; Confidence: Medium; Modules: pkg/ddl, pkg/ddl/schemaver, pkg/meta/model
  What: Compatibility/correctness risk: in non-MDL mode the restart/owner-change pre-sync is skipped when `job.LastSchemaVersion==0`; for jobs persisted by older TiDB versions (field absent => decoded as 0), this can skip required schema-version synchronization and break the online DDL 2-version invariant.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-62477.json

- PR #62570: bootstrap: use reserved ID for system schemas/tables in nextgen (#62570)
  Severity: High; Confidence: High; Modules: pkg/meta, pkg/session, pkg/ddl, pkg/infoschema
  What: Nextgen bootstrap uses reserved IDs for system schemas/tables but only checks existence by ID; upgrading from an already-bootstrapped nextgen cluster with non-reserved system schema IDs can create duplicate `mysql`/`sys` schema names and cause infoschema/metadata nondeterminism.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-62570.json

- PR #62759: ddl,parser: modify schema to store the partial condition (#62759)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/meta/model, pkg/infoschema, pkg/executor, pkg/parser/ast, pkg/util/dbterror
  What: Partial primary key condition is inconsistently handled: CREATE TABLE blocks `PRIMARY KEY(...) WHERE ...`, but ALTER TABLE ADD PRIMARY KEY accepts it and then silently drops the predicate because `IndexOption.Condition` isn’t marshaled and `ConditionString` isn’t populated for PK jobs.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-62759.json

- PR #62762: ddl,tables: only write the index when it meets partial index condition (#62762)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/ddl/copr, pkg/meta, pkg/meta/model, pkg/table, pkg/table/tables, pkg/infoschema/perfschema
  What: Backward-incompatible metadata/DDL-job JSON key rename for partial index condition: `IndexInfo.ConditionExprString` and `IndexArg.ConditionString` changed tag names without a decode shim, so upgrades or mixed-version clusters can silently lose the partial-index predicate.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-62762.json

- PR #62979: table: keep `table.Columns` order by states during modifying column (#62979)
  Severity: High; Confidence: Medium; Modules: pkg/ddl, pkg/meta/model, pkg/util
  What: `removingObjPrefix` rename (`_Tomestone$_` → `_Tombstone$_`) can break mixed-version / rolling-upgrade compatibility for MODIFY COLUMN tombstone name normalization when legacy-prefixed metadata exists.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-62979.json

- PR #63397: disttask: support metering on next-gen (#63397)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/domain, pkg/disttask/framework/metering, pkg/config
  What: Potential nil-pointer panic on metering initialization: `InitDistTaskLoop` assumes `metering.NewMeter` never returns `nil` on success, but `NewMeter` can return `(nil, nil)` for configs with empty bucket (reachable via valid-looking URIs), and the code still starts the flush loop on `m`.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-63397.json

- PR #63901: ddl: find old column by name for old version modify column job (#63901)
  Severity: High; Confidence: Medium; Modules: pkg/ddl, pkg/meta/model
  What: `getModifyColumnInfo` can misidentify the target column when `OldColumnID` is missing: it now looks up `_Tombstone$_<oldName>` before `<oldName>`, so tables that already contain a `_Tombstone$_<oldName>` column can cause `MODIFY COLUMN <oldName>` to operate on the wrong column.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-63901.json

- PR #63970: modify column: support ingest/DXF mode to recreate indexes (#63970)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/ddl/ingest, pkg/meta/model
  What: Rolling-upgrade incompatibility: in-flight `MODIFY COLUMN` reorg jobs from pre-#63970 can get stuck because `AnalyzeState` moved into `job.ReorgMeta` and a new persisted `Stage` gate was introduced, but there is no migration/default handling for missing `reorg_meta.stage/analyze_state`.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-63970.json

- PR #64096: ddl: add timeout mechanism for add index analyze (#64096)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/meta/model, pkg/executor, tests/realtikvtest
  What: `AnalyzeStateTimeout` (value 4) is persisted into DDL job metadata but first-parent code has no handler/default for this new value. During mixed-version rolling upgrades, if ownership falls back to an older TiDB while an add-index/modify-column job is between “set AnalyzeStateTimeout” and “publish/finalize”, the older owner can’t advance the state machine and the DDL job can get stuck.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-64096.json

- PR #64111: ddl: add unit test for lossy column change (#64111)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/table/tables
  What: Potential nil-pointer panic in `worker.onModifyColumn`: deferred `failpoint.InjectCall` dereferences `args` even when `GetModifyColumnArgs` returns an error (args can be `nil`).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-64111.json

- PR #64171: ddl: add checkpoint operator for DXF local add index (#64171)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/ddl/ingest, pkg/disttask/framework, pkg/disttask/framework/storage
  What: Regression in checkpoint initialization: when a checkpoint object exists but has `ts=0` (the default initial reorg_meta state), `CheckpointManager` now returns early and never allocates/persists an initial ingest TS. This undermines the intended failover/idempotency behavior around the first import and delays persisting `instance_addr`.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-64171.json

- PR #64337: ddl, model: fix unexpected missing analyze for multi schema change (#64337)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/meta/model, pkg/executor
  What: Potential nil dereference introduced in the new multi-schema-change embedded-analyze path: `onMultiSchemaChange` now always calls `doAnalyzeWithoutReorg`, which assumes `job.ReorgMeta` is non-nil, but upgrade-compat initialization only runs on reorg/background workers; MSC jobs can be dispatched to the general worker (`reorg=0`).
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-64337.json

- PR #64356: ddl: add auto ID rebase create table submitted by BR (#64356)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/meta, pkg/meta/autoid, pkg/infoschema
  What: `autoIDAccessor.CopyTo` now treats `curr==0` as “nothing to copy”, but 0 can be an explicitly stored value (not just “missing”). This can break sequence RESTART semantics across schema transfers: after `ALTER SEQUENCE ... RESTART WITH 1` (base becomes 0) and a subsequent rename back to a schema with a stale non-zero sequence value, the stale value can survive because the explicit 0 is not copied.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-64356.json

- PR #64584: add-index/import-into: meter row and kv size data and disable some options for import-into in nextgen (#64584)
  Severity: High; Confidence: Medium; Modules: pkg/ddl, pkg/disttask/framework/handle, pkg/disttask/framework/metering, pkg/disttask/importinto, pkg/executor/importer
  What: IMPORT INTO cleanup can become effectively non-retriable if metering send fails: `CleanUp` returns error after deleting sorted data, but still redacts `Plan.CloudStorageURI` (masking credential query params). On the next cleanup retry, the redacted CloudStorageURI may no longer authenticate to the object store, preventing cleanup from reaching metering again and leaving finished tasks stuck in active task tables.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-64584.json

- PR #65049: ddl: sync affinity group for pd and support `show affinity` (#65049)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/domain/affinity, pkg/domain/infosync, pkg/infoschema/context, pkg/executor, pkg/planner/core
  What: `SHOW AFFINITY` bypasses database/table privilege visibility checks and can expose affinity-enabled objects (and PD topology/state) to unprivileged users.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-65049.json

- PR #65133: ddl: support region split policy (#65133)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/meta/model
  What: Persistent region split policy accepts arbitrarily large `REGIONS` values (no `SplitRegionMaxNum` cap), enabling unbounded split-key generation and potential DoS/stability issues.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-65133.json

- PR #65380: table: Non-clustered table non-unique global index needs partid in key, v1 (#65380)
  Severity: High; Confidence: High; Modules: pkg/ddl, pkg/meta/model, pkg/tablecodec, pkg/table/tables, pkg/executor, tests/integrationtest
  What: Mixed-version/downgrade incompatibility: `GlobalIndexVersionV1` changes global non-distinct index key format by inserting `PartitionIDFlag`+partitionID into the key, but there is no compatibility gating to prevent creating these v1 indexes when older TiDB binaries may still be serving traffic. Older binaries would ignore the new metadata field and also cannot decode the new key suffix flag (126), risking query/index correctness during rolling upgrades or after downgrade.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-65380.json

- PR #66701: importinto: introduce a param to control conflict resolution (#66701)
  Severity: High; Confidence: High; Modules: pkg/dxf/importinto, pkg/executor/importer
  What: `Plan.GetOnDupKeyMode()` defaults missing (legacy) task metadata to `error`, which changes conflict handling for IMPORT INTO DXF tasks created before this option existed (legacy capture → now error) when those tasks are resumed after upgrade.
  Result: /Users/bba/.codex-context/tidb--906fbbe4--0f8c9a77/artifacts/pr-review-2y/results/pr-66701.json

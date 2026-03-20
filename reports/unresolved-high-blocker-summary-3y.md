# Combined Unresolved Blocker/High Findings

- Generated at: 2026-03-20T13:28:55.302224+08:00
- Worthy deep-review queue: 484 PRs, fully reviewed
- Unresolved findings: 80 total
- Blocker: 12
- High: 68

## Blocker

- PR #40634 (2y): ddl: enable modify column on partitioned table (#40634)
  What: `MODIFY COLUMN` on partitioned tables is enabled, but partitioning-column safety checks don’t account for `PARTITION BY KEY`: lossy conversions on KEY-partitioning columns (e.g., shrinking string length, reducing time/datetime FSP, or other conversions that can change value under non-strict SQL mode) can change the hashed partition key while the DDL reorg rewrites rows in-place without moving them across physical partitions. This can leave rows stored in the “wrong” partition, causing incorrect results under partition pruning/point-get.

- PR #47450 (3y_to_2y): ddl: fix can't pause add index in dist task mode (#47450)
  What: `executeDistGlobalTask` can incorrectly treat a paused dist global task as successful completion when resuming a paused DDL job while the task is still in `pausing`/transitions to `paused` (resume is a silent no-op for non-`paused` tasks, and `WaitGlobalTask` returns `nil` on `paused`).

- PR #50917 (3y_to_2y): *: store db ID in model.TableInfo and move SchemaByTable method out from InfoSchema (#50917)
  What: Local temporary table truncation can panic after dropping and recreating a database with the same name: `SessionTables` retains a stale `dbInfo` keyed by schema name, but new temp tables get `TableInfo.DBID` set to the new schema ID. `SchemaByID` then fails to find the schema (returns `(nil,false)`), and `TruncateLocalTemporaryTable` ignores the boolean and passes `nil` into `AddTable`, leading to a nil dereference.

- PR #53277 (2y): ddl: Reorganize partition supporting global index (#53277)
  What: `findNextNonTouchedPartitionID` has a loop whose DroppingDefinitions membership check is evaluated only once (the `notFoundErr` condition is never recomputed). If the immediate next partition is a dropped partition, the loop will keep advancing to the end and return 0, skipping remaining non-touched partitions. This can leave rebuilt global indexes incomplete after `REORGANIZE PARTITION` when dropped partitions are in the middle of `pi.Definitions`.

- PR #55129 (2y): domain,infoschema: avoid v1/v2 switch when loading snapshot infoschema (#55129)
  What: Snapshot infoschema full-load can build infoschema v1 while `tidb_schema_cache_size>0`, but schema/table fetching is still optimized for v2 and only loads “must-load” table infos; v1 builder then omits most tables, yielding an incomplete snapshot infoschema.

- PR #55847 (2y): infoschema, meta: fix wrong auto id after `rename table` (#55847)
  What: `Allocator.Transfer` + `AutoIDAccessor.CopyTo` migrates and switches autoid ownership to the new schema during infoschema diff apply, but cross-schema rename explicitly allows concurrent DML; in a multi-node mixed-schema window this can split allocations across old/new meta keys and can non-monotonically overwrite the target autoid, risking duplicate auto-increment IDs or wrong base.

- PR #56786 (2y): ddl: Corrected index management during REORGANIZE PARTITION (#56786)
  What: `PartitionInfo.DDLChangedIndex` is not cleared on successful `ActionReorganizePartition` completion. Because it is persisted in table meta and later reused by both `tables.newPartitionedTable()` (to decide which indexes exist on dropping/adding partitions) and rollback conversion (to decide which indexes to drop), stale entries can break subsequent REORGANIZE PARTITION executions and can even cause rollback to drop an index that pre-existed the current DDL.

- PR #57770 (2y): ddl/ingest: remove backend context manager and refactor code (#57770)
  What: `IngestIfQuotaExceeded` can import+reset engines while writers are concurrently appending, because flush locks are released before `unsafeImportAndResetAllEngines()`. This can race with local backend import/reset and lead to data loss or corruption in add-index ingest.

- PR #59407 (2y): ddl: split partition tables subtasks like normal tables (#59407)
  What: `generatePlanForPhysicalTable` can silently return partial/empty subtask plans by swallowing planning-time errors (notably `allocNewTS` failures and potentially `getTableRange` errors). With this PR, partitioned tables now use this function, so an intermittent PD/GetTS or snapshot-range error can lead to an incomplete add-index backfill without the framework noticing.

- PR #61200 (2y): ddl, disttask: import into integrate table mode (#61200)
  What: `IMPORT INTO` classic-kernel submission can leave a table permanently in `TableModeImport` if task creation/commit fails after the mode switch, because `ddl.AlterTableMode` is not transactional with `TaskManager.WithNewTxn` and there is no rollback/compensation on error.

- PR #63465 (2y): ddl: make some `MODIFY COLUMN` skip row reorg (#63465)
  What: Rollback cleanup for skip-row-reorg `MODIFY COLUMN` can clear an existing `NOT NULL` constraint when only `ChangingFieldType` was used as an in-progress marker (i.e., when NULLability wasn’t being changed).

- PR #63965 (2y): ddl: some code refactor for lossy ddl optimization (#63965)
  What: Cancel/rollback path for `ActionModifyColumn` assumes refactored `ModifyColumnType` enum values, but does not handle the legacy `mysql.TypeNull` (6) value used by older jobs, which can leave a job stuck in `JobStateCancelling`.

## High

- PR #44442 (3y_to_2y): lightning/backend/local: fix `buildIndexDupTasks` (#44442)
  What: `buildIndexDupTasks` omits `tikvCodec.EncodeRange`, so remote duplicate detection for a specific index (`IndexID != 0`, used by DDL ingest unique-index remote dup check) can scan incorrect key ranges in keyspace/APIv2 mode and miss duplicates.

- PR #47084 (3y_to_2y): ddl: clean up cloud storage files after global sort (#47084)
  What: `S3Storage.DeleteFiles` ignores per-object failures returned by S3 `DeleteObjects`, so cleanup can incorrectly succeed even when some objects were not deleted.

- PR #47648 (3y_to_2y): *: record end key rather than max key (#47648)
  What: Rolling-upgrade/resume compatibility break: persisted DDL/DXF global-sort subtask meta produced by the first parent (`start_key/end_key`, `min-key/max-key`) will not decode correctly after this commit’s JSON tag/schema changes, causing nil/invalid key boundaries to propagate into range splitting and execution.

- PR #48797 (3y_to_2y): ddl,lightning: fix ingest data unexpectedly using zero commit ts (#48797)
  What: ResetEngine failure cleanup can orphan local-engine ingest goroutines/resources: DDL calls `ClosedEngine.Cleanup` (which delegates to `CleanupEngine`) without first closing the engine, then drops `openedEngine` references, making it hard/impossible to stop `ingestSSTLoop` and writers deterministically.

- PR #50311 (3y_to_2y): disttask: merge transfer task/subtask (#50311)
  What: `TransferTasks2History` can incorrectly return nil (and commit) when deleting tasks from `tidb_global_task` fails, due to overwriting the delete error with later subtask-transfer results.

- PR #50475 (3y_to_2y): disttask: add available resource param to step executor (#50475)
  What: DXF/disttask manager construction becomes a hard startup dependency on `memory.MemTotal() > 0`. In container/cgroup configurations where the memory controller isn’t detected, `memory.MemTotal()` can return 0 (without error), causing `BuildManager` to return an error and `Domain.InitDistTaskLoop` to abort TiDB startup.

- PR #50550 (3y_to_2y): disttask: remove revert subtask (#50550)
  What: High: Rolling-upgrade incompatibility — old schedulers wait on legacy revert subtasks, but new executors no longer process them, so tasks can hang in `reverting` during mixed-version clusters.

- PR #51071 (3y_to_2y): disttask: refine manager retry (#51071)
  What: `Manager.InitMeta()` (and other manager metadata writes) now reuse scheduler/task-executor SQL retry settings (exponential backoff starting at 3s, up to 30 retries), which can block synchronous startup and manager-loop paths for ~14 minutes on persistent metadata/SQL errors, despite the caller treating `InitMeta` failure as non-fatal.

- PR #51242 (3y_to_2y): ddl, disttask: add scan-region check before dispatch subtasks (#51242)
  What: `handle.RunWithRetry` is misused: the non-continuous-regions path returns `(retryable=true, err=nil)`, which cannot trigger retries. This makes the new “retry with backoff before dispatch” logic non-functional and causes immediate plan-generation failure whenever the region scan is temporarily non-continuous.

- PR #52873 (2y): pkg/infoschema: revert cache policy ref (#52873)
  What: `ALTER/REPLACE PLACEMENT POLICY` may not refresh partition-level placement bundles when the altered policy is referenced only by partitions (table-level `PlacementPolicyRef` is nil), leaving partition bundles stale in infoschema.

- PR #53217 (2y): ddl: sync schema version using watch, notify sessions on owner node by job id (#53217)
  What: `SyncJobSchemaVerLoop` now performs a full-prefix etcd `Get`/`Watch` over `/tidb/ddl/all_schema_by_job_versions/` and caches all job-version keys, but the code only deletes these keys for `JobStateSynced` jobs. Terminal rollback/cancel paths (and any deletion failures) can leave per-job keys behind indefinitely, making the watched keyspace and cache unbounded over time and risking MDL schema-sync stalls or resource blowups.

- PR #53301 (2y): *: keep in-memory resident for model.TableInfo with special attributes (#53301)
  What: `infoschema` v2 `ListTablesWithSpecialAttribute` can miss special-attribute tables after cross-database rename because it deduplicates resident entries using only `tableID`, while resident entries are keyed/ordered by `(dbName, tableID, schemaVersion)`, allowing a tomb entry for the old DB to suppress the live entry for the new DB.

- PR #53544 (2y): ddl: Reorganize Partition failure/rollback tests and fixes (#53544)
  What: Backward-compatibility break in persisted metadata: changing `PartitionInfo.NewPartitionIDs` from an untagged field (JSON key `NewPartitionIDs`) to a tagged snake_case key (`new_partition_ids`) prevents new code from decoding legacy metadata during in-flight truncate-partition DDL, and can bypass truncating-partition write blocking.

- PR #53548 (2y): ddl: decouple job scheduler from 'ddl' and make it run/exit as owner changes (#53548)
  What: FLASHBACK CLUSTER: `splitRegionsByKeyRanges` retries `SplitRegions` in a tight infinite loop without checking context cancellation. After this change, it is invoked with the scheduler/worker context (canceled on owner change), so an owner change/shutdown during StageWriteOnly can spin/hang and block scheduler exit.

- PR #53821 (2y): infoschema: make build bundle faster for infoschema v2 (#53821)
  What: Full placement-bundle rebuild in infoschema v2 can miss some placement-policy tables after cross-database `RENAME TABLE`, due to `ListTablesWithSpecialAttribute` iterating `tableInfoResident` ordered by dbName and deduping only consecutive `tableID`s (with tomb handling after dedup). In certain key-order adjacency cases, the old-db tomb record can cause the new-db live record to be skipped, leaving `ruleBundleMap` incomplete.

- PR #54008 (2y): ddl: clean ingest temp data dir in background loop (#54008)
  What: Potential regression: stale ingest temp dirs are no longer cleaned just-in-time before the ingest disk pre-check, because cleanup moved to a background loop that first runs only after a 1-minute tick. This creates a window where `PreCheckUsage()` can fail due to stale-dir disk bloat and the add-index job can be cancelled (non-retryable) even though the new cleanup loop would have freed space soon.

- PR #54185 (2y): *: optimize the speed of full load (#54185)
  What: Schema-cache full-load fast path uses regex + partial unescaping for table names; JSON `\uXXXX` escapes (e.g., from `<`, `>`, `&`) are not decoded, so `TableName2ID` keys can be wrong and infoschema v2 name lookups can fail.

- PR #54804 (2y): pkg/parser: support SWITCH_GROUP syntax for runaway watch (#54804)
  What: Bootstrap schema upgrade adds `switch_group_name` to runaway watch system tables, but inserts rely on `INSERT ... VALUES` without column lists; during rolling upgrades, older TiDB binaries will start failing inserts after any newer node upgrades the tables.

- PR #55025 (2y): ddl: integrate fast create table into normal general DDL workflow (#55025)
  What: Merged `ActionCreateTables` jobs drop shared `InvolvingSchemaInfo` (e.g. placement policy refs), allowing the DDL scheduler to run conflicting policy DDL concurrently and potentially commit tables with dangling placement policy references.

- PR #55049 (2y): ddl: record get owner TS and compare it before runReorgJob quit (#55049)
  What: Owner-generation stamp uses `time.Now().Unix()` (seconds), so rapid owner re-elections can reuse the same `ownerTS`, defeating the stale-result filter in `runReorgJob()`.

- PR #55264 (2y): *: Require GLOBAL IndexOption for creating Global Index (#55264)
  What: Forward-compat regression for in-flight DDL jobs: old queued add-index / add-primary-key jobs that relied on the legacy job-arg `global` boolean may be executed with `IndexInfo.Global=false` after this commit, because the worker no longer decodes/uses the legacy `global` flag and instead derives `IndexInfo.Global` solely from `IndexOption.Global`.

- PR #55891 (2y): ddl, planner: avoid running queries on the non-Public state table (#55891)
  What: Implicit foreign-key cascade DML is likely broken by the new “non-Public table => ErrTableNotExists” planner gate: the PR/comment states cascades should still modify `WriteOnly/DeleteOnly` tables, but FK cascades are executed via internal SQL planning that now rejects those tables.

- PR #56020 (2y): resource_control: support set resource limit for background tasks (#56020)
  What: `BACKGROUND=NULL` / `BACKGROUND=()` is not honored: DDL code always re-initializes `ResourceGroupSettings.Background` to a non-nil empty struct even when the AST indicates “clear background settings”.

- PR #56130 (2y): ddl: args v2 for add/drop/rename index, include PK/vector-index (#56130)
  What: JobVersion1 finished add-index args lose the global-index flag: encoder uses `IndexArg.Global` instead of `IndexArg.IsGlobal`, so global indexes are treated as non-global in delete-range/GC paths.

- PR #56194 (2y): ddl: args v2 for lock/unlock/repair table and recover table/schema (#56194)
  What: `JobVersion1` lock/unlock table DDL jobs are not backward-compatible across upgrade: this commit changes the persisted JSON field names for the lock/unlock args (CamelCase → snake_case tags), so older in-flight jobs may decode to empty args and then either never finish (lock) or finish without unlocking (unlock).

- PR #56409 (2y): *: support the vector index (#56409)
  What: Vector index DDL can wait indefinitely because TiFlash schema-sync and progress polling treat certain failures as success (non-2xx HTTP responses and empty `information_schema.tiflash_indexes` result sets).

- PR #56630 (2y): ddl: add `lease not found` and `deadline exceed` to retryable errors (#56630)
  What: Dist add-index disttask now retries nearly all non-`*terror.Error` errors (unknown errors default to retryable), which can keep subtasks in running state indefinitely (no retry budget) and leave DDL jobs stuck waiting for the global task on persistent infra/logic errors.

- PR #56702 (2y): ddl: support dropping multiple foreign keys (#56702)
  What: Multi-schema `DROP FOREIGN KEY` subjobs are not rollback-safe: a later subjob failure (e.g., duplicate `DROP FOREIGN KEY` on the same name, or mixed DDL where a later operation fails) can leave earlier FK drops applied even though the overall `ALTER TABLE` fails.

- PR #56864 (2y): ddl: enable the ddl notifier by default (#56864)
  What: Potential nil-store panic introduced by enabling notifier publish unconditionally: `asyncNotifyEvent` now always calls `notifier.PubSchemeChangeToStore` using `jobCtx.eventPublishStore`, but `EventPublishStore` remains optional in `ddl.NewDDL` options and the only non-nil check is `intest.Assert` (no-op in release builds).

- PR #56963 (2y): ddl: mitigate the issue that multiple owner might exist during force to be owner (#56963)
  What: `ForceToBeOwner` swallows errors after retries (always returns nil), so upgrade-mode startup can proceed even when “force to be owner” did not succeed.

- PR #57017 (2y): *: let TempIndex support encode/decode partitionID flag (#57017)
  What: TempIndexValue encoding change (global unique index delete marker adds 'p'+partitionID bytes) is not forward-compatible: pre-#57017 TiDB cannot decode new-format temp-index values, risking rolling upgrade/downgrade failures when temp-index is active.

- PR #57336 (2y): ddl: support modify the related reorg config by SQL (#57336)
  What: `ADMIN ALTER DDL JOBS` incorrectly mutates `Job.AdminOperator`, which is also used as the pause/resume ownership guard (system vs end user). This breaks the DDL job pause/resume state-machine invariant, especially for system-paused jobs during upgrading.

- PR #57392 (2y): ddl: add COMMENTS column to DDL jobs and enhance job reorg meta handling (#57392)
  What: `ADMIN SHOW DDL JOBS` `COMMENTS` column is mis-associated with rows for `ActionMultiSchemaChange` jobs due to column-12 append order, causing comments to shift between the parent job row and subjob rows.

- PR #57468 (2y): ddl: dynamically adjusting the concurrency and batch size of reorganization job (#57468)
  What: Shrinking txn-backfill concurrency can cancel workers in a way that drops in-flight task results (including errors/panics), so reorg/add-index may continue and complete even though some backfill tasks failed.

- PR #57766 (2y): *: support cancel query like 'select * from information_schema.tables' (#57766)
  What: `listTablesForEachSchema` incorrectly returns `errors.Trace(err)` (nil) when `ctx.Err()!=nil`, swallowing cancellation and allowing information_schema scans to “succeed” under cancellation.

- PR #57813 (2y): ddl: check context done in isReorgRunnable function (#57813)
  What: `backfillWorker.handleBackfillTask` checks reorg runnability using `d.ctx` (ddl/owner lifetime context) rather than the per-job step context that is canceled on job pause/cancel. Since backfill txn operations use `context.Background()`, this prevents timely interruption of long-running txn backfill loops on `ADMIN CANCEL/PAUSE`, and can delay scheduler/worker shutdown that waits on backfill workers.

- PR #57945 (2y): ddl: handle context done after sending DDL jobs (#57945)
  What: Possible `ddl.Stop()` hang: `DoDDLJobWrapper` can return on DDL context cancel before receiving the job submission result, but `JobSubmitter.NotifyResult` uses blocking sends on unbuffered `ResultCh`; if cancellation happens while the submitter is still going to `NotifyResult`, it can block forever (no receiver), preventing `d.wg.Wait()` in `ddl.close()` from completing.

- PR #58062 (2y): domain,infoschema: make infoschema activity block GC safepoint advancing (#58062)
  What: `InfoCache.GetAndResetRecentInfoSchemaTS()` uses a non-atomic `Load()+Store()` reset, so concurrent `infoschemaV2.keepAlive()` updates can be lost, undermining the GC-safepoint pinning this change introduces.

- PR #58990 (2y): dxf: support modify app param on scheduler part (#58990)
  What: `TaskTypeExample` scheduler lacks an explicit `ModifyMeta` implementation; due to `BaseScheduler` embedding/forwarding `Extension`, calling `ModifyMeta` during task modification can recurse into itself and crash.

- PR #59009 (2y): ddl: Implement TableMode feature (#59009)
  What: TableMode protection can be bypassed for `ALTER TABLE ... ADD FOREIGN KEY` when `foreign_key_checks=0`, allowing schema changes (and possibly auto-created indexes) on tables in Import/Restore mode.

- PR #59165 (2y): ddl: reuse backend for DXF subtasks of same step (#59165)
  What: DXF local-sort read-index step can leak open engines on early errors: with backend reuse and `BackendCtx.Close()` no longer closing the backend, a `buildLocalStorePipeline` failure after `Register` returns without engine cleanup, leaving engines open in the shared `*local.Backend` and breaking subsequent retries/subtasks (Pebble lock/open-engine failures, potential step wedging).

- PR #59343 (2y): ddl: modify param at runtime for add-index on DXF local sort (#59343)
  What: DXF runtime concurrency modification does not update the StepExecutor’s stored `GetResource()` when applied during a running subtask. Because `readIndexStepExecutor.RunSubtask` re-reads concurrency from `GetResource().CPU.Capacity()` at each subtask start, a successful mid-subtask concurrency decrease can be lost for later subtasks, potentially rebuilding pipelines with stale (higher) concurrency and violating resource/slot assumptions.

- PR #59966 (2y): globalsort: write sorted kv meta to external storage (#59966)
  What: Global-sort add-index subtask meta format becomes backward-incompatible (ExternalPath + external storage), but disttask scheduling has no version/capability gating, so mixed-version clusters can dispatch these subtasks to older executors that can’t read required meta.

- PR #60395 (2y): disttask: distribute merge sort subtasks to all available nodes (#60395)
  What: `external.DivideMergeSortDataFiles` is introduced/used to distribute merge-sort subtasks by node count, but its remainder batching can exceed the documented `MergeSortFileCountStep` cap, making its `maxTargetFilesPerSubtask := max(16, mergeConc)` overlap-budget check unsound. For some `mergeConc>16` + large-file-count inputs, this can produce merge-sort subtasks whose internal merge fanout (and thus downstream overlap/reader pressure) exceeds the intended `MergeSortOverlapThreshold` budget, risking global-sort add-index instability.

- PR #60403 (2y): ddl: Fix ddl owner doesn't use etcd keyspace prefix (#60403)
  What: Keyspace (API V2) rolling upgrade split-brain risk: DDL owner election keyspace prefix change makes old and new versions campaign on different etcd paths, allowing multiple concurrent DDL owners.

- PR #60828 (2y): ddl: update row count periodically when running reorg job (#60828)
  What: Pause handling can leave reorg work running after a job becomes `JobStatePaused`: because reorg now returns periodic timeouts while preserving `jobCtx.stepCtx`, but the `JobStatePausing/Paused` fast-path returns without canceling that long-lived context, and the scheduler exits on paused (final state), the background reorg goroutine/workers can continue after a successful pause.

- PR #61865 (2y): ddl: refresh TiFlash PlacementRules periodically (#61865)
  What: Periodic placement-rule “repair” can override user TiFlash replica changes due to stale job args: it submits `ActionSetTiFlashReplica` using snapshot Count/Labels, and when that job executes later it rewrites `TiFlashReplica` metadata even if a prior queued user DDL already changed/disabled TiFlash replica.

- PR #62014 (2y): ddl: send result chunks to writers after scan task complete (#62014)
  What: Potential large memory spike/OOM risk: table scan now buffers all result chunks for a PD-region-sized `TableScanTask` before sending to writers, removing the prior streaming/backpressure behavior.

- PR #62120 (2y): ddl/dxf: modify ingest param at runtime for add-index with global sort (#62120)
  What: Runtime concurrency updates for global-sort add-index don’t reliably carry over to external-engine creation for later subtasks because `engineManager.closeEngine` uses a stale `WorkerConcurrency` copy (separate from the updated `local.Backend`), risking resource oversubscription/stalls after downscaling.

- PR #62190 (2y): ddl: add retry for updateSelfVersion (#62190)
  What: High risk of permanent hang / restart-safety bypass when MDL is disabled: UpdateSelfVersion now retries etcd Put “unlimited” using a non-cancelable context (via callers passing context.Background) and a lease captured once. If the etcd session/lease becomes invalid (e.g., prolonged etcd outage > TTL or domain close), the Put can never succeed and Reload can block indefinitely, preventing SyncLoop from handling syncer.Done() to stop validator and restart the syncer.

- PR #62477 (2y): ddl: skip waiting version synced without MDL for multi-schema-change (#62477)
  What: Compatibility/correctness risk: in non-MDL mode the restart/owner-change pre-sync is skipped when `job.LastSchemaVersion==0`; for jobs persisted by older TiDB versions (field absent => decoded as 0), this can skip required schema-version synchronization and break the online DDL 2-version invariant.

- PR #62570 (2y): bootstrap: use reserved ID for system schemas/tables in nextgen (#62570)
  What: Nextgen bootstrap uses reserved IDs for system schemas/tables but only checks existence by ID; upgrading from an already-bootstrapped nextgen cluster with non-reserved system schema IDs can create duplicate `mysql`/`sys` schema names and cause infoschema/metadata nondeterminism.

- PR #62759 (2y): ddl,parser: modify schema to store the partial condition (#62759)
  What: Partial primary key condition is inconsistently handled: CREATE TABLE blocks `PRIMARY KEY(...) WHERE ...`, but ALTER TABLE ADD PRIMARY KEY accepts it and then silently drops the predicate because `IndexOption.Condition` isn’t marshaled and `ConditionString` isn’t populated for PK jobs.

- PR #62762 (2y): ddl,tables: only write the index when it meets partial index condition (#62762)
  What: Backward-incompatible metadata/DDL-job JSON key rename for partial index condition: `IndexInfo.ConditionExprString` and `IndexArg.ConditionString` changed tag names without a decode shim, so upgrades or mixed-version clusters can silently lose the partial-index predicate.

- PR #62979 (2y): table: keep `table.Columns` order by states during modifying column (#62979)
  What: `removingObjPrefix` rename (`_Tomestone$_` → `_Tombstone$_`) can break mixed-version / rolling-upgrade compatibility for MODIFY COLUMN tombstone name normalization when legacy-prefixed metadata exists.

- PR #63397 (2y): disttask: support metering on next-gen (#63397)
  What: Potential nil-pointer panic on metering initialization: `InitDistTaskLoop` assumes `metering.NewMeter` never returns `nil` on success, but `NewMeter` can return `(nil, nil)` for configs with empty bucket (reachable via valid-looking URIs), and the code still starts the flush loop on `m`.

- PR #63901 (2y): ddl: find old column by name for old version modify column job (#63901)
  What: `getModifyColumnInfo` can misidentify the target column when `OldColumnID` is missing: it now looks up `_Tombstone$_<oldName>` before `<oldName>`, so tables that already contain a `_Tombstone$_<oldName>` column can cause `MODIFY COLUMN <oldName>` to operate on the wrong column.

- PR #63970 (2y): modify column: support ingest/DXF mode to recreate indexes (#63970)
  What: Rolling-upgrade incompatibility: in-flight `MODIFY COLUMN` reorg jobs from pre-#63970 can get stuck because `AnalyzeState` moved into `job.ReorgMeta` and a new persisted `Stage` gate was introduced, but there is no migration/default handling for missing `reorg_meta.stage/analyze_state`.

- PR #64096 (2y): ddl: add timeout mechanism for add index analyze (#64096)
  What: `AnalyzeStateTimeout` (value 4) is persisted into DDL job metadata but first-parent code has no handler/default for this new value. During mixed-version rolling upgrades, if ownership falls back to an older TiDB while an add-index/modify-column job is between “set AnalyzeStateTimeout” and “publish/finalize”, the older owner can’t advance the state machine and the DDL job can get stuck.

- PR #64111 (2y): ddl: add unit test for lossy column change (#64111)
  What: Potential nil-pointer panic in `worker.onModifyColumn`: deferred `failpoint.InjectCall` dereferences `args` even when `GetModifyColumnArgs` returns an error (args can be `nil`).

- PR #64171 (2y): ddl: add checkpoint operator for DXF local add index (#64171)
  What: Regression in checkpoint initialization: when a checkpoint object exists but has `ts=0` (the default initial reorg_meta state), `CheckpointManager` now returns early and never allocates/persists an initial ingest TS. This undermines the intended failover/idempotency behavior around the first import and delays persisting `instance_addr`.

- PR #64337 (2y): ddl, model: fix unexpected missing analyze for multi schema change (#64337)
  What: Potential nil dereference introduced in the new multi-schema-change embedded-analyze path: `onMultiSchemaChange` now always calls `doAnalyzeWithoutReorg`, which assumes `job.ReorgMeta` is non-nil, but upgrade-compat initialization only runs on reorg/background workers; MSC jobs can be dispatched to the general worker (`reorg=0`).

- PR #64356 (2y): ddl: add auto ID rebase create table submitted by BR (#64356)
  What: `autoIDAccessor.CopyTo` now treats `curr==0` as “nothing to copy”, but 0 can be an explicitly stored value (not just “missing”). This can break sequence RESTART semantics across schema transfers: after `ALTER SEQUENCE ... RESTART WITH 1` (base becomes 0) and a subsequent rename back to a schema with a stale non-zero sequence value, the stale value can survive because the explicit 0 is not copied.

- PR #64584 (2y): add-index/import-into: meter row and kv size data and disable some options for import-into in nextgen (#64584)
  What: IMPORT INTO cleanup can become effectively non-retriable if metering send fails: `CleanUp` returns error after deleting sorted data, but still redacts `Plan.CloudStorageURI` (masking credential query params). On the next cleanup retry, the redacted CloudStorageURI may no longer authenticate to the object store, preventing cleanup from reaching metering again and leaving finished tasks stuck in active task tables.

- PR #65049 (2y): ddl: sync affinity group for pd and support `show affinity` (#65049)
  What: `SHOW AFFINITY` bypasses database/table privilege visibility checks and can expose affinity-enabled objects (and PD topology/state) to unprivileged users.

- PR #65133 (2y): ddl: support region split policy (#65133)
  What: Persistent region split policy accepts arbitrarily large `REGIONS` values (no `SplitRegionMaxNum` cap), enabling unbounded split-key generation and potential DoS/stability issues.

- PR #65380 (2y): table: Non-clustered table non-unique global index needs partid in key, v1 (#65380)
  What: Mixed-version/downgrade incompatibility: `GlobalIndexVersionV1` changes global non-distinct index key format by inserting `PartitionIDFlag`+partitionID into the key, but there is no compatibility gating to prevent creating these v1 indexes when older TiDB binaries may still be serving traffic. Older binaries would ignore the new metadata field and also cannot decode the new key suffix flag (126), risking query/index correctness during rolling upgrades or after downgrade.

- PR #66701 (2y): importinto: introduce a param to control conflict resolution (#66701)
  What: `Plan.GetOnDupKeyMode()` defaults missing (legacy) task metadata to `error`, which changes conflict handling for IMPORT INTO DXF tasks created before this option existed (legacy capture → now error) when those tasks are resumed after upgrade.

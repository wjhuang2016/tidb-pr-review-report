# TiDB 最近三年相关 PR 未修复高危问题最终报告

## 范围与方法

- 范围：过去 3 年合入 `master`，且属于 `DDL / add index / infoschema / dxf / metadata` 的 PR。
- 流程：先做相关性筛选，再做 worthy 筛选，再对 worthy PR 用 `worktree + merged commit + 静态深审` 逐条复核。
- 过滤条件：只保留 `Blocker / High`，并排除后续已经修复的问题。
- 说明：本报告不包含测试结果；全部结论来自 merged commit 上下文的静态代码审查。

## 总览

- Worthy 深审范围：`484` 条 PR，已全部完成。
- 未修复高危问题：`80` 条，其中 `Blocker 12` 条，`High 68` 条。
- 时间窗拆分：近两年 `69` 条（`Blocker 10 / High 59`），更早一年 `11` 条（`Blocker 2 / High 9`）。

## 时间分布

### 按年

- `2023`：`5`
- `2024`：`39`
- `2025`：`33`
- `2026`：`3`

### 按季度

- `2023-Q2`：`1`（`High 1`）
- `2023-Q3`：`1`（`High 1`）
- `2023-Q4`：`3`（`Blocker 1，High 2`）
- `2024-Q1`：`6`（`Blocker 1，High 5`）
- `2024-Q2`：`7`（`Blocker 1，High 6`）
- `2024-Q3`：`11`（`Blocker 2，High 9`）
- `2024-Q4`：`15`（`Blocker 1，High 14`）
- `2025-Q1`：`6`（`Blocker 2，High 4`）
- `2025-Q2`：`5`（`High 5`）
- `2025-Q3`：`7`（`Blocker 1，High 6`）
- `2025-Q4`：`15`（`Blocker 3，High 12`）
- `2026-Q1`：`3`（`High 3`）

### 按月峰值

- `2024-11`：`8`
- `2025-10`：`7`
- `2024-09`：`6`
- `2024-10`：`5`
- `2025-11`：`5`

### 简析

- 全部 `80` 条未修复高危项在时间上分成三段：`2023-Q4 ~ 2024-Q1` 的早期分布式任务/状态机问题、`2024-Q3 ~ 2024-Q4` 的 infoschema/metadata/分区问题高峰，以及 `2025-Q4` 的 add-index/DXF/兼容性问题高峰。
- 从类型上看，较早窗口的问题更偏 disttask / pause-resume / retry / mixed-version 兼容性；近两年窗口则更偏 infoschema、modify column、global index、checkpoint、import-into 与 DXF 执行路径。
- `Blocker` 在三个区段都出现过，但集中高点出现在 `2025-Q4`，说明后期不仅问题数量更多，严重度也更高，且更偏正确性与状态机不变量破坏。

## 按根因归并

### 1. 兼容性 / 持久化元数据 / 混部升级回退风险（19）

- 统计：`Blocker 1`，`High 18`。

- `#47648` `High` Rolling-upgrade/resume compatibility break: persisted DDL/DXF global-sort subtask meta produced by the first parent (`start_key/end_key`, `min-key/max-key`) will not decode correctly after this commit’s JSON tag/schema changes, causing nil/invalid key boundaries to propagate into range splitting and execution.
- `#50550` `High` High: Rolling-upgrade incompatibility — old schedulers wait on legacy revert subtasks, but new executors no longer process them, so tasks can hang in `reverting` during mixed-version clusters.
- `#53544` `High` Backward-compatibility break in persisted metadata: changing `PartitionInfo.NewPartitionIDs` from an untagged field (JSON key `NewPartitionIDs`) to a tagged snake_case key (`new_partition_ids`) prevents new code from decoding legacy metadata during in-flight truncate-partition DDL, and can bypass truncating-partition write blocking.
- `#54804` `High` Bootstrap schema upgrade adds `switch_group_name` to runaway watch system tables, but inserts rely on `INSERT ... VALUES` without column lists; during rolling upgrades, older TiDB binaries will start failing inserts after any newer node upgrades the tables.
- `#55264` `High` Forward-compat regression for in-flight DDL jobs: old queued add-index / add-primary-key jobs that relied on the legacy job-arg `global` boolean may be executed with `IndexInfo.Global=false` after this commit, because the worker no longer decodes/uses the legacy `global` flag and instead derives `IndexInfo.Global` solely from `IndexOption.Global`.
- `#56130` `High` JobVersion1 finished add-index args lose the global-index flag: encoder uses `IndexArg.Global` instead of `IndexArg.IsGlobal`, so global indexes are treated as non-global in delete-range/GC paths.
- `#56194` `High` `JobVersion1` lock/unlock table DDL jobs are not backward-compatible across upgrade: this commit changes the persisted JSON field names for the lock/unlock args (CamelCase → snake_case tags), so older in-flight jobs may decode to empty args and then either never finish (lock) or finish without unlocking (unlock).
- `#57017` `High` TempIndexValue encoding change (global unique index delete marker adds 'p'+partitionID bytes) is not forward-compatible: pre-#57017 TiDB cannot decode new-format temp-index values, risking rolling upgrade/downgrade failures when temp-index is active.
- `#59966` `High` Global-sort add-index subtask meta format becomes backward-incompatible (ExternalPath + external storage), but disttask scheduling has no version/capability gating, so mixed-version clusters can dispatch these subtasks to older executors that can’t read required meta.
- `#60403` `High` Keyspace (API V2) rolling upgrade split-brain risk: DDL owner election keyspace prefix change makes old and new versions campaign on different etcd paths, allowing multiple concurrent DDL owners.
- `#62477` `High` Compatibility/correctness risk: in non-MDL mode the restart/owner-change pre-sync is skipped when `job.LastSchemaVersion==0`; for jobs persisted by older TiDB versions (field absent => decoded as 0), this can skip required schema-version synchronization and break the online DDL 2-version invariant.
- `#62570` `High` Nextgen bootstrap uses reserved IDs for system schemas/tables but only checks existence by ID; upgrading from an already-bootstrapped nextgen cluster with non-reserved system schema IDs can create duplicate `mysql`/`sys` schema names and cause infoschema/metadata nondeterminism.
- `#62762` `High` Backward-incompatible metadata/DDL-job JSON key rename for partial index condition: `IndexInfo.ConditionExprString` and `IndexArg.ConditionString` changed tag names without a decode shim, so upgrades or mixed-version clusters can silently lose the partial-index predicate.
- `#62979` `High` `removingObjPrefix` rename (`_Tomestone$_` → `_Tombstone$_`) can break mixed-version / rolling-upgrade compatibility for MODIFY COLUMN tombstone name normalization when legacy-prefixed metadata exists.
- `#63965` `Blocker` Cancel/rollback path for `ActionModifyColumn` assumes refactored `ModifyColumnType` enum values, but does not handle the legacy `mysql.TypeNull` (6) value used by older jobs, which can leave a job stuck in `JobStateCancelling`.
- `#63970` `High` Rolling-upgrade incompatibility: in-flight `MODIFY COLUMN` reorg jobs from pre-#63970 can get stuck because `AnalyzeState` moved into `job.ReorgMeta` and a new persisted `Stage` gate was introduced, but there is no migration/default handling for missing `reorg_meta.stage/analyze_state`.
- `#64096` `High` `AnalyzeStateTimeout` (value 4) is persisted into DDL job metadata but first-parent code has no handler/default for this new value. During mixed-version rolling upgrades, if ownership falls back to an older TiDB while an add-index/modify-column job is between “set AnalyzeStateTimeout” and “publish/finalize”, the older owner can’t advance the state machine and the DDL job can get stuck.
- `#65380` `High` Mixed-version/downgrade incompatibility: `GlobalIndexVersionV1` changes global non-distinct index key format by inserting `PartitionIDFlag`+partitionID into the key, but there is no compatibility gating to prevent creating these v1 indexes when older TiDB binaries may still be serving traffic. Older binaries would ignore the new metadata field and also cannot decode the new key suffix flag (126), risking query/index correctness during rolling upgrades or after downgrade.
- `#66701` `High` `Plan.GetOnDupKeyMode()` defaults missing (legacy) task metadata to `error`, which changes conflict handling for IMPORT INTO DXF tasks created before this option existed (legacy capture → now error) when those tasks are resumed after upgrade.

### 2. DDL 生命周期 / 回滚 / 取消 / owner 切换 / 重试状态机（15）

- 统计：`Blocker 1`，`High 14`。

- `#47450` `Blocker` `executeDistGlobalTask` can incorrectly treat a paused dist global task as successful completion when resuming a paused DDL job while the task is still in `pausing`/transitions to `paused` (resume is a silent no-op for non-`paused` tasks, and `WaitGlobalTask` returns `nil` on `paused`).
- `#50311` `High` `TransferTasks2History` can incorrectly return nil (and commit) when deleting tasks from `tidb_global_task` fails, due to overwriting the delete error with later subtask-transfer results.
- `#51071` `High` `Manager.InitMeta()` (and other manager metadata writes) now reuse scheduler/task-executor SQL retry settings (exponential backoff starting at 3s, up to 30 retries), which can block synchronous startup and manager-loop paths for ~14 minutes on persistent metadata/SQL errors, despite the caller treating `InitMeta` failure as non-fatal.
- `#53217` `High` `SyncJobSchemaVerLoop` now performs a full-prefix etcd `Get`/`Watch` over `/tidb/ddl/all_schema_by_job_versions/` and caches all job-version keys, but the code only deletes these keys for `JobStateSynced` jobs. Terminal rollback/cancel paths (and any deletion failures) can leave per-job keys behind indefinitely, making the watched keyspace and cache unbounded over time and risking MDL schema-sync stalls or resource blowups.
- `#53548` `High` FLASHBACK CLUSTER: `splitRegionsByKeyRanges` retries `SplitRegions` in a tight infinite loop without checking context cancellation. After this change, it is invoked with the scheduler/worker context (canceled on owner change), so an owner change/shutdown during StageWriteOnly can spin/hang and block scheduler exit.
- `#55025` `High` Merged `ActionCreateTables` jobs drop shared `InvolvingSchemaInfo` (e.g. placement policy refs), allowing the DDL scheduler to run conflicting policy DDL concurrently and potentially commit tables with dangling placement policy references.
- `#55049` `High` Owner-generation stamp uses `time.Now().Unix()` (seconds), so rapid owner re-elections can reuse the same `ownerTS`, defeating the stale-result filter in `runReorgJob()`.
- `#56702` `High` Multi-schema `DROP FOREIGN KEY` subjobs are not rollback-safe: a later subjob failure (e.g., duplicate `DROP FOREIGN KEY` on the same name, or mixed DDL where a later operation fails) can leave earlier FK drops applied even though the overall `ALTER TABLE` fails.
- `#56963` `High` `ForceToBeOwner` swallows errors after retries (always returns nil), so upgrade-mode startup can proceed even when “force to be owner” did not succeed.
- `#57336` `High` `ADMIN ALTER DDL JOBS` incorrectly mutates `Job.AdminOperator`, which is also used as the pause/resume ownership guard (system vs end user). This breaks the DDL job pause/resume state-machine invariant, especially for system-paused jobs during upgrading.
- `#57766` `High` `listTablesForEachSchema` incorrectly returns `errors.Trace(err)` (nil) when `ctx.Err()!=nil`, swallowing cancellation and allowing information_schema scans to “succeed” under cancellation.
- `#57813` `High` `backfillWorker.handleBackfillTask` checks reorg runnability using `d.ctx` (ddl/owner lifetime context) rather than the per-job step context that is canceled on job pause/cancel. Since backfill txn operations use `context.Background()`, this prevents timely interruption of long-running txn backfill loops on `ADMIN CANCEL/PAUSE`, and can delay scheduler/worker shutdown that waits on backfill workers.
- `#57945` `High` Possible `ddl.Stop()` hang: `DoDDLJobWrapper` can return on DDL context cancel before receiving the job submission result, but `JobSubmitter.NotifyResult` uses blocking sends on unbuffered `ResultCh`; if cancellation happens while the submitter is still going to `NotifyResult`, it can block forever (no receiver), preventing `d.wg.Wait()` in `ddl.close()` from completing.
- `#60828` `High` Pause handling can leave reorg work running after a job becomes `JobStatePaused`: because reorg now returns periodic timeouts while preserving `jobCtx.stepCtx`, but the `JobStatePausing/Paused` fast-path returns without canceling that long-lived context, and the scheduler exits on paused (final state), the background reorg goroutine/workers can continue after a successful pause.
- `#62190` `High` High risk of permanent hang / restart-safety bypass when MDL is disabled: UpdateSelfVersion now retries etcd Put “unlimited” using a non-cancelable context (via callers passing context.Background) and a lease captured once. If the etcd session/lease becomes invalid (e.g., prolonged etcd outage > TTL or domain close), the Put can never succeed and Reload can block indefinitely, preventing SyncLoop from handling syncer.Done() to stop validator and restart the syncer.

### 3. Infoschema / 元数据加载 / rename / temp table / placement 正确性（10）

- 统计：`Blocker 3`，`High 7`。

- `#50917` `Blocker` Local temporary table truncation can panic after dropping and recreating a database with the same name: `SessionTables` retains a stale `dbInfo` keyed by schema name, but new temp tables get `TableInfo.DBID` set to the new schema ID. `SchemaByID` then fails to find the schema (returns `(nil,false)`), and `TruncateLocalTemporaryTable` ignores the boolean and passes `nil` into `AddTable`, leading to a nil dereference.
- `#52873` `High` `ALTER/REPLACE PLACEMENT POLICY` may not refresh partition-level placement bundles when the altered policy is referenced only by partitions (table-level `PlacementPolicyRef` is nil), leaving partition bundles stale in infoschema.
- `#53301` `High` `infoschema` v2 `ListTablesWithSpecialAttribute` can miss special-attribute tables after cross-database rename because it deduplicates resident entries using only `tableID`, while resident entries are keyed/ordered by `(dbName, tableID, schemaVersion)`, allowing a tomb entry for the old DB to suppress the live entry for the new DB.
- `#53821` `High` Full placement-bundle rebuild in infoschema v2 can miss some placement-policy tables after cross-database `RENAME TABLE`, due to `ListTablesWithSpecialAttribute` iterating `tableInfoResident` ordered by dbName and deduping only consecutive `tableID`s (with tomb handling after dedup). In certain key-order adjacency cases, the old-db tomb record can cause the new-db live record to be skipped, leaving `ruleBundleMap` incomplete.
- `#54185` `High` Schema-cache full-load fast path uses regex + partial unescaping for table names; JSON `\uXXXX` escapes (e.g., from `<`, `>`, `&`) are not decoded, so `TableName2ID` keys can be wrong and infoschema v2 name lookups can fail.
- `#55129` `Blocker` Snapshot infoschema full-load can build infoschema v1 while `tidb_schema_cache_size>0`, but schema/table fetching is still optimized for v2 and only loads “must-load” table infos; v1 builder then omits most tables, yielding an incomplete snapshot infoschema.
- `#55847` `Blocker` `Allocator.Transfer` + `AutoIDAccessor.CopyTo` migrates and switches autoid ownership to the new schema during infoschema diff apply, but cross-schema rename explicitly allows concurrent DML; in a multi-node mixed-schema window this can split allocations across old/new meta keys and can non-monotonically overwrite the target autoid, risking duplicate auto-increment IDs or wrong base.
- `#58062` `High` `InfoCache.GetAndResetRecentInfoSchemaTS()` uses a non-atomic `Load()+Store()` reset, so concurrent `infoschemaV2.keepAlive()` updates can be lost, undermining the GC-safepoint pinning this change introduces.
- `#61865` `High` Periodic placement-rule “repair” can override user TiFlash replica changes due to stale job args: it submits `ActionSetTiFlashReplica` using snapshot Count/Labels, and when that job executes later it rewrites `TiFlashReplica` metadata even if a prior queued user DDL already changed/disabled TiFlash replica.
- `#64356` `High` `autoIDAccessor.CopyTo` now treats `curr==0` as “nothing to copy”, but 0 can be an explicitly stored value (not just “missing”). This can break sequence RESTART semantics across schema transfers: after `ALTER SEQUENCE ... RESTART WITH 1` (base becomes 0) and a subsequent rename back to a schema with a stale non-zero sequence value, the stale value can survive because the explicit 0 is not copied.

### 4. 分区 / MODIFY COLUMN / 索引语义正确性（8）

- 统计：`Blocker 4`，`High 4`。

- `#40634` `Blocker` `MODIFY COLUMN` on partitioned tables is enabled, but partitioning-column safety checks don’t account for `PARTITION BY KEY`: lossy conversions on KEY-partitioning columns (e.g., shrinking string length, reducing time/datetime FSP, or other conversions that can change value under non-strict SQL mode) can change the hashed partition key while the DDL reorg rewrites rows in-place without moving them across physical partitions. This can leave rows stored in the “wrong” partition, causing incorrect results under partition pruning/point-get.
- `#53277` `Blocker` `findNextNonTouchedPartitionID` has a loop whose DroppingDefinitions membership check is evaluated only once (the `notFoundErr` condition is never recomputed). If the immediate next partition is a dropped partition, the loop will keep advancing to the end and return 0, skipping remaining non-touched partitions. This can leave rebuilt global indexes incomplete after `REORGANIZE PARTITION` when dropped partitions are in the middle of `pi.Definitions`.
- `#55891` `High` Implicit foreign-key cascade DML is likely broken by the new “non-Public table => ErrTableNotExists” planner gate: the PR/comment states cascades should still modify `WriteOnly/DeleteOnly` tables, but FK cascades are executed via internal SQL planning that now rejects those tables.
- `#56786` `Blocker` `PartitionInfo.DDLChangedIndex` is not cleared on successful `ActionReorganizePartition` completion. Because it is persisted in table meta and later reused by both `tables.newPartitionedTable()` (to decide which indexes exist on dropping/adding partitions) and rollback conversion (to decide which indexes to drop), stale entries can break subsequent REORGANIZE PARTITION executions and can even cause rollback to drop an index that pre-existed the current DDL.
- `#59009` `High` TableMode protection can be bypassed for `ALTER TABLE ... ADD FOREIGN KEY` when `foreign_key_checks=0`, allowing schema changes (and possibly auto-created indexes) on tables in Import/Restore mode.
- `#62759` `High` Partial primary key condition is inconsistently handled: CREATE TABLE blocks `PRIMARY KEY(...) WHERE ...`, but ALTER TABLE ADD PRIMARY KEY accepts it and then silently drops the predicate because `IndexOption.Condition` isn’t marshaled and `ConditionString` isn’t populated for PK jobs.
- `#63465` `Blocker` Rollback cleanup for skip-row-reorg `MODIFY COLUMN` can clear an existing `NOT NULL` constraint when only `ChangingFieldType` was used as an in-progress marker (i.e., when NULLability wasn’t being changed).
- `#63901` `High` `getModifyColumnInfo` can misidentify the target column when `OldColumnID` is missing: it now looks up `_Tombstone$_<oldName>` before `<oldName>`, so tables that already contain a `_Tombstone$_<oldName>` column can cause `MODIFY COLUMN <oldName>` to operate on the wrong column.

### 5. Add Index / Ingest / DXF / DistTask 资源与执行正确性（17）

- 统计：`Blocker 2`，`High 15`。

- `#44442` `High` `buildIndexDupTasks` omits `tikvCodec.EncodeRange`, so remote duplicate detection for a specific index (`IndexID != 0`, used by DDL ingest unique-index remote dup check) can scan incorrect key ranges in keyspace/APIv2 mode and miss duplicates.
- `#47084` `High` `S3Storage.DeleteFiles` ignores per-object failures returned by S3 `DeleteObjects`, so cleanup can incorrectly succeed even when some objects were not deleted.
- `#48797` `High` ResetEngine failure cleanup can orphan local-engine ingest goroutines/resources: DDL calls `ClosedEngine.Cleanup` (which delegates to `CleanupEngine`) without first closing the engine, then drops `openedEngine` references, making it hard/impossible to stop `ingestSSTLoop` and writers deterministically.
- `#50475` `High` DXF/disttask manager construction becomes a hard startup dependency on `memory.MemTotal() > 0`. In container/cgroup configurations where the memory controller isn’t detected, `memory.MemTotal()` can return 0 (without error), causing `BuildManager` to return an error and `Domain.InitDistTaskLoop` to abort TiDB startup.
- `#51242` `High` `handle.RunWithRetry` is misused: the non-continuous-regions path returns `(retryable=true, err=nil)`, which cannot trigger retries. This makes the new “retry with backoff before dispatch” logic non-functional and causes immediate plan-generation failure whenever the region scan is temporarily non-continuous.
- `#54008` `High` Potential regression: stale ingest temp dirs are no longer cleaned just-in-time before the ingest disk pre-check, because cleanup moved to a background loop that first runs only after a 1-minute tick. This creates a window where `PreCheckUsage()` can fail due to stale-dir disk bloat and the add-index job can be cancelled (non-retryable) even though the new cleanup loop would have freed space soon.
- `#56630` `High` Dist add-index disttask now retries nearly all non-`*terror.Error` errors (unknown errors default to retryable), which can keep subtasks in running state indefinitely (no retry budget) and leave DDL jobs stuck waiting for the global task on persistent infra/logic errors.
- `#57468` `High` Shrinking txn-backfill concurrency can cancel workers in a way that drops in-flight task results (including errors/panics), so reorg/add-index may continue and complete even though some backfill tasks failed.
- `#57770` `Blocker` `IngestIfQuotaExceeded` can import+reset engines while writers are concurrently appending, because flush locks are released before `unsafeImportAndResetAllEngines()`. This can race with local backend import/reset and lead to data loss or corruption in add-index ingest.
- `#58990` `High` `TaskTypeExample` scheduler lacks an explicit `ModifyMeta` implementation; due to `BaseScheduler` embedding/forwarding `Extension`, calling `ModifyMeta` during task modification can recurse into itself and crash.
- `#59165` `High` DXF local-sort read-index step can leak open engines on early errors: with backend reuse and `BackendCtx.Close()` no longer closing the backend, a `buildLocalStorePipeline` failure after `Register` returns without engine cleanup, leaving engines open in the shared `*local.Backend` and breaking subsequent retries/subtasks (Pebble lock/open-engine failures, potential step wedging).
- `#59343` `High` DXF runtime concurrency modification does not update the StepExecutor’s stored `GetResource()` when applied during a running subtask. Because `readIndexStepExecutor.RunSubtask` re-reads concurrency from `GetResource().CPU.Capacity()` at each subtask start, a successful mid-subtask concurrency decrease can be lost for later subtasks, potentially rebuilding pipelines with stale (higher) concurrency and violating resource/slot assumptions.
- `#59407` `Blocker` `generatePlanForPhysicalTable` can silently return partial/empty subtask plans by swallowing planning-time errors (notably `allocNewTS` failures and potentially `getTableRange` errors). With this PR, partitioned tables now use this function, so an intermittent PD/GetTS or snapshot-range error can lead to an incomplete add-index backfill without the framework noticing.
- `#60395` `High` `external.DivideMergeSortDataFiles` is introduced/used to distribute merge-sort subtasks by node count, but its remainder batching can exceed the documented `MergeSortFileCountStep` cap, making its `maxTargetFilesPerSubtask := max(16, mergeConc)` overlap-budget check unsound. For some `mergeConc>16` + large-file-count inputs, this can produce merge-sort subtasks whose internal merge fanout (and thus downstream overlap/reader pressure) exceeds the intended `MergeSortOverlapThreshold` budget, risking global-sort add-index instability.
- `#62014` `High` Potential large memory spike/OOM risk: table scan now buffers all result chunks for a PD-region-sized `TableScanTask` before sending to writers, removing the prior streaming/backpressure behavior.
- `#62120` `High` Runtime concurrency updates for global-sort add-index don’t reliably carry over to external-engine creation for later subtasks because `engineManager.closeEngine` uses a stale `WorkerConcurrency` copy (separate from the updated `local.Backend`), risking resource oversubscription/stalls after downscaling.
- `#64171` `High` Regression in checkpoint initialization: when a checkpoint object exists but has `ts=0` (the default initial reorg_meta state), `CheckpointManager` now returns early and never allocates/persists an initial ingest TS. This undermines the intended failover/idempotency behavior around the first import and delays persisting `instance_addr`.

### 6. IMPORT INTO / TableMode / 后台任务配置语义（3）

- 统计：`Blocker 1`，`High 2`。

- `#56020` `High` `BACKGROUND=NULL` / `BACKGROUND=()` is not honored: DDL code always re-initializes `ResourceGroupSettings.Background` to a non-nil empty struct even when the AST indicates “clear background settings”.
- `#61200` `Blocker` `IMPORT INTO` classic-kernel submission can leave a table permanently in `TableModeImport` if task creation/commit fails after the mode switch, because `ddl.AlterTableMode` is not transactional with `TaskManager.WithNewTxn` and there is no rollback/compensation on error.
- `#64584` `High` IMPORT INTO cleanup can become effectively non-retriable if metering send fails: `CleanUp` returns error after deleting sorted data, but still redacts `Plan.CloudStorageURI` (masking credential query params). On the next cleanup retry, the redacted CloudStorageURI may no longer authenticate to the object store, preventing cleanup from reaching metering again and leaving finished tasks stuck in active task tables.

### 7. 权限 / 安全 / 资源上限防护（2）

- 统计：`Blocker 0`，`High 2`。

- `#65049` `High` `SHOW AFFINITY` bypasses database/table privilege visibility checks and can expose affinity-enabled objects (and PD topology/state) to unprivileged users.
- `#65133` `High` Persistent region split policy accepts arbitrarily large `REGIONS` values (no `SplitRegionMaxNum` cap), enabling unbounded split-key generation and potential DoS/stability issues.

### 8. 特性执行安全性 / nil 处理 / 管理面可用性（6）

- 统计：`Blocker 0`，`High 6`。

- `#56409` `High` Vector index DDL can wait indefinitely because TiFlash schema-sync and progress polling treat certain failures as success (non-2xx HTTP responses and empty `information_schema.tiflash_indexes` result sets).
- `#56864` `High` Potential nil-store panic introduced by enabling notifier publish unconditionally: `asyncNotifyEvent` now always calls `notifier.PubSchemeChangeToStore` using `jobCtx.eventPublishStore`, but `EventPublishStore` remains optional in `ddl.NewDDL` options and the only non-nil check is `intest.Assert` (no-op in release builds).
- `#57392` `High` `ADMIN SHOW DDL JOBS` `COMMENTS` column is mis-associated with rows for `ActionMultiSchemaChange` jobs due to column-12 append order, causing comments to shift between the parent job row and subjob rows.
- `#63397` `High` Potential nil-pointer panic on metering initialization: `InitDistTaskLoop` assumes `metering.NewMeter` never returns `nil` on success, but `NewMeter` can return `(nil, nil)` for configs with empty bucket (reachable via valid-looking URIs), and the code still starts the flush loop on `m`.
- `#64111` `High` Potential nil-pointer panic in `worker.onModifyColumn`: deferred `failpoint.InjectCall` dereferences `args` even when `GetModifyColumnArgs` returns an error (args can be `nil`).
- `#64337` `High` Potential nil dereference introduced in the new multi-schema-change embedded-analyze path: `onMultiSchemaChange` now always calls `doAnalyzeWithoutReorg`, which assumes `job.ReorgMeta` is non-nil, but upgrade-compat initialization only runs on reorg/background workers; MSC jobs can be dispatched to the general worker (`reorg=0`).

## Blocker 清单

- `#40634` ddl: enable modify column on partitioned table (#40634)
- `#47450` ddl: fix can't pause add index in dist task mode (#47450)
- `#50917` *: store db ID in model.TableInfo and move SchemaByTable method out from InfoSchema (#50917)
- `#53277` ddl: Reorganize partition supporting global index (#53277)
- `#55129` domain,infoschema: avoid v1/v2 switch when loading snapshot infoschema (#55129)
- `#55847` infoschema, meta: fix wrong auto id after `rename table` (#55847)
- `#56786` ddl: Corrected index management during REORGANIZE PARTITION (#56786)
- `#57770` ddl/ingest: remove backend context manager and refactor code (#57770)
- `#59407` ddl: split partition tables subtasks like normal tables (#59407)
- `#61200` ddl, disttask: import into integrate table mode (#61200)
- `#63465` ddl: make some `MODIFY COLUMN` skip row reorg (#63465)
- `#63965` ddl: some code refactor for lossy ddl optimization (#63965)

## 备注

- 以上是按 PR 维度保留的原始高危结果，并已做“后续已修复”过滤。
- 不同 PR 之间仍可能存在共享根因；本报告已经先按根因家族归并，但仍保留 PR 维度明细，便于定位具体改动。
<properties
   pageTitle="SQL 資料倉儲系統檢視表 | Microsoft Azure"
   description="SQL 資料倉儲的系統檢視表內容的連結。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="barbkess"/>


# 系統檢視

## SQL 資料倉儲目錄檢視

- [sys.pdw_column_distribution_properties](http://msdn.microsoft.com/library/mt204022.aspx)
- [sys.pdw_database_mappings](http://msdn.microsoft.com/library/mt203891.aspx)
- [sys.pdw_diag_event_properties](http://msdn.microsoft.com/library/mt203921.aspx)
- [sys.pdw_diag_events](http://msdn.microsoft.com/library/mt203895.aspx)
- [sys.pdw_diag_sessions](http://msdn.microsoft.com/library/mt203890.aspx)
- [sys.pdw_distributions](http://msdn.microsoft.com/library/mt203892.aspx)
- [sys.pdw_index_mappings](http://msdn.microsoft.com/library/mt203912.aspx)
- [sys.pdw_loader_backup_run_details](http://msdn.microsoft.com/library/mt203877.aspx)
- [sys.pdw_loader_backup_runs](http://msdn.microsoft.com/library/mt203884.aspx)
- [sys.pdw_loader_run_stages](http://msdn.microsoft.com/library/mt203879.aspx)
- [sys.pdw_nodes_column_store_dictionaries](http://msdn.microsoft.com/library/mt203902.aspx)
- [sys.pdw_nodes_column_store_row_groups](http://msdn.microsoft.com/library/mt203880.aspx)
- [sys.pdw_nodes_column_store_segments](http://msdn.microsoft.com/library/mt203916.aspx)
- [sys.pdw_nodes_columns](http://msdn.microsoft.com/library/mt203881.aspx)
- [sys.pdw_nodes_indexes](http://msdn.microsoft.com/library/mt203885.aspx)
- [sys.pdw_nodes_partitions](http://msdn.microsoft.com/library/mt203908.aspx)
- [sys.pdw_nodes_pdw_physical_databases](http://msdn.microsoft.com/library/mt203897.aspx)
- [sys.pdw_nodes_tables](http://msdn.microsoft.com/library/mt203886.aspx)
- [sys.pdw_table_distribution_properties](http://msdn.microsoft.com/library/mt203896.aspx)
- [sys.pdw_table_mappings](http://msdn.microsoft.com/library/mt203876.aspx)

## SQL Database 目錄檢視

- [dbo.server_quotas (Azure SQL Database)](http://msdn.microsoft.com/library/dn308512.aspx)
- [sys.bandwidth_usage (Azure SQL Database)](http://msdn.microsoft.com/library/dn269985.aspx)
- [sys.database_connection_stats (Azure SQL Database)](http://msdn.microsoft.com/library/dn269986.aspx)
- [sys.database_firewall_rules (Azure SQL Database)](http://msdn.microsoft.com/library/dn269982.aspx)
- [sys.database_usage (Azure SQL Database)](http://msdn.microsoft.com/library/azure/ff951626.aspx)
- [sys.event_log (Azure SQL Database)](http://msdn.microsoft.com/library/dn270018.aspx)
- [sys.database_firewall_rules (Azure SQL Database)](http://msdn.microsoft.com/library/dn269982.aspx)
- [sys.firewall_rules (Azure SQL Database)](http://msdn.microsoft.com/library/azure/ff951627.aspx)
- [sys.resource_stats (Azure SQL Database)](http://msdn.microsoft.com/library/dn269979.aspx)
- sys.resource_usage (Azure SQL Database)


## SQL 資料倉儲動態管理檢視 (DMV)

- [sys.dm_pdw_diag_processing_stats](http://msdn.microsoft.com/library/mt203914.aspx)
- [sys.dm_pdw_dms_cores](http://msdn.microsoft.com/library/mt203911.aspx)
- [sys.dm_pdw_dms_workers](http://msdn.microsoft.com/library/mt203878.aspx)
- [sys.dm_pdw_errors](http://msdn.microsoft.com/library/mt203904.aspx)
- [sys.dm_pdw_exec_connections](http://msdn.microsoft.com/library/mt203882.aspx)
- [sys.dm_pdw_exec_requests](http://msdn.microsoft.com/library/mt203887.aspx)
- [sys.dm_pdw_exec_sessions](http://msdn.microsoft.com/library/mt203883.aspx)
- [sys.dm_pdw_network_credentials](http://msdn.microsoft.com/library/mt203915.aspx)
- [sys.dm_pdw_nodes](http://msdn.microsoft.com/library/mt203907.aspx)
- [sys.dm_pdw_nodes_database_encryption_keys](http://msdn.microsoft.com/library/mt203922.aspx)
- [sys.dm_pdw_node_status](http://msdn.microsoft.com/library/mt203905.aspx)
- [sys.dm_pdw_os_event_logs](http://msdn.microsoft.com/library/mt203910.aspx)
- [sys.dm_pdw_or_performance_counters](http://msdn.microsoft.com/library/mt203875.aspx)
- [sys.dm_pdw_os_threads](http://msdn.microsoft.com/library/mt203917.aspx)
- 
- 
- 
- 
- 
- 
- 
- 
- 

## SQL Server 目錄檢視

- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 

## SQL 資料倉儲中可用的 SQL Server DMV 清單

SQL 資料倉儲公開許多 SQL Server 動態管理檢視 (DMV)。 在 SQL 資料倉儲中查詢這些檢視表時，將會報告分布區域上執行的 SQL Database 狀態。

由於 SQL 資料倉儲採用 Microsoft 的 MPP 技術，SQL 資料倉儲和分析平台系統的 Parallel Data Warehouse (PDW) 都使用相同的系統檢視表。

這就是為什麼每個 DMV 都有一個稱為 pdw_node_id 的特定資料行。 這是計算節點的識別碼。 在 PDW 中，計算節點是一種較強的架構概念。 在 SQL 資料倉儲中，架構更高度依賴分布區域。
>[AZURE.NOTE] 若要使用這些檢視表，請將 'pdw_nodes_' 插入名稱中，如下表所示。 


| SQL 資料倉儲中的 DMV 名稱| MSDN 上的 SQL Server Transact-SQL 主題連結|
| :----------------------------- | :-------------------------------------------- |
| sys.dm_pdw_nodes_db_file_space_usage| |
| sys.dm_pdw_nodes_db_index_usage_stats| |
| sys.dm_pdw_nodes_db_partition_stats| |
| sys.dm_pdw_nodes_db_session_space_usage| |
| sys.dm_pdw_nodes_db_task_space_usage| |
| sys.dm_pdw_nodes_exec_background_job_queue| |
| sys.dm_pdw_nodes_exec_background_job_queue_stats| |
| sys.dm_pdw_nodes_exec_cached_plans| |
| sys.dm_pdw_nodes_exec_connections| |
| sys.dm_pdw_nodes_exec_procedure_stats| |
| sys.dm_pdw_nodes_exec_query_memory_grants| |
| sys.dm_pdw_nodes_exec_query_optimizer_info| |
| sys.dm_pdw_nodes_exec_query_resource_semaphores| |
| sys.dm_pdw_nodes_exec_query_stats| |
| sys.dm_pdw_nodes_exec_requests| |
| sys.dm_pdw_nodes_exec_sessions| sys.dm_pdw_nodes_exec_sessions (Transact-SQL)|
| sys.dm_pdw_nodes_io_cluster_shared_drives| |
| sys.dm_pdw_nodes_io_pending_io_requests| |
| sys.dm_pdw_nodes_os_buffer_descriptors| |
| sys.dm_pdw_nodes_os_child_instances| |
| sys.dm_pdw_nodes_os_cluster_nodes| |
| sys.dm_pdw_nodes_os_dispatcher_pools| |
| sys.dm_pdw_nodes_os_dispatchers| 未提供 Transact-SQL 文件。|
| sys.dm_pdw_nodes_os_hosts| |
| sys.dm_pdw_nodes_os_latch_stats| [sys.dm_os_latch 統計資料 (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms175066.aspx)|
| sys.dm_pdw_nodes_os_loaded_modules| [sys.dm_os_loaded_modules (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms179907.aspx)|
| sys.dm_pdw_nodes_os_memory_brokers| [sys.dm_os_memory_brokers (TRANSACT-SQL)](http://msdn.microsoft.com/library/bb522548.aspx)|
| sys.dm_pdw_nodes_os_memory_cache_clock_hands| [sys.dm_os_memory_cache_clock_hands (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms173786.aspx)|
| sys.dm_pdw_nodes_os_memory_cache_counters| [sys.dm_os_memory_cache_counters (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms188760.aspx)|
| sys.dm_pdw_nodes_os_memory_cache_entries| [sys.dm_os_memory_cache_entries (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms189488.aspx)|
| sys.dm_pdw_nodes_os_memory_cache_hash_tables| [sys.dm_os_memory_cache_hash_tables (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms182388.aspx)|
| sys.dm_pdw_nodes_os_memory_clerks| [sys.dm_os_memory_clerks (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms175019.aspx)|
| sys.dm_pdw_nodes_os_memory_node_access_stats| 未提供 Transact-SQL 文件。|
| sys.dm_pdw_nodes_os_memory_nodes| [sys.dm_os_memory_nodes (TRANSACT-SQL)](http://msdn.microsoft.com/library/bb510622.aspx)|
| sys.dm_pdw_nodes_os_memory_pools| [sys.dm_os_memory_pools (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms175022.aspx)|
| sys.dm_pdw_nodes_os_nodes| [sys.dm_os_nodes (TRANSACT-SQL)](http://msdn.microsoft.com/library/bb510628.aspx)|
| sys.dm_pdw_nodes_os_performance_counters| [sys.dm_os_performance_counters (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms187743.aspx)|
| sys.dm_pdw_nodes_os_process_memory| [sys.dm_os_process_memory (TRANSACT-SQL)](http://msdn.microsoft.com/library/bb510747.aspx)|
| sys.dm_pdw_nodes_os_schedulers| [sys.dm_os_schedulers (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms177526.aspx)|
| sys.dm_pdw_nodes_os_spinlock_stats| sys.dm_os_spinlock_stats (Transact-SQL)|
| sys.dm_pdw_nodes_os_sys_info| [sys.dm_os_sys_info (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms175048.aspx)|
| sys.dm_pdw_nodes_os_sys_memory| [sys.dm_os_memory_nodes (TRANSACT-SQL)](http://msdn.microsoft.com/library/bb510622.aspx)|
| sys.dm_pdw_nodes_os_tasks| [sys.dm_os_tasks (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms174963.aspx)|
| sys.dm_pdw_nodes_os_threads| [sys.dm_os_threads (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms187818.aspx)|
| sys.dm_pdw_nodes_os_virtual_address_dump| sys.dm_virtual_address_dump (Transact-SQL)|
| sys.dm_pdw_nodes_os_wait_stats| sys.ldm_pdw_nodes_os_wait_stats (Transact-SQL)|
| sys.dm_pdw_nodes_os_waiting_tasks| sys.dm_waiting_tasks (Transact-SQL)|
| sys.dm_pdw_nodes_os_workers| sys.dm_workers (Transact-SQL)|
| sys.dm_pdw_nodes_resource_governor_resource_pools| [sys.dm_resource_governor_resource_pools (TRANSACT-SQL)](http://msdn.microsoft.com/library/bb934023.aspx)|
| sys.dm_pdw_nodes_resource_governor_workload_groups| [sys.dm_resource_governor_workload_groups (TRANSACT-SQL)](http://msdn.microsoft.com/library/bb934197.aspx)|
| sys.dm_pdw_nodes_tran_active_snapshot_database_transactions| [sys.dm_tran_active_snapshot_database_transactions (Transact_SQL)](http://msdn.microsoft.com/library/ms180023.aspx)|
| sys.dm_pdw_nodes_tran_active_transactions| [sys.dm_tran_active_transactions (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms174302.aspx)|
| sys.dm_pdw_nodes_tran_commit_table| 未提供 Transact-SQL 文件。|
| sys.dm_pdw_nodes_tran_current_snapshot| [sys.dm_tran_current_snapshot (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms184390.aspx)|
| sys.dm_pdw_nodes_tran_locks| [sys.dm_tran_locks (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms190345.aspx)|
| sys.dm_pdw_nodes_tran_session_transactions| [sys.dm_tran_session_transactions (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms188739.aspx)|
| sys.dm_pdw_nodes_tran_top_version_generators| [sys.dm_tran_top_version_generators (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms188778.aspx)|

## SQL 資料倉儲中可用的 SQL Server 2016 PolyBase dmv

- [sys.dm_exec_compute_node_errors (TRANSACT-SQL)](http://msdn.microsoft.com/library/mt146380.aspx)
- [sys.dm_exec_compute_node_status (TRANSACT-SQL)](http://msdn.microsoft.com/library/mt146382.aspx)
- sys.dm_exec_compute_nodes (Transact-SQL)
- sys.dm_exec_distributed_request_steps (Transact-SQL)
- [sys.dm_exec_distributed_requests (TRANSACT-SQL)](http://msdn.microsoft.com/library/mt146385.aspx)
- [sys.dm_exec_distributed_sql_requests (TRANSACT-SQL)](http://msdn.microsoft.com/library/mt146390.aspx)
- sys.dm_exec_dms_services (Transact-SQL)
- sys.dm_exec_dms_workers (Transact-SQL)
- sys.dm_exec_external_operations (Transact-SQL)
- sys.dm_exec_external_work (Transact-SQL)

## SQL 資料倉儲中可用的 SQL Database DMV

- sys.dm_continuous_copy_status
- [sys.dm_database_copies](http://msdn.microsoft.com/library/azure/ff951634.aspx)
- sys.dm_db_objects_impacted_on_version_change
- sys.dm_db_resource_stats
- sys.dm_db_wait_stats
- [sys.dm_operation_status](http://msdn.microsoft.com/library/azure/jj126282.aspx)


## SQL Server information_schema 檢視表

這些是 SQL Server PDW 中可用的 SQL Server 資訊結構描述檢視表的連結。 您預期在 SQL Server 中看到的所有 INFORMATION_SCHEMA 檢視表，都可以在 SQL Server PDW 中找到。

- [CHECK_CONSTRAINTS (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms189772.aspx)
- [COLUMN_DOMAIN_USAGE (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms189447.aspx)
- [COLUMN_PRIVILEGES (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms186812.aspx)
- [資料行 (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms188348.aspx)
- [條件約束資料行使用方式 (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms174431.aspx)
- [CONSTRAINT_TABLE_USAGE (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms179883.aspx)
- [網域 (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms190371.aspx)
- [KEY_COLUMN_USAGE (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms189789.aspx)
- [參數 (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms173796.aspx)
- [參考條件約束 (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms179987.aspx)
- [例行的資料行 (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms187350.aspx)
- [常式 (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms188757.aspx)
- [結構描述 (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms182642.aspx)
- [資料表條件約束 (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms181757.aspx)
- [資料表權限 (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms186233.aspx)
- [資料表 (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms186224.aspx)
- [檢視資料行使用方式 (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms190492.aspx)
- [檢視資料表使用方式 (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms173869.aspx)
- [檢視 (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms181381.aspx)

## 後續步驟

如需詳細參考資訊，請參閱 [SQL 資料倉儲參考概觀 []][]。













[sql data warehouse reference overview]: sql-data-warehouse-overview-reference.md 


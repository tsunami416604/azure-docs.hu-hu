---
title: Az SQL-készlet munkaterhelésének figyelése DMV-kkel
description: Megtudhatja, hogyan figyelheti az Azure Synapse Analytics SQL-készlet számítási feladatait és a lekérdezések végrehajtását a DMV-k használatával.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/24/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: synapse-analytics
ms.openlocfilehash: cf6f25e8839ead5738eb7259cc4fccb674a4adea
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633193"
---
# <a name="monitor-your-azure-synapse-analytics-sql-pool-workload-using-dmvs"></a>Az Azure Synapse Analytics SQL-készlet terhelésének figyelése dmv-k használatával

Ez a cikk azt ismerteti, hogyan használhatja a dinamikus felügyeleti nézetek (DMV-k) a számítási feladatok figyelése, beleértve a lekérdezés végrehajtásának lekérdezések lekérdezések az SQL-készletben.

## <a name="permissions"></a>Engedélyek

A cikkben szereplő DMV-k lekérdezéséhez **a DATABASE STATE megtekintése** vagy a **CONTROL** engedély szükséges. Általában a **VIEW DATABASE STATE** megadása az előnyben részesített engedély, mivel sokkal korlátozóbb.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Kapcsolatok figyelése

Az adattárházba való összes bejelentkezés a [sys.dm_pdw_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)fájlba kerül.  Ez a dmv tartalmazza az utolsó 10.000 bejelentkezést.  A session_id az elsődleges kulcs, és minden új bejelentkezéshez egymás után van hozzárendelve.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Lekérdezés végrehajtásának figyelése

Az SQL készletben végrehajtott összes lekérdezést a rendszer a [sys.dm_pdw_exec_requests.](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  Ez a dmv tartalmazza az utolsó 10 000 végrehajtott lekérdezéseket.  A request_id egyedileg azonosítja az egyes lekérdezéseket, és a DMV elsődleges kulcsa.  A request_id minden új lekérdezéshez egymás után van hozzárendelve, és a QID előtaggal van ellátva, amely a lekérdezésazonosítót jelenti.  A DMV lekérdezése egy adott session_id egy adott bejelentkezésösszes lekérdezését jeleníti meg.

> [!NOTE]
> A tárolt eljárások több kérelemazonosítót használnak.  A kérelemazonosítók sorrendben vannak hozzárendelve.

Az alábbi lépéseket követve vizsgálja meg a lekérdezés végrehajtási tervek és időpontok egy adott lekérdezés.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>1. LÉPÉS: Azonosítsa a vizsgálni kívánt lekérdezést

```sql
-- Monitor active queries
SELECT *
FROM sys.dm_pdw_exec_requests
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 *
FROM sys.dm_pdw_exec_requests
ORDER BY total_elapsed_time DESC;

```

Az előző lekérdezés eredményében vegye figyelembe a megvizsgálni kívánt lekérdezés **kérelemazonosítóját.**

**A felfüggesztett** állapotú lekérdezések nagyszámú aktív futó lekérdezés miatt várólistára lehet állni. Ezek a lekérdezések a [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) a UserConcurrencyResourceType típussal várakozik. Az egyidejűségi korlátokról a [Memória- és egyidejűségi korlátok](memory-concurrency-limits.md) vagy [a számítási feladatok kezeléséhez szükséges erőforrásosztályok](resource-classes-for-workload-management.md)című témakörben talál. A lekérdezések más okokból is várhatnak, például az objektumzárolások miatt.  Ha a lekérdezés egy erőforrásra vár, olvassa el a cikkben az [erőforrásokra várakozó lekérdezések vizsgálata](#monitor-waiting-queries) című témakört.

A [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) tábla lekérdezésének megkeresésének egyszerűsítéséhez a [LABEL](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) segítségével rendeljen megjegyzést a lekérdezéshez, amely a sys.dm_pdw_exec_requests nézetben kereshető.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

### <a name="step-2-investigate-the-query-plan"></a>2. LÉPÉS: A lekérdezési terv vizsgálata

A kérelemazonosító segítségével olvassa be a lekérdezés elosztott SQL (DSQL) tervét a [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Amikor egy DSQL terv a vártnál tovább tart, az ok lehet egy összetett terv, sok DSQL lépéssel, vagy csak egy lépés hosszú időt vesz igénybe.  Ha a terv több lépésből áll, és több áthelyezési műveletet is elkövet, fontolja meg a táblaeloszlások optimalizálását az adatok mozgásának csökkentése érdekében. A [Tábla terjesztési](sql-data-warehouse-tables-distribute.md) cikk ismerteti, hogy miért kell adatokat áthelyezni a lekérdezés megoldásához. A cikk néhány terjesztési stratégiát is ismerteti az adatok mozgásának minimalizálása érdekében.

Egyetlen lépés további részleteinek vizsgálatához a *operation_type* a hosszú ideig futó lekérdezési lépés oszlopát, és vegye figyelembe a **lépésindexet:**

* Folytassa a 3a lépéssel az **SQL-műveletekhez:** OnOperation, RemoteOperation, ReturnOperation.
* Folytassa a 3b lépéssel **az adatmozgatási műveletekhez:** ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3-investigate-sql-on-the-distributed-databases"></a>3. lépés: SQL vizsgálata az elosztott adatbázisokon

A kérelemazonosító és a lépésindex segítségével a [sys.dm_pdw_sql_requests](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)fájlból kérhet adatokat, amelyek az összes elosztott adatbázis lekérdezési lépésének végrehajtási adatait tartalmazzák.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

A lekérdezési lépés futásakor a [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) segítségével lekérheti az SQL Server becsült tervét az SQL Server tervgyorsítótárából az adott disztribúción futó lépéshez.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL pool or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-4-investigate-data-movement-on-the-distributed-databases"></a>4. LÉPÉS: Az elosztott adatbázisok adatmozgásának vizsgálata

A kérelemazonosító és a lépésindex segítségével az egyes disztribúciókon futó adatmozgatási lépésekkel kapcsolatos információkat kérheti le [a sys.dm_pdw_dms_workers.](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
-- Find information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Ellenőrizze a *total_elapsed_time* oszlopban, hogy egy adott eloszlás lényegesen tovább tart-e, mint mások az adatok mozgatása kor.
* A hosszú ideig futó elosztáshoz ellenőrizze a *rows_processed* oszlopban, hogy az elosztásból áthelyezett sorok száma jelentősen nagyobb-e, mint mások. Ha igen, ez az eredmény az alapul szolgáló adatok ferdítésére utalhat.

Ha a lekérdezés fut, a [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) segítségével lekérheti az SQL Server becsült tervét az SQL Server tervgyorsítótárából az adott disztribúción belül jelenleg futó SQL Step esetében.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL pool Compute or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Várakozó lekérdezések figyelése

Ha azt tapasztalja, hogy a lekérdezés nem halad előre, mert egy erőforrásra vár, itt van egy lekérdezés, amely a lekérdezés által várt összes erőforrást jeleníti meg.

```sql
-- Find queries
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Ha a lekérdezés aktívan vár egy másik lekérdezés erőforrásaira, akkor az állapot **acquireresources**lesz.  Ha a lekérdezés rendelkezik az összes szükséges erőforrással, akkor az állapot **megadva**lesz.

## <a name="monitor-tempdb"></a>Tempdb figyelése

A Tempdb a köztes eredmények tárolására szolgál a lekérdezés végrehajtása során. A tempdb adatbázis magas kihasználtsága lassú lekérdezési teljesítményhez vezethet. Minden DW100c konfigurálva, 399 GB tempdb helyet foglalnak (DW1000c volna 3,99 TB teljes tempdb hely).  Az alábbiakban tippeket a tempdb használat figyelése és a csökkenő tempdb használat a lekérdezések.

### <a name="monitoring-tempdb-with-views"></a>Tempdb figyelése nézetekkel

A tempdb használatának figyeléséhez először telepítse a [microsoft.vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) nézetet a [Microsoft Toolkit for SQL készletből](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring). Ezután a következő lekérdezést hajthatja végre az összes végrehajtott lekérdezés csomópontonkénti tempdb-használatának megtekintéséhez:

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN microsoft.vw_sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa'
ORDER BY sr.request_id;
```

Ha olyan lekérdezéssel rendelkezik, amely nagy mennyiségű memóriát használ fel, vagy a tempdb lefoglalásával kapcsolatos hibaüzenetet kapott, annak oka lehet egy nagyon nagy [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) vagy INSERT [SELECT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) utasítás, amely a végső adatmozgatási műveletben sikertelen. Ez általában shufflemove műveletként azonosítható az elosztott lekérdezési tervben közvetlenül a végső INSERT SELECT előtt.  A shufflemove műveletek figyeléséhez használja a [sys.dm_pdw_request_steps-t.](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

A leggyakoribb megoldás a CTAS vagy INSERT SELECT utasítás több terhelési utasításra való megszakítása, így az adatmennyiség nem haladja meg az 1 TB/csomópont tempdb korlátot. A fürt nagyobb méretre is méretezhető, amely a tempdb méretet több csomópontra osztja, csökkentve a tempdb-t az egyes csomópontokon.

A CTAS és insert SELECT utasítások mellett a kevés memóriával rendelkező nagy, összetett lekérdezések a tempdb-be is kiömlehetnek, ami a lekérdezések sikertelensítését okozhatja.  Fontolja meg egy nagyobb [erőforrás-osztály](resource-classes-for-workload-management.md) futtatását, hogy elkerülje a tempdb-be való kiömlést.

## <a name="monitor-memory"></a>Monitor memória

A memória lehet a lassú teljesítmény és a memóriaproblémák kiváltó oka. Fontolja meg az adattárház méretezését, ha úgy találja, hogy az SQL Server memóriahasználata eléri a korlátait a lekérdezés végrehajtása során.

A következő lekérdezés az SQL Server memóriáját és a csomópontonkénti memórianyomást adja vissza:

```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB,
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```

## <a name="monitor-transaction-log-size"></a>Tranzakciónapló méretének figyelése

A következő lekérdezés minden egyes felosztáson visszaadja a tranzakciónapló méretét. Ha a naplófájlok egyike eléri a 160 GB-ot, fontolja meg a példány bővítését vagy a tranzakció méretének korlátozását.

```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id
FROM sys.dm_pdw_nodes_os_performance_counters
WHERE
instance_name like 'Distribution_%'
AND counter_name = 'Log File(s) Used Size (KB)'
```

## <a name="monitor-transaction-log-rollback"></a>Tranzakciónapló-visszaállítás figyelése

Ha a lekérdezések sikertelenek, vagy hosszú időt vesz igénybe a folytatáshoz, ellenőrizheti és figyelheti, hogy vannak-e visszagörgetési tranzakciók.

```sql
-- Monitor rollback
SELECT
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="monitor-polybase-load"></a>A PolyBase terhelésének monitorozása

A következő lekérdezés a terhelés előrehaladásának hozzávetőleges becslését tartalmazza. A lekérdezés csak a jelenleg folyamatban lévő fájlokat jeleníti meg.

```sql

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="next-steps"></a>További lépések

A DMV-kről további információt a [Rendszernézetek című témakörben talál.](sql-data-warehouse-reference-tsql-system-views.md)

---
title: A számítási feladat monitorozása DMV |} A Microsoft Docs
description: Ismerje meg, a számítási feladatok DMV-k figyelése.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: ff1f613dfdfb5c43b727bcc9c7f7a1f0afca0975
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546896"
---
# <a name="monitor-your-workload-using-dmvs"></a>Monitor your workload using DMVs
Ez a cikk ismerteti, hogyan lehet a számítási feladat monitorozása dinamikus felügyeleti nézetekkel (DMV-kkel) segítségével. Ez magában foglalja, vizsgálja meg a lekérdezés végrehajtása az Azure SQL Data warehouse-bA.

## <a name="permissions"></a>Engedélyek
Ebben a cikkben a dinamikus felügyeleti nézetek lekérdezéséhez, VIEW DATABASE STATE vagy a CONTROL engedély szükséges. Próbáltak VIEW DATABASE STATE általában az előnyben részesített engedély, mivel jóval szigorúbb.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Kapcsolatok monitorozása
Az SQL Data Warehouse összes bejelentkezés naplózása [sys.dm_pdw_exec_sessions][sys.dm_pdw_exec_sessions].  A DMV az utolsó 10 000 bejelentkezéseket tartalmazza.  A munkamenet-azonosítónak az elsődleges kulcs, és minden egyes új bejelentkezés egymás után van hozzárendelve.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>A figyelő a lekérdezés végrehajtása
Az SQL Data warehouse-bA végrehajtott összes lekérdezés a rendszer naplózza [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests].  A DMV tartalmazza az utolsó 10 000 végrehajtott lekérdezések.  A request_id egyedi módon azonosítja az egyes lekérdezések és a DMV elsődleges kulcsát.  A request_id minden egyes új lekérdezés egymás után van hozzárendelve, és a következő előtaggal kezdődik QID jelző lekérdezés azonosítóját.  Az adott bejelentkezéshez az összes lekérdezés az egy adott munkamenet-azonosítónak a DMV lekérdezéséhez jeleníti meg.

> [!NOTE]
> Tárolt eljárások használata több kérelem azonosítók.  Kérelem azonosítók egymást követő sorrendben vannak hozzárendelve. 
> 
> 

Az alábbiakban a lekérdezések végrehajtási tervét, és többször egy adott lekérdezésre vonatkozó utasításokat.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>1. LÉPÉS: A vizsgálni kívánt lekérdezés azonosítása
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

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

A fenti lekérdezés eredménye a **jegyezze fel a kérés Azonosítóját** a lekérdezés, amely, amelyet szeretne vizsgálni.

A lekérdezések a **felfüggesztett** állapot várólistára tehető aktív futó lekérdezések nagy száma miatt. Ezeket a lekérdezéseket is megjelennek a [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql) vár lekérdezés UserConcurrencyResourceType típusú. Egyidejűségi korlátját kapcsolatos tudnivalókat lásd: [teljesítményszintek](performance-tiers.md) vagy [erőforrásosztályok számítási feladatok kezeléséhez](resource-classes-for-workload-management.md). Lekérdezések is, amíg a többi okai például objektum zárolása.  Ha a lekérdezés egy erőforrást vár, tekintse meg [erőforrások váró lekérdezések kivizsgálása] [ Investigating queries waiting for resources] további ebben a cikkben.

A keresést a lekérdezés egyszerűsítése érdekében a [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) táblában, használja [címke] [ LABEL] Megjegyzés rendelhet hozzá a lekérdezéshez, amely a sys.dm_pdw_exec_ kereshetők kérelmek megtekintése.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>2. LÉPÉS: A lekérdezésterv vizsgálata
A kérés Azonosítóját használatával lekérheti az elosztott SQL (DSQL) csomagot a lekérdezés az [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

DSQL csomagot a vártnál tovább tart, amikor az OK lehet egy összetett terv számos DSQL lépéseket vagy hosszú ideig tart egy lépés.  Ha a csomag több áthelyezési művelet számos lépést, érdemes csökkentése érdekében az adatok áthelyezése a táblázat disztribúciókat optimalizálása. A [Táblaelosztással] [ Table distribution] a cikk azt ismerteti, miért adatok oldja meg a lekérdezés át kell helyezni, és ismerteti az egyes terjesztési stratégia adatáthelyezés minimalizálása érdekében.

A további vizsgálat érdekében egy lépésben részleteit a *operation_type* oszlop a hosszan futó lekérdezést lépés és megjegyzés a **Lépésindex**:

* A lépés 3a folytatásához **SQL-műveletek**: OnOperation, RemoteOperation, ReturnOperation.
* Folytassa a 3/b. lépés **adatáthelyezési műveletek**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>3a. lépés: Az elosztott adatbázisok SQL vizsgálata
A kérés Azonosítóját és a Lépésindex használatával lekérheti adatait [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], amelyek végrehajtási információkat tartalmaz a lekérdezési lépés az összes elosztott adatbázisok.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

A lekérdezés lépés futtatásakor [DBCC PDW_SHOWEXECUTIONPLAN] [ DBCC PDW_SHOWEXECUTIONPLAN] lekérni az SQL Server becsült terv a lépés egy adott terjesztési fut az SQL Server tervgyorsítótárból származó is használható.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>3/b. lépés: Adatok áthelyezése az elosztott adatbázisok vizsgálata
A kérés Azonosítóját és a Lépésindex használatával lekérheti az adatok mozgását lépés fut az egyes terjesztési információ [sys.dm_pdw_dms_workers][sys.dm_pdw_dms_workers].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Ellenőrizze a *total_elapsed_time* oszlopban tekintheti meg, ha egy adott terjesztési jelentősen a vártnál tovább tart, mint a többi az adatok áthelyezését.
* A hosszú ideig futó terjesztési, ellenőrizze a *rows_processed* , ha jelentősen nagyobb, mint a többi-e, hogy a terjesztés az áthelyezni kívánt sorok számát oszlop. Ha igen, ez találja az alapul szolgáló adatok torzulása utalhat.

Ha a lekérdezés fut, [DBCC PDW_SHOWEXECUTIONPLAN] [ DBCC PDW_SHOWEXECUTIONPLAN] is lehet lekérni az SQL Server becsült terv az SQL Server tervgyorsítótárból származó, az aktuálisan futó SQL lépés belül egy adott terjesztési.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Várakozás lekérdezések figyelése
Ha a tudomására jut, hogy a lekérdezés nem készít folyamatban van, mert egy erőforrást vár, Íme egy lekérdezést, amely bemutatja az összes erőforrást egy lekérdezést vár.

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

Ha a lekérdezés aktívan vár az erőforrásokat, egy másik lekérdezést, majd állapota lesz **AcquireResources**.  Ha a lekérdezésben található az összes szükséges erőforrást, majd állapota lesz **engedélyezve**.

## <a name="monitor-tempdb"></a>A figyelő a tempdb
A TempDB szolgál, amely tárolja a köztes eredményeket a lekérdezés végrehajtása során. A tempdb-adatbázis magas kihasználtság lekérdezési teljesítmény lassú vezethet. Az Azure SQL Data Warehouse minden fürtcsomópont körülbelül 1 TB nyers tempdb-terület. Az alábbiakban a tempdb-használat monitorozásával és a tempdb-használat, a lekérdezések a csökkenő tippeket. 

### <a name="monitoring-tempdb-with-views"></a>A nézetek a tempdb figyelése
A tempdb-használat figyeléséhez, először telepítse a [microsoft.vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) nézet első eleme a [az SQL Data Warehouse a Microsoft Toolkit](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring). A következő lekérdezést a tempdb használati minden végrehajtott lekérdezések csomópontonként, majd futtathatja:

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

Ha egy lekérdezést, amely jelentős mennyiségű memóriát is használja, vagy a TempDB adatbázis foglalási kapcsolatos hibaüzenetet kapott, ez a legtöbbször miatt egy nagyon nagy [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) vagy [INSERT SELECT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) az utasítás fut, amely végső soron az adatok mozgását művelet sikertelen. Ez általában ellenőrizhető, ShuffleMove műveletként az elosztott lekérdezési terv közvetlenül a végső INSERT SELECT előtt.

A leggyakoribb megoldás, hogy a CTAS vagy INSERT SELECT utasítás felosztása több betöltési utasításokat, így az adatmennyiség nem haladhatja meg az 1 TB-os csomópont tempdb-korlát. A fürt egy nagyobb méretű, ami lesz a TempDB adatbázis mérete elosztva csökkenti a tempdb minden egyes csomóponton több csomópont is méretezheti. 

## <a name="monitor-memory"></a>A figyelő memória

Memória lehet a hiba okát a lassú teljesítmény és az memóriahiba-ból. Vegye figyelembe, hogy az adattárház méretezése, ha SQL Server memóriahasználatának kapacitásukkal elérése a lekérdezés végrehajtása során.

A következő lekérdezés az SQL Server és memóriaterhelés csomópontonként adja vissza:   
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
## <a name="monitor-transaction-log-size"></a>Tranzakciós napló méretétől figyelése
A következő lekérdezést a tranzakciós napló mérete az egyes terjesztési adja vissza. Ha eléri a naplófájlok egyik 160 GB, érdemes a példány vertikális felskálázásával, vagy a tranzakciók mérete korlátozza. 
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
## <a name="monitor-transaction-log-rollback"></a>A figyelő tranzakciós napló visszaállítása
Ha a lekérdezések sikertelen, vagy folytatja hosszú ideig tart, ellenőrizheti és figyelése, ha bármely tranzakció visszagörgetése.
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

## <a name="next-steps"></a>További lépések
DMV-vel kapcsolatos további információkért lásd: [rendszernézetek][System views].


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: https://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx

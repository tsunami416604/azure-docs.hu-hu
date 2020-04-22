---
title: Teljesítmény figyelése dmv-k használatával
description: Megtudhatja, hogy miként észlelheti és diagnosztizálhatja a gyakori teljesítményproblémákat a Microsoft Azure SQL-adatbázis figyeléséhez használt dinamikus felügyeleti nézetek használatával.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 04/19/2020
ms.openlocfilehash: 6f33f49be74419a8f0cd31d973d64798f5d76a2c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683015"
---
# <a name="monitoring-performance-azure-sql-database-using-dynamic-management-views"></a>Teljesítményfigyelés Az Azure SQL Database dinamikus felügyeleti nézetek használatával

A Microsoft Azure SQL Database lehetővé teszi a dinamikus felügyeleti nézetek egy részhalmazát a teljesítményproblémák diagnosztizálására, amelyeket blokkolt vagy hosszan futó lekérdezések, erőforrás-szűk keresztmetszetek, gyenge lekérdezési tervek és így tovább okozhatnak. Ez a témakör a gyakori teljesítményproblémák dinamikus felügyeleti nézetek használatával történő észleléséről nyújt tájékoztatást.

Az SQL Database részben támogatja a dinamikus felügyeleti nézetek három kategóriáját:

- Adatbázissal kapcsolatos dinamikus felügyeleti nézetek.
- Végrehajtással kapcsolatos dinamikus felügyeleti nézetek.
- Tranzakcióval kapcsolatos dinamikus felügyeleti nézetek.

A dinamikus felügyeleti nézetekről az SQL Server Books Online [dinamikus felügyeleti nézeteiről és funkcióiról (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) olvashat részletesen.

## <a name="permissions"></a>Engedélyek

Az SQL Database rendszerben a dinamikus felügyeleti nézet lekérdezéséhez **VIEW DATABASE STATE** engedélyek szükségesek. A **VIEW DATABASE STATE** engedély az aktuális adatbázis összes objektumára vonatkozó információt adja vissza.
Ha a **VIEW DATABASE STATE** engedélyt egy adott adatbázis-felhasználónak szeretné megadni, futtassa a következő lekérdezést:

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

A helyszíni SQL Server egy példányában a dinamikus felügyeleti nézetek a kiszolgáló állapotadatait adják vissza. Az SQL Database csak az aktuális logikai adatbázisra vonatkozó információkat ad vissza.

Ez a cikk olyan DMV-lekérdezések gyűjteményét tartalmazza, amelyeket az SQL Server Management Studio vagy az Azure Data Studio használatával futtathat a következő típusú lekérdezési teljesítményproblémák észleléséhez:

- [A túlzott CPU-fogyasztással kapcsolatos lekérdezések azonosítása](#identify-cpu-performance-issues)
- [PAGELATCH_* és WRITE_LOG az IO szűk keresztmetszeteivel kapcsolatos várakozás](#identify-io-performance-issues)
- [PAGELATCH_* várakozás okozta bytTempDB versengés](#identify-tempdb-performance-issues)
- [RESOURCE_SEMAHPORE várakozások a memóriatámogatás várakozási problémái miatt](#identify-memory-grant-wait-performance-issues)
- [Adatbázis- és objektumméretek azonosítása](#calculating-database-and-objects-sizes)
- [Aktív munkamenetekkel kapcsolatos információk beolvasása](#monitoring-connections)
- [Rendszerszintű és adatbázis-erőforrás-használati adatok beolvasása](#monitor-resource-use)
- [Lekérdezés teljesítményadatainak beolvasása](#monitoring-query-performance)

## <a name="identify-cpu-performance-issues"></a>A PROCESSZOR teljesítményével kapcsolatos problémák azonosítása

Ha a processzorfogyasztás hosszabb ideig meghaladja a 80%-ot, vegye figyelembe az alábbi hibaelhárítási lépéseket:

### <a name="the-cpu-issue-is-occurring-now"></a>A CPU-probléma most fordul elő

Ha most probléma merül fel, két lehetséges forgatókönyv lehetséges:

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>Számos egyedi lekérdezés, amelyek összesítve magas CPU-t fogyasztanak

A következő lekérdezés segítségével azonosíthatja a legfontosabb lekérdezési kihaszinteket:

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM (SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
    FROM (SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
                CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
    GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>A processzort használó, hosszú ideig futó lekérdezések még futnak

A következő lekérdezéssel azonosíthatja ezeket a lekérdezéseket:

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
    CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>A CPU-probléma a múltban történt

Ha a probléma korábban történt, és alapvető ok-elemzést szeretne végezni, használja a [Query Store -ot.](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) Az adatbázis-hozzáféréssel rendelkező felhasználók a T-SQL segítségével kérdezhetik le a Query Store adatait. A Query Store alapértelmezett konfigurációi 1 órás részletességgel történő használatát használják. Az alábbi lekérdezés segítségével tekintse meg a magas processzorfogyasztó lekérdezések tevékenységét. Ez a lekérdezés a 15 processzort fogyasztó lekérdezést adja vissza. Ne feledje, hogy változtatni: `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()`

```sql
-- Top 15 CPU consuming queries by query hash
-- note that a query  hash can have many query id if not parameterized or not parameterized properly
-- it grabs a sample query text by min
WITH AggregatedCPU AS (SELECT q.query_hash, SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec, SUM(count_executions * avg_cpu_time / 1000.0)/ SUM(count_executions) AS avg_cpu_millisec, MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec, MAX(max_logical_io_reads) max_logical_reads, COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans, COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids, SUM(CASE WHEN rs.execution_type_desc='Aborted' THEN count_executions ELSE 0 END) AS Aborted_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Regular' THEN count_executions ELSE 0 END) AS Regular_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Exception' THEN count_executions ELSE 0 END) AS Exception_Execution_Count, SUM(count_executions) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text
                       FROM sys.query_store_query_text AS qt
                            JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                            JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                            JOIN sys.query_store_runtime_stats AS rs ON rs.plan_id=p.plan_id
                            JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=rs.runtime_stats_interval_id
                       WHERE rs.execution_type_desc IN ('Regular', 'Aborted', 'Exception')AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                       GROUP BY q.query_hash), OrderedCPU AS (SELECT query_hash, total_cpu_millisec, avg_cpu_millisec, max_cpu_millisec, max_logical_reads, number_of_distinct_plans, number_of_distinct_query_ids, total_executions, Aborted_Execution_Count, Regular_Execution_Count, Exception_Execution_Count, sampled_query_text, ROW_NUMBER() OVER (ORDER BY total_cpu_millisec DESC, query_hash ASC) AS RN
                                                              FROM AggregatedCPU)
SELECT OD.query_hash, OD.total_cpu_millisec, OD.avg_cpu_millisec, OD.max_cpu_millisec, OD.max_logical_reads, OD.number_of_distinct_plans, OD.number_of_distinct_query_ids, OD.total_executions, OD.Aborted_Execution_Count, OD.Regular_Execution_Count, OD.Exception_Execution_Count, OD.sampled_query_text, OD.RN
FROM OrderedCPU AS OD
WHERE OD.RN<=15
ORDER BY total_cpu_millisec DESC;
```

Miután azonosította a problémás lekérdezéseket, itt az ideje, hogy ezeket a lekérdezéseket a PROCESSZOR-kihasználtság csökkentése érdekében hangolja be.  Ha nincs ideje a lekérdezések finomhangolására, az adatbázis SLO-jának frissítése is dönthet a probléma megkerüléséhez.

## <a name="identify-io-performance-issues"></a>Az IO teljesítményével kapcsolatos problémák azonosítása

Az IO-teljesítménnyel kapcsolatos problémák azonosításakor az IO-problémákhoz kapcsolódó legmagasabb várakozási típusok a következők:

- `PAGEIOLATCH_*`

  Adatfájl-IO-problémák `PAGEIOLATCH_SH`esetén `PAGEIOLATCH_EX` `PAGEIOLATCH_UP`(beleértve a , . ) .  Ha a várakozási típus neve **i/o-t** mutat, az io-problémára mutat. Ha nincs **IO** az oldalon zár várakozás nevét, akkor rámutat, hogy egy másik típusú probléma (például tempdb versengés).

- `WRITE_LOG`

  Tranzakciónapló i/o-problémáihoz.

### <a name="if-the-io-issue-is-occurring-right-now"></a>Ha az IO-probléma éppen most jelentkezik

Használja a [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) vagy [sys.dm_os_waiting_tasks,](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) hogy a `wait_type` és `wait_time`.

#### <a name="identify-data-and-log-io-usage"></a>Adatok azonosítása és i/o-használat naplózása

Az alábbi lekérdezés segítségével azonosíthatja az adatokat és naplózhatja az IO-használatot. Ha az adatok vagy a napló I/o-je meghaladja a 80%-ot, az azt jelenti, hogy a felhasználók az SQL DB szolgáltatási szinthez elérhető I/o-t használták.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

Ha elérte az I/O-korlátot, két lehetősége van:

- 1. lehetőség: A számítási méret vagy a szolgáltatási szint frissítése
- 2. lehetőség: Azonosítsa és hangolja be a legtöbb I/O-t fogyasztó lekérdezéseket.

#### <a name="view-buffer-related-io-using-the-query-store"></a>Pufferrel kapcsolatos Io megtekintése a Lekérdezéstároló használatával

A 2.

```sql
-- top queries that waited on buffer
-- note these are finished queries
WITH Aggregated AS (SELECT q.query_hash, SUM(total_query_wait_time_ms) total_wait_time_ms, SUM(total_query_wait_time_ms / avg_query_wait_time_ms) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text, MIN(wait_category_desc) AS wait_category_desc
                    FROM sys.query_store_query_text AS qt
                         JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                         JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                         JOIN sys.query_store_wait_stats AS waits ON waits.plan_id=p.plan_id
                         JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=waits.runtime_stats_interval_id
                    WHERE wait_category_desc='Buffer IO' AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                    GROUP BY q.query_hash), Ordered AS (SELECT query_hash, total_executions, total_wait_time_ms, sampled_query_text, wait_category_desc, ROW_NUMBER() OVER (ORDER BY total_wait_time_ms DESC, query_hash ASC) AS RN
                                                        FROM Aggregated)
SELECT OD.query_hash, OD.total_executions, OD.total_wait_time_ms, OD.sampled_query_text, OD.wait_category_desc, OD.RN
FROM Ordered AS OD
WHERE OD.RN<=15
ORDER BY total_wait_time_ms DESC;
GO
```

#### <a name="view-total-log-io-for-writelog-waits"></a>Writelog várakozásai teljes naplóinak megtekintése

Ha a várakozástípusa, `WRITELOG`használja a következő lekérdezést a teljes napló IO utasításszerint történő megtekintéséhez:

```sql
-- Top transaction log consumers
-- Adjust the time window by changing
-- rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
WITH AggregatedLogUsed
AS (SELECT q.query_hash,
           SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec,
           SUM(count_executions * avg_cpu_time / 1000.0) / SUM(count_executions) AS avg_cpu_millisec,
           SUM(count_executions * avg_log_bytes_used) AS total_log_bytes_used,
           MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec,
           MAX(max_logical_io_reads) max_logical_reads,
           COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans,
           COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Aborted' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Aborted_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Regular' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Regular_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Exception' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Exception_Execution_Count,
           SUM(count_executions) AS total_executions,
           MIN(qt.query_sql_text) AS sampled_query_text
    FROM sys.query_store_query_text AS qt
        JOIN sys.query_store_query AS q
            ON qt.query_text_id = q.query_text_id
        JOIN sys.query_store_plan AS p
            ON q.query_id = p.query_id
        JOIN sys.query_store_runtime_stats AS rs
            ON rs.plan_id = p.plan_id
        JOIN sys.query_store_runtime_stats_interval AS rsi
            ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
    WHERE rs.execution_type_desc IN ( 'Regular', 'Aborted', 'Exception' )
          AND rsi.start_time >= DATEADD(HOUR, -2, GETUTCDATE())
    GROUP BY q.query_hash),
     OrderedLogUsed
AS (SELECT query_hash,
           total_log_bytes_used,
           number_of_distinct_plans,
           number_of_distinct_query_ids,
           total_executions,
           Aborted_Execution_Count,
           Regular_Execution_Count,
           Exception_Execution_Count,
           sampled_query_text,
           ROW_NUMBER() OVER (ORDER BY total_log_bytes_used DESC, query_hash ASC) AS RN
    FROM AggregatedLogUsed)
SELECT OD.total_log_bytes_used,
       OD.number_of_distinct_plans,
       OD.number_of_distinct_query_ids,
       OD.total_executions,
       OD.Aborted_Execution_Count,
       OD.Regular_Execution_Count,
       OD.Exception_Execution_Count,
       OD.sampled_query_text,
       OD.RN
FROM OrderedLogUsed AS OD
WHERE OD.RN <= 15
ORDER BY total_log_bytes_used DESC;
GO
```

## <a name="identify-tempdb-performance-issues"></a>Teljesítményproblémák azonosítása `tempdb`

Az io-teljesítménnyel kapcsolatos problémák azonosításakor a problémákhoz kapcsolódó `tempdb` legmagasabb várakozási típusok (nem `PAGELATCH_*` ). `PAGEIOLATCH_*` Azonban `PAGELATCH_*` a várakozás nem mindig `tempdb` jelenti azt, hogy van viszálykodás.  Ez a várakozás azt is jelentheti, hogy a felhasználói objektum adatoldal-versengése az azonos adatoldalt célzó egyidejű kérelmek miatt. A versengés `tempdb` további megerősítéséhez használja a [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) fájlsegítségével annak megerősítését, hogy a wait_resource érték az adatbázis azonosítójával `2:x:y` `tempdb` kezdődik- e, `x` a fájlazonosítóval és `y` az oldalazonosítóval.  

A tempdb versengés esetében gyakori módszer a . `tempdb`  A `tempdb` gyakori felhasználási területek a következők:

- Ideiglenes táblák
- Táblaváltozók
- Tábla értékű paraméterek
- Verziószám-tároló használata (kifejezetten a hosszú ideig futó tranzakciókhoz társítva)
- Rendezési, kivonatillesztéseket és várómedencéket használó lekérdezési terveket tartalmazó lekérdezések

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>Táblaváltozókat és ideiglenes táblákat használó leggyakoribb lekérdezések

Az alábbi lekérdezéssegítségével azonosíthatja a táblaváltozókat és ideiglenes táblákat használó leggyakoribb lekérdezéseket:

```sql
SELECT plan_handle, execution_count, query_plan
INTO #tmpPlan
FROM sys.dm_exec_query_stats
     CROSS APPLY sys.dm_exec_query_plan(plan_handle);
GO

WITH XMLNAMESPACES('http://schemas.microsoft.com/sqlserver/2004/07/showplan' AS sp)
SELECT plan_handle, stmt.stmt_details.value('@Database', 'varchar(max)') 'Database', stmt.stmt_details.value('@Schema', 'varchar(max)') 'Schema', stmt.stmt_details.value('@Table', 'varchar(max)') 'table'
INTO #tmp2
FROM(SELECT CAST(query_plan AS XML) sqlplan, plan_handle FROM #tmpPlan) AS p
    CROSS APPLY sqlplan.nodes('//sp:Object') AS stmt(stmt_details);
GO

SELECT t.plan_handle, [Database], [Schema], [table], execution_count
FROM(SELECT DISTINCT plan_handle, [Database], [Schema], [table]
     FROM #tmp2
     WHERE [table] LIKE '%@%' OR [table] LIKE '%#%') AS t
    JOIN #tmpPlan AS t2 ON t.plan_handle=t2.plan_handle;
```

### <a name="identify-long-running-transactions"></a>Hosszú ideig futó tranzakciók azonosítása

A következő lekérdezés segítségével azonosíthatja a hosszú ideig futó tranzakciókat. A hosszú ideig futó tranzakciók megakadályozzák a verziótároló karbantartását.

```sql
SELECT DB_NAME(dtr.database_id) 'database_name',
       sess.session_id,
       atr.name AS 'tran_name',
       atr.transaction_id,
       transaction_type,
       transaction_begin_time,
       database_transaction_begin_time transaction_state,
       is_user_transaction,
       sess.open_transaction_count,
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   txt.text,
                                                                   (req.statement_start_offset / 2) + 1,
                                                                   ((CASE req.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(txt.text)
                                                                         ELSE
                                                                             req.statement_end_offset
                                                                     END - req.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) Running_stmt_text,
       recenttxt.text 'MostRecentSQLText'
FROM sys.dm_tran_active_transactions AS atr
    INNER JOIN sys.dm_tran_database_transactions AS dtr
        ON dtr.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_tran_session_transactions AS sess
        ON sess.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_exec_requests AS req
        ON req.session_id = sess.session_id
           AND req.transaction_id = sess.transaction_id
    LEFT JOIN sys.dm_exec_connections AS conn
        ON sess.session_id = conn.session_id
    OUTER APPLY sys.dm_exec_sql_text(req.sql_handle) AS txt
    OUTER APPLY sys.dm_exec_sql_text(conn.most_recent_sql_handle) AS recenttxt
WHERE atr.transaction_type != 2
      AND sess.session_id != @@spid
ORDER BY start_time ASC;
```

## <a name="identify-memory-grant-wait-performance-issues"></a>Memóriatámogatás várakozási teljesítményével kapcsolatos problémák azonosítása

Ha a felső `RESOURCE_SEMAHPORE` várakozási típus, és nincs magas CPU-használat probléma, akkor lehet, hogy a memória támogatás várakozási probléma.

### <a name="determine-if-a-resource_semahpore-wait-is-a-top-wait"></a>Annak megállapítása, hogy a `RESOURCE_SEMAHPORE` várakozás felüli várakozás-e

A következő lekérdezés segítségével `RESOURCE_SEMAHPORE` állapíthatja meg, hogy a várakozás felüli várakozás-e

```sql
SELECT wait_type,
       SUM(wait_time) AS total_wait_time_ms
FROM sys.dm_exec_requests AS req
    JOIN sys.dm_exec_sessions AS sess
        ON req.session_id = sess.session_id
WHERE is_user_process = 1
GROUP BY wait_type
ORDER BY SUM(wait_time) DESC;
```

### <a name="identity-high-memory-consuming-statements"></a>Az identitásmagas memória-fogyasztó utasítások

A következő lekérdezéssel azonosíthatja a nagy memóriafogyasztó állításokat:

```sql
SELECT IDENTITY(INT, 1, 1) rowId,
    CAST(query_plan AS XML) query_plan,
    p.query_id
INTO #tmp
FROM sys.query_store_plan AS p
    JOIN sys.query_store_runtime_stats AS r
        ON p.plan_id = r.plan_id
    JOIN sys.query_store_runtime_stats_interval AS i
        ON r.runtime_stats_interval_id = i.runtime_stats_interval_id
WHERE start_time > '2018-10-11 14:00:00.0000000'
      AND end_time < '2018-10-17 20:00:00.0000000';
GO
;WITH cte
AS (SELECT query_id,
        query_plan,
        m.c.value('@SerialDesiredMemory', 'INT') AS SerialDesiredMemory
    FROM #tmp AS t
        CROSS APPLY t.query_plan.nodes('//*:MemoryGrantInfo[@SerialDesiredMemory[. > 0]]') AS m(c) )
SELECT TOP 50
    cte.query_id,
    t.query_sql_text,
    cte.query_plan,
    CAST(SerialDesiredMemory / 1024. AS DECIMAL(10, 2)) SerialDesiredMemory_MB
FROM cte
    JOIN sys.query_store_query AS q
        ON cte.query_id = q.query_id
    JOIN sys.query_store_query_text AS t
        ON q.query_text_id = t.query_text_id
ORDER BY SerialDesiredMemory DESC;
```

### <a name="identify-the-top-10-active-memory-grants"></a>Azonosítsa a 10 legjobb aktív memóriatámogatást

A következő lekérdezés segítségével azonosíthatja a 10 legfontosabb aktív memóriatámogatást:

```sql
SELECT TOP 10
    CONVERT(VARCHAR(30), GETDATE(), 121) AS runtime,
       r.session_id,
       r.blocking_session_id,
       r.cpu_time,
       r.total_elapsed_time,
       r.reads,
       r.writes,
       r.logical_reads,
       r.row_count,
       wait_time,
       wait_type,
       r.command,
       OBJECT_NAME(txt.objectid, txt.dbid) 'Object_Name',
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   text,
                                                                   (r.statement_start_offset / 2) + 1,
                                                                   ((CASE r.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(text)
                                                                         ELSE
                                                                             r.statement_end_offset
                                                                     END - r.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) stmt_text,
       mg.dop,                                               --Degree of parallelism
       mg.request_time,                                      --Date and time when this query requested the memory grant.
       mg.grant_time,                                        --NULL means memory has not been granted
       mg.requested_memory_kb / 1024.0 requested_memory_mb,  --Total requested amount of memory in megabytes
       mg.granted_memory_kb / 1024.0 AS granted_memory_mb,   --Total amount of memory actually granted in megabytes. NULL if not granted
       mg.required_memory_kb / 1024.0 AS required_memory_mb, --Minimum memory required to run this query in megabytes.
       max_used_memory_kb / 1024.0 AS max_used_memory_mb,
       mg.query_cost,                                        --Estimated query cost.
       mg.timeout_sec,                                       --Time-out in seconds before this query gives up the memory grant request.
       mg.resource_semaphore_id,                             --Non-unique ID of the resource semaphore on which this query is waiting.
       mg.wait_time_ms,                                      --Wait time in milliseconds. NULL if the memory is already granted.
       CASE mg.is_next_candidate --Is this process the next candidate for a memory grant
           WHEN 1 THEN
               'Yes'
           WHEN 0 THEN
               'No'
           ELSE
               'Memory has been granted'
       END AS 'Next Candidate for Memory Grant',
       qp.query_plan
FROM sys.dm_exec_requests AS r
    JOIN sys.dm_exec_query_memory_grants AS mg
        ON r.session_id = mg.session_id
           AND r.request_id = mg.request_id
    CROSS APPLY sys.dm_exec_sql_text(mg.sql_handle) AS txt
    CROSS APPLY sys.dm_exec_query_plan(r.plan_handle) AS qp
ORDER BY mg.granted_memory_kb DESC;
```

## <a name="calculating-database-and-objects-sizes"></a>Adatbázis- és objektumméretek számítása

A következő lekérdezés az adatbázis méretét adja vissza (megabájtban):

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

A következő lekérdezés az adatbázis ban lévő egyes objektumok méretét adja vissza (megabájtban):

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Kapcsolatok figyelése

A [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) nézet ben lekérheti az azure-beli SQL Database-kiszolgálóhoz létesített kapcsolatokra és az egyes kapcsolatok részleteire vonatkozó információkat. Ezenkívül a [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) nézet hasznos az összes aktív felhasználói kapcsolatra és belső feladatra vonatkozó információk beolvasásakor.
A következő lekérdezés az aktuális kapcsolatadatait olvassa be:

```sql
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> Ha a **sys.dm_exec_requests** és a **sys.dm_exec_sessions nézeteket**hajtja végre, ha rendelkezik **az adatbázis adatbázis állapotának megtekintése** engedéllyel, akkor az adatbázis összes végrehajtó munkamenete megjelenik; ellenkező esetben csak az aktuális munkamenet jelenik meg.

## <a name="monitor-resource-use"></a>Erőforrás-használat figyelése

Az erőforrás-használatot az [SQL Database Query Performance Insight](sql-database-query-performance.md) és a Query [Store](https://msdn.microsoft.com/library/dn817826.aspx)segítségével figyelheti.

A használatot a következő két nézet használatával is figyelheti:

- [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdm_db_resource_stats"></a>sys.dm_db_resource_stats

A [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) nézetet minden SQL-adatbázisban használhatja. A **sys.dm_db_resource_stats** nézet a legutóbbi erőforrás-használati adatokat jeleníti meg a szolgáltatási szinthez viszonyítva. A processzor, az adatok IO- és a naplóírások és a memória átlagos százaléka 15 másodpercenként kerül rögzítésre, és 1 órán keresztül megmarad.

Mivel ez a nézet részletesebb áttekintést nyújt az erőforrás-használatról, először használja **a sys.dm_db_resource_stats-t** az aktuális állapot elemzéséhez vagy hibaelhárításához. Ez a lekérdezés például az aktuális adatbázis átlagos és maximális erőforrás-felhasználását mutatja az elmúlt órában:

```sql
SELECT  
    AVG(avg_cpu_percent) AS 'Average CPU use in percent',
    MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
    AVG(avg_data_io_percent) AS 'Average data IO in percent',
    MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
    AVG(avg_log_write_percent) AS 'Average log write use in percent',
    MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
    AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
    MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
FROM sys.dm_db_resource_stats;  
```

További lekérdezéseket a [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)című témakörben talál.

### <a name="sysresource_stats"></a>sys.resource_stats

A [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) nézet a **fő** adatbázisban további információkat tartalmaz, amelyek segítségével figyelheti az SQL-adatbázis teljesítményét az adott szolgáltatási szint és a számítási méret. Az adatokat 5 percenként gyűjtjük, és körülbelül 14 napig őrizjük meg. Ez a nézet hosszabb távú előzményelemzésként használható, hogy az SQL-adatbázis hogyan használja az erőforrásokat.

A következő grafikon a CPU-erőforrás használatát egy prémium szintű adatbázis a P2 számítási mérete minden órában egy héten. Ez a grafikon hétfőn kezdődik, 5 munkanapot jelenít meg, majd egy hétvégét jelenít meg, amikor sokkal kevesebb történik az alkalmazásban.

![SQL-adatbázis-erőforrás használata](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Az adatokból ez az adatbázis jelenleg a P2 számítási mérethez képest alig több mint 50 százalékos CPU-használatú processzorterheléssel rendelkezik (kedden délben). Ha a CPU a domináns tényező az alkalmazás erőforrás-profiljában, majd dönthet úgy, hogy a P2 a megfelelő számítási méret, amely garantálja, hogy a munkaterhelés mindig illeszkedik. Ha azt várja, hogy egy alkalmazás idővel növekszik, célszerű egy extra erőforrás-pufferrel rendelkezni, hogy az alkalmazás soha ne érje el a teljesítményszintű korlátot. Ha növeli a számítási méretet, segíthet elkerülni az ügyfél által látható hibákat, amelyek akkor fordulhatnak elő, ha egy adatbázis nem rendelkezik elegendő energiával a kérelmek hatékony feldolgozásához, különösen a késésre érzékeny környezetekben. Erre példa egy olyan adatbázis, amely támogatja az adatbázis-hívások eredményei alapján weblapokat festő alkalmazást.

Más alkalmazástípusok eltérően értelmezhetik ugyanazt a grafikont. Ha például egy alkalmazás minden nap megpróbálja feldolgozni a bérlistaadatait, és ugyanazt a diagramot keresi, az ilyen típusú "kötegelt feldolgozás" modell p1 számítási méretben is jól teljesíthet. A P1 számítási mérete 100 DDU-val rendelkezik, szemben a P2 számítási méretben lévő 200 DDU-val. A P1 számítási mérete a P2 számítási méret ének felét biztosítja. Tehát a P2-ben a CPU-használat 50 százaléka 100 százalékos CPU-használatot jelent a P1-ben. Ha az alkalmazás nem rendelkezik időtúljárom, előfordulhat, hogy nem számít, ha a feladat 2 óra vagy 2,5 órát vesz igénybe, ha ez lesz kész ma. Ebben a kategóriában egy alkalmazás valószínűleg használhatja a P1 számítási méretet. Kihasználhatja azt a tényt, hogy vannak olyan időszakok a nap folyamán, amikor az erőforrás-felhasználás alacsonyabb, így minden "nagy csúcs" átfolyhat az egyik vályúba a nap folyamán. A P1 számítási mérete lehet jó az ilyen típusú alkalmazás (és pénzt takarít meg), mindaddig, amíg a feladatok befejezheti időben minden nap.

Az Azure SQL Database minden egyes aktív adatbázis felhasznált erőforrásadatait elérhetővé teszi a **sys.resource_stats** nézetben, az egyes kiszolgálók **fő** adatbázisa. A táblázatban szereplő adatok 5 perces időközönként összesítve vannak. Az alapszintű, standard és prémium szintű szolgáltatási szintek, az adatok több mint 5 percet vesz igénybe a táblázatban megjelenő, így ezek az adatok hasznosabbak a korábbi elemzés, nem pedig közel valós idejű elemzés. A **sys.resource_stats** nézet lekérdezésével megtekintheti az adatbázis legutóbbi előzményeit, és ellenőrizheti, hogy a kiválasztott foglalás szükség esetén a kívánt teljesítményt nyújtott-e.

> [!NOTE]
> A **sys.resource_stats** lekérdezéséhez az ALÁBBI példákban csatlakoznia kell az SQL Database-kiszolgáló **fő** adatbázisához.

Ez a példa bemutatja, hogyan jelennek meg az adatok ebben a nézetben:

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![A sys.resource_stats katalógusnézet](./media/sql-database-performance-guidance/sys_resource_stats.png)

A következő példa a **sys.resource_stats** katalógusnézet ének különböző módjait mutatja be az SQL-adatbázis erőforrásainak felhasználásáról szóló információk lehívására:

1. A userdb1 adatbázis múlt heti erőforrás-felhasználásának vizsgálatához futtassa a lekérdezést:

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. Annak kiértékeléséhez, hogy a számítási feladatok mennyire illeszkednek a számítási mérethez, az erőforrás-metrikák minden egyes aspektusát le kell ásania: processzor, olvasás, írás, dolgozók száma és munkamenetek száma. Az alábbi átdolgozott lekérdezés a **sys.resource_stats** használatával az erőforrás-metrikák átlagos és maximális értékeinek jelentéséhez:

    ```sql
    SELECT
        avg(avg_cpu_percent) AS 'Average CPU use in percent',
        max(avg_cpu_percent) AS 'Maximum CPU use in percent',
        avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
        max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
        avg(avg_log_write_percent) AS 'Average log write use in percent',
        max(avg_log_write_percent) AS 'Maximum log write use in percent',
        avg(max_session_percent) AS 'Average % of sessions',
        max(max_session_percent) AS 'Maximum % of sessions',
        avg(max_worker_percent) AS 'Average % of workers',
        max(max_worker_percent) AS 'Maximum % of workers'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

3. Az egyes erőforrás-metrikák átlag- és maximális értékére vonatkozó információkkal felmérheti, hogy a számítási feladatok mennyire illeszkednek a kiválasztott számítási mérethez. Általában a **sys.resource_stats** átlagos értékei jó kiindulási értéket biztosítanak a célmérethez képest. Ez legyen az elsődleges mérőpálca. Például előfordulhat, hogy a Standard service tier S2 számítási méret. A CPU és az IO olvasási és írási műveletek átlagos használati százaléka 40 százalék alatt van, a dolgozók átlagos száma 50 alatt van, és a munkamenetek átlagos száma 200 alatt van. Előfordulhat, hogy a számítási feladatok beleférnek az S1 számítási méretébe. Könnyen megállapíthatja, hogy az adatbázis illeszkedik-e a dolgozói és a munkamenet-korlátokba. Annak ellenőrzéséhez, hogy egy adatbázis elfér-e alacsonyabb számítási méretben a PROCESSZOR, az olvasás és az írás tekintetében, ossza el az alacsonyabb számítási méret DTU-számát az aktuális számítási méret DTU-számával, majd szorozza meg az eredményt 100-zal:

    `S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40`

    Az eredmény a két számítási méret százalékos relatív teljesítménykülönbsége. Ha az erőforrás-használat nem haladja meg ezt az összeget, a számítási feladatok elférnek az alacsonyabb számítási méret. Azonban meg kell vizsgálnia az erőforrás-használati értékek összes tartományát, és százalékonként meg kell határoznia, hogy az adatbázis-munkaterhelés milyen gyakran illeszkedik az alacsonyabb számítási méretbe. A következő lekérdezés erőforrásdimenziónkénti illeszkedési százalékot adja ki a példában kiszámított 40 százalékos küszöbérték alapján:

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Az adatbázis-szolgáltatási szint alapján eldöntheti, hogy a számítási feladatok illeszkednek-e az alacsonyabb számítási méretbe. Ha az adatbázis-munkaterhelés célja 99,9 százalék, és az előző lekérdezés 99,9 százaléknál nagyobb értékeket ad vissza mindhárom erőforrásdimenzióesetében, a munkaterhelés valószínűleg belefér az alacsonyabb számítási méretbe.

    Az illeszkedési százalék vizsgálata azt is bepillantást nyújt, hogy a cél eléréséhez lépjen-e át a következő magasabb számítási méretre. A userdb1 például a következő CPU-használatot jeleníti meg az elmúlt héten:

   | Átlagos CPU-százalék | Maximális CPU-százalék |
   | --- | --- |
   | 24.5 |100.00 |

    Az átlagos CPU körülbelül egynegyede a számítási méret, amely jól illeszkedik az adatbázis számítási méretét. De a maximális érték azt mutatja, hogy az adatbázis eléri a számítási méret korlátját. Át kell lépnie a következő nagyobb számítási méretre? Tekintse meg, hogy a munkaterhelés hányalkalommal éri el a 100 százalékot, majd hasonlítsa össze az adatbázis-munkaterhelés icélkitűzésével.

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Ha ez a lekérdezés 99,9 százaléknál kisebb értéket ad vissza a három erőforrásdimenzió bármelyikéhez, fontolja meg a következő nagyobb számítási méretre való áttérést, vagy használjon alkalmazáshangolási technikákat az SQL-adatbázis terhelésének csökkentéséhez.

4. Ez a gyakorlat is figyelembe veszi a tervezett munkaterhelés növekedése a jövőben.

Rugalmas készletek esetén az ebben a szakaszban leírt technikákkal az adatbáziskészlet egyes adatbázisait is figyelheti. De ugyanígy figyelheti az adatbáziskészlet egészét is. További információkért lásd: [Rugalmas készlet figyelése és kezelése](sql-database-elastic-pool-manage-portal.md).

### <a name="maximum-concurrent-requests"></a>Egyidejű kérelmek maximális

Az egyidejű kérelmek számának megtekintéséhez futtassa ezt a Transact-SQL lekérdezést az SQL-adatbázisban:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R;
```

A helyszíni SQL Server-adatbázis munkaterhelésének elemzéséhez módosítsa ezt a lekérdezést az elemezni kívánt adatbázis szűréséhez. Ha például egy Saját adatbázis nevű helyszíni adatbázissal rendelkezik, ez a Transact-SQL lekérdezés az adatbázisban lévő egyidejű kérelmek számát adja vissza:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R
INNER JOIN sys.databases D ON D.database_id = R.database_id
AND D.name = 'MyDatabase';
```

Ez csak egy pillanatfelvétel egyetlen időpontban. A számítási feladatok és az egyidejű kérelemkövetelmények jobb megértéséhez idővel sok mintát kell gyűjtenie.

### <a name="maximum-concurrent-logins"></a>Egyidejű bejelentkezések maximális

Elemezheti a felhasználói és alkalmazásmintákat, hogy képet kapjon a bejelentkezések gyakoriságáról. Valós terheléseket is futtathat egy tesztkörnyezetben, hogy megbizonyosodjon arról, hogy nem éri el ezt vagy más korlátokat, amelyeket ebben a cikkben tárgyalunk. Nincs egyetlen lekérdezés vagy dinamikus felügyeleti nézet (DMV), amely meg tudja mutatni az egyidejű bejelentkezési számlálókat vagy előzményeket.

Ha több ügyfél használja ugyanazt a kapcsolati karakterláncot, a szolgáltatás hitelesíti az egyes bejelentkezéseket. Ha egyszerre 10 felhasználó csatlakozik egy adatbázishoz ugyanazzal a felhasználónévvel és jelszóval, akkor 10 egyidejű bejelentkezés rejlehet. Ez a korlát csak a bejelentkezés és a hitelesítés időtartamára vonatkozik. Ha ugyanaz a 10 felhasználó egymás után csatlakozik az adatbázishoz, az egyidejű bejelentkezések száma soha nem lesz nagyobb, mint 1.

> [!NOTE]
> Ez a korlát jelenleg nem vonatkozik a rugalmas készletekben lévő adatbázisokra.

### <a name="maximum-sessions"></a>Munkamenetek maximális száma

Az aktuális aktív munkamenetek számának megtekintéséhez futtassa ezt a Transact-SQL lekérdezést az SQL-adatbázisban:

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections
```

Ha egy helyszíni SQL Server-számítási feladatot elemez, módosítsa a lekérdezést úgy, hogy egy adott adatbázisra összpontosítson. Ez a lekérdezés segít meghatározni az adatbázis lehetséges munkamenet-igényeit, ha azt fontolgatja, hogy áthelyezi az Azure SQL Database-be.

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections C
INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
INNER JOIN sys.databases D ON (D.database_id = S.database_id)
WHERE D.name = 'MyDatabase'
```

Ezek a lekérdezések ismét egy időpontban történő számot adnak vissza. Ha idővel több mintát gyűjt, a legjobban ismeri a munkamenet-használatot.

Az SQL Database-elemzéshez a [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) nézet lekérdezésével és a **active_session_count** oszlop áttekintésével korábbi statisztikákat kaphat a munkamenetekről.

## <a name="monitoring-query-performance"></a>Lekérdezésteljesítményének figyelése

A lassú vagy hosszú ideig futó lekérdezések jelentős rendszererőforrásokat használhatnak fel. Ez a szakasz bemutatja, hogyan használhatja a dinamikus felügyeleti nézetek et néhány gyakori lekérdezési teljesítményprobléma észlelésére. A hibaelhárításhoz egy régebbi, de még mindig hasznos hivatkozási alap a Microsoft TechNet [SQL Server 2008 sql server 2008-as cikkének hibaelhárítási problémáival](https://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) kapcsolatos hibaelhárítása.

### <a name="finding-top-n-queries"></a>Top N-lekérdezések keresése

A következő példa az első öt lekérdezésátlagos CPU-idő szerint rangsorolt adatait adja vissza. Ez a példa a lekérdezési kivonatuk alapján összesíti a lekérdezéseket, így a logikailag egyenértékű lekérdezések a halmozott erőforrás-felhasználás szerint vannak csoportosítva.

```sql
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
     MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
FROM sys.dm_exec_query_stats AS QS
CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Blokkolt lekérdezések figyelése

A lassú vagy hosszan futó lekérdezések hozzájárulhatnak a túlzott erőforrás-felhasználáshoz, és a blokkolt lekérdezések következménye lehet. A blokkolás oka lehet a rossz alkalmazástervezés, a hibás lekérdezési tervek, a hasznos indexek hiánya és így tovább. A sys.dm_tran_locks nézet segítségével információkat kaphat az Azure SQL-adatbázis aktuális zárolási tevékenységéről. Például kód: [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) az SQL Server Books Online.For example code, see sys.dm_tran_locks (Transact-SQL) in SQL Server Books Online.

### <a name="monitoring-query-plans"></a>Lekérdezési tervek figyelése

A nem hatékony lekérdezési terv is növelheti a CPU-felhasználás. A következő példa a [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) nézetben határozza meg, hogy melyik lekérdezés használja a legtöbb kumulatív CPU-t.

```sql
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Lásd még

[Bevezetés az SQL Database-be](sql-database-technical-overview.md)

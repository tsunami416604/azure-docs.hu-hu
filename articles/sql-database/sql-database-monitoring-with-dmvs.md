---
title: Figyelés dinamikus felügyeleti nézetek használatával az Azure SQL Database |} A Microsoft Docs
description: Megtudhatja, hogyan észlelheti és diagnosztizálhatja a teljesítményproblémákat gyakran figyelése a Microsoft Azure SQL Database dinamikus felügyeleti nézetek használatával.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/22/2018
ms.openlocfilehash: 5dba091f62e320d6f9a48bea0208ef74db351402
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50095505"
---
# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Az Azure SQL Database felügyelete dinamikus felügyeleti nézetek használatával

A Microsoft Azure SQL Database lehetővé teszi, hogy a dinamikus felügyeleti nézetek által letiltott vagy hosszú ideig futó lekérdezéseket, erőforrás-keresztmetszetek, gyenge lekérdezési tervek és így tovább okozhatta teljesítményproblémák diagnosztizálásához egy részét. Ez a témakör információt nyújt az gyakori teljesítményproblémák észlelése dinamikus felügyeleti nézetek használatával.

Az SQL Database támogatja a dinamikus felügyeleti nézetek három kategóriába részben:

- Adatbázissal kapcsolatos dinamikus felügyeleti nézetek.
- Végrehajtási kapcsolatos dinamikus felügyeleti nézetek.
- Tranzakció-hoz kapcsolódó dinamikus felügyeleti nézetek.

A dinamikus felügyeleti nézetek részletes információkért lásd: [dinamikus felügyeleti nézetek és függvények (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) az SQL Server Online könyvekben.

## <a name="permissions"></a>Engedélyek

Az SQL Database dinamikus felügyeleti nézetek lekérdezéséhez szükséges **VIEW DATABASE STATE** engedélyeket. A **VIEW DATABASE STATE** engedélyt az aktuális adatbázisban lévő összes objektum adatait adja vissza.
Megadását a **VIEW DATABASE STATE** engedélyt egy adott adatbázis-felhasználót a következő lekérdezés futtatásával:

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

A helyszíni SQL Server-példányt, a dinamikus felügyeleti nézetek kiszolgáló állapot adatait adja vissza. SQL Database-ben, csak az aktuális logikai adatbázis kapcsolatos információkat ad vissza.

## <a name="identify-cpu-performance-issues"></a>CPU-teljesítménnyel kapcsolatos problémák azonosítása

Ha a CPU-használat 80 % felett van, a hosszabb ideig, vegye figyelembe az alábbi hibaelhárítási lépéseket:

### <a name="the-cpu-issue-is-occurring-now"></a>A Processzor probléma már jelentkezett

Ha a probléma most jelentkezik, két forgatókönyv közül választhat lehetséges:

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>Használja a magas CPU-összesítve számos egyéni lekérdezések

Felső lekérdezés kivonatok azonosításához használja a következő lekérdezést:

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM(SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
     FROM(SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
               CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
     GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>Használja a CPU-hosszú ideig futó lekérdezések továbbra is futnak.

A következő lekérdezés használatával azonosíthatja ezeket a lekérdezéseket:

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
     CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>A CPU-hiba történt az elmúlt

Ha a probléma a múltban lezajlott és kiváltó szeretné okok, akkor használja [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store). Az adatbázis-hozzáférés felhasználók használhatják a T-SQL Query Store adatokat lekérdezni.  Query Store alapértelmezett-konfigurációk használata egy 1 órás részletességgel.  Használja a következő lekérdezést, és tekintse meg a magas Processzorigényű lekérdezések tevékenység. Ez a lekérdezés visszaadja az első 15 CPU fogyasztó lekérdezések.  Ne felejtse el módosítani `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()`:

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

Miután azonosította a problémát okozó lekérdezéseket, ideje csökkenteni a Processzor kihasználtságát a Lekérdezések finomhangolása.  Ha nincs ideje a Lekérdezések finomhangolása, akkor előfordulhat, hogy is magasabb szintű csomagra váltani az slo-t az adatbázis a probléma megkerüléséhez.

## <a name="identify-io-performance-issues"></a>I/o-teljesítménybeli problémák azonosítása

I/o-teljesítmény problémák azonosításában, ha a felső várakozási társított i/o-hibák a következők:

- `PAGEIOLATCH_*`

  Adatok fájl i/o-hibák (beleértve a `PAGEIOLATCH_SH`, `PAGEIOLATCH_EX`, `PAGEIOLATCH_UP`).  Ha a várakozási típusnév **i/o** , IO-hibát mutat. Ha nincs **i/o** probléma (például a tempdb versengés) különböző típusú mutat a lap zárolás várakozási nevét.

- `WRITE_LOG`

  Tranzakciós napló i/o-problémák.

### <a name="if-the-io-issue-is-occurring-right-now"></a>Ha az i/o-probléma most

Használja a [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) vagy [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) megtekintéséhez a `wait_type` és `wait_time`.

#### <a name="identify-data-and-log-io-usage"></a>Azonosíthatja az adatokat, és jelentkezzen az i/o-kihasználtsága

A következő lekérdezés használatával azonosíthatja az adatokat, és naplózhatják i/o-használatát. Ha az adat- vagy naplófájl IO meghaladja a 80 %-os, az azt jelenti, használó felhasználók a rendelkezésre álló i/o az SQL DB szolgáltatási szinten.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

Ha a rendszer elérte az i/o-korlát, két lehetősége van:

- 1. lehetőség: A számítási méret frissítése vagy a szolgáltatásszintet
- 2. lehetőség: Azonosítsa, és a legtöbb i/o felhasználása a Lekérdezések finomhangolása.

#### <a name="view-buffer-related-io-using-the-query-store"></a>A Query Store segítségével nézet puffer kapcsolatos IO

2. lehetőség használhatja a következő lekérdezés elleni puffer kapcsolatos IO a Query Store az elmúlt két órában nyomon követett tevékenység megtekintése:

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

#### <a name="view-total-log-io-for-writelog-waits"></a>Teljes napló megtekintése IO: WRITELOG vár

Ha a várakozási `WRITELOG`, használja a következő lekérdezést megtekintése teljes naplózási IO-utasítás által:

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

## <a name="identify-tempdb-performance-issues"></a>Azonosítsa `tempdb` teljesítménnyel kapcsolatos problémák

I/o-teljesítmény problémák azonosításában, ha a felső várjon társított típusok `tempdb` problémákhoz `PAGELATCH_*` (nem `PAGEIOLATCH_*`). Azonban `PAGELATCH_*` vár nem mindig jelenti azt, hogy `tempdb` versengés.  A Várakozás is jelentheti, hogy a felhasználó-objektum adatok lap versengés az azonos lap célzó egyidejű kérelmek miatt. További megerősítésére `tempdb` versengés, használja [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) annak ellenőrzéséhez, hogy a wait_resource érték kezdődik `2:x:y` ahol 2 az `tempdb` az adatbázis-azonosító `x` a fájl azonosítója és `y` oldal azonosítója.  

A tempdb versengés, gyakori módszer, hogy csökkentse vagy írja át a létrehozó támaszkodik `tempdb`.  Közös `tempdb` használati területek a következők:

- Ideiglenes táblák
- Táblaváltozók
- Táblázat értékű paramétereket
- Verziók tároló használata (kifejezetten társított hosszú ideig futó tranzakció)
- Számos, a kivonatoló illesztések és cséve használó terveket tartalmazó lekérdezések

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>Leggyakoribb lekérdezések, amelyek a Táblaváltozók és ideiglenes táblák

A következő lekérdezés segítségével azonosíthatja a leggyakoribb lekérdezések, amelyek a Táblaváltozók és ideiglenes táblák:

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

### <a name="identify-long-running-transactions"></a>A hosszabb ideig futó tranzakció azonosítása

Használja a következő lekérdezést, azonosíthatja a hosszú tranzakció fut. Megakadályozza, hogy hosszú ideig futó tranzakció verzió tároló karbantartása.

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

## <a name="identify-memory-grant-wait-performance-issues"></a>Memória biztosítása várjon teljesítménybeli problémák azonosítása

Ha a legfelső várjon típusa van `RESOURCE_SEMAHPORE` és magas a Processzor a probléma nem rendelkezik, előfordulhat, hogy egy tárról várakozási probléma.

### <a name="determine-if-a-resourcesemahpore-wait-is-a-top-wait"></a>Határozza meg, ha egy `RESOURCE_SEMAHPORE` várakozási egy felső wait

A következő lekérdezés használata annak meghatározásához, hogy egy `RESOURCE_SEMAHPORE` várakozási egy felső wait

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

### <a name="identity-high-memory-consuming-statements"></a>Magas memória-igényes utasítások identitás

Magas memória-igényes utasítások azonosításához használja a következő lekérdezést:

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

### <a name="identify-the-top-10-active-memory-grants"></a>Az első 10 aktív memóriát biztosít azonosítása

A felső 10 aktív memóriát biztosít azonosításához használja a következő lekérdezést:

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

## <a name="calculating-database-and-objects-sizes"></a>Adatbázis és objektumok méretek kiszámítása

A következő lekérdezés (megabájtban) az adatbázis méretét adja vissza:

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

A következő lekérdezés az adatbázis méretét (megabájtban) objektumokat adja vissza:

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Kapcsolatok ellenőrzése

Használhatja a [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) kérhet le információt a kapcsolatok egy adott Azure SQL Database-kiszolgáló és minden kapcsolat részleteinek megtekintése. Emellett a [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) megtekintése hasznos, amikor az összes aktív felhasználói kapcsolat és belső feladatok adatainak lekérése.
Az alábbi lekérdezés lekérdezi az aktuális kapcsolat információkat:

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
> Végrehajtásakor a **sys.dm_exec_requests** és **sys.dm_exec_sessions nézetek**, ha rendelkezik **VIEW DATABASE STATE** engedéllyel az adatbázis láthatja az összes végrehajtása munkamenetek a az adatbázisban. Ellenkező esetben tekintse meg az aktuális munkamenet.

## <a name="monitor-resource-use"></a>Erőforrás-használat figyelése

Erőforrás-használat használatával figyelheti [SQL Database lekérdezési Terheléselemző](sql-database-query-performance.md) és [Query Store](https://msdn.microsoft.com/library/dn817826.aspx).

Használatának két nézetet is ellenőrizheti:

- [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats

Használhatja a [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) nézetben minden egyes SQL database-ben. A **sys.dm_db_resource_stats** a nézet jeleníti meg a legutóbbi erőforrás adatait a szolgáltatási szinthez képest. A CPU, adat IO, napló írási műveletek és a memória átlagos százalékos 15 másodpercenként rögzíti, és 1 órára vonatkozó karbantartása.

Mivel ez a nézet biztosít egy részletesebb erőforrás-használat, **sys.dm_db_resource_stats** első bármely jelenlegi-állapot elemzéshez vagy hibaelhárítási. Ez a lekérdezés például az elmúlt egy órában az aktuális adatbázisra vonatkozó átlagos és maximális erőforrás használatát mutatja:

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

Más lekérdezések lévő példák [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### <a name="sysresourcestats"></a>sys.resource_stats

A [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) megtekintheti a **fő** adatbázis rendelkezik, amelyek segítségével az adott szolgáltatásszinten az SQL-adatbázis teljesítményének figyelése és a számítási méret további információt. Az adatok 5 percenként összegyűjtött, és körülbelül két hétben változatlan marad. Ez a nézet hasznos hosszabb távú előzményadatok elemzése érdekében hogyan az SQL database erőforrásokat használ.

A következő diagram bemutatja a CPU erőforrás használható egy prémium szintű adatbázis P2 számítási méretű óránként egy hét. Ehhez a diagramhoz egy hétfőn kezdődő, 5 munkanapok megjeleníti, és megjeleníti a hétvégi, amikor sok kisebb történik, az alkalmazás.

![Az SQL database erőforrás-használat](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Az adatokból, ez az adatbázis jelenleg rendelkezik egy maximális CPU-terhelés alig több mint 50 %-os CPU-használat a P2 viszonyított számítási méret (csúcspontját a "frissítő kedd"). Ha CPU a meghatározó tényező az alkalmazás erőforrás-profilt, majd dönthet, hogy P2-e a megfelelő számítási méret garantálja, hogy a számítási feladatok mindig megfelel-e. Ha egy alkalmazás, hogy idővel egyre, célszerű egy további erőforrás az puffer kell, hogy az alkalmazás soha nem eléri a teljesítményszint korlátot. Ha növeli a számítási méret, segíthet, amelyek akkor jelentkeznek, amikor egy adatbázis nem rendelkezik elegendő a kérelmek feldolgozását hatékonyan, különösen a késésre érzékeny környezetekben power ügyfél látható hibák elkerülése érdekében. Ilyen például, egy adatbázis, amely támogatja az adatbázis-hívások eredményei alapján weblapok aggasztó alkalmazás.

Többi alkalmazástípus esetében előfordulhat, hogy ugyanazon a grafikonon másképp értelmezi. Például ha egy alkalmazás megpróbálja feldolgozni az Bérlista-adatokat minden nap, és rendelkezik ugyanabban a diagramban, "batch-feladat" modell az ilyen típusú előfordulhat, hogy tegye részletes P1 szintű számítási méretben. P1 szintű számítási mérete 100 dtu-k 200 dtu-k, a P2-höz képest méretű számítási rendelkezik. P1 szintű számítási mérete a P2 fele a teljesítmény számítási méret nyújt. Így a P2 CPU-használat 50 %-os egyenlő 100 %-os CPU-használat a P1 szintben foglalt. Ha az alkalmazás nem rendelkezik időtúllépések, előfordulhat, hogy nem számít, hogy a feladat 2 óráig, azaz 2,5 órát, alatt Ha ma kapja meg. Egy alkalmazás ebbe a kategóriába valószínűleg használhatja a P1 szintű számítási méretét. Használja ki, hogy nincsenek-e a nap, erőforrás-használat esetén alacsonyabb, így bármilyen "big Data típusú csúcs" lehet, hogy kerülnek a csatornák egy későbbi részében a nap folyamán ideig is igénybe vehet. P1 szintű számítási mérete az adott alkalmazás (és pénz megtakarítása), érdemes lehet mindaddig, amíg a feladat be tudja időben minden nap.

Az Azure SQL Database közzéteszi felhasznált minden aktív adatbázis az erőforrás adatait a **sys.resource_stats** nézete a **fő** adatbázis az egyes kiszolgálókon. A tábla összesített értéket jelenít meg az 5 perces időközönként. Az alapszintű, Standard és prémium szolgáltatási szinteken, az adatok több mint 5 percig is eltarthat, a táblázatban jelennek meg, így ezek az adatok közel valós idejű elemzési helyett előzményadataik elemzésére hasznosabb. Lekérdezés a **sys.resource_stats** adatbázis legutóbbi előzményeinek megtekintéséhez megtekintése és ellenőrzése-i azt szeretné, hogy szükség esetén a teljesítmény akár a Foglalás választotta.

> [!NOTE]
> Akkor kapcsolódnia kell a **fő** adatbázis a logikai SQL adatbázis-kiszolgáló lekérdezéséhez **sys.resource_stats** a következő példákban.

Ez a példa bemutatja, hogyan érhető el ebben a nézetben az adatokat:

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![A sys.resource_stats katalógusnézet](./media/sql-database-performance-guidance/sys_resource_stats.png)

A következő példa bemutatja, hogy különböző módon használhatja a **sys.resource_stats** katalógus nézetét, ahol információkat találhat az SQL database erőforrásokat:

1. Az elmúlt egy hét erőforrás meg az adatbázis userdb1 használja, ez a lekérdezés futtatása:

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. Arról, hogy a számítási feladatok megfelel-e a számítási méret kipróbálhatók egyes funkcióiról, az erőforrás-mérőszámok feltárásához kell: Processzor, olvasási, írási, feldolgozók száma és munkamenetek számát. Íme a módosított lekérdezés használatával **sys.resource_stats** jelentéséhez ezen erőforrás-mérőszámok átlagos és maximális értékeit:

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

3. Ezekkel az információkkal kapcsolatban az átlagos és maximális értékeket minden egyes erőforrás-metrika értékelhet arról, hogy a számítási feladatok illeszkedik a kiválasztott számítási mérete. Általában az átlagérték **sys.resource_stats** jó alapterv használata a célméretet szemben biztosítanak. Az elsődleges mérési meghajtó kell lennie. Például előfordulhat, hogy használni a Standard szintű szolgáltatáscsomagban S2 számítási méretű. Átlagos százalékos használata a Processzor- és i/o-olvasási és írási műveletek az alábbiakban 40 %-os, feldolgozók átlagos száma 50 alá van és munkamenetek átlagos száma nem éri a 200-as. A számítási feladat lehet, hogy illeszkedik az S1 szintű számítási mérete. Könnyen látható, hogy az adatbázis megfelel a feldolgozó és a munkamenet korlátok. Lásd: e adatbázis illeszkedik a Processzor, olvasási és írási oszd meg alacsonyabb számítási méret, az alacsonyabb dtu-k száma számítási méret az aktuális számítási méret dtu-k száma alapján, és az eredmény megszorozza 100:

    ```S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40```

    Ez a relatív teljesítménybeli különbség a két számítási méret százalékban. Az erőforrások használatával Ez a mennyiség nem haladja meg, ha a számítási feladatok a kisebb számítási méret előfordulhat, hogy illeszkedik. Azonban szüksége, tekintse meg az erőforrás-használati értékek összes tartományát, és meghatározhatja, százalékos, milyen gyakran az adatbázisok számítási lenne elférnek a kisebb számítási mérete. A következő lekérdezés kimenete a illesztése százalékos erőforrás dimenziónként, 40 %-kal, azt ebben a példában számított küszöbérték alapján:

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Az adatbázisokra vonatkozó szolgáltatási szint alapján, eldöntheti, hogy a számítási feladatok illeszkedik a kisebb számítási mérete. Ha az adatbázis számítási feladat célja 99,9 százalékos rendelkezésre állást, és az előző lekérdezés visszaadja az értékek nagyobb, mint az összes három erőforrás dimenzió 99,9 %-os, a számítási feladatok nagy valószínűséggel a kisebb számítási méret illeszkedik.

    Hibaoldal illesztése százalékos is biztosít abba, hogy helyezze át a következő nagyobb számítási méretét felel meg a célkitűzés. Például userdb1 látható, a következő CPU-használat az elmúlt egy hét:

   | Átlagos CPU-százalék | Maximális Processzorhasználat százalékos |
   | --- | --- |
   | 24.5 |100.00 |

    Az átlagos Processzorhasználat, a számítási méret, amely akkor jól illeszkedik a számítási az adatbázis mérete legfeljebb negyedév szól. De a maximális érték jelenik meg, hogy az adatbázis eléri a korlátot, a számítási méret. Szeretne áthelyezni a következő nagyobb számítási méret? Keresse meg, hogyan lehet több alkalommal a számítási feladat eléri 100 %-os, és hasonlítsa össze az adatbázisban munkaterhelés cél.

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Ha a lekérdezés visszaad egy értéket kisebb, mint 99,9 %-a három erőforrás dimenziók bármelyikét érdemes vagy magasabb szintű számítási méretére, vagy alkalmazás hangolási módszerek használatáról az SQL database terhelésének csökkentése érdekében.

4. Ebben a gyakorlatban is figyelembe veszi a várható számítási feladatok növekedése a jövőben.

Rugalmas készletek esetén az ebben a szakaszban leírt technikákkal az adatbáziskészlet egyes adatbázisait is figyelheti. De ugyanígy figyelheti az adatbáziskészlet egészét is. További információkért lásd: [Rugalmas készlet figyelése és kezelése](sql-database-elastic-pool-manage-portal.md).

### <a name="maximum-concurrent-requests"></a>Egyidejű kérelmek maximális száma

Az egyidejű kérelmek számra megtekintéséhez futtassa az SQL Database a Transact-SQL-lekérdezést:

    ```sql
    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R;
    ```

Egy helyszíni SQL Server-adatbázis, a számítási feladatok elemzése, módosítsa a lekérdezést az adott adatbázis szűrés elemezni szeretné. Például ha egy helyszíni adatbázisból MyDatabase nevű, a Transact-SQL-lekérdezés adja vissza az egyidejű kérelmek száma ebben az adatbázisban:

    ```sql
    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase';
    ```

Ez az egyetlen időpontban csak egy pillanatkép időben. Ismerje meg a számítási feladatok és a párhuzamos kérés követelményeinek, szüksége lesz számos mintákat gyűjtsön idővel.

### <a name="maximum-concurrent-logins"></a>Egyidejű bejelentkezések maximális

Elemezheti a felhasználói és -minták meghatározására, hogy képet kapjon a bejelentkezések gyakoriságát. Emellett futtathatja valós terhelések győződjön meg arról, hogy nem állt jelen vagy egyéb korlátok ebben a cikkben bemutatjuk, hogy egy tesztkörnyezetben. Egyetlen lekérdezés vagy a dinamikus felügyeleti nézet (DMV), amely meg tudja jeleníteni az egyidejű bejelentkezési counts vagy előzmények nem áll rendelkezésre.

Ha több ügyfél használja ugyanazt a kapcsolati karakterláncot, a szolgáltatás végzi a hitelesítést minden egyes bejelentkezés. 10 felhasználó egyszerre csatlakozhat adatbázis ugyanazt a felhasználónevet és jelszót, ha 10 egyidejű bejelentkezések lenne. Ez a korlátozás csak a bejelentkezési és hitelesítési időtartama vonatkozik. Ha az azonos 10 felhasználó egymás után csatlakozhat az adatbázishoz, egyidejű bejelentkezések száma soha nem lenne 1-nél nagyobb.

> [!NOTE]
> Jelenleg ez a korlátozás nem vonatkozik a rugalmas készletekben található adatbázisokat.

### <a name="maximum-sessions"></a>Munkamenetek maximális száma

A jelenlegi aktív munkamenetek számának megtekintéséhez futtassa az SQL Database a Transact-SQL-lekérdezést:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Ha elemzett egy helyszíni SQL Server számítási feladatok, módosítsa a lekérdezést, hogy arra koncentrálhasson, egy adott adatbázishoz. Ez a lekérdezés segít meghatározni, hogy lehetséges munkamenet igényeinek, az adatbázis Ha áthelyezi az Azure SQL Database fontolgatja.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Ezeket a lekérdezéseket,-időponthoz számát adja vissza. Ha a több mintához idővel, rendelkezni fog a legjobb ismeretekkel a munkamenet használja.

Az SQL Database az elemzést követően megjelenik a korábbi statisztika munkamenetek lekérdezésével a [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) megtekintése és ellenőrzése a **active_session_count** oszlop.

## <a name="monitoring-query-performance"></a>A lekérdezési teljesítmény figyelése

Lassú vagy hosszú ideig futó lekérdezések jelentős rendszer-erőforrásokat használhatnak fel. Ez a szakasz bemutatja, hogyan dinamikus felügyeleti nézetek használatával néhány gyakori lekérdezési teljesítmény problémák észleléséhez. Egy régebbi, de továbbra is hasznos lehet a hibaelhárításhoz, a hivatkozás a [teljesítménybeli problémák elhárítása az SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) a cikk a Microsoft TechNet webhelyen.

### <a name="finding-top-n-queries"></a>Legfontosabb N lekérdezések keresése

Az alábbi példa szerint átlagos CPU-idő az első öt lekérdezés adatait adja vissza. Ebben a példában a lekérdezések a lekérdezés kivonata alapján összesíti az, hogy logikailag egyenértékű lekérdezések azok összesített erőforrás-használat szerint vannak csoportosítva.

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

### <a name="monitoring-blocked-queries"></a>Letiltott lekérdezések figyelése

Lassú vagy hosszú ideig futó lekérdezések hozzájárulhatnak a túlzott erőforrás-használat és a letiltott lekérdezések következménye lehet. A blokkolás okát gyenge alkalmazástervezést, a hibás terveket, a hasznos indexeket, és így tovább hiánya lehet. A sys.dm_tran_locks nézet segítségével az aktuális zárolási tevékenység adatainak lekérése az Azure SQL Database-ben. Például kódját, lásd: [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) az SQL Server Online könyvekben.

### <a name="monitoring-query-plans"></a>Figyelési lekérdezési tervek

Egy hatékony lekérdezési terv is előfordulhat, hogy növelje CPU-felhasználás. Az alábbi példában a [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) nézet segítségével meghatározhatja, melyik lekérdezésben használja a legtöbb összegző Processzor.

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

[Az SQL Database bemutatása](sql-database-technical-overview.md)

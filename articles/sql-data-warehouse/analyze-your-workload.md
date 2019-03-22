---
title: A számítási feladatok – Azure SQL Data Warehouse elemzése |} A Microsoft Docs
description: A lekérdezés rangsorolási az Azure SQL Data Warehouse a számítási feladatok elemzése technikákat.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 434cbb18a109308844dbc7ff219d40948678e86e
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310727"
---
# <a name="analyze-your-workload-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse a számítási feladatok elemzése

Az Azure SQL Data Warehouse a számítási feladatok elemzése technikákat.

## <a name="resource-classes"></a>Erőforrásosztályok

Az SQL Data warehouse-bA a lekérdezések rendszererőforrások hozzárendelése erőforrásosztályok biztosít.  Erőforrásosztályok további információkért lásd: [erőforrás osztályok és a számítási feladatok kezelése](resource-classes-for-workload-management.md).  Lekérdezések várakozik, ha a lekérdezéshez hozzárendelt erőforrásosztályhoz van szüksége, mint a jelenleg elérhető további források.

## <a name="queued-query-detection-and-other-dmvs"></a>Aszinkron lekérdezés észlelési és más dinamikus felügyeleti nézetek

Használhatja a `sys.dm_pdw_exec_requests` DMV feldolgozási sorba váró lekérdezések azonosítása. A párhuzamosság üzembe helyezési pont váró lekérdezések állapotú **felfüggesztve**.

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

Számítási feladat felügyeleti szerepkörök tekinthet meg a `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

A következő lekérdezés jeleníti meg, hogy melyik szerepkör minden felhasználóhoz hozzá van rendelve.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

Az SQL Data Warehouse a következő típusok várjon rendelkezik:

* **LocalQueriesConcurrencyResourceType**: Egyidejűségi tárolóhely keretein kívül található lekérdezések. DMV-lekérdezések és a rendszer függvények, mint például `SELECT @@VERSION` példa a helyi lekérdezéseket.
* **UserConcurrencyResourceType**: Egyidejűségi tárolóhely keretein belül található lekérdezések. A végfelhasználói táblákra mutató lekérdezések példa, amelyek az erőforrástípushoz használna képviseli.
* **DmsConcurrencyResourceType**: Az adatátviteli műveletek eredő vár.
* **BackupConcurrencyResourceType**: A Várakozás azt jelzi, hogy egy adatbázis biztonsági mentésének folyamatban van. Az erőforrástípus maximális értéke 1. Ha több biztonsági mentése egy időben, a többi kért várólista. Általában javasoljuk, hogy egy minimális idő 10 perc egymást követő pillanatkép között. 

A `sys.dm_pdw_waits` DMV Lekérdezéséhez használható kérést arra vár, hogy milyen erőforrásokat.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

A `sys.dm_pdw_resource_waits` DMV egy adott lekérdezésre vonatkozó várakozási információit jeleníti meg. Erőforrás várakozási idő mértékekkel az idő, Várakozás erőforrásokra kell adni. Jel várakozási idő az az idő, az alakzatot a Processzor a lekérdezés ütemezése az alapul szolgáló SQL Server szükséges.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```

Is használhatja a `sys.dm_pdw_resource_waits` DMV kiszámíthatja, hány egyidejű helyet foglalnak le kapott.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

A `sys.dm_pdw_wait_stats` DMV is használható nagy horderejű tendenciája vár.

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>További lépések

Adatbázis-felhasználók és biztonsági kezelésével kapcsolatos további információkért lásd: [biztonságossá egy adatbázis az SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md). További információ a módját a nagyobb erőforrásosztályok javíthatja a fürtözött oszlopcentrikus index minőségét, lásd: [szegmens minőségének javítására indexek újraépítése](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

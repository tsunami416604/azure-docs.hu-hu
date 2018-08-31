---
title: A számítási feladatok – Azure SQL Data Warehouse elemzése |} A Microsoft Docs
description: A lekérdezés rangsorolási az Azure SQL Data Warehouse a számítási feladatok elemzése technikákat.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 4ce84e9714b580bcc243285dc1da5ae24a27e8e5
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43248093"
---
# <a name="analyze-your-workload-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse a számítási feladatok elemzése
A lekérdezés rangsorolási az Azure SQL Data Warehouse a számítási feladatok elemzése technikákat.

## <a name="workload-groups"></a>Munkaterhelés-csoport 
Az SQL Data Warehouse erőforrásosztályok számítási feladatok csoportok használatával valósítja meg. Nincsenek összesen nyolc munkaterhelési csoportokat, amelyek vezérlik a erőforrásosztályok viselkedését a különböző DWU-méretek. Bármely DWU az SQL Data Warehouse csak közül négyet használ a nyolc munkaterhelési csoportokban. Ez a módszer van értelme, mert egyes tevékenységprofil-csoport van négy erőforrás osztályok egyike: smallrc, mediumrc, largerc, vagy xlargerc. A számítási feladatok csoportok ismertetése fontossága, hogy a számítási feladatok csoportok vannak beállítva a magasabb *fontosság*. Fontosság használt Processzor ütemezés. Lekérdezések futtatása a nagyon fontosként megjelölve első háromszor további CPU-ciklusok közepes fontosság futtassa a lekérdezéseket. Egyidejűségi tárolóhely leképezések ezért is processzorprioritás határozza meg. A lekérdezés 16 vagy több tárhelyek használ fel, amikor nagyon fontosként megjelölve keresőszolgáltatás.

Az alábbi táblázat az egyes tevékenységprofil-csoport fontosság leképezéseit.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Eszközcsoport-leképezéseket számítási feladatok egyidejű helyet foglalnak le, és a fontosság

| Munkaterhelés-csoport | Egyidejűségi tárhely leképezése | MB / terjesztési (rugalmasság) | MB / terjesztési (számítás) | Fontosság leképezés |
|:---------------:|:------------------------:|:------------------------------:|:---------------------------:|:------------------:|
| SloDWGroupC00   | 1                        |    100                         | 250                         | Közepes             |
| SloDWGroupC01   | 2                        |    200                         | 500                         | Közepes             |
| SloDWGroupC02   | 4                        |    400                         | 1000                        | Közepes             |
| SloDWGroupC03   | 8                        |    800                         | 2000                        | Közepes             |
| SloDWGroupC04   | 16                       |  1,600                         | 4000                        | Magas               |
| SloDWGroupC05   | 32                       |  3,200                         | 8000                        | Magas               |
| SloDWGroupC06   | 64                       |  6,400                         | 16,000                      | Magas               |
| SloDWGroupC07   | 128                      | 12,800                         | 32,000                      | Magas               |
| SloDWGroupC08   | 256                      | 25,600                         | 64,000                      | Magas               |

<!-- where are the allocation and consumption of concurrency slots charts? --> A **foglalási és felhasználási egyidejű helyet foglalnak le** diagram bemutatja egy DW500 használja 1, 4, 8 és 16 egyidejű helyet foglalnak le a smallrc, mediumrc, largerc vagy xlargerc, jelölik. Minden egyes erőforrásosztályhoz fontosságának megkereséséhez ezeket az értékeket az előző diagramon kereshet.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>Fontosság erőforrásosztályokat DW500 leképezése
| Erőforrásosztály | A tevékenységprofil-csoport | Párhuzamos időszeletek használt | MB / terjesztési | Fontosság |
|:-------------- |:-------------- |:----------------------:|:-----------------:|:---------- |
| smallrc        | SloDWGroupC00  | 1                      | 100               | Közepes     |
| mediumrc       | SloDWGroupC02  | 4                      | 400               | Közepes     |
| largerc        | SloDWGroupC03  | 8                      | 800               | Közepes     |
| xlargerc       | SloDWGroupC04  | 16                     | 1,600             | Magas       |
| staticrc10     | SloDWGroupC00  | 1                      | 100               | Közepes     |
| staticrc20     | SloDWGroupC01  | 2                      | 200               | Közepes     |
| staticrc30     | SloDWGroupC02  | 4                      | 400               | Közepes     |
| staticrc40     | SloDWGroupC03  | 8                      | 800               | Közepes     |
| staticrc50     | SloDWGroupC03  | 16                     | 1,600             | Magas       |
| staticrc60     | SloDWGroupC03  | 16                     | 1,600             | Magas       |
| staticrc70     | SloDWGroupC03  | 16                     | 1,600             | Magas       |
| staticrc80     | SloDWGroupC03  | 16                     | 1,600             | Magas       |

## <a name="view-workload-groups"></a>Számítási feladatok csoportok megtekintése
A következő lekérdezés az erőforrás-vezérlő szempontjából memória erőforrás-elosztás részleteket jeleníti meg. Ez hasznos lehet a munkaterhelési csoportokban aktív és korábbi használatának elemzése esetén végzett hibaelhárításhoz.

```sql
WITH rg
AS
(   SELECT  
     pn.name                                AS node_name
    ,pn.[type]                              AS node_type
    ,pn.pdw_node_id                         AS node_id
    ,rp.name                                AS pool_name
    ,rp.max_memory_kb*1.0/1024              AS pool_max_mem_MB
    ,wg.name                                AS group_name
    ,wg.importance                          AS group_importance
    ,wg.request_max_memory_grant_percent    AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                             AS group_max_dop
    ,wg.effective_max_dop                   AS group_effective_max_dop
    ,wg.total_request_count                 AS group_total_request_count
    ,wg.total_queued_request_count          AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
    AND     rp.name    = 'SloDWPool'
)
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
        node_name
,       group_request_max_memory_grant_pcnt
,       group_importance
;
```

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

* **LocalQueriesConcurrencyResourceType**: lekérdezések egyidejűségi tárolóhely keretein kívül található. DMV-lekérdezések és a rendszer függvények, mint például `SELECT @@VERSION` példa a helyi lekérdezéseket.
* **UserConcurrencyResourceType**: lekérdezések egyidejűségi tárolóhely keretein belül található. A végfelhasználói táblákra mutató lekérdezések példa, amelyek az erőforrástípushoz használna képviseli.
* **DmsConcurrencyResourceType**: vár, az adatátviteli műveletek keletkezett.
* **BackupConcurrencyResourceType**: A várakozás azt jelzi, hogy egy adatbázis biztonsági mentésének folyamatban van. Az erőforrástípus maximális értéke 1. Ha több biztonsági mentése egy időben, a többi kért várólista.

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

A `sys.dm_pdw_resource_waits` DMV csak a megadott lekérdezés által igénybe vett erőforrás vár jeleníti meg. Erőforrás várakozási idő csak a Várakozás az erőforrások figyelésekor jel várakozási idő, amely az alapul szolgáló SQL-kiszolgálók a CPU-kiszolgálóra a lekérdezés ütemezése szükséges idő adni időt méri.

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



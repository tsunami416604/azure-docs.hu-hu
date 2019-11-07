---
title: A számítási feladatok elemzése
description: Módszerek a számítási feladatok rangsorolásának elemzéséhez Azure SQL Data Warehouseban.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 14e53c1ebe63fac0f7c8e29f66ee5aa0cb3b9526
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693112"
---
# <a name="analyze-your-workload-in-azure-sql-data-warehouse"></a>A számítási feladatok elemzése Azure SQL Data Warehouse

Technikák a számítási feladatok elemzéséhez Azure SQL Data Warehouseban.

## <a name="resource-classes"></a>Erőforrásosztályok

A SQL Data Warehouse erőforrás-osztályokat biztosít a lekérdezésekhez szükséges rendszererőforrások hozzárendeléséhez.  Az erőforrás-osztályokkal kapcsolatos további információkért lásd: [erőforrás-osztályok & munkaterhelés-kezelés](resource-classes-for-workload-management.md).  A lekérdezések megvárhatják, ha a lekérdezéshez hozzárendelt erőforrás-osztálynak több erőforrásra van szüksége, mint amennyi jelenleg elérhető.

## <a name="queued-query-detection-and-other-dmvs"></a>Várólistán lévő lekérdezések észlelése és egyéb DMV

A `sys.dm_pdw_exec_requests` DMV használatával azonosíthatja a párhuzamossági sorban Várakozó lekérdezéseket. Az egyidejű tárolóhelyre várakozó lekérdezések **felfüggesztve**állapottal rendelkeznek.

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

A számítási feladatok felügyeleti szerepkörei `sys.database_principals`megtekinthetők.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

A következő lekérdezés azt jeleníti meg, hogy az egyes felhasználók melyik szerepkörhöz vannak rendelve.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

SQL Data Warehouse a következő várakozási típusok közül:

* **LocalQueriesConcurrencyResourceType**: az egyidejű tárolóhely-keretrendszeren kívüli lekérdezések. A DMV-lekérdezések és a rendszerfüggvények, például a `SELECT @@VERSION` a helyi lekérdezések példái.
* **UserConcurrencyResourceType**: az egyidejű tárolóhely-keretrendszerben található lekérdezések. A végfelhasználói táblákra irányuló lekérdezések olyan példákat mutatnak be, amelyek ezt az erőforrástípust használják.
* **DmsConcurrencyResourceType**: megvárja az adatáthelyezési műveletek eredményét.
* **BackupConcurrencyResourceType**: Ez a várakozás azt jelzi, hogy egy adatbázis biztonsági mentése folyamatban van. Az erőforrástípus maximális értéke 1. Ha egy időben több biztonsági mentést is kértek, a többi üzenetsor. Általánosságban elmondható, hogy az egymást követő Pillanatképek közül legalább 10 percet ajánlunk. 

A `sys.dm_pdw_waits` DMV használatával megtekintheti, hogy mely erőforrásokra vár kérelmek.

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

A `sys.dm_pdw_resource_waits` DMV egy adott lekérdezés várakozási adatait jeleníti meg. Az erőforrás-várakozási idő az erőforrások számára várakozási időt méri. A várakozási idő az az idő, ameddig a mögöttes SQL-kiszolgálók a lekérdezéseket a CPU-ra ütemezhetik.

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

Azt is megteheti, hogy a `sys.dm_pdw_resource_waits` DMV kiszámítja, hogy hány párhuzamossági tárolóhelyet adtak meg.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

A `sys.dm_pdw_wait_stats` DMV a várakozások történelmi trendek elemzésére használható.

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

Az adatbázis-felhasználók és a biztonság kezelésével kapcsolatos további információkért lásd: [adatbázis biztonságossá tétele SQL Data Warehouseban](sql-data-warehouse-overview-manage-security.md). Ha többet szeretne megtudni arról, hogy a nagyobb erőforrás-osztályok Hogyan javíthatják a fürtözött oszlopcentrikus index minőségét, tekintse meg az [indexek újraépítése a szegmens minőségének javítása érdekében című szakaszt](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

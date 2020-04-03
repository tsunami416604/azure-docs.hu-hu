---
title: A számítási feladatok elemzése
description: Az Azure Synapse Analytics lekérdezési rangsorolásának elemzésére szolgáló technikák.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: d10a642f9309e4bb93368564488fc75be15fa27c
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586078"
---
# <a name="analyze-your-workload-in-azure-synapse-analytics"></a>A számítási feladatok elemzése az Azure Synapse Analytics szolgáltatásban

A Synapse SQL-számítási feladatának elemzésére szolgáló technikák az Azure Synapse Analytics szolgáltatásban.

## <a name="resource-classes"></a>Erőforrásosztályok

A Synapse SQL erőforrásosztályokat biztosít a rendszererőforrások lekérdezésekhez való hozzárendeléséhez.  Az erőforrásosztályokról további információt az [Erőforrásosztályok & a munkaterhelés-kezelés című](resource-classes-for-workload-management.md)témakörben talál.  A lekérdezések megvárják, hogy a lekérdezéshez rendelt erőforrásosztálynak a jelenleg rendelkezésre állónál több erőforrásra van szüksége.

## <a name="queued-query-detection-and-other-dmvs"></a>Várakozással váró lekérdezésészlelése és egyéb DMV-k

A DMV segítségével azonosíthatja az `sys.dm_pdw_exec_requests` egyidejűségi várólistában várakozó lekérdezéseket. Az egyidejűségi tárolóhelyre váró lekérdezések állapota **felfüggesztett.**

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

A számítási feladatok kezelése `sys.database_principals`szerepkörök megtekinthetők a segítségével.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

A következő lekérdezés megmutatja, hogy az egyes felhasználók melyik szerepkörhöz vannak hozzárendelve.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

A Szinapszis SQL a következő várakozási típusokkal rendelkezik:

* **LocalQueriesConcurrencyResourceType**: Olyan lekérdezések, amelyek az egyidejűségi tárolóhely keretrendszerén kívül helyezkednek el. A dmv-lekérdezések `SELECT @@VERSION` és a rendszerfüggvények, például a helyi lekérdezések példái.
* **UserConcurrencyResourceType**: Lekérdezések, amelyek az egyidejűségi tárolóhely keretrendszerén belül ülnek. A végfelhasználói tábláklekérdezései olyan példákat jelentenek, amelyek ezt az erőforrástípust használnák.
* **DmsConcurrencyResourceType**: Az adatmozgatási műveletekből eredő várakozás.
* **BackupConcurrencyResourceType**: Ez a várakozás azt jelzi, hogy egy adatbázisról biztonsági másolat készül. Az erőforrástípus maximális értéke 1. Ha egyszerre több biztonsági mentést is kért, a többi várólistára kerül. Általában azt javasoljuk, hogy egy minimális idő között egymást követő pillanatképek 10 perc. 

A `sys.dm_pdw_waits` DMV segítségével megtekinthető, hogy a kérelem mely erőforrásokra vár.

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

A `sys.dm_pdw_resource_waits` DMV egy adott lekérdezés várakozási adatait jeleníti meg. Az erőforrások várakozási ideje az erőforrások megadására váró időt méri. A jelvárakozási idő az az idő, ameddig az alapul szolgáló SQL-kiszolgálók a lekérdezést a processzorra ütemezik.

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

A `sys.dm_pdw_resource_waits` DMV-ben is használhatja, hogy hány egyidejűségi bővítőhely kapott.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

A `sys.dm_pdw_wait_stats` DMV használható történelmi trend elemzése vár.

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

Az adatbázis-felhasználók kezeléséről és a biztonságról az [Adatbázis védelme a Synapse SQL rendszerben](sql-data-warehouse-overview-manage-security.md)című témakörben talál további információt. Ha többet szeretne tudni arról, hogy a nagyobb erőforrásosztályok hogyan javíthatják a fürtözött oszlopcentrikus index minőségét, olvassa [el az Indexek létrehozása a szegmensminőség javítása érdekében](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)című témakört.

---
title: Figyelés dinamikus felügyeleti nézetek használatával az Azure SQL Database |} A Microsoft Docs
description: Megtudhatja, hogyan észlelheti és diagnosztizálhatja a teljesítményproblémákat gyakran figyelése a Microsoft Azure SQL Database dinamikus felügyeleti nézetek használatával.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: carlrab
ms.openlocfilehash: c4d1170bd2fe4acb135c88191b447f734e312723
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715957"
---
# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Az Azure SQL Database felügyelete dinamikus felügyeleti nézetek használatával
A Microsoft Azure SQL Database lehetővé teszi, hogy a dinamikus felügyeleti nézetek által letiltott vagy hosszú ideig futó lekérdezéseket, erőforrás-keresztmetszetek, gyenge lekérdezési tervek és így tovább okozhatta teljesítményproblémák diagnosztizálásához egy részét. Ez a témakör információt nyújt az gyakori teljesítményproblémák észlelése dinamikus felügyeleti nézetek használatával.

Az SQL Database támogatja a dinamikus felügyeleti nézetek három kategóriába részben:

* Adatbázissal kapcsolatos dinamikus felügyeleti nézetek.
* Végrehajtási kapcsolatos dinamikus felügyeleti nézetek.
* Tranzakció-hoz kapcsolódó dinamikus felügyeleti nézetek.

A dinamikus felügyeleti nézetek részletes információkért lásd: [dinamikus felügyeleti nézetek és függvények (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) az SQL Server Online könyvekben.

## <a name="permissions"></a>Engedélyek
Az SQL Database dinamikus felügyeleti nézetek lekérdezéséhez szükséges **VIEW DATABASE STATE** engedélyeket. A **VIEW DATABASE STATE** engedélyt az aktuális adatbázisban lévő összes objektum adatait adja vissza.
Megadását a **VIEW DATABASE STATE** engedélyt egy adott adatbázis-felhasználót a következő lekérdezés futtatásával:

```GRANT VIEW DATABASE STATE TO database_user; ```

A helyszíni SQL Server-példányt, a dinamikus felügyeleti nézetek kiszolgáló állapot adatait adja vissza. SQL Database-ben, csak az aktuális logikai adatbázis kapcsolatos információkat ad vissza.

## <a name="calculating-database-size"></a>Adatbázis méretének kiszámítása
A következő lekérdezés (megabájtban) az adatbázis méretét adja vissza:

```
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

A következő lekérdezés az adatbázis méretét (megabájtban) objektumokat adja vissza:

```
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

```
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
> 
> 

## <a name="monitoring-query-performance"></a>A lekérdezési teljesítmény figyelése
Lassú vagy hosszú ideig futó lekérdezések jelentős rendszer-erőforrásokat használhatnak fel. Ez a szakasz bemutatja, hogyan dinamikus felügyeleti nézetek használatával néhány gyakori lekérdezési teljesítmény problémák észleléséhez. Egy régebbi, de továbbra is hasznos lehet a hibaelhárításhoz, a hivatkozás a [teljesítménybeli problémák elhárítása az SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) a cikk a Microsoft TechNet webhelyen.

### <a name="finding-top-n-queries"></a>Legfontosabb N lekérdezések keresése
Az alábbi példa szerint átlagos CPU-idő az első öt lekérdezés adatait adja vissza. Ebben a példában a lekérdezések a lekérdezés kivonata alapján összesíti az, hogy logikailag egyenértékű lekérdezések azok összesített erőforrás-használat szerint vannak csoportosítva.

```
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

```
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


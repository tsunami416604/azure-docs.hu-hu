---
title: "Az Azure SQL adatbázis dinamikus felügyeleti nézetekkel figyelése |} Microsoft Docs"
description: "Megtudhatja, hogyan észlelheti és diagnosztizálhatja a gyakori problémák a Microsoft Azure SQL Database figyelése dinamikus felügyeleti nézetek használatával."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
tags: 
ms.assetid: d08f505f-3c62-47d4-bab7-35c9a834b79b
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: 298adcad78163e82a926abb684a172d56b4a33dd
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Az Azure SQL Database felügyelete dinamikus felügyeleti nézetek használatával
A Microsoft Azure SQL Database lehetővé teszi, hogy a dinamikus felügyeleti nézetekkel teljesítményproblémákat, amely valószínűleg az okozza letiltott vagy hosszan futó lekérdezések, erőforrás-keresztmetszetek, gyenge lekérdezésterveket, és így tovább diagnosztizálásához egy részét. Ez a témakör információkat kapcsolatos gyakori problémák észlelése dinamikus felügyeleti nézetek használatával.

SQL-adatbázis részben három kategóriáinak dinamikus felügyeleti nézetekkel támogatja:

* Adatbázissal kapcsolatos dinamikus felügyeleti nézetekkel.
* Végrehajtási kapcsolatos dinamikus felügyeleti nézetekkel.
* Tranzakció-hoz kapcsolódó dinamikus felügyeleti nézetek.

A dinamikus felügyeleti nézetekkel részletes információkért lásd: [dinamikus felügyeleti nézetek és függvények (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) az SQL Server Online könyvekben.

## <a name="permissions"></a>Engedélyek
Az SQL-adatbázis, a dinamikus felügyeleti nézetben lekérdezése szükséges **adatbázis állapotának megtekintése** engedélyek. A **adatbázis állapotának megtekintése** engedélyt az aktuális adatbázisban lévő objektumok információt ad vissza.
Megadását a **adatbázis állapotának megtekintése** engedéllyel a megadott adatbázis-felhasználó, futtassa a következő lekérdezést:

```GRANT VIEW DATABASE STATE TO database_user; ```

A helyszíni SQL Server egy példányát, a dinamikus felügyeleti nézetekkel kiszolgáló állapota adatokat ad vissza. Az SQL-adatbázis akkor csak az aktuális logikai adatbázis vonatkozó információkat adja vissza.

## <a name="calculating-database-size"></a>Adatbázisméret kiszámítása
A következő lekérdezés az adatbázis (megabájtban) a méretét adja vissza:

```
-- Calculates the size of the database.
SELECT SUM(reserved_page_count)*8.0/1024
FROM sys.dm_db_partition_stats;
GO
```

A következő lekérdezés az adatbázis méretét (megabájtban) egyéni objektumokat adja vissza:

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Kapcsolatok figyelése
Használhatja a [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) nézet az adott Azure SQL adatbázis-kiszolgálót és mindegyik kapcsolat részleteinek fennálló kapcsolatok vonatkozó információk lekéréséhez. Emellett a [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) nézet akkor hasznos, ha az összes aktív felhasználói kapcsolatok és belső feladatok adatainak lekérése.
Az alábbi lekérdezés lekéri az aktuális kapcsolat információkat:

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
> Végrehajtásakor a **sys.dm_exec_requests** és **sys.dm_exec_sessions nézetek**, ha **adatbázis állapotának megtekintése** engedéllyel az adatbázishoz, megjelenik az összes végrehajtás alatt álló munkamenetek az adatbázisban. Ellenkező esetben tekintse meg az aktuális munkamenet.
> 
> 

## <a name="monitoring-query-performance"></a>Lekérdezés teljesítmény figyelése
Lassú vagy hosszú ideig futó lekérdezések jelentős rendszererőforrásokat is felhasználhatnak. Ez a szakasz bemutatja, hogyan kell néhány gyakori lekérdezési teljesítmény problémák észlelése dinamikus felügyeleti nézetek használatával. A hibaelhárításhoz, egy régebbi, de továbbra is hasznos a hivatkozás a [teljesítményproblémák elhárítása az SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) a Microsoft TechNet cikket.

### <a name="finding-top-n-queries"></a>Legfontosabb N lekérdezések keresése
Az alábbi példa átlagos CPU-idő szerinti sorrendben első öt lekérdezések információt ad vissza. Ebben a példában a lekérdezés kivonat szerint a lekérdezések összesíti az, hogy logikailag egyenértékű lekérdezések saját összesítő hálózatierőforrás-fogyasztás szerint vannak csoportosítva.

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
Lassú vagy hosszan futó lekérdezések járulnak hozzá sok erőforrást használ, és letiltott lekérdezések következménye lehet. A blokkolás okát gyenge alkalmazás tervét, hibás lekérdezésterveket, hasznos indexek, és így tovább hiánya lehet. A sys.dm_tran_locks nézet segítségével az Azure SQL-adatbázisban az aktuális zárolási tevékenység adatainak beolvasása. Például kódját, lásd: [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) az SQL Server Online könyvekben.

### <a name="monitoring-query-plans"></a>Figyelési lekérdezésterveket
Egy hatékony lekérdezésterv is növelheti CPU-használat. Az alábbi példában a [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) nézet segítségével meghatározhatja, melyik lekérdezés használja a legtöbb összegző CPU-t.

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

## <a name="see-also"></a>Lásd még:
[SQL-adatbázis bemutatása](sql-database-technical-overview.md)


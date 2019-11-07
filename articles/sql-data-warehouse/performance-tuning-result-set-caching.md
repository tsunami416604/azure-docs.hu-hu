---
title: Teljesítmény-Finomhangolás az eredményhalmaz gyorsítótárazásával
description: Az eredményhalmaz gyorsítótárazási funkcióinak áttekintése Azure SQL Data Warehouse
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: seo-lt-2019
ms.openlocfilehash: 461320b9c3ed48176fb60fe695704c582edcd552
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692954"
---
# <a name="performance-tuning-with-result-set-caching"></a>Teljesítmény-Finomhangolás az eredményhalmaz gyorsítótárazásával  
Ha engedélyezve van az eredményhalmaz gyorsítótárazása, Azure SQL Data Warehouse automatikusan gyorsítótárazza a lekérdezés eredményeit a felhasználói adatbázisban ismétlődő használatra.  Ez lehetővé teszi, hogy a későbbi lekérdezés-végrehajtások közvetlenül a megőrzött gyorsítótárból kapjanak eredményeket, így az újraszámításra nincs szükség.   Az eredményhalmaz gyorsítótárazása javítja a lekérdezési teljesítményt, és csökkenti a számítási erőforrások használatát.  Emellett a gyorsítótárazott eredményeket használó lekérdezések nem használnak párhuzamossági tárolóhelyeket, így nem számítanak bele a meglévő egyidejűségi korlátokba. A biztonság érdekében a felhasználók csak akkor érhetik el a gyorsítótárazott eredményeket, ha ugyanazokat az adatelérési engedélyeket használják, mint a gyorsítótárazott eredményeket létrehozó felhasználók.  

## <a name="key-commands"></a>Legfontosabb parancsok
[Eredményhalmaz gyorsítótárazásának be-vagy kikapcsolása felhasználói adatbázishoz](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[Eredményhalmaz gyorsítótárazásának be-vagy kikapcsolása munkamenethez](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[A gyorsítótárazott eredményhalmaz méretének ellenőrzéséhez](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[A gyorsítótár kiürítése](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Nem gyorsítótárazott  

Ha az eredményhalmaz gyorsítótárazása be van kapcsolva egy adatbázishoz, a rendszer az összes lekérdezés esetében gyorsítótárazza az eredményeket, amíg a gyorsítótár megtelt, kivéve a következő lekérdezéseket:
- Nem determinisztikus függvények, például DateTime. Now () használatával történő lekérdezések
- Felhasználó által definiált függvényeket használó lekérdezések
- A sor szintű biztonsági vagy az oszlop szintű biztonsággal rendelkező táblákat használó lekérdezések
- Az 64 kb nagyobb sorszámú adatvisszaadó lekérdezések

> [!IMPORTANT]
> Az eredményhalmaz gyorsítótárának létrehozásához és az adatok a gyorsítótárból való lekéréséhez szükséges műveletek egy adatraktár-példány vezérlési csomópontján történnek. Ha a eredményhalmaz gyorsítótárazása be van kapcsolva, akkor a nagyméretű eredményhalmaz (például > 1 millió sor) lekérdezése nagy CPU-használatot okozhat a vezérlési csomóponton, és lelassíthatja a példányon a lekérdezésre adott teljes választ.  Ezeket a lekérdezéseket általában az adatfelderítési vagy ETL-műveletek során használják. A vezérlő csomópontjának kihangsúlyozása és a teljesítménnyel kapcsolatos probléma elkerülése érdekében a felhasználóknak ki kell kapcsolniuk az eredményhalmaz gyorsítótárazását az adatbázison az ilyen típusú lekérdezések futtatása előtt.  

Futtassa ezt a lekérdezést az eredményhalmaz gyorsítótárazási műveletei által a lekérdezéshez megadott időtartamra:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command 
FROM sys.dm_pdw_request_steps 
WHERE request_id  = <'request_id'>; 
```

Az alábbi példa egy, az eredményhalmaz gyorsítótárazásával letiltott lekérdezés kimenetét jeleníti meg.

![Lekérdezés – Steps-with-RSC – letiltva](media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Az alábbi példa egy, az eredményhalmaz gyorsítótárazásával végrehajtott lekérdezés kimenetét jeleníti meg.

![Query-Steps-with-RSC-enabled](media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>A gyorsítótárazott eredmények használatakor

A gyorsítótárazott eredményhalmaz egy lekérdezés esetében újra használatos, ha az alábbi követelmények mindegyike teljesül:
- A lekérdezést futtató felhasználónak hozzáférése van a lekérdezésben hivatkozott összes táblához.
- Az új lekérdezés és az eredményül kapott gyorsítótárat létrehozó korábbi lekérdezés között pontos egyezés van.
- Nincs olyan adatvagy séma-változás abban a táblázatban, amelyben a gyorsítótárazott eredményhalmaz létrejött.

Futtassa ezt a parancsot annak ellenőrzéséhez, hogy egy lekérdezés végrehajtása sikeres volt-e a találati gyorsítótárban. Ha van gyorsítótár-találat, a result_cache_hit 1 értéket ad vissza.

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Gyorsítótárazott eredmények kezelése 

Az eredményhalmaz gyorsítótárának maximális mérete 1 TB/adatbázis.  A gyorsítótárazott eredményeket a rendszer automatikusan érvényteleníti, amikor az alapul szolgáló lekérdezési adat megváltoznak.  

A gyorsítótár kiürítését a Azure SQL Data Warehouse automatikusan a következő ütemterv szerint kezeli: 
- 48 óránként, ha az eredményhalmaz nem lett felhasználva vagy érvénytelenítve lett. 
- Ha az eredményhalmaz gyorsítótára a maximális méretet közelíti meg.

A felhasználók a teljes eredményhalmaz gyorsítótárát manuálisan is kiürítheti a következő lehetőségek egyikének használatával: 
- Az eredményhalmaz gyorsítótár-funkciójának kikapcsolása az adatbázishoz 
- DBCC-DROPRESULTSETCACHE futtatása az adatbázishoz való csatlakozás közben

Az adatbázis szüneteltetése nem üres a gyorsítótárazott eredményhalmaz számára.  

## <a name="next-steps"></a>További lépések
További fejlesztési tippek: [SQL Data Warehouse fejlesztői áttekintés](sql-data-warehouse-overview-develop.md). 

---
title: Teljesítmény-finomhangolás eredményhalmaz gyorsítótárazásával
description: Az eredményhalmaz gyorsítótárazási funkcióinak áttekintése a szinapszis SQL-készlethez az Azure szinapszis Analyticsben
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: eadbe13269ce1259b4560af117f5b15b3b294151
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81730604"
---
# <a name="performance-tuning-with-result-set-caching"></a>Teljesítmény-finomhangolás eredményhalmaz gyorsítótárazásával

Ha az eredményhalmaz gyorsítótárazása engedélyezve van, az SQL Analytics automatikusan gyorsítótárazza a lekérdezés eredményeit a felhasználói adatbázisban ismétlődő használatra.  Ez lehetővé teszi, hogy a későbbi lekérdezés-végrehajtások közvetlenül a megőrzött gyorsítótárból kapjanak eredményeket, így az újraszámításra nincs szükség.   Az eredményhalmaz gyorsítótárazása javítja a lekérdezési teljesítményt, és csökkenti a számítási erőforrások használatát.  Emellett a gyorsítótárazott eredményeket használó lekérdezések nem használnak párhuzamossági tárolóhelyeket, így nem számítanak bele a meglévő egyidejűségi korlátokba. A biztonság érdekében a felhasználók csak akkor érhetik el a gyorsítótárazott eredményeket, ha ugyanazokat az adatelérési engedélyeket használják, mint a gyorsítótárazott eredményeket létrehozó felhasználók.  

## <a name="key-commands"></a>Legfontosabb parancsok

[Eredményhalmaz gyorsítótárazásának be-vagy kikapcsolása felhasználói adatbázishoz](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Eredményhalmaz gyorsítótárazásának be-vagy kikapcsolása felhasználói adatbázishoz](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Eredményhalmaz gyorsítótárazásának be-vagy kikapcsolása munkamenethez](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[A gyorsítótárazott eredményhalmaz méretének ellenőrzéséhez](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

[A gyorsítótár kiürítése](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Nem gyorsítótárazott  

Ha az eredményhalmaz gyorsítótárazása be van kapcsolva egy adatbázishoz, a rendszer az összes lekérdezés esetében gyorsítótárazza az eredményeket, amíg a gyorsítótár megtelt, kivéve a következő lekérdezéseket:

- Nem determinisztikus függvények, például DateTime. Now () használatával történő lekérdezések
- Felhasználó által definiált függvényeket használó lekérdezések
- A sor szintű biztonsági vagy az oszlop szintű biztonsággal rendelkező táblákat használó lekérdezések
- Az 64 kb nagyobb sorszámú adatvisszaadó lekérdezések
- Nagyméretű adatmennyiséget (>10 GB-ot) visszaadó lekérdezések 

> [!IMPORTANT]
> Az eredményhalmaz gyorsítótárának létrehozásához és az adatok a gyorsítótárból való lekéréséhez szükséges műveletek a szinapszis SQL Pool-példány vezérlés csomópontján történnek.
> Ha a eredményhalmaz gyorsítótárazása be van kapcsolva, a nagyméretű eredményhalmaz (például >1GB) visszaadó lekérdezések futtatása nagy sávszélességet eredményezhet a vezérlő csomóponton, és lelassítja a példányon a lekérdezésre adott teljes választ.  Ezeket a lekérdezéseket általában az adatfelderítési vagy ETL-műveletek során használják. A vezérlő csomópontjának kihangsúlyozása és a teljesítménnyel kapcsolatos probléma elkerülése érdekében a felhasználóknak ki kell kapcsolniuk az eredményhalmaz gyorsítótárazását az adatbázison az ilyen típusú lekérdezések futtatása előtt.  

Futtassa ezt a lekérdezést az eredményhalmaz gyorsítótárazási műveletei által a lekérdezéshez megadott időtartamra:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command
FROM sys.dm_pdw_request_steps
WHERE request_id  = <'request_id'>;
```

Az alábbi példa egy, az eredményhalmaz gyorsítótárazásával letiltott lekérdezés kimenetét jeleníti meg.

![Lekérdezés – Steps-with-RSC – letiltva](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Az alábbi példa egy, az eredményhalmaz gyorsítótárazásával végrehajtott lekérdezés kimenetét jeleníti meg.

![Query-Steps-with-RSC-enabled](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>A gyorsítótárazott eredmények használatakor

A gyorsítótárazott eredményhalmaz egy lekérdezés esetében újra használatos, ha az alábbi követelmények mindegyike teljesül:

- A lekérdezést futtató felhasználónak hozzáférése van a lekérdezésben hivatkozott összes táblához.
- Az új lekérdezés és az eredményül kapott gyorsítótárat létrehozó korábbi lekérdezés között pontos egyezés van.
- Nincs olyan adatvagy séma-változás abban a táblázatban, amelyben a gyorsítótárazott eredményhalmaz létrejött.

Futtassa ezt a parancsot annak ellenőrzéséhez, hogy egy lekérdezés végrehajtása sikeres volt-e a találati gyorsítótárban. A result_cache_hit oszlop 1 értéket ad vissza a gyorsítótár találata esetén, 0 a gyorsítótár-Kihagyás esetén, valamint a negatív értékeket, amelyek miatt a rendszer nem használta az eredményhalmaz gyorsítótárazását. A részletekért lásd: [sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Gyorsítótárazott eredmények kezelése

Az eredményhalmaz gyorsítótárának maximális mérete 1 TB/adatbázis.  A gyorsítótárazott eredményeket a rendszer automatikusan érvényteleníti, amikor az alapul szolgáló lekérdezési adat megváltoznak.  

A gyorsítótár kiürítését az SQL Analytics automatikusan kezeli az alábbi ütemterv szerint:

- 48 óránként, ha az eredményhalmaz nem lett felhasználva vagy érvénytelenítve lett.
- Ha az eredményhalmaz gyorsítótára a maximális méretet közelíti meg.

A felhasználók a teljes eredményhalmaz gyorsítótárát manuálisan is kiürítheti a következő lehetőségek egyikének használatával:

- Az eredményhalmaz gyorsítótár-funkciójának kikapcsolása az adatbázishoz
- DBCC-DROPRESULTSETCACHE futtatása az adatbázishoz való csatlakozás közben

Az adatbázis szüneteltetése nem üres a gyorsítótárazott eredményhalmaz számára.  

## <a name="next-steps"></a>További lépések

További fejlesztési tippek: a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md).

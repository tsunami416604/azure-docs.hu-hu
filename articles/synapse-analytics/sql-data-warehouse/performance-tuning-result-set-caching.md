---
title: Teljesítmény-finomhangolás eredményhalmaz gyorsítótárazásával
description: Eredménykészlet-gyorsítótárazási funkció áttekintése a Synapse SQL-készlethez az Azure Synapse Analytics szolgáltatásban
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
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730604"
---
# <a name="performance-tuning-with-result-set-caching"></a>Teljesítmény-finomhangolás eredményhalmaz gyorsítótárazásával

Ha az eredményhalmaz-gyorsítótárazás engedélyezve van, az SQL Analytics automatikusan gyorsítótárazza a lekérdezési eredményeket a felhasználói adatbázisban ismétlődő használat érdekében.  Ez lehetővé teszi, hogy a lekérdezés-végrehajtások közvetlenül a megőrzött gyorsítótárból kapjanak eredményeket, így nincs szükség újraszámításra.   Az eredményhalmaz-gyorsítótárazás javítja a lekérdezési teljesítményt és csökkenti a számítási erőforrás-használatot.  Emellett a gyorsítótárazott eredményhalmazt használó lekérdezések nem használnak egyidejűségi tárolóhelyeket, és így nem számítanak bele a meglévő egyidejűségi korlátokba. A biztonság érdekében a felhasználók csak akkor férhetnek hozzá a gyorsítótárazott eredményekhez, ha ugyanolyan adatelérési engedélyekkel rendelkeznek, mint a gyorsítótárazott eredményeket létrehozó felhasználók.  

## <a name="key-commands"></a>Billentyűparancsok

[Felhasználói adatbázis eredménykészletének be- és kikapcsolása](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Felhasználói adatbázis eredménykészletének be- és kikapcsolása](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Be-/KI eredménykészlet gyorsítótárazása egy munkamenethez](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[A gyorsítótárazott eredményhalmaz méretének ellenőrzése](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

[A gyorsítótár karbantartása](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Mi nincs gyorsítótárazva  

Ha az eredményhalmaz-gyorsítótárazás be van kapcsolva egy adatbázisban, a rendszer az összes lekérdezéshez gyorsítótárba helyezi az eredményeket, amíg a gyorsítótár meg nem telik, kivéve ezeket a lekérdezéseket:

- Nem determinisztikus függvényeket használó lekérdezések, például DateTime.Now()
- Felhasználó által definiált függvényeket használó lekérdezések
- Olyan táblákat használó lekérdezések, amelyeknél a sorszintű biztonság vagy az oszlopszintű biztonság engedélyezve van
- 64 KB-nál nagyobb sorméretű adatokat visszaválaszoló lekérdezések
- Nagy méretű (>10 GB-os) adatokat visszaadó lekérdezések 

> [!IMPORTANT]
> Az eredményhalmaz gyorsítótárának létrehozásához és a gyorsítótárból történő adatok lekéréséhez a synapszi SQL-készletpéldány vezérlőcsomópontján történik.
> Ha az eredményhalmaz-gyorsítótárazás be van kapcsolva, a nagy eredményhalmazt visszaadó lekérdezések futtatása (például >1 GB) nagy szabályozást okozhat a vezérlőcsomóponton, és lelassíthatja a példány általános lekérdezési válaszát.  Ezeket a lekérdezéseket gyakran használják az adatok feltárása vagy etl műveletek során. A vezérlőcsomópont hangsúlyozásának és a teljesítményproblémák nak a elkerülése érdekében a felhasználóknak ki kell kapcsolniuk az eredménykészlet-gyorsítótárazást az adatbázisban, mielőtt futtatnák az ilyen típusú lekérdezéseket.  

Futtassa ezt a lekérdezést a lekérdezés eredményhalmaz-gyorsítótárazási műveletei által megtett időre:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command
FROM sys.dm_pdw_request_steps
WHERE request_id  = <'request_id'>;
```

Íme egy példa kimenet egy lekérdezés tletiltva eredménykészlet gyorsítótárazás.

![Lekérdezés-lépések-az-rsc-letiltva](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Íme egy példa kimenet egy olyan lekérdezéshez, amelyen az eredménykészlet-gyorsítótárazás engedélyezve van.

![Lekérdezés-lépések-az-rsc-kompatibilis](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>Gyorsítótárazott eredmények használata esetén

A gyorsítótárazott eredményhalmazt újra felhasználja a rendszer egy lekérdezéshez, ha az alábbi követelmények mindegyike teljesül:

- A lekérdezést futtató felhasználó hozzáfér a lekérdezésben hivatkozott összes táblához.
- Pontos egyezés van az új lekérdezés és az eredményhalmaz gyorsítótárát létrehozó előző lekérdezés között.
- Nincs adat- vagy sémaváltozás azokban a táblákban, amelyekből a gyorsítótárazott eredményhalmaz létrejött.

Futtassa ezt a parancsot annak ellenőrzéséhez, hogy a lekérdezés végrehajtása eredménygyorsítótár-találattal vagy -tévesztéssel történt-e. A result_cache_hit oszlop 1-et ad vissza a gyorsítótár letöréséhez, 0-t a gyorsítótár-tévesztéshez, és negatív értékeket, amelyek miatt az eredménykészlet-gyorsítótárazás nem volt használva. Ellenőrizze [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) a részleteket.

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Gyorsítótárazott eredmények kezelése

Az eredményhalmaz gyorsítótárának maximális mérete adatbázisonként 1 TB.  A gyorsítótárazott eredmények automatikusan érvénytelenítik, amikor az alapul szolgáló lekérdezési adatok megváltoznak.  

A gyorsítótár-kilakoltatást az SQL Analytics automatikusan kezeli az alábbi ütemezés szerint:

- 48 óránként, ha az eredményhalmazt nem használták vagy érvénytelenítették.
- Amikor az eredményhalmaz gyorsítótára megközelíti a maximális méretet.

A felhasználók manuálisan kiüríthetik a teljes eredményhalmaz gyorsítótárát az alábbi lehetőségek egyikével:

- Az adatbázis eredményhalmaz-gyorsítótár-szolgáltatásának kikapcsolása
- DbCC DROPRESULTSETCACHE futtatása az adatbázishoz való csatlakozás közben

Az adatbázis szüneteltetése nem üríti ki a gyorsítótárazott eredményhalmazt.  

## <a name="next-steps"></a>További lépések

További fejlesztési tippeket a [fejlesztés áttekintése című témakörben talál.](sql-data-warehouse-overview-develop.md)

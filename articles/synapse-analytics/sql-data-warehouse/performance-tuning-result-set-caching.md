---
title: Teljesítmény-finomhangolás eredményhalmaz gyorsítótárazásával
description: Az Sql Analytics eredménykészlet-gyorsítótárazási funkciójának áttekintése az Azure Synapse Analytics szolgáltatásában
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
ms.openlocfilehash: da476dc14949ebab1a054a9624d91acb25b9f2b4
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474481"
---
# <a name="performance-tuning-with-result-set-caching"></a>Teljesítmény-finomhangolás eredményhalmaz gyorsítótárazásával  
Ha az eredményhalmaz-gyorsítótárazás engedélyezve van, az SQL Analytics automatikusan gyorsítótárazza a lekérdezési eredményeket a felhasználói adatbázisban ismétlődő használat érdekében.  Ez lehetővé teszi, hogy a lekérdezés-végrehajtások közvetlenül a megőrzött gyorsítótárból kapjanak eredményeket, így nincs szükség újraszámításra.   Az eredményhalmaz-gyorsítótárazás javítja a lekérdezési teljesítményt és csökkenti a számítási erőforrás-használatot.  Emellett a gyorsítótárazott eredményhalmazt használó lekérdezések nem használnak egyidejűségi tárolóhelyeket, és így nem számítanak bele a meglévő egyidejűségi korlátokba. A biztonság érdekében a felhasználók csak akkor férhetnek hozzá a gyorsítótárazott eredményekhez, ha ugyanolyan adatelérési engedélyekkel rendelkeznek, mint a gyorsítótárazott eredményeket létrehozó felhasználók.  

## <a name="key-commands"></a>Billentyűparancsok
[Felhasználói adatbázis eredménykészletének be- és kikapcsolása](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[Be-/KI eredménykészlet gyorsítótárazása egy munkamenethez](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[A gyorsítótárazott eredményhalmaz méretének ellenőrzése](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[A gyorsítótár karbantartása](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Mi nincs gyorsítótárazva  

Ha az eredményhalmaz-gyorsítótárazás be van kapcsolva egy adatbázisban, a rendszer az összes lekérdezéshez gyorsítótárba helyezi az eredményeket, amíg a gyorsítótár meg nem telik, kivéve ezeket a lekérdezéseket:
- Nem determinisztikus függvényeket használó lekérdezések, például DateTime.Now()
- Felhasználó által definiált függvényeket használó lekérdezések
- Olyan táblákat használó lekérdezések, amelyeknél a sorszintű biztonság vagy az oszlopszintű biztonság engedélyezve van
- 64 KB-nál nagyobb sorméretű adatokat visszaválaszoló lekérdezések

> [!IMPORTANT]
> Az eredményhalmaz-gyorsítótár létrehozásához és a gyorsítótárból történő adatok lekéréséhez az SQL Analytics-példány vezérlőcsomópontján történik.
> Ha az eredményhalmaz-gyorsítótárazás be van kapcsolva, a nagy eredményhalmazt (például >1 millió sort) visszaadó lekérdezések futtatása magas PROCESSZOR-használatot okozhat a vezérlőcsomóponton, és lelassíthatja a példány általános lekérdezési válaszát.  Ezeket a lekérdezéseket gyakran használják az adatok feltárása vagy etl műveletek során. A vezérlőcsomópont hangsúlyozásának és a teljesítményproblémák nak a elkerülése érdekében a felhasználóknak ki kell kapcsolniuk az eredménykészlet-gyorsítótárazást az adatbázisban, mielőtt futtatnák az ilyen típusú lekérdezéseket.  

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

Futtassa ezt a parancsot annak ellenőrzéséhez, hogy a lekérdezés végrehajtása eredménygyorsítótár-találattal vagy -tévesztéssel történt-e. A result_set_cache oszlop 1-et ad vissza a gyorsítótár letöréséhez, 0-t a gyorsítótár-tévesztéshez, és negatív értékeket, amelyek miatt az eredménykészlet-gyorsítótárazás nem volt használva. Ellenőrizze [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7) a részleteket.

```sql
SELECT request_id, command, result_set_cache FROM sys.dm_pdw_exec_requests
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

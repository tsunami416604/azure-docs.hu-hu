---
title: Az Azure SQL Data Warehouse kibocsátási megjegyzések 2018 április |} A Microsoft Docs
description: Kibocsátási megjegyzések az Azure SQL Data warehouse-hoz.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 77f0444915a845a409f15d4533fe9593730e9bbb
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282634"
---
# <a name="whats-new-in-azure-sql-data-warehouse-april-2018"></a>Mi az új Azure SQL Data warehouse? 2018. április
Az Azure SQL Data Warehouse fejlesztései folyamatosan kap. Ez a cikk ismerteti az új szolgáltatásokat és változásokat tartalmazza, amelyek a 2018 április.

## <a name="ability-to-truncate-a-partition-before-a-switch"></a>Lehetővé teszi, hogy csonkolja a partíció egy kapcsoló előtt
Ügyfeleink gyakran használhatók a partícióváltás mintaként adatok betöltése az egyik táblából egy másikba keresztül a tábla metaadatainak módosításával a `ALTER TABLE SourceTable SWITCH PARTITION X TO TargetTable PARTITION X` szintaxist. Az SQL Data Warehouse nem támogatja a partíció közötti váltás, ha a célpartíció adatokat tartalmaz. Ha a célpartíció már tartalmaz adatokat, az ügyfél a célpartíción truncate, és végezze el a kapcsolót kell.

Az SQL Data Warehouse már támogatják ezt a műveletet egy T-SQL-utasítás.

```sql
ALTER TABLE SourceTable 
    SWITCH PARTITION X TO TargetTable PARTITION X
    WITH (TRUNCATE_TARGET_PARTITION = ON)
```
További információkért lásd: a [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) cikk.

## <a name="improved-query-compilation-performance"></a>Továbbfejlesztett lekérdezési fordítási teljesítmény
Az SQL Data Warehouse bevezetett változások a lekérdezés fordítása lépés, elosztott lekérdezések javítása érdekében. Ezek a változások javítása fordítási gyorsaság akár **10 x** csökkenti a teljes lekérdezés végrehajtási modulok. Az adattárházakkal, amelyeknél az objektumok (táblák, függvények, nézetek, eljárások) nagy számú több különül el ezeket a módosításokat.

## <a name="dbcc-commands-do-not-consume-concurrency-slots-behavior-change"></a>DBCC-parancsok nem igényelnek egyidejű helyet foglalnak le (viselkedésének módosítása)
Az SQL Data Warehouse a T-SQL egy részét támogatja [DBCC-parancsok](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) például [DBCC DROPCLEANBUFFERS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropcleanbuffers-transact-sql). Korábban ezek a parancsok használata lenne egy [egyidejűségi tárolóhely](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#concurrency-slots) csökkenti a felhasználói terhelések/lekérdezések száma, amelyek sikerült végrehajtani. A `DBCC` parancsok futtatása most, hogy egy erőforrás-tárhely teljes lekérdezés végrehajtása a teljesítmény fokozása nincs szükség helyi várólista.

## <a name="updated-error-message-for-excessive-literals-behavior-change"></a>Frissített hibaüzenet túlzott literálok (viselkedésének módosítása)
Korábban, az SQL Data Warehouse tartalmazhat egy *hozzávetőleges* száma, ha a lekérdezés túl sok literálok tartalmazott.
```
Msg 100086
Cannot have more than 20,000 literals in the query. The query contains [n] literals.
```

A hibaüzenet frissítve lett csak jelzi, hogy elérte a maximális számát.
```
Msg 100086
The number of literals in the query is beyond the limit. Please rewrite your query.
```

További információkért lásd: a [lekérdezések](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits#queries) szakaszában a [Kapacitáskorlátait](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits) cikk további részleteket a maximális korlátig.

## <a name="removed-the-syspdwdatabasemappings-view-behavior-change"></a>Eltávolítja a SYS. PDW_DATABASE_MAPPINGS megtekintése (viselkedésének módosítása)
Ez `sys.pdw_database_mappings` nézet nincs használatban, az SQL Data Warehouse. Korábban a nézet egy SELECT lenne ne adjon vissza eredményt. A nézet el lett távolítva. 

## <a name="next-steps"></a>További lépések
Most, hogy jobban megismerte az SQL Data warehouse, ismerje meg, hogyan lehet gyorsan [SQL Data Warehouse létrehozása] [SQL Data Warehouse létrehozása] és [mintaadatokat tölthet be] [mintaadatokat tölthet be]. Ha most ismerkedik az Azure-ba, hasznosnak találhatja az [Azure szószedet] [Azure szószedetét], akkor új fogalmakkal ismerkedik. Vagy tekintsen meg néhányat a többi SQL Data Warehouse-erőforrás közül.  

* [Ügyfelek sikertörténetei]
* [Blogok]
* [Funkciókérések]
* [Videók]
* [Az ügyféltanácsadói csapat blogjai]
* [Stack Overflow-fórum]
* [Twitter]


[Blogok]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Az ügyféltanácsadói csapat blogjai]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Ügyfelek sikertörténetei]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Funkciókérések]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow-fórum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videók]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
---
title: Az Azure SQL Data Warehouse-kibocsátási megjegyzéseket. április 2018 |} Microsoft Docs
description: Kibocsátási megjegyzések az Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 05/28/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 49ffc3ddfd586964ae8a9688aeb48fffdd327b45
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738934"
---
# <a name="whats-new-in-azure-sql-data-warehouse-april-2018"></a>Újdonságok az Azure SQL Data Warehouse (április 2018)?
Az SQL Data Warehouse fejlesztései folyamatosan kap. Ez a cikk ismerteti az új szolgáltatásokat és módosításokat vezettek be. április 2018.

## <a name="features"></a>Szolgáltatások

### <a name="ability-to-truncate-a-partition-before-a-switch"></a>A kapcsoló előtt partíció csonkolni lehetősége
Az ügyfelek gyakran segítségével mintaként partícióváltás adatok betöltése az egyik tábla másik keresztül a tábla metaadatainak módosítása a `ALTER TABLE SourceTable SWITCH PARTITION X TO TargetTable PARTITION X` szintaxist. Az SQL Data Warehouse nem támogatja a partíció vált, ha a célpartíció tartalmaz adatokat. A célpartíciót már tartalmaz adatokat, ha az ügyfél a célpartíció truncate, és végezze el a kapcsolót kell.

Az SQL Data Warehouse mostantól támogatja a művelet egy T-SQL-utasítás.

```sql
ALTER TABLE SourceTable 
    SWITCH PARTITION X TO TargetTable PARTITION X
    WITH (TRUNCATE_TARGET_PARTITION = ON)
```
További információkért lásd: a [az ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) cikk.

### <a name="improved-query-compilation-performance"></a>Továbbfejlesztett fordítási teljesítmény-küszöbérték
Az SQL Data Warehouse rendszerben jelent meg a lekérdezés összeállítása lépés elosztott lekérdezések javító módosításokat készlete. Ezek a változások javítása lekérdezés fordítási alkalommal akár **10 x** csökkenti a teljes lekérdezés végrehajtási futtatókörnyezetek. A nagy számú objektumok (táblák, Funkciók, nézetek, eljárások) adatraktárak több különül el ezeket a módosításokat.

## <a name="behavior-changes"></a>Viselkedésváltozások

### <a name="dbcc-commands-do-not-consume-concurrency-slots"></a>A DBCC utasításokban nem igényelnek párhuzamossági tárhelyek
Az SQL Data Warehouse támogatja a T-SQL egy részét [a DBCC utasításokban](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) például [DBCC DROPCLEANBUFFERS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropcleanbuffers-transact-sql). Korábban ezek a parancsok volna használni egy [egyidejűségi tárolóhely](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#concurrency-slots) felhasználói terhelések/lekérdezések sikerült végrehajtani a számának csökkentését. A `DBCC` parancs futtatása most a helyi várólista, amelyek nem igényelnek erőforrás tárhely lekérdezés végrehajtási teljesítményének javítása.

### <a name="updated-error-message-for-excessive-literals"></a>Túl sok literálok frissített hibaüzenet
Korábban, az SQL Data Warehouse beletartozik egy *hozzávetőleges* száma, amikor a lekérdezés túl sok literálok tartalmazott.
```
Msg 100086
Cannot have more than 20,000 literals in the query. The query contains [n] literals.
```

A hibaüzenet csak jelzi, hogy elérte a maximális frissítve lett.
```
Msg 100086
The number of literals in the query is beyond the limit. Please rewrite your query.
```

További információkért lásd: a [lekérdezések](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits#queries) szakasza a [Kapacitáskorlátait](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits) cikk további részleteket a határok.

### <a name="removed-the-syspdwdatabasemappings-view"></a>Eltávolítja a SYS. PDW_DATABASE_MAPPINGS megtekintése
Ez `sys.pdw_database_mappings` nézet nincs használatban, az SQL Data Warehouse. Korábban a nézet egy SELECT alakítanák vissza nem járt eredménnyel. A nézet el lett távolítva. 
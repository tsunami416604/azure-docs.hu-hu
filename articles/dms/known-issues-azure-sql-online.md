---
title: A cikk kapcsolatos ismert problémák és a migrálás korlátozások az online migrálást az Azure SQL Database |} A Microsoft Docs
description: Ismerje meg az online migrálást az Azure SQL Database ismert problémák és a migrálás korlátozások.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/09/2019
ms.openlocfilehash: 1a8f46c74693b00fd8e30b1e1a78d90111dea08b
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59520745"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-db"></a>Ismert problémák és a migrálás korlátozások az online migrálást az Azure SQL DB

Ismert problémák és korlátozások online migrálást SQL Serverről az Azure SQL Database társított alább ismertetjük.

> [!IMPORTANT]
> Online migrálás esetén az SQL Server az Azure SQL Database, az SQL_variant adattípusú áttelepítése nem támogatott.

### <a name="migration-of-temporal-tables-not-supported"></a>Áttelepítés nem támogatott a historikus táblák

**Tünet**

Ha a forrásadatbázis egy vagy több historikus táblát tartalmaz, az adatbázis-migrálást az "adatok teljes betöltése" művelet során nem sikerül, és a következő üzenet jelenhet meg:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![A historikus tábla hibák példa](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Megkerülő megoldás**

1. Keresse meg az időbeli verziózású táblák a forrás-séma használatával az alábbi lekérdezést.
     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```
2. Ezek a táblák a kizárása a **migrálási beállítások konfigurálása** panel, amelyen megadhatja a táblák az áttelepítéshez.

3. Futtassa újra a migrálási tevékenységet.

**Erőforrások**

További információkért tekintse meg a cikket [időbeli Verziózású táblák](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).
 
### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>A táblák áttelepítési tartalmaz egy vagy több oszlop a hierarchyid adattípusú

**Tünet**

Láthatja, hogy egy SQL-kivétel javasolásával "ntext nem kompatibilis a hierarchyid" a "adatok teljes betöltése" művelet során:
     
![hierarchyid hibák példa](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Megkerülő megoldás**

1. A felhasználói táblák használatával az alábbi lekérdezést a hierarchyid adattípusú oszlopokat tartalmazó találja.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ``` 

2. Ezek a táblák a kizárása a **migrálási beállítások konfigurálása** panel, amelyen megadhatja a táblák az áttelepítéshez.

3. Futtassa újra a migrálási tevékenységet.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Az "adatok teljes betöltése" vagy "adatok növekményes szinkronizálása" során a sémában aktív eseményindítókat használó különböző integritás szabálysértések áttelepítési hibák

**Megkerülő megoldás**

1. Keresse meg az eseményindítókat, amelyek a jelenleg aktív, a forrásadatbázis, használja az alábbi lekérdezést:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Tiltsa le az eseményindítók a forrásadatbázison, az a cikkben ismertetett lépéseket követve [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

3. Futtassa újra a migrálási tevékenységet.

### <a name="support-for-lob-data-types"></a>A LOB adattípus támogatása

**Tünet**

Ha nagyméretű objektum (LOB) oszlop hossza 32 KB-nál nagyobb méretű, adatok előfordulhat, hogy első csonkolva, a cél. Az alábbi lekérdezés használatával LOB oszlop hossza ellenőrizheti: 

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Megkerülő megoldás**

Ha egy LOB oszlop, amely 32 KB-nál nagyobb méretű, lépjen kapcsolatba a mérnöki csapathoz, [kérje meg az Azure adatbázis-Migrálási](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Időbélyegző-oszlopok problémái

**Tünet**

A DMS nem áttelepíteni a forrás timestamp értéket; Ehelyett a DMS a céloldali tábla hoz létre egy új időbélyegző-érték.

**Megkerülő megoldás**

Ha DMS áttelepíteni a forrástábla tárolt időbélyeg pontos érték van szüksége, forduljon a mérnöki csapathoz, [kérje meg az Azure adatbázis-Migrálási](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Adatok áttelepítési hibák ne adjon meg további részleteket az adatbázis részletes állapota panel.

**Tünet**

Ha áttelepítési hiba az adatbázisok részletek állapot nézetben, válassza a **adatok áttelepítési hibák** hivatkozást a felső szalagon nem rendelkezhetnek a migrálási hibák további adatait.

![adatok áttelepítési hibák semmilyen részleteinek példa](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Megkerülő megoldás**

Az adott hiba részletei lekéréséhez kövesse az alábbi lépéseket.

1. Zárja be a Migrálási tevékenység képernyő megjelenítéséhez az adatbázis részletes állapota panel.

     ![migrálási tevékenység képernyő](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Válassza ki **lásd a hibarészleteket** konkrét hibaüzeneteket, amelyek segítségével áttelepítési hibák elhárítása megtekintéséhez.

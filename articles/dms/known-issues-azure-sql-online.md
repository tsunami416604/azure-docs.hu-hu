---
title: A cikk kapcsolatos ismert problémák és a migrálás korlátozások az online migrálást az Azure SQL Database |} A Microsoft Docs
description: Ismerje meg az online migrálást az Azure SQL Database ismert problémák és a migrálás korlátozások.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: d228fbde230f89848d895bd1c004724b88de4431
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48883822"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-db"></a>Ismert problémák és a migrálás korlátozások az online migrálást az Azure SQL DB

Ismert problémák és korlátozások online migrálást SQL Serverről az Azure SQL Database társított alább ismertetjük.

### <a name="migration-of-temporal-tables-not-supported"></a>Áttelepítés nem támogatott a historikus táblák

**Jelenség**

Ha a forrásadatbázis egy vagy több historikus táblát tartalmaz, az adatbázis-migrálást az "adatok teljes betöltése" művelet során nem sikerül, és a következő üzenet jelenhet meg:

{"erőforrás-azonosító": "/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType": "Adatbázis-migrálási hiba", "errorEvents": "[" rögzítési funkciói nem állítható be. RetCode: Való SqlState: 42000 NativeError: 13570 üzenetet: [Microsoft] [SQL Server natív ügyfél 11.0] [SQL Server] a replikáció nem támogatott a rendszerverzióval ellátott historikus tábla a(z) [alkalmazás. Város]: sor: 1 oszlop: -1 "]"}
 
 ![A historikus tábla hibák példa](media\known-issues-azure-sql-online\dms-temporal-tables-errors.png)

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

**Jelenség**

Láthatja, hogy egy SQL-kivétel javasolásával "ntext nem kompatibilis a hierarchyid" a "adatok teljes betöltése" művelet során:
     
![hierarchyid hibák példa](media\known-issues-azure-sql-online\dms-hierarchyid-errors.png)

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

**Jelenség**

Ha nagyméretű objektum (LOB) oszlop hossza 32 KB-nál nagyobb méretű, adatok előfordulhat, hogy első csonkolva, a cél. Az alábbi lekérdezés használatával LOB oszlop hossza ellenőrizheti: 

``` 
SELECT max(len(ColumnName)) as LEN from TableName
```

**Megkerülő megoldás**

Ha egy LOB oszlop, amely 32 KB-nál nagyobb méretű, lépjen kapcsolatba a mérnöki csapathoz, [ dmsfeedback@microsoft.com ](mailto:dmsfeedback@microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Időbélyegző-oszlopok problémái

**Jelenség**

A DMS nem áttelepíteni a forrás timestamp értéket; Ehelyett a DMS a céloldali tábla hoz létre egy új időbélyegző-érték.

**Megkerülő megoldás**

Ha DMS áttelepíteni a forrástábla tárolt időbélyeg pontos érték van szüksége, forduljon a mérnöki csapathoz, [ dmsfeedback@microsoft.com ](mailto:dmsfeedback@microsoft.com).

### <a name="data-migration-errors-do-not-provide-additional-details-on-the-database-detailed-status-blade"></a>Adatok áttelepítési hibák nem biztosít további részleteket az adatbázis részletes állapota panel.

**Jelenség**

Az adatbázisok részletek állapot nézetben a migrálási hibák hibát tapasztal, amikor kiválasztja a **adatok áttelepítési hibák** hivatkozást a felső szalagon nem rendelkezhetnek a migrálási hibák további adatait.

![adatok áttelepítési hibák semmilyen részleteinek példa](media\known-issues-azure-sql-online\dms-data-migration-errors-no-details.png)

**Megkerülő megoldás**

Az adott hiba részletei lekéréséhez kövesse az alábbi lépéseket.

1. Zárja be a Migrálási tevékenység képernyő megjelenítéséhez az adatbázis részletes állapota panel.

     ![migrálási tevékenység képernyő](media\known-issues-azure-sql-online\dms-migration-activity-screen.png)

2. Válassza ki **lásd a hibarészleteket** konkrét hibaüzeneteket, amelyek segítségével áttelepítési hibák elhárítása megtekintéséhez.

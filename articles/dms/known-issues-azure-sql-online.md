---
title: A Azure SQL Database online áttelepítéssel kapcsolatos ismert problémákkal/áttelepítési korlátozásokkal foglalkozó cikk | Microsoft Docs
description: Ismerje meg a Azure SQL Database online áttelepítésével kapcsolatos ismert problémákat/áttelepítési korlátozásokat.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: afafaa86988905329a0e4ff45f29bea9d1d57820
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501042"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>Ismert problémák/áttelepítési korlátozások az online áttelepítéssel Azure SQL Database

Az SQL Serverról Azure SQL Databasere való online áttelepítéssel kapcsolatos ismert problémák és korlátozások alább olvashatók.

> [!IMPORTANT]
> A SQL Server online áttelepítésével Azure SQL Database a SQL_variant adattípusok áttelepítése nem támogatott.

### <a name="migration-of-temporal-tables-not-supported"></a>Az ideiglenes táblák migrálása nem támogatott

**Tünet** Ha a forrásadatbázis egy vagy több ideiglenes táblából áll, az adatbázis áttelepítése a "teljes adatterhelés" művelet során meghiúsul, és a következő üzenet jelenhet meg:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Példa az időbeli táblázat hibáira](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Áthidaló megoldás** Kövesse az alábbi lépéseket.

1. Az alábbi lekérdezéssel megkeresheti a forrás sémában található időbeli táblákat.

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. Zárja ki ezeket a táblákat az áttelepítési **beállítások konfigurálása** panelen, amelyen megadhatja az áttelepítéshez szükséges táblákat.

3. Futtassa újra az áttelepítési tevékenységet.

**Erőforrások** További információkért tekintse meg a cikk [időbeli táblázatait](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>A táblák áttelepítése egy vagy több oszlopot tartalmaz a hierarchyid adattípussal.

**Tünet** A "teljes adatterhelés" művelet során az "ntext nem kompatibilis a hierarchyid" nevű SQL-kivételt láthatja:

![hierarchyid-hibák – példa](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Áthidaló megoldás** Kövesse az alábbi lépéseket.

1. Keresse meg a hierarchyid adattípusú oszlopokat tartalmazó felhasználói táblákat az alábbi lekérdezés használatával.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. Zárja ki ezeket a táblákat az áttelepítési **beállítások konfigurálása** panelen, amelyen megadhatja az áttelepítéshez szükséges táblákat.

3. Futtassa újra az áttelepítési tevékenységet.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>A "teljes adatterhelés" vagy a "növekményes adatok szinkronizálása" során a séma aktív eseményindítóinak különböző integritási megsértésekkel rendelkező áttelepítési hibák

**Áthidaló megoldás** Kövesse az alábbi lépéseket.

1. Keresse meg a forrás adatbázisban jelenleg aktív eseményindítókat az alábbi lekérdezés használatával:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Tiltsa le az eseményindítókat a forrás-adatbázison az [eseményindító letiltása (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017)című cikkben ismertetett lépések alapján.

3. Futtassa újra az áttelepítési tevékenységet.

### <a name="support-for-lob-data-types"></a>LOB-adattípusok támogatása

**Tünet** Ha a nagyméretű objektum (LOB) oszlop hossza meghaladja a 32 KB-ot, a célhelyen az adatmennyiség lerövidíthető. A LOB-oszlop hosszát az alábbi lekérdezéssel tekintheti meg:

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Áthidaló megoldás** Ha 32 KB-nál nagyobb LOB-oszloppal rendelkezik, lépjen kapcsolatba a mérnöki csapattal az [Azure Database](mailto:AskAzureDatabaseMigrations@service.microsoft.com)áttelepítések megkérdezése című résznél.

### <a name="issues-with-timestamp-columns"></a>Időbélyeg-oszlopokkal kapcsolatos problémák

**Tünet** Azure Database Migration Service nem telepíti át a forrás timestamp értékét; Ehelyett a Azure Database Migration Service új időbélyeg-értéket hoz létre a cél táblában.

**Megkerülő megoldás**

Ha Azure Database Migration Servicera van szüksége a forrás táblában tárolt pontos időbélyeg-érték áttelepítéséhez, forduljon a mérnöki csapathoz az [Azure-adatbázis](mailto:AskAzureDatabaseMigrations@service.microsoft.com)áttelepítésekor.

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Az adatáttelepítési hibák nem biztosítanak további részleteket az adatbázis részletes állapot paneljén.

**Tünet** Ha az adatbázisok részletek állapota nézetben áttelepítési hibákba ütközik, a felső menüszalagon lévő adatáttelepítési **hibák** hivatkozásra kattintva nem adhat meg további részleteket az áttelepítési hibákról.

![adat-áttelepítési hibák – nem részletes példa](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Áthidaló megoldás** Az adott hiba részleteinek megismeréséhez kövesse az alábbi lépéseket.

1. Az áttelepítés tevékenység képernyő megjelenítéséhez zárjuk be az adatbázis részletes állapota panelt.

     ![áttelepítési tevékenység képernyő](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Az áttelepítési hibák elhárítását segítő hibaüzenetek megtekintéséhez válassza a **hiba részleteinek** megtekintése lehetőséget.

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>Az SQLDB online Migrálás nem támogatja a földrajzi adattípust

**Tünet** Az áttelepítés meghiúsul, és a következő szöveget tartalmazó hibaüzenet jelenik meg:

 "* * végzetes hiba történt", "errorEvents":<Table>.<Column> "FÖLDRAJZ" típusú, amelyet a teljes LOB támogatási módban nem támogat a teljes terhelés.

**Áthidaló megoldás** Míg a Azure Database Migration Service támogatja az offline Migrálás földrajzi adattípusát Azure SQL Database, online áttelepítéshez, a földrajzi adattípus nem támogatott. Próbálkozzon alternatív módszerekkel a forrás adattípusának egy támogatott típusra való módosításához, mielőtt a rendszer megpróbálja használni az adatbázis online áttelepítését Azure Database Migration Service.

### <a name="supported-editions"></a>Támogatott kiadások

**Tünet** Az áttelepítés meghiúsul, és a következő szöveget tartalmazó hibaüzenet jelenik meg:

 Áttelepítési beállítások érvényesítési hibája: A kiszolgáló [Business Intelligence Edition (64-bit)] kiadása nem felel meg a támogatott kiadás (ok) [Enterprise, standard, Developer] verziónak.

**Áthidaló megoldás** Az online áttelepítések támogatása Azure SQL Database használatával Azure Database Migration Service csak a vállalati, a standard és a fejlesztői kiadásokra terjed ki. Az áttelepítési folyamat megkezdése előtt győződjön meg róla, hogy támogatott kiadást használ.

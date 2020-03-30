---
title: 'Ismert problémák: Online áttelepítések az SQL Database-be'
titleSuffix: Azure Database Migration Service
description: Ismerje meg az Azure Database Migration Service használatával az Azure SQL Database-be történő online áttelepítésekkel kapcsolatos ismert problémákat/áttelepítési korlátozásokat.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: e7efdb7244e2c7e4651a4507b538123f8d320c1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650775"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>Az Azure SQL Database-be való online áttelepítéssel kapcsolatos ismert problémák/áttelepítési korlátozások

Az SQL Serverről az Azure SQL Database-be történő online áttelepítéssel kapcsolatos ismert problémák és korlátozások az alábbiakban találhatók.

> [!IMPORTANT]
> Az SQL Server online áttelepítése az Azure SQL Database-be, SQL_variant adattípusok áttelepítése nem támogatott.

### <a name="migration-of-temporal-tables-not-supported"></a>Nem támogatott időbeli táblázatok áttelepítése

**Tünet**

Ha a forrásadatbázis egy vagy több időbeli táblából áll, az adatbázis áttelepítése sikertelen lesz a "Teljes adatbetöltés" művelet során, és a következő üzenet jelenhet meg:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Példa a halitábla hibáira](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Workaround**

Ehhez a következő lépések szükségesek.

1. Keresse meg a adatforrásséma időbeli tábláit az alábbi lekérdezéssel.

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. Zárja ki ezeket a táblákat az **Áttelepítési beállítások konfigurálása** panelből, amelyen táblákat ad meg az áttelepítéshez.

3. Futtassa újra az áttelepítési tevékenységet.

**Erőforrások**

További információt a Temporális táblázatok című [cikkben talál.](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017)

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>A táblák áttelepítése egy vagy több hierarchiaadat-típusú oszlopot tartalmaz

**Tünet**

A "Teljes adatbetöltés" művelet során megjelenhet egy SQL-kivétel, amely arra utal, hogy "az ntext nem kompatibilis a hierarchyid azonosítóval":

![hierarchyid hibák példa](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Workaround**

Ehhez a következő lépések szükségesek.

1. Keresse meg azokat a felhasználói táblákat, amelyek a hierarchiaazonosító adattípussal rendelkező oszlopokat tartalmazzák az alábbi lekérdezéssel.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. Zárja ki ezeket a táblákat az **Áttelepítési beállítások konfigurálása** panelből, amelyen táblákat ad meg az áttelepítéshez.

3. Futtassa újra az áttelepítési tevékenységet.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Áttelepítési hibák különböző integritási megsértések aktív eseményindítók a sémában során "Teljes adatbetöltés" vagy "Növekményes adatszinkronizálás"

**Workaround**

Ehhez a következő lépések szükségesek.

1. Keresse meg azokat az eseményindítókat, amelyek jelenleg aktívak a forrásadatbázisban az alábbi lekérdezéssel:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Tiltsa le a forrásadatbázis eseményindítóit a [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017)című cikkben ismertetett lépésekkel.

3. Futtassa újra az áttelepítési tevékenységet.

### <a name="support-for-lob-data-types"></a>A lob-adattípusok támogatása

**Tünet**

Ha a nagy objektum (LOB) oszlophossza nagyobb, mint 32 KB, előfordulhat, hogy az adatok csonkulnak a célnál. Az alábbi lekérdezéssel ellenőrizheti a LOB oszlop hosszát:

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Workaround**

Ha 32 KB-nál nagyobb lob-oszlopot rendelkezik, lépjen kapcsolatba az Ask Azure Database Migrations mérnöki [csapatával.](mailto:AskAzureDatabaseMigrations@service.microsoft.com)

### <a name="issues-with-timestamp-columns"></a>Problémák az időbélyeg oszlopaival

**Tünet**

Az Azure Database Migration Service nem telepíti át a forrás időbélyeg értékét; ehelyett az Azure Database Migration Service új időbélyeg-értéket hoz létre a céltáblában.

**Workaround**

Ha az Azure Database Migration Service-nek szüksége van a forrástáblában tárolt pontos időbélyeg-érték áttelepítéséhez, forduljon az [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com)mérnöki csapatához.

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Az adatáttelepítési hibák nem nyújtanak további részleteket az Adatbázis részletes állapotpaneljén

**Tünet**

Ha az Adatbázisok részletei állapotnézetben áttelepítési hibákat észlel, előfordulhat, hogy a felső menüszalagon az **Adatáttelepítési hibák** hivatkozása nem tartalmaz további részleteket az áttelepítési hibákról.

![adatáttelepítési hibák nincsenek részletek példa](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Workaround**

A konkrét hiba részleteinek megismeréséhez kövesse az alábbi lépéseket.

1. Zárja be az Adatbázis részletes állapotpaneljét az Áttelepítési tevékenység képernyő megjelenítéséhez.

     ![áttelepítési tevékenység képernyő](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Válassza **a Hibarészletek megtekintése** lehetőséget az áttelepítési hibák elhárítását segítő hibaüzenetek megtekintéséhez.

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>Az SQLDB online áttelepítése nem támogatja a földrajzi adattípust

**Tünet**

Az áttelepítés sikertelen, a következő szöveget tartalmazó hibaüzenet telje meg:

     “** encountered a fatal error”, "errorEvents":<Table>.<Column> is of type 'GEOGRAPHY', which is not supported by 'Full Load' under 'Full LOB' support mode."

**Workaround**

Míg az Azure Database Migration Service támogatja a földrajz adattípus offline áttelepítések az Azure SQL Database, az online áttelepítések, a Földrajz adattípus nem támogatott. Próbálkozzon alternatív módszerekkel, hogy módosítsa az adattípust a forrásnál egy támogatott típusra, mielőtt az Azure Database Migration Service-t használná az adatbázis online áttelepítéséhez.

### <a name="supported-editions"></a>Támogatott kiadások

**Tünet**

Az áttelepítés sikertelen, a következő szöveget tartalmazó hibaüzenet telje meg:

    Migration settings validation error: The edition of the server [Business Intelligence Edition (64-bit)] does not match the supported edition(s) [Enterprise,Standard,Developer].

**Workaround**

Az Azure Database Migration Service használatával az Azure SQL Database-adatbázisba történő online áttelepítések támogatása csak a vállalati, standard és fejlesztői kiadásokra terjed ki. Az áttelepítési folyamat megkezdése előtt győződjön meg arról, hogy támogatott kiadást használ.

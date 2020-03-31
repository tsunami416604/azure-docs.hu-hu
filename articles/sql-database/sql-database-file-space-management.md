---
title: Egy/készletezésű adatbázisok fájlterületénnek kezelése
description: Ez a lap bemutatja, hogyan kezelheti a fájlterületet egy- és összevont adatbázisokkal az Azure SQL Database-ben, és kódmintákat biztosít annak meghatározásához, hogy egy-e vagy egy készletező adatbázist kell-e zsugorítania, valamint hogyan hajthatja végre az adatbázis-zsugorítási műveletet.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 007bbffbd7c4fcad339f88eb78991eb39fb829e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74420981"
---
# <a name="manage-file-space-for-single-and-pooled-databases-in-azure-sql-database"></a>Az egy- és készletezésű adatbázisok fájlterületének kezelése az Azure SQL Database-ben

Ez a cikk az Azure SQL Database-ben az egy- és készletalapú adatbázisok különböző típusú tárhelyét ismerteti, és azokat a lépéseket, amelyek et akkor lehet elvégezni, amikor az adatbázisok és rugalmas készletek számára lefoglalt fájlterületet explicit módon kell kezelni.

> [!NOTE]
> Ez a cikk nem vonatkozik a felügyelt példány üzembe helyezésének beállítás az Azure SQL Database.This article does not apply to the managed instance deployment option in Azure SQL Database.

## <a name="overview"></a>Áttekintés

Az Azure SQL Database-ben egy- és készletezésű adatbázisok olyan számítási feladatok mintái vannak, ahol az alapul szolgáló adatfájlok adatbázisokhoz való lefoglalása nagyobb lehet, mint a használt adatlapok mennyisége. Ez akkor fordulhat elő, amikor növekszik a használt terület, majd később adatok törlődnek. Ennek az az oka, hogy az adatok törlésekor a lefoglalt fájlterület nem kerül automatikusan visszanyerésre.

A következő esetekben szükség lehet a fájlterület használatának monitorozására és az adatfájlok zsugorítására:

- Ha lehetővé szeretné tenni egy rugalmas készletben található adatmennyiség növelését, ha az adatbázisokhoz lefoglalt fájlterület eléri a készlet maximális méretét.
- Ha lehetővé szeretné tenni egy önálló adatbázis vagy rugalmas készlet maximális méretének csökkentését.
- Ha lehetővé szeretné tenni egy önálló adatbázis vagy rugalmas készlet módosítását egy másik, kisebb maximális méretű szolgáltatási vagy teljesítményszintre.

### <a name="monitoring-file-space-usage"></a>A fájlterület használatának figyelése

Az Azure Portalon megjelenő legtöbb tárterület-metrikák és a következő API-k csak a használt adatlapok méretét mérik:

- Az Azure Resource Manager alapú metrikák API-k, beleértve a PowerShell [get-metrikák](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetric)
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

A következő API-k azonban az adatbázisok és rugalmas készletek számára lefoglalt terület nagyságát is mérik:

- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>Adatfájlok zsugorítása

Az SQL Database szolgáltatás nem zsugorítja automatikusan az adatfájlokat, hogy visszaszerezzék a fel nem használt lefoglalt területet az adatbázis teljesítményére gyakorolt lehetséges hatás miatt.  Az ügyfelek azonban az általuk választott időpontban önkiszolgáló módon zsugoríthatják az adatfájlokat a [fel nem használt hely visszaigénylése](#reclaim-unused-allocated-space)című lépések végrehajtásával.

> [!NOTE]
> Az adatfájlokkal ellentétben az SQL Database szolgáltatás automatikusan zsugorítja a naplófájlokat, mivel ez a művelet nem befolyásolja az adatbázis teljesítményét.

## <a name="understanding-types-of-storage-space-for-a-database"></a>Az adatbázis tárhelytípusainak ismertetése

A következő tárterület-mennyiségek megértése fontos az adatbázis fájlterületének kezeléséhez.

|Adatbázis mennyisége|Meghatározás|Megjegyzések|
|---|---|---|
|**Használt adatterület**|Az adatbázisadatok 8 KB-os lapokban történő tárolására használt terület.|A lapkákon (törléseken) általában nő (csökken) a felhasznált terület. Bizonyos esetekben a felhasznált terület nem változik a beszúrásokon vagy a törléseken a műveletben részt vevő adatok mennyiségétől és mintájától, valamint a töredezettségtől függően. Ha például egy sort vesz fel minden adatlapról, az nem feltétlenül csökkenti a felhasznált területet.|
|**Lefoglalt adatterület**|Az adatbázisadatok tárolására rendelkezésre bocsátott formázott fájlterület mennyisége.|A lefoglalt terület mennyisége automatikusan növekszik, de a törlés után soha nem csökken. Ez a viselkedés biztosítja, hogy a jövőbeli beszúrások gyorsabbak legyenek, mivel a helyet nem kell újraformázni.|
|**Lefoglalt, de nem használt adatterület**|A lefoglalt adatterület és a felhasznált adatterület közötti különbség.|Ez a mennyiség azt a maximális szabad területet jelenti, amely az adatbázis-adatfájlok zsugorításával visszanyerhető.|
|**Adatok maximális mérete**|Az adatbázisadatok tárolására használható maximális terület.|A lefoglalt adatterület mennyisége nem nőhet túl az adatok maximális méreténél.|

Az alábbi ábra az adatbázis különböző típusú tárhelyeközötti kapcsolatot mutatja be.

![tárolóhely-típusok és kapcsolatok](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>Egyetlen adatbázis lekérdezése a tárhelyadataihoz

A következő lekérdezések segítségével határozhatja meg egyetlen adatbázis tárterület-mennyiségét.  

### <a name="database-data-space-used"></a>Használt adatbázis-adatterület

Módosítsa a következő lekérdezést a felhasznált adatbázis-adatterület visszaadásához.  A lekérdezés eredményének egységei MB-ban vannak.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Lefoglalt adatbázis-adatterület és fel nem használt lefoglalt terület

A következő lekérdezéssel adja vissza a lefoglalt adatbázis-adatterület és a lefoglalt fel nem használt terület nagy részét.  A lekérdezés eredményének egységei MB-ban vannak.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB,
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```

### <a name="database-data-max-size"></a>Adatbázis-adatok maximális mérete

Módosítsa a következő lekérdezést az adatbázis-adatok maximális méretének visszaadásához.  A lekérdezés eredményének egységei bájtban vannak.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>A rugalmas készlet tárhelytípusainak ismertetése

A következő tárolóhely-mennyiségek megértése fontos a rugalmas készlet fájlterületének kezeléséhez.

|Rugalmas készletmennyiség|Meghatározás|Megjegyzések|
|---|---|---|
|**Használt adatterület**|A rugalmas készletben lévő összes adatbázis által használt adatterület összegzése.||
|**Lefoglalt adatterület**|A rugalmas készletben lévő összes adatbázis által lefoglalt adatterület összegzése.||
|**Lefoglalt, de nem használt adatterület**|A lefoglalt adatterület és a rugalmas készletben lévő összes adatbázis által használt adatterület közötti különbség.|Ez a mennyiség a rugalmas készlet számára lefoglalt maximális helyet jelöli, amely az adatbázis-adatfájlok zsugorításával nyerhető vissza.|
|**Adatok maximális mérete**|A rugalmas készlet által az összes adatbázishoz használható maximális adatterület.|A rugalmas készlet számára lefoglalt terület nem haladhatja meg a rugalmas készlet maximális méretét.  Ha ez a feltétel bekövetkezik, akkor a fel nem használt terület visszanyerhető az adatbázis-adatfájlok zsugorításával.|

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Rugalmas készlet lekérdezése a tárhely adataihoz

A következő lekérdezések segítségével határozza meg a rugalmas készlet tárolóhely-mennyiségeit.  

### <a name="elastic-pool-data-space-used"></a>Rugalmas készlet adatterület használt

Módosítsa a következő lekérdezést a felhasznált rugalmas készletadatterület mennyiségének visszaadásához.  A lekérdezés eredményének egységei MB-ban vannak.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Lefoglalt készletadatterület és a fel nem használt lefoglalt terület

Módosítsa a következő példákat egy olyan táblázat visszaadására, amely egy rugalmas készletben lévő egyes adatbázisok számára lefoglalt és fel nem használt helyet tartalmaz. A tábla a legtöbb fel nem használt lefoglalt területtel rendelkező adatbázisokból rendeli az adatbázisokat a legkevésbé fel nem használt lefoglalt területszámára.  A lekérdezés eredményének egységei MB-ban vannak.  

A lekérdezés eredményei a készlet egyes adatbázisai számára lefoglalt terület meghatározásához hozzáadhatóak a rugalmas készlet számára lefoglalt teljes terület meghatározásához. A lefoglalt rugalmas készletterület nem haladhatja meg a rugalmas készlet maximális méretét.  

> [!IMPORTANT]
> A PowerShell Azure Resource Manager (RM) modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Az AzureRM-modul legalább 2020 decemberéig továbbra is megkapja a hibajavításokat.  Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak. A kompatibilitásukról az [Új Azure PowerShell Az modul bemutatása](/powershell/azure/new-azureps-module-az)című témakörben lehet további további további információkért.

A PowerShell-parancsfájl hoz szükség SQL Server PowerShell modul – [lásd: PowerShell-modul letöltése](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module) a telepítéshez.

```powershell
$resourceGroupName = "<resourceGroupName>"
$serverName = "<serverName>"
$poolName = "<poolName>"
$userName = "<userName>"
$password = "<password>"

# get list of databases in elastic pool
$databasesInPool = Get-AzSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# for each database in the elastic pool, get space allocated in MB and space allocated unused in MB
foreach ($database in $databasesInPool) {
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn -Database $database.DatabaseName `
            -Username $userName -Password $password -Query $sqlCommand)
}

# display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort -Property DatabaseDataSpaceAllocatedUnusedInMB -Descending | Format-Table
```

A következő képernyőkép egy példa a parancsfájl kimenetére:

![rugalmas készlet lefoglalt terület és a fel nem használt lefoglalt terület példa](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Rugalmas készletadatok maximális mérete

Módosítsa a következő T-SQL-lekérdezést a rugalmas készletadatok maximális méretének visszaadásához.  A lekérdezés eredményének egységei MB-ban vannak.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>A fel nem használt lefoglalt terület visszaigénylése

> [!NOTE]
> Ez a parancs hatással lehet az adatbázis teljesítményére futás közben, és ha lehetséges, alacsony használat ú időszakokban kell futtatni.

### <a name="dbcc-shrink"></a>DBCC zsugorítás

Miután a fel nem használt lefoglalt terület visszanyeréséhez azonosította az adatbázisokat, módosítsa az adatbázis nevét a következő parancsban az egyes adatbázisok adatfájljainak zsugorításához.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

Ez a parancs hatással lehet az adatbázis teljesítményére futás közben, és ha lehetséges, alacsony használat ú időszakokban kell futtatni.  

A parancsról további információt a [SHRINKDATABASE című](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql)témakörben talál.

### <a name="auto-shrink"></a>Automatikus zsugorítás

Másik lehetőségként engedélyezhető az automatikus zsugorítás egy adatbázishoz.  Az automatikus zsugorítás csökkenti a fájlkezelés `SHRINKDATABASE` összetettségét, és kevésbé befolyásolja az adatbázis teljesítményét, mint a . `SHRINKFILE`  Az automatikus zsugorítás különösen hasznos lehet a sok adatbázissal rendelkező rugalmas készletek kezeléséhez.  Az automatikus zsugorítás azonban kevésbé `SHRINKDATABASE` `SHRINKFILE`hatékony lehet a fájlterület visszaszerzésében, mint a és a .
Az automatikus zsugorítás engedélyezéséhez módosítsa az adatbázis nevét a következő parancsban.

```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON
```

A parancsról további információt az [ADATBÁZIS-beállításbeállításai](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) című témakörben talál.

### <a name="rebuild-indexes"></a>Indexek újraépítése

Az adatbázis-adatfájlok összezsugorodása után az indexek töredezetté válhatnak, és elveszíthetik teljesítményoptimalizálási hatékonyságukat. Ha teljesítménycsökkenés következik be, fontolja meg az adatbázis-indexek újraépítését. Az indexek töredezettségáról és újraépítéséről az [Indexek átrendezése és újraépítése](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

- Az adatbázis maximális méretéről a következő témakörben talál további információt:
  - [Az Azure SQL Database virtuálismag-alapú vásárlási modell korlátai egyetlen adatbázishoz](sql-database-vcore-resource-limits-single-databases.md)
  - [Erőforráskorlátok a DTU-alapú vásárlási modellt használó egyetlen adatbázisokhoz](sql-database-dtu-resource-limits-single-databases.md)
  - [Az Azure SQL Database virtuálismag-alapú vásárlási modell korlátai rugalmas készletekhez](sql-database-vcore-resource-limits-elastic-pools.md)
  - [A DTU-alapú vásárlási modellt használó rugalmas készletek erőforráskorlátai](sql-database-dtu-resource-limits-elastic-pools.md)
- A parancsról `SHRINKDATABASE` további információt a [SHRINKDATABASE című](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql)témakörben talál.
- Az indexek töredezettségáról és újraépítéséről az [Indexek átrendezése és újraépítése](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)című témakörben talál további információt.

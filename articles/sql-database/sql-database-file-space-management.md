---
title: Önálló/készletezett adatbázisok – tárterület-kezelés
description: Ez a lap leírja, hogyan kezelheti a tárhelyet a Azure SQL Database önálló és készletezett adatbázisaival, és Hogyan határozható meg, hogy miként lehet egy vagy több készletezett adatbázist összezsugorodni, valamint hogyan kell egy adatbázis-zsugorodó műveletet végrehajtani.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "74420981"
---
# <a name="manage-file-space-for-single-and-pooled-databases-in-azure-sql-database"></a>A tárterület kezelése az önálló és a készletezett adatbázisok esetében Azure SQL Database

Ez a cikk a Azure SQL Database önálló és készletezett adatbázisainak különböző tárolási területeit ismerteti, valamint azokat a lépéseket, amelyek akkor hajthatók végre, ha az adatbázisokhoz és a rugalmas készletekhez lefoglalt lemezterületet explicit módon kell kezelni.

> [!NOTE]
> Ez a cikk a Azure SQL Database felügyelt példányok központi telepítésére vonatkozó beállítására nem vonatkozik.

## <a name="overview"></a>Áttekintés

A Azure SQL Databaseban található önálló és készletezett adatbázisok esetében olyan számítási feladatok szerepelnek, amelyekben az adatbázis alapjául szolgáló adatfájlok kiosztása nagyobb lehet, mint a felhasznált adatlapok mennyisége. Ez akkor fordulhat elő, amikor növekszik a használt terület, majd később adatok törlődnek. Ennek az az oka, hogy a lefoglalt tárterületet a rendszer nem állítja automatikusan vissza az Adattörléskor.

A következő esetekben szükség lehet a fájlterület használatának monitorozására és az adatfájlok zsugorítására:

- Ha lehetővé szeretné tenni egy rugalmas készletben található adatmennyiség növelését, ha az adatbázisokhoz lefoglalt fájlterület eléri a készlet maximális méretét.
- Ha lehetővé szeretné tenni egy önálló adatbázis vagy rugalmas készlet maximális méretének csökkentését.
- Ha lehetővé szeretné tenni egy önálló adatbázis vagy rugalmas készlet módosítását egy másik, kisebb maximális méretű szolgáltatási vagy teljesítményszintre.

### <a name="monitoring-file-space-usage"></a>A tárterület használatának figyelése

A Azure Portalban megjelenő legtöbb tárolóhely-metrika és a következő API-k csak a felhasznált adatlapok méretét mérik:

- Azure Resource Manager alapú mérőszámok API-k, beleértve a PowerShell [Get-metrikákat](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetric)
- T-SQL: [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

A következő API-k ugyanakkor az adatbázisokhoz és rugalmas készletekhez lefoglalt terület méretét is mérik:

- T-SQL: [sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>Az adatfájlok zsugorítása

A SQL Database szolgáltatás nem csökkenti automatikusan az adatfájlokat a fel nem használt lefoglalt terület visszaigényléséhez, mert az adatbázis teljesítményének lehetséges következményei vannak.  Az ügyfelek azonban az önkiszolgáló szolgáltatással is csökkenthetik az adatfájlokat az adott időpontban, a [használaton kívüli lefoglalt terület visszaigénylése](#reclaim-unused-allocated-space)című cikkben ismertetett lépéseket követve.

> [!NOTE]
> Az adatfájlok eltérően a SQL Database szolgáltatás automatikusan csökkenti a naplófájlokat, mivel az adott művelet nem befolyásolja az adatbázis teljesítményét.

## <a name="understanding-types-of-storage-space-for-a-database"></a>Az adatbázis tárterület-típusainak ismertetése

A következő tárolóhelyek mennyiségének megismerése fontos az adatbázisok fájlméretének kezeléséhez.

|Adatbázis mennyisége|Meghatározás|Megjegyzések|
|---|---|---|
|**Felhasznált adatterület**|Az adatbázis-adatmennyiségek 8 KB-os lapokon való tárolására szolgáló tárterület.|Általánosságban elmondható, hogy a felhasznált terület növekedése (csökkenés) a lapkáknál (deletes). Bizonyos esetekben a felhasznált terület nem változik a lapkákon és a törléseken a műveletben érintett adatmennyiségtől és a töredezettségtől függően. Ha például egy sort töröl minden adatoldalról, nem feltétlenül csökkenti a felhasznált területet.|
|**Lefoglalt adatterület**|Az adatbázis-adattárolás számára elérhetővé tett formázott fájl mérete.|A lefoglalt terület mennyisége automatikusan növekszik, de a törlés után soha nem csökken. Ez a viselkedés biztosítja, hogy a jövőbeli lapkák gyorsabbak legyenek, mivel a tárhelyet nem kell formázni.|
|**Lefoglalt, de fel nem használt adatterület**|A lefoglalt adatterületek és a felhasznált adatterületek mennyisége közötti különbség.|Ez a mennyiség az adatbázis-adatfájlok zsugorodásával visszaigényelhető szabad terület maximális mennyiségét jelöli.|
|**Az adatmaximális méret**|Az adatbázis-adatmennyiség tárolására használható maximális tárterület.|A lefoglalt adatterület mennyisége nem növekedhet az adatmennyiség maximális méretén túl.|

Az alábbi ábra az adatbázis különböző típusai közötti kapcsolatot szemlélteti.

![tárolóhelyek típusai és kapcsolatai](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>Egyetlen adatbázis lekérdezése a tárolóhelyekre vonatkozó információkhoz

A következő lekérdezések használhatók a tárolóhelyek mennyiségének meghatározására egyetlen adatbázis esetében.  

### <a name="database-data-space-used"></a>Az adatbázis által használt adatterület

Módosítsa a következő lekérdezést a felhasznált adatbázis-adatterületek mennyiségének visszaküldéséhez.  A lekérdezés eredményének egységei MB-ban vannak.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Az adatbázis-adatterület lefoglalt és nem használt lefoglalt terület

A következő lekérdezéssel visszaállíthatja a lefoglalt adatbázis-adatterületek mennyiségét, valamint a lefoglalt fel nem használt terület mennyiségét.  A lekérdezés eredményének egységei MB-ban vannak.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB,
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```

### <a name="database-data-max-size"></a>Adatbázis-adatmennyiség maximális mérete

Módosítsa a következő lekérdezést az adatbázis-adatmennyiség maximális méretének visszaküldéséhez.  A lekérdezés eredményének egységei bájtban vannak megadva.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>A rugalmas készlethez tartozó tárterület-típusok ismertetése

A következő tárolóhelyek mennyiségének megismerése fontos a rugalmas készlet tárterületének kezeléséhez.

|Rugalmas készlet mennyisége|Meghatározás|Megjegyzések|
|---|---|---|
|**Felhasznált adatterület**|A rugalmas készletben lévő összes adatbázis által használt adatterület összegzése.||
|**Lefoglalt adatterület**|A rugalmas készlet összes adatbázisa által lefoglalt adatterület összegzése.||
|**Lefoglalt, de fel nem használt adatterület**|A rugalmas készletben lévő összes adatbázis által lefoglalt adatterületek és adatterületek mennyisége közötti különbség.|Ez a mennyiség a rugalmas készlet számára lefoglalt maximális tárterületet jelöli, amelyet az adatbázis-adatfájlok zsugorodásával lehet visszaigényelni.|
|**Az adatmaximális méret**|A rugalmas készlet által az összes adatbázisához felhasználható adatterület maximális mennyisége.|A rugalmas készlethez lefoglalt terület mérete nem haladhatja meg a rugalmas készlet maximális méretét.  Ha ez az állapot előfordul, akkor a felhasználatlan helyet a rendszer az adatbázis-adatfájlok zsugorodása után visszaigényelheti.|

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Rugalmas készlet lekérdezése a tárolóhelyekkel kapcsolatos információkhoz

A következő lekérdezések segítségével határozható meg a rugalmas készlet tárolóhelyek mennyisége.  

### <a name="elastic-pool-data-space-used"></a>Rugalmas készlethez felhasznált adatterület

Módosítsa a következő lekérdezést, hogy visszaállítsa a felhasznált rugalmas készlet adatterületének mennyiségét.  A lekérdezés eredményének egységei MB-ban vannak.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>A rugalmas készlethez lefoglalt és fel nem használt lemezterület

Módosítsa az alábbi példákat egy olyan tábla visszaküldéséhez, amely a rugalmas készletben lévő minden adatbázishoz lefoglalt területet és fel nem használt területet tartalmaz. A táblázat ezeket az adatbázisokat a legkevesebb felhasználatlan lefoglalt terület közül a legnagyobb mennyiségű, nem felhasznált lefoglalt terület közül rendeli.  A lekérdezés eredményének egységei MB-ban vannak.  

A rendszer a készletben lévő egyes adatbázisok számára lefoglalt terület meghatározására szolgáló lekérdezés eredményeit hozzáadhatja a rugalmas készlethez lefoglalt teljes terület meghatározásához. A lefoglalt rugalmas készlet területe nem haladhatja meg a rugalmas készlet maximális méretét.  

> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager (RM) modult, de a jövőbeli fejlesztés az az. SQL modulhoz kapcsolódik. A AzureRM modul továbbra is megkapja a hibajavításokat, amíg legalább december 2020-ra nem kerül sor.  Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A kompatibilitással kapcsolatos további információkért lásd: [az új Azure PowerShell bemutatása az Module](/powershell/azure/new-azureps-module-az).

A PowerShell-parancsfájlhoz SQL Server PowerShell-modulra van szükség – lásd: a telepítéshez szükséges [PowerShell-modul letöltése](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module) .

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

A következő képernyőkép a parancsfájl kimenetét szemlélteti:

![a rugalmas készlet lefoglalt területe és a fel nem használt szabad terület – példa](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Rugalmas készletre vonatkozó adatmennyiség maximális mérete

Módosítsa a következő T-SQL-lekérdezést a rugalmas készlet adatmaximális méretének visszaküldéséhez.  A lekérdezés eredményének egységei MB-ban vannak.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Nem használt lefoglalt terület visszaigénylése

> [!NOTE]
> Ez a parancs hatással lehet az adatbázis teljesítményére a futása közben, és ha lehetséges, alacsony használati időszakok alatt kell futnia.

### <a name="dbcc-shrink"></a>DBCC zsugorodása

Miután azonosította az adatbázisokat a fel nem használt lefoglalt terület visszaigényléséhez, módosítsa az adatbázis nevét az alábbi parancsban az egyes adatbázisok adatfájljainak összezsugorodása érdekében.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

Ez a parancs hatással lehet az adatbázis teljesítményére a futása közben, és ha lehetséges, alacsony használati időszakok alatt kell futnia.  

További információ erről a parancsról: [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).

### <a name="auto-shrink"></a>Automatikus zsugorodás

Másik lehetőségként az automatikus zsugorodás is engedélyezhető az adatbázishoz.  Az automatikus zsugorodás csökkenti a fájlkezelési bonyolultságot, és kevésbé befolyásolja az adatbázis teljesítményét `SHRINKDATABASE` , `SHRINKFILE`mint a vagy a.  Az automatikus zsugorodás különösen hasznos lehet a sok adatbázissal rendelkező rugalmas készletek kezeléséhez.  Az automatikus zsugorodás azonban kevésbé hatékony lehet a fájlméret `SHRINKDATABASE` visszaigénylése során. `SHRINKFILE`
Az automatikus zsugorodás engedélyezéséhez módosítsa az adatbázis nevét a következő parancsban.

```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON
```

További információ erről a parancsról: [adatbázis-beállítási](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) beállítások.

### <a name="rebuild-indexes"></a>Indexek újraépítése

Az adatbázis-adatfájlok összezsugorodása után az indexek töredezettek lehetnek, és elveszítik a teljesítmény optimalizálásának hatékonyságát. Ha a teljesítmény romlása történik, érdemes megfontolnia az adatbázis-indexek újjáépítését. Az indexek töredezettségével és újraépítésével kapcsolatos további információkért lásd: az [indexek újrarendezése és újraépítése](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>További lépések

- Az adatbázis maximális méretével kapcsolatos információkért lásd:
  - [Azure SQL Database virtuális mag-alapú beszerzési modell korlátai egyetlen adatbázishoz](sql-database-vcore-resource-limits-single-databases.md)
  - [Az DTU-alapú vásárlási modellt használó önálló adatbázisok erőforrás-korlátai](sql-database-dtu-resource-limits-single-databases.md)
  - [Azure SQL Database virtuális mag-alapú beszerzési modell korlátai rugalmas készletekhez](sql-database-vcore-resource-limits-elastic-pools.md)
  - [A rugalmas készletek erőforrásokra vonatkozó korlátai a DTU-alapú vásárlási modell használatával](sql-database-dtu-resource-limits-elastic-pools.md)
- További információ a `SHRINKDATABASE` parancsról: [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).
- Az indexek töredezettségével és újraépítésével kapcsolatos további információkért lásd: az [indexek újrarendezése és újraépítése](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

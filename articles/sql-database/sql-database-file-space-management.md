---
title: Az Azure SQL Database egyetlen vagy készletezett adatbázisok fájlt a címterület-kezelés |} A Microsoft Docs
description: Ezen a lapon azt ismerteti, hogyan kezelheti az Azure SQL Database-ben egyetlen vagy készletezett adatbázisok területe, és annak megállapítása, ha szeretné-e zsugorítani egyetlen vagy készletezett adatbázisként, valamint, hogy hogyan végrehajtásához egy adatbázis tömörítése a művelet-mintakódot biztosít.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, carlrab
manager: craigg
ms.date: 03/07/2019
ms.openlocfilehash: a6fccb087d8a2c78bb1ad47758952b76b245dbde
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576191"
---
# <a name="manage-file-space-for-single-and-pooled-databases-in-azure-sql-database"></a>Az Azure SQL Database-ben egyetlen vagy készletezett adatbázisok területe kezelése

Ez a cikk ismerteti a különböző típusú egyetlen vagy készletezett adatbázisok az Azure SQL Database és a lépéseket, amelyek segítségével kell elvégezni, ha a fájl terület adatbázisok számára lefoglalt tárhely és rugalmas adatbáziskészletekhez explicit módon kell kezelnie.

> [!NOTE]
> Ez a cikk az Azure SQL Database felügyelt példány beállítás nem vonatkozik.

## <a name="overview"></a>Áttekintés

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Egyetlen vagy készletezett adatbázisok Azure SQL Database-ben nincsenek munkaterhelési mintákat, az adatbázisok alapjául szolgáló adatfájlok elosztása nagyobb, mint a használt adatok oldalak mennyisége válhat. Ez akkor fordulhat elő, amikor növekszik a használt terület, majd később adatok törlődnek. Az az oka, mert lefoglalt terület fájl van nem igényli automatikusan vissza adatok törlésekor.

A következő esetekben szükség lehet a fájlterület használatának monitorozására és az adatfájlok zsugorítására:

- Ha lehetővé szeretné tenni egy rugalmas készletben található adatmennyiség növelését, ha az adatbázisokhoz lefoglalt fájlterület eléri a készlet maximális méretét.
- Ha lehetővé szeretné tenni egy önálló adatbázis vagy rugalmas készlet maximális méretének csökkentését.
- Ha lehetővé szeretné tenni egy önálló adatbázis vagy rugalmas készlet módosítását egy másik, kisebb maximális méretű szolgáltatási vagy teljesítményszintre.

### <a name="monitoring-file-space-usage"></a>Fájl lemezterület-használat figyelése

A legtöbb tárolási hely metrikák jelennek meg az Azure portal és a következő API-k csak mérésére használt lapok mérete:

- Az Azure Resource Manager-alapú API-k metrikák többek között a PowerShell [get-metrikák](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetric)
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

Azonban a következő API-kat is mérheti az adatbázisok és rugalmas lefoglalt terület méretét készletek:

- T-SQL:  [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>Adatok fájlok zsugorítása folyamatban

Az SQL Database szolgáltatás automatikusan nem csökkentheti az adatfájlokat az adatbázis teljesítményét a lehetséges hatás miatt nem használt lefoglalt terület felszabadítását.  Azonban ügyfelek előfordulhat, hogy adatokat fájlok zsugorítása keresztül önkiszolgáló ismertetett lépéseket követve hozhatna egyszerre [visszaigénylési fel nem használt lefoglalt terület](#reclaim-unused-allocated-space).

> [!NOTE]
> Adatfájlok, ellentétben az SQL Database szolgáltatás automatikusan zsugorítja naplófájlok, mivel a művelet nem befolyásolja az adatbázis teljesítményét. 

## <a name="understanding-types-of-storage-space-for-a-database"></a>Egy adatbázis-tárolási terület típusainak ismertetése

A következő tárolási hely mennyiségek ismertetése fontosak a lapozófájl-terület az adatbázis kezeléséhez.

|Adatbázis-mennyiség|Meghatározás|Megjegyzések|
|---|---|---|
|**Adatok felhasznált lemezterület**|A 8 KB-os lapokon található adatbázis-adatok tárolására felhasznált lemezterület mennyiségét.|Általában a hely nő (csökkenő) használt Beszúrások (törli). Bizonyos esetekben a felhasznált lemezterület nem módosítja a beszúrások, vagy törli az összeg és az adatok a művelet és bármely töredezettség módja, attól függően. Például egy sor minden adatok oldalról törlése nem feltétlenül csökkenthető a használt területet.|
|**Adatok lefoglalt terület**|Mennyisége formátumú fájl hely adatbázis-adatok tárolására szolgáló elérhetővé.|A lefoglalt terület mennyisége automatikusan nő, de soha nem csökkenti a törlése után. Ezt a viselkedést biztosítja, hogy jövőbeli Beszúrások gyorsabb, mivel a helynek nem kell formázni.|
|**De nem használt lefoglalt adatok terület**|Adatok lefoglalt terület mennyisége és a felhasznált adatok lemezterület közötti különbség.|Ez a mennyiség jelenti a legnagyobb szabad terület arányát az adatbázis-adatfájlok zsugorításával igényelheti.|
|**Adatok maximális mérete**|Adatbázis adatainak tárolására szolgáló terület a maximális mennyisége.|Adatok lefoglalt terület mennyisége meghaladja a maximális adatméret nem növelhető.|
||||

A következő ábra szemlélteti a különböző típusú tárterület-adatbázis közötti kapcsolat.

![tárolási hely adattípusok és a kapcsolatok](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>Önálló adatbázis tárolási hely információk lekérdezése

A következő lekérdezések segítségével határozza meg a tárolási terület mennyisége egyetlen adatbázishoz.  

### <a name="database-data-space-used"></a>Használt adatbázis-adatterület

Módosítsa a következő lekérdezést, a használt adatbázis-adatok terület mennyiségét adja vissza.  A lekérdezés eredményének egységek olyan MB-ban.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Adatbázis adatait a lefoglalt terület és a fel nem használt lefoglalt terület

Használja a következő lekérdezést a adatbázis adatait a lefoglalt terület mennyisége és a fel nem használt lefoglalt terület mennyisége visszaadása.  A lekérdezés eredményének egységek olyan MB-ban.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-data-max-size"></a>Adatbázis adatainak maximális mérete

Módosítsa a következő lekérdezés az adatbázis adatainak maximális méretét adja vissza.  A lekérdezés eredményének egységek bájtban vannak megadva.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Rugalmas készlet tárolási terület típusainak ismertetése

Ismertetése a következő tárolási hely mennyiségek fontosak a fájlhely a rugalmas készlet kezeléséhez.

|Rugalmas készlet mennyiség|Meghatározás|Megjegyzések|
|---|---|---|
|**Adatok felhasznált lemezterület**|A rugalmas készletben található összes adatbázis által használt adatok területet a háttéradatok.||
|**Adatok lefoglalt terület**|A rugalmas készletben található összes adatbázis által lefoglalt adatok terület a háttéradatok.||
|**De nem használt lefoglalt adatok terület**|Adatok lefoglalt terület mennyisége és a rugalmas készletben található összes adatbázis által használt adatok hely közötti különbség.|Ez a mennyiség a legnagyobb a rugalmas készlet, amely az adatbázis-adatfájlok zsugorításával igényelheti számára lefoglalt terület jelöli.|
|**Adatok maximális mérete**|A maximális mennyisége, használhatja a rugalmas készlet összes adatbázisa adatok területtel rendelkezik.|A rugalmas készlet számára lefoglalt terület nem haladhatja meg a rugalmas készlet maximális méretét.  Ez az állapot akkor fordul elő, ha majd a program nem használt lefoglalt terület szabadítható adatbázis adatfájlok zsugorításával.|
||||

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Rugalmas készlet tárolási hely információk lekérdezése

A következő lekérdezések segítségével rugalmas készletek tárolási terület mennyisége határozza meg.  

### <a name="elastic-pool-data-space-used"></a>Rugalmas készlet adatokat a felhasznált lemezterület

Módosítsa a következő lekérdezés a rugalmas készlet adatok elfoglalt mennyiségű vissza.  A lekérdezés eredményének egységek olyan MB-ban.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Rugalmas készlet adatokat a lefoglalt terület és a fel nem használt lefoglalt terület

Módosítsa a következő PowerShell-parancsprogram lefoglalt terület felsoroló táblát adja vissza és a fel nem használt lefoglalt tárhely rugalmas készletben található minden egyes adatbázishoz. A tábla rendelések ezeket az adatbázisokat a fel nem használt legnagyobb méretű adatbázisok lefoglalt terület a lehető legkevesebb nem használt lefoglalt terület.  A lekérdezés eredményének egységek olyan MB-ban.  

A lekérdezés eredményeit az egyes a készletben található adatbázisok számára lefoglalt terület is hozzáadhatók együtt a teljes lemezterület meghatározásához a rugalmas készlet számára lefoglalt meghatározásához. A rugalmas készlet lefoglalt terület nem haladhatja meg a rugalmas készlet maximális méretét.  

A PowerShell-parancsprogram szükséges az SQL Server PowerShell-modul – lásd: [letöltése a PowerShell-modul](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module) telepítéséhez.

```powershell
# Resource group name
$resourceGroupName = "rg1" 
# Server name
$serverName = "ls2" 
# Elastic pool name
$poolName = "ep1"
# User name for server
$userName = "name"
# Password for server
$password = "password"

# Get list of databases in elastic pool
$databasesInPool = Get-AzSqlElasticPoolDatabase `
    -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# For each database in the elastic pool,
# get its space allocated in MB and space allocated unused in MB.
  
foreach ($database in $databasesInPool)
{
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn `
        -Database $database.DatabaseName `
        -Username $userName `
        -Password $password `
        -Query $sqlCommand)
}
# Display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort `
    -Property DatabaseDataSpaceAllocatedUnusedInMB `
    -Descending | Format-Table
```

Az alábbi képernyőfelvételen látható egy példa a kimenetre a parancsfájl:

![rugalmas készlet lefoglalt lemezterület és a fel nem használt lefoglalt terület példa](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Rugalmas készlet adatok maximális mérete

Módosítsa a következő T-SQL-lekérdezést, a rugalmas készlet adatok maximális mérete.  A lekérdezés eredményének egységek olyan MB-ban.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Nem használt lefoglalt terület felszabadítását

### <a name="dbcc-shrink"></a>DBCC zsugorítás

Adatbázisok igényelni a nem használt lefoglalt terület azonosították, miután módosítsa a következő parancsban az adatfájlokat az egyes adatbázisok zsugorítása az adatbázis nevét.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

Ez a parancs befolyásolhatja a teljesítményt adatbázisban, amíg fut, és ha lehetséges kell futtatni az alacsony kihasználtságú időszakok során.  

Ezzel a paranccsal kapcsolatos további információkért lásd: [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 

### <a name="auto-shrink"></a>Automatikus – zsugorítás

Azt is megteheti az Automatikus zsugorítás adatbázis esetén is engedélyezhető.  Automatikus zsugorítás csökkenti a felügyelet összetettségét fájlt, és kevesebb hatásos adatbázis teljesítményét, mint `SHRINKDATABASE` vagy `SHRINKFILE`.  Automatikus zsugorítás a rugalmas készletek kezelése a számos adatbázis különösen hasznos lehet.  Azonban az Automatikus zsugorítás kevésbé hatékonyak a címek újraigénylési funkcióját, mint a fájlhely lehet `SHRINKDATABASE` és `SHRINKFILE`.
Automatikus zsugorítás engedélyezéséhez módosítsa az alábbi parancsban az adatbázis nevét.


```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON
```

Ezzel a paranccsal kapcsolatos további információkért lásd: [adatbázis beállítása](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=sql-server-2017) beállítások. 

### <a name="rebuild-indexes"></a>Indexek újraépítése

Után az adatbázis-adatfájlok vannak zsugorítani, indexek töredezetté válhat, és elveszti a teljesítmény optimalizálása hatékonyságát. Ha a teljesítménycsökkenés, majd fontolja meg adatbázis indexek újraépítése. Töredezettség és az indexek újraépítése további információkért lásd: [Reorganize és indexek újraépítése](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>További lépések

- Adatbázis maximális mérete kapcsolatos információkért lásd:
  - [Az Azure SQL Database Virtuálismag-alapú vásárlási modell korlátok egy önálló adatbázis](sql-database-vcore-resource-limits-single-databases.md)
  - [A DTU-alapú vásárlási modell segítségével az önálló adatbázisok számára erőforráskorlátok](sql-database-dtu-resource-limits-single-databases.md)
  - [Az Azure SQL Database Virtuálismag-alapú vásárlási modell a rugalmas készletek korlátai](sql-database-vcore-resource-limits-elastic-pools.md)
  - [A DTU-alapú vásárlási modellt használó rugalmas készletek korlátai erőforrások](sql-database-dtu-resource-limits-elastic-pools.md)
- További információ a `SHRINKDATABASE` paranccsal [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 
- Töredezettség és az indexek újraépítése további információkért lásd: [Reorganize és indexek újraépítése](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

---
title: Fájl címterület-kezelés az Azure SQL Database |} A Microsoft Docs
description: Ezen a lapon azt ismerteti, hogyan kezelheti az Azure SQL Database területe, és miként állapítható meg, ha szeretné-e egy adatbázist, valamint csökkentheti, hogy hogyan végrehajtásához egy adatbázis tömörítése a művelet-mintakódot biztosít.
services: sql-database
author: oslake
manager: craigg
ms.service: sql-database
ms.custom: how-to
ms.topic: conceptual
ms.date: 08/15/2018
ms.author: moslake
ms.openlocfilehash: 498e83e7c312480af6d2eff7d44bd13aee9c55fd
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42055727"
---
# <a name="manage-file-space-in-azure-sql-database"></a>Lapozófájl-terület az Azure SQL Database kezelése
Ez a cikk ismerteti a különböző típusú tárterület az Azure SQL Database és a lépések, amelyeket elvégezhet a fájlhely lefoglalt adatbázisok és rugalmas adatbáziskészletekhez explicit módon kell kezelnie.

## <a name="overview"></a>Áttekintés

Azure SQL Database-ben tároló terület legtöbb metrika megjelenik az Azure portal és a következő API-k mérheti az adatbázisokhoz és rugalmas készletek használt lapok száma:
- Az Azure Resource Manager-alapú API-k metrikák többek között a PowerShell [get-metrikák](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermmetric)
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

Nincsenek munkaterhelési mintákat, ahol a mögöttes adatok fájlok adatbázisok lefoglalt nagyobb, mint a használt adatok oldalak mennyisége válhat.  Ez akkor fordulhat elő, amikor növekszik lefoglalt terület és adatok törlődnek.  Ez azért, mert lefoglalt terület fájl van nem igényli automatikusan vissza adatok törlésekor.  Ilyen esetekben egy adatbázis vagy készlet számára lefoglalt terület előfordulhat, hogy meghaladja a támogatott határértékeket megakadályozzák az adatmennyiség növekedése vagy a teljesítmény szint módosításának megakadályozása, és szükséges a megoldásához adatok fájlok zsugorítása folyamatban.

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

## <a name="query-a-database-for-storage-space-information"></a>Egy adatbázis a tárolási terület adatainak lekérdezése

A következő lekérdezések segítségével határozza meg a tárolási terület mennyisége adatbázishoz.  

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
|**Adatok maximális mérete**|A maximális mennyisége, használhatja a rugalmas készlet összes adatbázisa adatok területtel rendelkezik.|A rugalmas készlet számára lefoglalt terület nem haladhatja meg a rugalmas készlet maximális méretét.  Ha ez történik, majd a fel nem használt lefoglalt terület igényelheti vissza zsugorításával adatbázis adatfájlokat.|
||||

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Rugalmas készlet tárolási hely információk lekérdezése

A következő lekérdezések segítségével rugalmas készletek tárolási terület mennyisége határozza meg.  

### <a name="elastic-pool-data-space-used"></a>Rugalmas készlet adatokat a felhasznált lemezterület
Módosítsa a következő lekérdezés a rugalmas készlet adatok elfoglalt mennyiségű vissza.  A lekérdezés eredményének egységek olyan MB-ban.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Rugalmas készlet adatokat a lefoglalt terület és a fel nem használt lefoglalt terület

Módosítsa a következő PowerShell-parancsprogram lefoglalt terület felsoroló táblát adja vissza és a fel nem használt lefoglalt tárhely rugalmas készletben található minden egyes adatbázishoz. A tábla rendelések azoktól a fel nem használt legnagyobb méretű adatbázisok lefoglalt terület a lehető legkevesebb nem használt lefoglalt terület.  A lekérdezés eredményének egységek olyan MB-ban.  

A lekérdezés eredményeit az egyes a készletben található adatbázisok számára lefoglalt terület is hozzáadhatók együtt a teljes lemezterület meghatározásához a rugalmas készlet számára lefoglalt meghatározásához. A rugalmas készlet lefoglalt terület nem haladhatja meg a rugalmas készlet maximális méretét.  

A PowerShell-parancsprogram szükséges az SQL Server PowerShell-modul – lásd: [letöltése a PowerShell-modul](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module?view=sql-server-2017) telepítéséhez.

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
$databasesInPool = Get-AzureRmSqlElasticPoolDatabase `
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

Adatbázisok igényelni a nem használt lefoglalt terület azonosították, miután módosítsa a következő parancsot az adatfájlokat az egyes adatbázisok zsugorítani.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

Ezzel a paranccsal kapcsolatos további információkért lásd: [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 

> [!IMPORTANT] 
> Fontolja meg az újraépítési adatbázisa indexei után adatbázis-adatfájlok vannak zsugorítani, indexek töredezetté válhat, és elveszti a teljesítmény optimalizálása hatékonyságát. Ha ez történik, az indexeket kell építeni. Töredezettség és az indexek újraépítése további információkért lásd: [Reorganize és indexek újraépítése](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>További lépések

- Adatbázis maximális mérete kapcsolatos információkért lásd:
  - [Az Azure SQL Database Virtuálismag-alapú vásárlási modell korlátok egy önálló adatbázis](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)
  - [A DTU-alapú vásárlási modell segítségével az önálló adatbázisok számára erőforráskorlátok](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases)
  - [Az Azure SQL Database Virtuálismag-alapú vásárlási modell a rugalmas készletek korlátai](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)
  - [A DTU-alapú vásárlási modellt használó rugalmas készletek korlátai erőforrások](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-elastic-pools)
- További információ a `SHRINKDATABASE` paranccsal [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 
- Töredezettség és az indexek újraépítése további információkért lásd: [Reorganize és indexek újraépítése](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

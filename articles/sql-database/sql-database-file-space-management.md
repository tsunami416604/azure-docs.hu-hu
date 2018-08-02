---
title: Fájl címterület-kezelés az Azure SQL Database |} A Microsoft Docs
description: Ezen a lapon azt ismerteti, hogyan kezelheti az Azure SQL Database területe, és miként állapítható meg, ha szeretné-e egy adatbázist, valamint csökkentheti, hogy hogyan végrehajtásához egy adatbázis tömörítése a művelet-mintakódot biztosít.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: how-to
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 1ecc0ce08ef42f5f5935bca29e8269be2ea142f0
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39415147"
---
# <a name="manage-file-space-in-azure-sql-database"></a>Lapozófájl-terület az Azure SQL Database kezelése

Ez a cikk ismerteti a különböző típusú tárterület az Azure SQL Database és a lépések, amelyeket elvégezhet a fájlhely lefoglalt adatbázisok és rugalmas adatbáziskészletekhez az ügyfélnek kell kezelnie.

## <a name="overview"></a>Áttekintés

Azure SQL Database-ben jelenik meg az Azure portal és a következő API-k méretének tármetrikák mérheti az adatbázisokhoz és rugalmas készletek használt lapok száma:
- Az Azure Resource Manager-alapú API-k metrikák többek között a PowerShell [get-metrikák](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermmetric)
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

Nincsenek munkaterhelési mintákat, amelyben a lefoglalt területet az adatbázisok esetében az alapul szolgáló adatfájlok válik nagyobb, mint az adatfájlokat a használt lapok száma. Ebben a forgatókönyvben akkor fordulhat elő, amikor lefoglalt terület nő, és ezután adatok törlődnek. Az adatok törlése esetén a fájl lefoglalt terület van nem igényli automatikusan vissza az adatok törlésekor. Ilyen esetekben lefoglalt terület egy adatbázis vagy készlet haladhatja meg a támogatott maximális korlát beállítása (vagy annak támogatott) az adatbázishoz, és az eredményeként megakadályozzák az adatmennyiség növekedése vagy megakadályozza a teljesítmény szint módosítását, annak ellenére, hogy a ténylegesen használt adatbázis területet a kisebb, mint a maximális lemezterület-korlát. Csökkentése érdekében, szükség lehet az adatbázis az adatbázis, de nem használt lefoglalt lemezterület csökkentésére zsugorítani.

Az SQL Database szolgáltatás automatikusan nem csökkentheti az adatbázis teljesítményét a lehetséges hatás miatt nem használt lefoglalt terület felszabadítását adatbázisfájlokat. Azonban zsugorítását a fájl egy adatbázisban a következő témakörben ismertetett szabadon egyszerre [visszaigénylési fel nem használt lefoglalt terület](#reclaim-unused-allocated-space). 

> [!NOTE]
> Adatfájlok, ellentétben az SQL Database szolgáltatás automatikusan zsugorítja naplófájlok, mivel a művelet nem befolyásolja az adatbázis teljesítményét.

## <a name="understanding-the-types-of-storage-space-for-a-database"></a>A tárolóhely-adatbázis típusú ismertetése

Lapozófájl-terület kezelésére, az adatbázis tárolási egy önálló adatbázis és rugalmas készletek kapcsolatos alábbi feltételeinek értelmezése kell.

|Tárolási hely kifejezés|Meghatározás|Megjegyzések|
|---|---|---|
|**Adatok felhasznált lemezterület**|A 8 KB-os lapokon található adatbázis-adatok tárolására felhasznált lemezterület mennyiségét.|Általában ez a terület nő (csökkenő) használt Beszúrások (törli). Bizonyos esetekben a felhasznált lemezterület nem módosítja a beszúrások, vagy törli az összeg és az adatok a művelet és bármely töredezettség módja, attól függően. Például egy sor minden adatok oldalról törlése nem feltétlenül csökkenthető a használt területet.|
|**Lefoglalt terület**|Mennyisége formázott elérhetővé az adatbázis-adatok tárolására szolgáló területe|A lefoglalt terület automatikusan nő, de soha nem csökkenti a törlése után. Ezt a viselkedést biztosítja, hogy jövőbeli Beszúrások gyorsabb, mivel a helynek nem kell formázni.|
|**De nem használt lefoglalt terület**|Az adatbázis számára lefoglalt fel nem használt adatok fájl terület mennyisége.|Ez a mennyiség lefoglalt terület mennyisége és a felhasznált lemezterület közötti különbséget, és jelenti a legnagyobb adatbázisfájlok zsugorításával igényelheti területtel rendelkezik.|
|**Max. mérete**|Az adatterület, az adatbázis által felhasználható maximális mérete.|Az adatok lefoglalt terület nem lehet nagyobb legyen az adatok maximális méretét.|
||||

A következő ábra szemlélteti a tárolóhely típusú közötti kapcsolatot.

![tárolási hely adattípusok és a kapcsolatok](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-database-for-storage-space-information"></a>Egy adatbázis a tárolási terület adatainak lekérdezése

Határozza meg, ha Ön rendelkezik lefoglalt, de nem használt adatokat egy önálló adatbázis-terület, hogy Kezdésként érdemes lehet felszabadítani, használja az alábbi lekérdezéseket:

### <a name="database-data-space-used"></a>Használt adatbázis-adatterület
Módosítsa a következő lekérdezést a MB-ban használt adatbázis-adatok terület mennyiségét adja vissza.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-allocated-and-allocated-space-unused"></a>Adatbázis adatainak lefoglalva, és a fel nem használt helyet foglalt le
Módosítsa a következő lekérdezést a lefoglalt adatbázis adatok mennyisége és a fel nem használt helyet foglalt le visszaadása.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-max-size"></a>Adatbázis maximális mérete
Módosítsa a következő lekérdezés adja vissza az adatbázis maximális mérete (bájt).

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Rugalmas készlet tárolási hely információk lekérdezése

Határozza meg, ha, de nem használt elkülönített adatterület rugalmas készletben, és minden készletezett adatbázis számára, hogy Kezdésként érdemes lehet felszabadítani, használja a következő lekérdezéseket:

### <a name="elastic-pool-data-space-used"></a>Rugalmas készlet adatokat a felhasznált lemezterület
Módosítsa a következő lekérdezés a rugalmas készlet adatok terület MB-ban mennyiségét adja vissza.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-allocated-and-allocated-space-unused"></a>Rugalmas készlet adatok lefoglalva, és a fel nem használt helyet foglalt le

Módosítsa a következő PowerShell-parancsprogram a lefoglalt teljes lemezterület és a fel nem használt, rugalmas készletben található minden egyes adatbázishoz lefoglalt terület felsoroló táblát adja vissza. A tábla orders lefoglalt terület a legmagasabb szintű rendelkező adatbázisok nem használt lefoglalt terület legkisebb mennyiség fel nem használt.  

A lekérdezés eredményeit az egyes a készletben található adatbázisok számára lefoglalt terület meghatározásához a állapítsa meg a rugalmas készlet terület lefoglalt együtt is hozzáadhatók. A rugalmas készlet lefoglalt terület nem haladhatja meg a rugalmas készlet maximális méretét.  

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
# Requires SQL Server PowerShell module – see here to install.  
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

### <a name="elastic-pool-max-size"></a>Rugalmas készlet maximális méret

A következő T-SQL-lekérdezés használatával adja vissza a rugalmas adatbázis maximális mérete (MB).

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Nem használt lefoglalt terület felszabadítását

Miután eldöntötte, hogy a fel nem használt lefoglalt terület, akinél szeretné felszabadítani, használja az alábbi parancsot az adatbázishoz lefoglalt terület zsugorítani. 

> [!IMPORTANT]
> A rugalmas készletben található adatbázisok esetében adatbázis, a legtöbb lefoglalt terület a fel nem használt területe leggyorsabb visszaigényléséhez kell zsugorítható meg először.  

Az alábbi parancs segítségével csökkentheti az adatfájlokat a megadott adatbázis összes:

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'<database_name>')
```

Ezzel a paranccsal kapcsolatos további információkért lásd: [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).

> [!IMPORTANT] 
> Fontolja meg az újraépítési adatbázisa indexei után adatbázis-adatfájlok vannak zsugorítani, indexek töredezetté válhat, és elveszti a teljesítmény optimalizálása hatékonyságát. Ha ez történik, az indexeket kell építeni. Töredezettség és az indexek újraépítése további információkért lásd: [Reorganize és indexek újraépítése](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>További lépések

- Maximális adatbázis-méretekkel kapcsolatos információért lásd:
  - [Az Azure SQL Database Virtuálismag-alapú vásárlási modell korlátok egy önálló adatbázis](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)
  - [A DTU-alapú vásárlási modell segítségével az önálló adatbázisok számára erőforráskorlátok](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases)
  - [Az Azure SQL Database Virtuálismag-alapú vásárlási modell a rugalmas készletek korlátai](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)
  - [A DTU-alapú vásárlási modellt használó rugalmas készletek korlátai erőforrások](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-elastic-pools)
- További információ a `SHRINKDATABASE` paranccsal [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 
- Töredezettség és az indexek újraépítése további információkért lásd: [Reorganize és indexek újraépítése](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).
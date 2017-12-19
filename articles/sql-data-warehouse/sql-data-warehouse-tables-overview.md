---
title: "Az SQL Data Warehouse táblák áttekintése |} Microsoft Docs"
description: "Ismerkedés az Azure SQL Data Warehouse tábláiba."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 2114d9ad-c113-43da-859f-419d72604bdf
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 12/14/2017
ms.author: barbkess
ms.openlocfilehash: 46f7d2ea19a88e65b2d039fdf36d1619c4d74020
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="overview-of-tables-in-sql-data-warehouse"></a>Az SQL Data Warehouse táblák áttekintése
> [!div class="op_single_selector"]
> * [– Áttekintés][Overview]
> * [Adattípusok][Data Types]
> * [Terjesztése][Distribute]
> * [Index][Index]
> * [Partíció][Partition]
> * [Statisztika][Statistics]
> * [Ideiglenes][Temporary]
> 
> 

Ismerkedés az SQL Data Warehouse-táblázatok létrehozása felettébb egyszerű.  A basic [CREATE TABLE] [ CREATE TABLE] szintaxis követi a legnagyobb valószínűséggel közös szintaxist már ismeri, a más adatbázisok nem működnek.  Hozzon létre egy táblát, egyszerűen szüksége a tábla neve, az oszlopok neve és az egyes oszlopok adattípusok definiálása.  Ha más adatbázisokban is hozzon létre táblák, ez Bizonyára ismerős nagyon Önnek.

```sql  
CREATE TABLE Customers (FirstName VARCHAR(25), LastName VARCHAR(25))
 ``` 

A fenti példa létrehoz egy két oszlop, az utónév és Vezetéknév ügyfelek nevű táblát.  Egyes oszlopok VARCHAR(25), amely korlátozza a 25 karakterből kell az adatok típusú adatokat van definiálva.  Egy tábla, valamint más, alapvető attribútumaihoz megegyeznek többnyire más adatbázisok.  Adattípusok minden egyes oszlophoz van definiálva, és az adatok integritásának biztosításában.  Indexek teljesítményének javításával i/o csökkentésével lehet hozzáadni.  Particionálás teljesítményének javításával, ha módosítania kell adatokat lehet hozzáadni.

[Átnevezése] [ RENAME] egy SQL Data Warehouse tábla néz ki:

```sql  
RENAME OBJECT Customer TO CustomerOrig; 
 ```

## <a name="distributed-tables"></a>Elosztott táblák
Elosztott rendszerek például az SQL Data warehouse által bevezetett új alapvető attribútum a **terjesztési oszlop**.  A terjesztési oszlop túlságosan azok milyen úgy tűnik, hogy hasonló.  Az oszlop, amely meghatározza, hogyan kell terjeszteni, vagy ossza, az adatok a háttérben is.  Ha a terjesztési oszlopok megadása nélkül hozunk létre táblát, a tábla a rendszer automatikusan továbbítja használatával **ciklikus multiplexelés**.  Ciklikus multiplexelés táblák elegendő bizonyos esetekben lehetnek, terjesztési oszlopok meghatározása csökkenti adatátvitelt jelölik a lekérdezések, így a teljesítmény optimalizálása során.  Olyan esetekben ha kis mennyiségű táblákban tárolt adatokat, válassza a tábla létrehozásához a **replikálása** telepítési típus minden számítási csomópont másolja az adatokat, és menti a adatátvitelt jelölik a lekérdezések végrehajtása során. Lásd: [terjesztése egy tábla] [ Distribute] további információt a terjesztési oszlop kiválasztása.

## <a name="indexing-and-partitioning-tables"></a>Indexelő és táblák particionálása
Válnak fejlettebb, az SQL Data Warehouse használatával, és szeretné, hogy a teljesítmény optimalizálása, érdemes Táblatervezés tájékozódhat.  További tudnivalókért tekintse meg a cikkek [tábla adattípusok][Data Types], [terjesztése egy tábla][Distribute], [tábla indexelő] [ Index] és [tábla particionáló][Partition].

## <a name="table-statistics"></a>Tábla statisztikai adatainak
A statisztikákat egy rendkívül fontos, hogy a lehető legjobb teljesítményt nehézséget okoz az SQL Data Warehouse.  Mivel az SQL Data Warehouse automatikusan még nem létrehozása, és statisztikai adatainak frissítése, például az Azure SQL Database számítson rendelkezik olyan, a cikk elolvasása a [statisztika] [ Statistics] lehet az egyik legfontosabb cikkekben elolvasta biztosítják, hogy a legjobb teljesítmény érdekében a lekérdezésből.

## <a name="temporary-tables"></a>Ideiglenes táblák
Az ideiglenes táblák olyan táblák csak a bejelentkezési időtartama létezik, és más felhasználók által nem láthatók.  Az ideiglenes táblák lehet szeretné megakadályozni, hogy mások ideiglenes eredmények megtekintése és karbantartása szükségességét is csökkentheti.  Az ideiglenes táblák is a helyi tároló használatára, mivel egyes műveletek esetében gyorsabb teljesítményt is biztosítanak.  Tekintse meg a [ideiglenes tábla] [ Temporary] cikkek az ideiglenes táblák további információt.

## <a name="external-tables"></a>Külső táblák
Külső táblák, más néven a Polybase táblák olyan táblák, amely az SQL Data Warehouse, de pont lekérdezhetők, az SQL Data Warehouse külső adatokhoz.  Például pontot hozhat létre a külső tábla mely Azure Blob Storage vagy az Azure Data Lake Store-fájlokba.  Hogyan hozhat létre, és a külső tábla lekérdezése a további részletekért lásd: [adatok betöltése a Polybase][Load data with Polybase].  

## <a name="unsupported-table-features"></a>A tábla nem támogatott funkciók
Az SQL Data Warehouse számos más adatbázisok által kínált tábla funkciót tartalmaz, amíg nincsenek néhány funkció, amely még nem támogatottak.  Alább felsoroljuk a néhány tábla funkciója még nem támogatott.

| Nem támogatott funkciók |
| --- |
| Elsődleges kulcs, külső kulcsok, egyedi és a jelölőnégyzet [tábla megkötések][Table Constraints] |
| [Egyedi indexek][Unique Indexes] |
| [A számított oszlopok][Computed Columns] |
| [Ritka oszlopokat][Sparse Columns] |
| [Felhasználó által definiált típusok][User-Defined Types] |
| [Feladatütemezési][Sequence] |
| [Eseményindítók][Triggers] |
| [Az indexelt nézetek][Indexed Views] |
| [Szinonimák][Synonyms] |

## <a name="table-size-queries"></a>Tábla méretét lekérdezések
Egy hely és egy táblázat minden a 60 azokat a terjesztéseket, felhasznált sorok legegyszerűbb használandó [DBCC PDW_SHOWSPACEUSED][DBCC PDW_SHOWSPACEUSED].

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Azonban a DBCC-parancsokkal is kell meglehetősen korlátozása.  Dinamikus felügyeleti nézetekkel (dinamikus felügyeleti nézetek) lehetővé teszi a sokkal részletesebben lásd, valamint a lekérdezési eredmények sokkal nagyobb szabályozható.  Először hozzon létre ebben a nézetben keresztül ez, és további cikkeit a jelen példában számos hivatkoznak.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### <a name="table-space-summary"></a>Összefoglaló táblázat terület
Ez a lekérdezés táblázat adja vissza a sorok és a helyet.  Tekintse meg a táblák a legnagyobb táblák, és azok csatlakoznak-e időszeletelés, replikált vagy elosztott kivonatoló kiváló lekérdezés is.  A kivonattáblákkal elosztott azt is látható a terjesztési oszlop.  A legtöbb esetben a legnagyobb kell szerepelnie. a fürtözött oszlopcentrikus indexszel rendelkező elosztott kivonatát.

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Tábla hely telepítési típusonként
```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Tábla terület index típus szerint
```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Terjesztési hely összefoglaló
```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Következő lépések
További tudnivalókért tekintse meg a cikkek [tábla adattípusok][Data Types], [terjesztése egy tábla][Distribute], [tábla indexelő][Index], [tábla particionáló][Partition], [fenntartása a tábla statisztikai adatainak] [ Statistics] és [az ideiglenes táblák][Temporary].  Gyakorlati tanácsok kapcsolatban bővebben lásd: [SQL Data Warehouse gyakorlati tanácsok][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md
[Load data with Polybase]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md

<!--MSDN references-->
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[Table Constraints]: https://msdn.microsoft.com/library/ms188066.aspx
[Computed Columns]: https://msdn.microsoft.com/library/ms186241.aspx
[Sparse Columns]: https://msdn.microsoft.com/library/cc280604.aspx
[User-Defined Types]: https://msdn.microsoft.com/library/ms131694.aspx
[Sequence]: https://msdn.microsoft.com/library/ff878091.aspx
[Triggers]: https://msdn.microsoft.com/library/ms189799.aspx
[Indexed Views]: https://msdn.microsoft.com/library/ms191432.aspx
[Synonyms]: https://msdn.microsoft.com/library/ms177544.aspx
[Unique Indexes]: https://msdn.microsoft.com/library/ms188783.aspx

<!--Other Web references-->

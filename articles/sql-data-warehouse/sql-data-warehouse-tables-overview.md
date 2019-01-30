---
title: Táblák – Azure SQL Data Warehouse kialakítása |} A Microsoft Docs
description: Bevezetés az Azure SQL Data Warehouse táblák tervezése.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 290230237a68730a908c6fd0fb0df1d63035b93b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247340"
---
# <a name="designing-tables-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse táblák tervezése

Ismerje meg az Azure SQL Data Warehouse táblák tervezése alapfogalmak. 

## <a name="determine-table-category"></a>Határozza meg a tábla kategória 

A [csillagsémával](https://en.wikipedia.org/wiki/Star_schema) tény- és dimenziótáblákból rendezik az adatokat. Mielőtt áthelyezi táblához (tény) vagy a dimenzió azt táblák integráció vagy az átmeneti adatok használhatók. Egy tábla kialakításakor, döntse el, e a táblaadatokat tartozik (tény), dimenzió vagy integrációs tábla. Ezt a döntést arról tájékoztatja a megfelelő táblaszerkezet és a terjesztési. 

- **A ténytáblák** mennyiségi adatokat tartalmaznak, amelyek gyakran jönnek létre egy tranzakciós rendszerben, és aztán betölti a data warehouse-bA. Például egy kiskereskedelmi vállalkozások hoz létre az értékesítési tranzakció naponta, és majd betölti az adatokat egy adatraktár-ténytábla számoszlopaira elemzés céljából.

- **Dimenziótáblák** attribútum adatokat tartalmaznak, amelyek változhatnak, de általában csak ritkán változnak. Például egy ügyfél nevét és címét vannak egy dimenziótáblával tárolja, és frissítve, csak ha az ügyfél profil változik. Egy nagyméretű ténytábla méretének minimalizálása érdekében az ügyfél nevét és címét nem kell lenniük minden sorában egy ténytáblát. Ehelyett a ténytáblát és a dimenziótáblában oszthatnak meg egy ügyfél-azonosítót. A lekérdezés a két táblázat ügyfél profil és a tranzakciók csatlakozhat. 

- **Integráció táblák** integrálásával, vagy az adatok egy helyen adja meg. Egy integrációs tábla normál táblákhoz, egy külső táblát vagy egy ideiglenes táblát hozhat létre. Például adatok betöltése az előkészítési táblába, átalakításokat az adatokat az átmeneti állapotában is, és helyezze az adatokat egy éles táblába.

## <a name="schema-and-table-names"></a>Séma és a táblázat neve
Az SQL Data Warehouse hogy az adattárház az adatbázisok egy típusa. Az adatraktárban lévő táblák mindegyike ugyanabban az adatbázisban található.  Nem lehet csatlakozni, táblák között több adattárházzal. Ez a viselkedés eltér az SQL-kiszolgálóra, amely támogatja az adatbázisok közti összekapcsolásokat. 

SQL Server-adatbázis előfordulhat, hogy használja a dimenzió-, tény vagy integrálása a sémanevek a. Ha az áttelepítés egy SQL Server-adatbázis az SQL Data Warehouse, azt működik a legjobban a tény, dimenziót és integrációs táblák mindegyike egy sémát az SQL Data Warehouse áttelepítése. Ha például az összes tábla tárolhatja a [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap) minta-adattárházon belül wwi nevű egy sémát. Az alábbi kód létrehoz egy [felhasználó által definiált séma](/sql/t-sql/statements/create-schema-transact-sql) wwi nevezik.

```sql
CREATE SCHEMA wwi;
```

Az SQL Data Warehouse, a szervezet a táblák megjelenítése (tény), a (dimenzió) és a int sikerült használata, a táblanevek előtagokat. Az alábbi táblázat néhány a sémára és táblára nevét a WideWorldImportersDW. Összehasonlítja a neve, az SQL Server és az SQL Data Warehouse neve. 

| WideWorldImportersDW tábla  | Tábla típusa | SQL Server | SQL Data Warehouse |
|:-----|:-----|:------|:-----|
| Város | Dimenzió | Dimension.City | wwi.DimCity |
| Rendelés | (Tény) | Fact.Order | wwi.FactOrder |


## <a name="table-persistence"></a>Tábla adatmegőrzés 

Táblák véglegesen az Azure Storage-ban, ideiglenesen az Azure Storage-ban vagy a data warehouse-bA külső tárolóban tárolhatja az adatokat.

### <a name="regular-table"></a>Normál táblákhoz

Normál adatokat az Azure Storage, az adatraktár részeként tárolja. A táblázat és az adatok megőrzése, függetlenül attól, hogy a munkamenet nyitva-e.  Ebben a példában két oszlopot hoz létre egy normál táblákhoz. 

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Az ideiglenes tábla
A munkamenet időtartama alatt csak létezik egy ideiglenes táblát. Egy ideiglenes táblát is használhatja, megakadályozza, hogy más célra ideiglenes eredmények jelennek meg és a karbantartásához csökkentése érdekében.  Ideiglenes táblák is a helyi tárolók használatára, mivel ezek néhány művelet nagyobb teljesítményt kínálnak.  További információkért lásd: [ideiglenes táblák](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Külső tábla
A külső tábla mutat az Azure Storage-blobból vagy Azure Data Lake Store található adatokat. A CREATE TABLE AS SELECT utasítás együtt használja, ha egy külső táblából importálja az adatokat az SQL Data warehouse-bA. Külső táblák ezért hasznosak lehetnek az adatok betöltéséhez. Betöltési oktatóanyag: lásd: [bA a PolyBase használatával az adatok betöltése az Azure blob storage-ból](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Adattípusok
Az SQL Data Warehouse által támogatott adattípusok a leggyakrabban használt. A támogatott adattípusokat listáját lásd: [adattípusok a CREATE TABLE referencia](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) a CREATE TABLE utasításban. Az adattípusok méretének minimalizálása segít a lekérdezési teljesítmény javításához. Adattípusok használatával kapcsolatos útmutatóért lásd: [adattípusok](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Elosztott táblák
Az SQL Data Warehouse alapvető jellemzője, tárolására és 60 közötti a táblák művelethez [disztribúciók](massively-parallel-processing-mpp-architecture.md#distributions).  A táblák Ciklikus időszeleteléses, vagy a replikációs módszer használatával vannak osztva.

### <a name="hash-distributed-tables"></a>Kivonatoló elosztott táblák
A kivonatoló terjesztéssel osztja el a sorokat, a terjesztési oszlopban szereplő érték alapján. A kivonatoló elosztott tábla célja a lekérdezési összekapcsolásokat, a nagyméretű táblák a magas teljesítmény eléréséhez. Nincsenek számos tényező befolyásolja az elosztási oszlop kiválasztása. 

További információkért lásd: [tervezési útmutató a elosztott táblák](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Replikált táblák
Egy replikált tábla rendelkezik elérhető táblák teljes másolatának minden számítási csomóponton. Futtassa a gyors lekérdezések replikált táblákról, mivel a replikált táblák illesztések nem igénylik az adatmozgatás. Replikációs extra tárterület szükséges, és nem gyakorlati nagy táblák esetében. 

További információkért lásd: [tervezési útmutató a replikált táblák](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Ciklikus időszeleteléses táblák
Ciklikus időszeleteléses tábla táblasorokat egyenlően elosztja minden disztribúcióján használhatók. A sorok véletlenszerűen oszlanak meg. Adatok betöltése egy Ciklikus időszeleteléses táblába gyors.  Lekérdezések azonban további adatmozgatás, mint az egyéb telepítési módszerek is szükséges. 

További információkért lásd: [tervezési útmutató a elosztott táblák](sql-data-warehouse-tables-distribute.md).


### <a name="common-distribution-methods-for-tables"></a>Táblák közös terjesztési módok
A tábla kategória határozza meg, melyik lehetőség kiválasztása a terjesztés az a tábla gyakran. 

| Tábla kategória | Ajánlott telepítési lehetőség |
|:---------------|:--------------------|
| (Tény)           | Használja a kivonatoló terjesztési fürtözött oszlopcentrikus indexszel rendelkező. Javítja a teljesítményt, ha a két kivonattáblák csatlakoznak a terjesztési ugyanabban az oszlopban. |
| Dimenzió      | Replikált táblák kisebb használja. Ha táblákat tárolásához minden számítási csomóponton túl nagy, használja a kivonatoló elosztott. |
| Fájlok másolása folyamatban        | Használjon ciklikus időszeletelést az átmeneti tárolási tábla. A CTAS terhelés gyors. Amint az adatok az előkészítési táblában lévő, használja az INSERT... Válassza ki az adatok áthelyezése a termelési táblákba. |

## <a name="table-partitions"></a>Táblapartíciók
Particionált tábla tárolja, és a táblázat sorait az adattartomány alapján műveleteket hajt végre. Például egy táblázat sikerült kell particionálni, naponta, hónap vagy év szerint. Javíthatja a lekérdezési teljesítmény keresztül partíció eltávolítási, amely korlátozza a lekérdezés megvizsgálja, az adatok egy partíción belül. Emellett akkor is fenntartható a partíció közötti váltás az adatokat. Az adatokat az SQL Data Warehouse már terjesztve van, mert túl sok partíció lelassíthatja a lekérdezések teljesítményét. További információkért lásd: [particionálási útmutató](sql-data-warehouse-tables-partition.md).

## <a name="columnstore-indexes"></a>Oszlopcentrikus indexek
Alapértelmezés szerint az SQL Data Warehouse tárolja egy tábla egy fürtözött oszlopcentrikus indexet. Az ilyen típusú adatok tárolási éri el a magas az adattömörítés és a lekérdezési teljesítmény nagy táblák.  A fürtözött oszlopcentrikus index általában a legjobb választás, de bizonyos esetekben egy fürtözött index, vagy egy halommemóriában a megfelelő tárolási struktúra.

Oszlopcentrikus szolgáltatások listáját lásd: [Újdonságok az oszlopcentrikus indexek](/sql/relational-databases/indexes/columnstore-indexes-what-s-new). Tekintse meg az oszlopcentrikus indexet a teljesítmény javítása érdekében [sorcsoport minőségi az oszlopcentrikus indexek maximalizálása](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Statisztika
A lekérdezésoptimalizáló oszlopszintű statisztikai használja, amikor létrehozza a lekérdezést végrehajtó tervezése. Lekérdezési teljesítmény javításához fontos létrehozni statisztikákat a egyes oszlopain, különösen a lekérdezés alkalmazásban használt oszlopokat. Statisztikák létrehozása és frissítése nem történik meg automatikusan. [Statisztika létrehozása](/sql/t-sql/statements/create-statistics-transact-sql) , tábla létrehozása után. Statisztika frissítése után jelentős számú sort is vehetők fel vagy módosíthatók. Ha például a betöltés után statisztika frissítése. További információkért lásd: [statisztika útmutatást](sql-data-warehouse-tables-statistics.md).

## <a name="commands-for-creating-tables"></a>Parancsok a táblázatok létrehozásával
Létrehozhat egy táblát, egy új üres táblát. Hozhat létre, és töltse fel egy táblát egy kiválasztási utasítás eredményeivel. Az alábbiakban a tábla létrehozása a T-SQL parancsokkal.

| T-SQL Statement | Leírás |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | Egy üres táblát hoz létre a tábla oszlopait és a beállítások megadásával. |
| [KÜLSŐ TÁBLA LÉTREHOZÁSA](/sql/t-sql/statements/create-external-table-transact-sql) | Egy külső táblát hoz létre. A tábla definícióját az SQL Data Warehouse tárolja. A tábla adatait az Azure Blob storage vagy az Azure Data Lake Store van tárolva. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | Megjelenik egy új táblát egy kiválasztási utasítás eredményeivel. A tábla és oszlop adattípusok a kiválasztási utasítás eredményei alapulnak. Adatok importálása a jelen nyilatkozat jelölje be egy külső táblából. |
| [EXTERNAL TABLE AS SELECT LÉTREHOZÁSA](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | Létrehoz egy új külső táblát egy kiválasztási utasítás eredményei exportálásával egy külső helyre.  A hely az Azure Blob storage vagy Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Forrásadatok együttműködhessenek az adatraktárban

Adatok betöltése egy másik adatforrás által az adattárház táblái fel van töltve. Betöltés sikeres végrehajtásához az oszlopok a forrásadatok a szám és adattípusok a tábla definícióját az adatraktárban kell igazodva. Való megfelelés érdekében az adatok beolvasása a a táblák tervezése nagyon nehéz része lehet. 

Adatok különböző várható, ha lekérte az adatokat az adattárház, és tárolja-integráció táblában. Amint adatok az integrációs táblázatban, használhatja a teljesítmény, az SQL Data Warehouse átalakítási műveletek végrehajtásához. Ha készen áll az adatok, termelési táblákba beilleszthető.

## <a name="unsupported-table-features"></a>A tábla nem támogatott funkciók
Az SQL Data Warehouse számos támogatja, de nem az összes, a tábla funkciók szavatolják más adatbázisok.  Az alábbi lista tartalmazza, amelyek nem támogatottak az SQL Data Warehouse tábla funkcióit.

- Elsődleges kulcs, külső kulcsok, ellenőrizze, egyedi [táblamegkötések](/sql/t-sql/statements/alter-table-table-constraint-transact-sql)

- [A számított oszlopok](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql)
- [Az indexelt nézetek](/sql/relational-databases/views/create-indexed-views)
- [Feladatütemezés](/sql/t-sql/statements/create-sequence-transact-sql)
- [Ritka oszlop](/sql/relational-databases/tables/use-sparse-columns)
- Helyettes kulcs. Valósíthatók meg [identitás](sql-data-warehouse-tables-identity.md).
- [A szinonimák](/sql/t-sql/statements/create-synonym-transact-sql)
- [Eseményindítók](/sql/t-sql/statements/create-trigger-transact-sql)
- [Egyedi indexek](/sql/t-sql/statements/create-index-transact-sql)
- [Felhasználó által meghatározott típusok](/sql/relational-databases/native-client/features/using-user-defined-types)

## <a name="table-size-queries"></a>Tábla méretét lekérdezések
Egy egyszerű módja, terület és a egy tábla minden egyes a 60 elosztás által felhasznált sorok [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Azonban a DBCC-parancsok használatával meglehetősen járhatnak.  Dinamikus felügyeleti nézetekkel (DMV-kkel), mint a DBCC-parancsok további részletek megjelenítése. Először hozzon létre ebben a nézetben.

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

Ez a lekérdezés a sorok és a hely táblát ad vissza.  Ez lehetővé teszi, hogy tekintse meg, mely a legnagyobb táblákba végzett, és hogy azokat Ciklikus időszeleteléses replikálja, vagy ujjlenyomat - terjesztése.  Kivonatoló elosztott tábla a lekérdezés megjeleníti az elosztási oszlop.  

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

### <a name="table-space-by-distribution-type"></a>Tábla hely terjesztési típus szerint

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

### <a name="distribution-space-summary"></a>Telepítési hely összegzése

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

## <a name="next-steps"></a>További lépések
Miután létrehozta a táblák a data warehouse-hoz, a következő lépés az adatok betöltése a táblába.  Betöltési oktatóanyag: lásd: [adatok betöltése az SQL Data Warehouse a](load-data-wideworldimportersdw.md).

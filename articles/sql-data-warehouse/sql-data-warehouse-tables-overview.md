---
title: Táblák - Azure SQL Data Warehouse tervezése |} Microsoft Docs
description: Bevezetés az Azure SQL Data Warehouse-táblázathoz.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: d299ff0d8e719040d503852af6056d9d87738b7d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31527939"
---
# <a name="designing-tables-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse táblák tervezése

Ismerje meg az Azure SQL Data Warehouse táblák tervezéséhez főbb fogalmait. 

## <a name="determine-table-category"></a>Határozza meg a tábla kategória 

A [csillagséma](https://en.wikipedia.org/wiki/Star_schema) tény- és dimenziótáblák táblákba alapul. Néhány tábla használ az integrációs vagy az átmeneti adatok előtt áthelyezi azt a tényt vagy dimenzió táblához. Tervezésének abban a táblázatban, döntse el, hogy a tábla adatai a tényt, dimenzió vagy integrációs tábla tartozik. Ez a döntés arról értesíti a megfelelő struktúrát és a terjesztési. 

- **A ténytáblák** tranzakciós rendszerben gyakran jönnek létre, és majd betölti az adatraktár mennyiségi adatokat tartalmaznak. Például egy kereskedelmi üzleti értékesítési tranzakciók minden nap létrehoz és majd betölti az adatokat a egy adatraktár-ténytábla elemzés céljából.

- **Táblák dimenzió** változhatnak, de általában ritkán változó attribútum adatokat tartalmaznak. Például az ügyfél nevét és címét dimenzió táblában tárolt és frissítése, csak ha a felhasználói profil változik. A nagy ténytábla méretének minimalizálása érdekében a felhasználói nevét és címét nem kell lenniük egy ténytábla minden sorában. Ehelyett a ténytábla és a dimenziótáblában oszthatnak meg egy felhasználói azonosítót. A lekérdezés hozzárendelni egy felhasználói profil és a tranzakciókért a két tábla kapcsolhatja össze. 

- **Integráció táblák** adjon meg egy olyan helyre integrálása vagy átmeneti adatokat. Az integráció a táblázatban egy rendszeres, külső táblát, vagy egy ideiglenes táblában hozhat létre. Például az átmeneti tárolási tábla adatainak betöltése, az átmeneti található adatok átalakításokat hajthattak végre, és helyezze az adatokat egy éles táblába.

## <a name="schema-and-table-names"></a>Séma és a táblázat neve
Az SQL Data Warehouse az adatraktár adatbázis típusú. Az adatraktárban lévő táblák összes belül az azonos adatbázist tartalmazza.  Táblák közötti több adatraktárak nem lehet csatlakozni. Ez a viselkedés eltér SQL Server, amely támogatja az adatbázisok közötti illesztéseket. 

SQL Server-adatbázisban előfordulhat, hogy a tény-dimenzió, vagy integrálható a séma neve. Ha az áttelepítés egy SQL Server-adatbázist az SQL Data Warehouse, legmegfelelőbb összes tényt, dimenziót és integrációs tábla áttelepítéséhez az SQL Data Warehouse egy séma. Például az összes tábla tárolhatja a [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap) minta adatraktár wwi nevű egy sémáján belül. Az alábbi kód létrehoz egy [felhasználói séma](/sql/t-sql/statements/create-schema-transact-sql) wwi nevezik.

```sql
CREATE SCHEMA wwi;
```

Az SQL Data Warehouse a szervezet a táblák megjelenítéséhez használható tény dim és int előtagok a táblanevek. Az alábbi táblázat néhány WideWorldImportersDW sémára és táblára nevek mutatja be. Összehasonlítja a neve, az SQL Server és az SQL Data Warehouse neve. 

| WideWorldImportersDW tábla  | Tábla típusa | SQL Server | SQL Data Warehouse |
|:-----|:-----|:------|:-----|
| Város | Dimenzió | Dimension.City | wwi.DimCity |
| Sorrend | (Tény) | Fact.Order | wwi.FactOrder |


## <a name="table-persistence"></a>Tábla adatmegőrzési 

Táblák adattárolásra véglegesen az Azure Storage-ban, ideiglenesen az Azure Storage vagy a külső adatraktár tárolóban.

### <a name="regular-table"></a>Normál táblákhoz

Normál táblákhoz adatot tárol az Azure Storage az adatraktár részeként. A táblázat és az adatok megmaradnak, függetlenül attól, hogy a munkamenet megnyitása.  Ez a példa normál táblákhoz két olyan oszlopot hoz létre. 

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Ideiglenes tábla
A munkamenet időtartama alatt csak létezik egy ideiglenes tábla. Egy ideiglenes tábla használható egyéb felhasználásra megakadályozása ideiglenes eredmények megtekintése és karbantartása szükség csökkentése érdekében.  Az ideiglenes táblák is a helyi tároló használatára, mivel egyes műveletek esetében gyorsabb teljesítményt is biztosítanak.  További információkért lásd: [az ideiglenes táblák](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Külső tábla
A külső tábla mutat, Azure Storage-blobba vagy az Azure Data Lake Store lévő adatokat. A CREATE TABLE AS SELECT utasítás együtt használják, ha egy külső táblából importálja az adatokat az SQL Data Warehouse. Külső táblák ezért hasznosak adatok betöltésekor. Betöltés oktatóanyagok esetén lásd: [adatok betöltése az Azure blob storage használata a PolyBase](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Adattípusok
Az SQL Data Warehouse támogatja a leggyakrabban használt adattípusokat. A támogatott adattípusok listájáért lásd: [létrehozása táblahivatkozás adattípusok](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) a CREATE TABLE utasításban. Adattípusok méretének minimalizálása segít a lekérdezési teljesítmény javítása érdekében. Az adattípusok használatával útmutatóért lásd: [adattípusok](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Elosztott táblák
Az SQL Data Warehouse alapvető jellemzője, tárolására és működhet a táblák között 60 [terjesztéseket](massively-parallel-processing-mpp-architecture.md#distributions).  A táblák terjesztése ciklikus multiplexelés, a kivonatoló vagy a replikációs módszerrel.

### <a name="hash-distributed-tables"></a>Kivonatoló elosztott táblák
A kivonatoló terjesztési osztja el a terjesztési oszlopban szereplő érték alapján sorokat. Célja, hogy a kivonat-elosztott tábla lekérdezés táblákra nagy táblák magas teljesítmény eléréséhez. Nincsenek számos tényező befolyásolja a terjesztési oszlop kiválasztása. 

További információkért lásd: [tervezési útmutató elosztott táblák](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Replikált táblák
A replikált tábla minden számítási csomópont a rendelkezésre álló tábla teljes másolata rendelkezik. Futtassa a gyors lekérdezések replikált táblák óta illesztések replikált táblák nem igényelnek adatátvitelt jelölik. Replikációs megnövelt tárhely szükséges, és nem nagy méretű táblákra vonatkozó gyakorlati. 

További információkért lásd: [kialakítási útmutató a replikált táblák](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Ciklikus multiplexelés táblák
Ciklikus multiplexelés tábla elosztása táblázat sorait egyenletesen valamennyi felosztást. A sorok véletlenszerűen terjesztése. Adatok betöltése a ciklikus multiplexelési táblába gyors.  Lekérdezések azonban további adatátvitelt jelölik a többi terjesztési módszernél lehet szükség. 

További információkért lásd: [tervezési útmutató elosztott táblák](sql-data-warehouse-tables-distribute.md).


### <a name="common-distribution-methods-for-tables"></a>Táblák közös telepítési módszerei
A tábla kategória gyakran határozza meg, melyik lehetőséget válassza a táblázatban terjesztéséhez. 

| Tábla kategória | Ajánlott terjesztési beállítás |
|:---------------|:--------------------|
| (Tény)           | Használja a kivonatoló terjesztési fürtözött oszlopcentrikus indexszel rendelkező. Teljesítmény javítja, abban az esetben, amikor két kivonattáblákkal kapcsolódnak-e a terjesztési ugyanarra az oszlopra. |
| Dimenzió      | Replikált táblák kisebb használja. Ha táblákat minden számítási csomóponton tárolására túl nagy, használja a kivonatoló elosztott. |
| Fájlok másolása folyamatban        | Ciklikus multiplexelés használja az átmeneti tárolási tábla. A terhelés CTAS a gyors. Ha az adatokat az átmeneti tárolási tábla, használja az INSERT... Válassza ki az adatok áthelyezése egy éles táblákat. |

## <a name="table-partitions"></a>Táblapartíciók
Particionált tábla tárolja, és a táblázat sorait az adattartomány alapján műveleteket hajt végre. Például egy táblázat sikerült particionálható naponta, hónap vagy év. A partíció eltávolítási, amely korlátozza a partíción belüli adatok lekérdezés megvizsgálja a lekérdezési teljesítmény javíthatja. Az adatok partíció váltás is kezelheti. Az adatokat az SQL Data Warehouse már terjesztve, mert túl sok partíciót lelassíthatja a lekérdezés teljesítményét. További információkért lásd: [útmutatást particionálás](sql-data-warehouse-tables-partition.md).

## <a name="columnstore-indexes"></a>Oszlopcentrikus indexek
Alapértelmezés szerint az SQL Data Warehouse tárolja egy tábla egy fürtözött oszlopcentrikus indexet. Az űrlap adattárolási éri el a magas adattömörítés és a lekérdezési teljesítmény nagy táblákon.  A fürtözött oszlopcentrikus index általában a legjobb választás, de bizonyos esetekben egy fürtözött index vagy egy halommemóriában a megfelelő tárolási struktúra.

Oszlopcentrikus szolgáltatások listáját lásd: [új oszlopcentrikus indexek](/sql/relational-databases/indexes/columnstore-indexes-whats-new). Oszlopcentrikus index a teljesítmény javítása érdekében tekintse meg a [maximalizálva az oszlopcentrikus indexek sorcsoport minőségi](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Statisztika
A lekérdezésoptimalizáló oszlopszintű statisztikai használ, amikor létrehozza a terv a lekérdezés végrehajtása. Lekérdezés teljesítmény javítása érdekében fontos a statisztikák létrehozása az egyes oszlopok, különösen a lekérdezés használt oszlopok. Létrehozásához, és frissítse a statisztikai adatokat nem történik meg automatikusan. [Statisztika létrehozása](/sql/t-sql/statements/create-statistics-transact-sql) tábla létrehozása után. Statisztika frissítése után jelentős számú sorok hozzáadásakor vagy módosítani. Például egy betöltés után statisztikai adatainak frissítése. További információkért lásd: [statisztika útmutatást](sql-data-warehouse-tables-statistics.md).

## <a name="commands-for-creating-tables"></a>Táblázatok létrehozása a parancsok
Létre lehet hozni egy új üres táblázatként. Hozhat létre, és egy tábla a select utasítás feltöltéséhez. Az alábbiakban a T-SQL-parancsokat a táblázatok létrehozásáról.

| T-SQL-utasításban | Leírás |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | Létrehoz egy üres táblát a táblázat oszlopainak és beállítások megadásával. |
| [KÜLSŐ TÁBLA LÉTREHOZÁSA](/sql/t-sql/statements/create-external-table-transact-sql) | Egy külső táblát hoz létre. A tábla definícióját az SQL Data Warehouse tárolja. A táblabeli adatok Azure Blob-tároló vagy az Azure Data Lake Store tárolja. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | Egy új tábla a select utasítás tölti fel. A tábla oszlopok és adattípusok a select utasítás eredmények alapulnak. Adatok importálása a jelen nyilatkozat külső tábla választja ki. |
| [KÜLSŐ TABLE AS SELECT LÉTREHOZÁSA](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | Táblázatot hoz létre új külső select utasítás eredményét exportálásával egy külső helyre.  A hely vagy az Azure Blob Storage tárolóban, vagy az Azure Data Lake Store az. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Az adatraktárban forrásadatok igazítása

Data warehouse tábláiba egy másik adatforrásból származó adatokat tölt fel van töltve. Egy sikeres terheléselosztási végrehajtásához a forrásadatok az oszlopok számát és az adatok típusú a tábla definícióját, az adatraktár kell igazodni. Akkor igazíthatók az adatok a táblák tervezése a nagyon nehéz része lehet. 

Ha az adatok több adattárolókhoz származik, hozni az adatokat az adatraktárba, és az integráció tábla tárolja. Ha adatokat az integráció táblázatban, használhatja az SQL Data Warehouse power átalakítási műveletek végrehajtásához. Ha az adatok készen áll a testreszabásra, éles táblákba beilleszthető.

## <a name="unsupported-table-features"></a>A tábla nem támogatott funkciók
Az SQL Data Warehouse számos támogatja, de nem összes, a table szolgáltatás más adatbázisok által kínált.  Az alábbi listában láthatók az egyes által nem támogatott az SQL Data Warehouse table szolgáltatásait.

- Elsődleges kulcs, külső kulcsok, egyedi, a jelölőnégyzet [tábla megkötések](/sql/t-sql/statements/alter-table-table-constraint-transact-sql)

- [A számított oszlopok](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql)
- [Az indexelt nézetek](/sql/relational-databases/views/create-indexed-views)
- [Feladatütemezési](/sql/t-sql/statements/create-sequence-transact-sql)
- [Ritka oszlopokat](/sql/relational-databases/tables/use-sparse-columns)
- [Kulcsok helyettesítő](). A megvalósítása [identitás](sql-data-warehouse-tables-identity.md).
- [Szinonimák](/sql/t-sql/statements/create-synonym-transact-sql)
- [Eseményindítók](/sql/t-sql/statements/create-trigger-transact-sql)
- [Egyedi indexek](/sql/t-sql/statements/create-index-transact-sql)
- [Felhasználó által definiált típusok](/sql/relational-databases/native-client/features/using-user-defined-types)

## <a name="table-size-queries"></a>Tábla méretét lekérdezések
Egy hely és egy táblázat minden a 60 azokat a terjesztéseket, felhasznált sorok legegyszerűbb használandó [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Azonban a DBCC-parancsokkal is kell meglehetősen korlátozása.  Dinamikus felügyeleti nézetekkel (dinamikus felügyeleti nézetek) részletes információkat jeleníthet mint a DBCC utasításokban. Először hozzon létre ebben a nézetben.

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

Ez a lekérdezés táblázat adja vissza a sorok és a helyet.  Lehetővé teszi, hogy mely táblákat a legnagyobb táblák és e időszeletelés, replikálva, vagy kivonat - terjesztése.  A kivonat-elosztott táblákhoz a lekérdezés a terjesztési oszlop jeleníti meg.  

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

## <a name="next-steps"></a>További lépések
Követően az adatraktár-táblázatok létrehozása, a következő lépés az adatok betöltése az a táblázat.  Betöltés oktatóanyagok esetén lásd: [az SQL Data Warehouse-adatok betöltése](load-data-wideworldimportersdw.md).
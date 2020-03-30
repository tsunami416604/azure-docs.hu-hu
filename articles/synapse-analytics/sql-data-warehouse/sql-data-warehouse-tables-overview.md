---
title: Táblázatok tervezése
description: Bevezetés a táblák tervezésébe az Azure SQL Data Warehouse-ban.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/15/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f116897bdaffa765404aa47fda4ae32a49fa99ac
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351269"
---
# <a name="designing-tables-in-azure-sql-data-warehouse"></a>Táblák tervezése az Azure SQL Data Warehouse-ban

Ismerje meg a táblák azure SQL Data Warehouse-ban történő tervezésének legfontosabb fogalmait. 

## <a name="determine-table-category"></a>Táblázatkategória meghatározása 

A [csillagséma](https://en.wikipedia.org/wiki/Star_schema) tény- és dimenziótáblákba rendezi az adatokat. Egyes táblák integrációs vagy átmeneti adatokra szolgálnak, mielőtt egy tény- vagy dimenziótáblára lépne. A tábla tervezésekor döntse el, hogy a táblaadatok tény-, dimenzió- vagy integrációs táblához tartoznak-e. Ez a határozat tájékoztatja a megfelelő táblaszerkezetet és -eloszlást. 

- **A ténytáblák** olyan mennyiségi adatokat tartalmaznak, amelyeket általában tranzakciós rendszerben hoznak létre, majd betöltenek az adatraktárba. Egy kiskereskedelmi vállalkozás például minden nap értékesítési tranzakciókat generál, majd elemzésre betölti az adatokat egy adattárház ténytáblájába.

- **A dimenziótáblák** olyan attribútumadatokat tartalmaznak, amelyek változhatnak, de általában ritkán változnak. Például egy vevő nevét és címét egy dimenziótáblában tárolják, és csak akkor frissítik, ha a vevő profilja megváltozik. A nagy ténytábla méretének minimalizálása érdekében a vevő nevének és címének nem kell a ténytábla minden sorában lennie. Ehelyett a ténytábla és a dimenziótábla megoszthatja a vevőazonosítót. A lekérdezés a két táblához kapcsolódva társíthatja a vevő profilját és tranzakcióit. 

- **Az integrációs táblák** helyet biztosítanak az adatok integrálásához vagy az előkészítéshez. Integrációs táblát létrehozhat normál táblaként, külső táblaként vagy ideiglenes táblaként. Betölthetpéldák például adatokat egy átmeneti táblába, átalakításokat hajthatunk végre az adatokon az előkészítéssorán, majd beszúrhatja az adatokat egy éles táblába.

## <a name="schema-and-table-names"></a>Séma- és táblanevek
A sémák jó módszeratáblák csoportosítására, amelyeket hasonló módon használnak együtt.  Ha több adatbázist telepít át egy helyszíni megoldásból az SQL Data Warehouse-ba, akkor az a legjobb, ha az összes tény-, dimenzió- és integrációs táblát áttelepíti az SQL Data Warehouse egyetlen sémába. Például a [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap) minta adattárház összes tábláját egy wwi nevű sémán belül tárolhatja. A következő kód létrehoz egy [felhasználó által definiált sémát,](/sql/t-sql/statements/create-schema-transact-sql) a wwi.The following code creates a user-defined séa called wwi.

```sql
CREATE SCHEMA wwi;
```

Ha meg szeretné jelenmutatni a táblák szervezését az SQL Data Warehouse-ban, használhat tény, halvány és int előtagként a táblanevekhez. Az alábbi táblázat a WideWorldImportersDW séma- és táblaneveit mutatja be.  

| WideWorldImportersDW tábla  | Táblázat típusa | SQL Data Warehouse |
|:-----|:-----|:------|:-----|
| Város | Dimenzió | wwi. DimCity (DimCity) |
| Rendelés | Fact | wwi. FactOrder (FactOrder) |


## <a name="table-persistence"></a>A táblázat megőrzése 

A táblák vagy véglegesen tárolják az adatokat az Azure Storage-ban, ideiglenesen az Azure Storage-ban, vagy egy adatraktáron kívüli adattárban.

### <a name="regular-table"></a>Normál asztal

A rendszeres tábla tárolja az adatokat az Azure Storage az adatraktár részeként. A tábla és az adatok megmaradnak, függetlenül attól, hogy egy munkamenet meg van-e nyitva.  Ez a példa egy normál táblát hoz létre két oszloppal. 

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Ideiglenes tábla
Ideiglenes tábla csak a munkamenet időtartama alatt létezik. Az ideiglenes tábla segítségével megakadályozhatja, hogy más felhasználók ideiglenes eredményeket lássanak, és csökkentheti a karbantartás szükségességét.  Az ideiglenes táblák a helyi tárhelyet használják a gyors teljesítmény érdekében.  További információt az [Ideiglenes táblák című témakörben](sql-data-warehouse-tables-temporary.md)talál.

### <a name="external-table"></a>Külső tábla
Egy külső tábla az Azure Storage blobban vagy az Azure Data Lake Store-ban található adatokra mutat. Ha a CREATE TABLE AS SELECT utasítással együtt használja, a külső tábla kiválasztásával adatokat importál az SQL Data Warehouse programba. A külső táblák ezért hasznosak az adatok betöltéséhez. A betöltési oktatóanyagról a [PolyBase használata adatok betöltése az Azure blob storage-ból.](load-data-from-azure-blob-storage-using-polybase.md)

## <a name="data-types"></a>Adattípusok
Az SQL Data Warehouse támogatja a leggyakrabban használt adattípusokat. A támogatott adattípusok listáját a CREATE TABLE utasítás CREATE TABLE hivatkozás ában található [adattípusok](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) között található. Az adattípusok használatával kapcsolatban az [Adattípusok](sql-data-warehouse-tables-data-types.md)témakörben talál útmutatást.

## <a name="distributed-tables"></a>Elosztott táblák
Az SQL Data Warehouse egyik alapvető jellemzője, hogy hogyan tud tárolni és működni a [különböző disztribúciók tábláin.](massively-parallel-processing-mpp-architecture.md#distributions)  Az SQL Data Warehouse három módszert támogat az adatok elosztására, a ciklikus multiplexelést (alapértelmezett), a kivonatot és a replikált at.

### <a name="hash-distributed-tables"></a>Kivonat alapján elosztott táblák
A kivonatoló elosztott tábla a felosztási oszlop értéke alapján osztja el a sorokat. A kivonatoló elosztott tábla nagy táblák lekérdezéseinek nagy teljesítményének elérésére szolgál. A terjesztési oszlop kiválasztásakor számos tényezőt kell figyelembe venni. 

További információt az [elosztott táblák tervezési útmutatója](sql-data-warehouse-tables-distribute.md)című témakörben talál.

### <a name="replicated-tables"></a>Replikált táblák
A replikált tábla rendelkezik a tábla teljes másolatát elérhető minden számítási csomóponton. A lekérdezések gyorsan futnak a replikált táblákon, mivel a replikált táblák illesztése nem igényel adatmozgást. A replikáció azonban további tárhelyet igényel, és nagy táblák esetén nem praktikus. 

További információt a [tervútmutató a replikált táblákhoz című témakörben talál.](design-guidance-for-replicated-tables.md)

### <a name="round-robin-tables"></a>Ciklikus multiplexelési táblázatok
A ciklikus multiplexelési tábla egyenletesen osztja el a táblasorokat az összes disztribúció között. A sorok véletlenszerűen oszlanak el. Az adatok ciklikus multiplexelési táblába való betöltése gyors.  A lekérdezések azonban több adatáthelyezést igényelhetnek, mint a többi terjesztési módszer. 

További információt az [elosztott táblák tervezési útmutatója](sql-data-warehouse-tables-distribute.md)című témakörben talál.

### <a name="common-distribution-methods-for-tables"></a>Táblázatok gyakori terjesztési módszerei
A táblakategória gyakran határozza meg, hogy melyik lehetőséget válassza a tábla terjesztéséhez. 

| Táblázat kategória | Ajánlott elosztási lehetőség |
|:---------------|:--------------------|
| Fact           | Kivonatoló-disztribúció használata fürtözött oszlopcentrikus indexszel. A teljesítmény akkor javul, ha két kivonattábla csatlakozik ugyanabba a terjesztési oszlophoz. |
| Dimenzió      | Kisebb táblákhoz használja a replikált at. Ha a táblák túl nagyok az egyes számítási csomópontokon való tároláshoz, használja a kivonatoló elosztott. |
| Előkészítés        | Ciklikus multiplexelés használata az átmeneti táblához. A CTAS terhelése gyors. Miután az adatok az átmeneti táblában vannak, használja az INSERT... SELECT az adatok éles táblákba való áthelyezéséhez. |

## <a name="table-partitions"></a>Táblapartíciók
A particionált tábla adattartományok szerint tárolja és hajtja végre a táblasorokműveleteit. Egy tábla például felosztható nappal, hónap vagy év szerint. A lekérdezési teljesítmény a partíciók eltávolításával javítható, ami a partíción belüli adatokra korlátozza a lekérdezésvizsgálatát. Az adatokat partícióváltással is karbantarthatja. Mivel az SQL Data Warehouse ban lévő adatok már el vannak osztva, túl sok partíció lassíthatja a lekérdezési teljesítményt. További információ: [Particionálási útmutató](sql-data-warehouse-tables-partition.md).  Ha a partíció nem üres táblapartíciókra vált, fontolja meg az [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) utasítás TRUNCATE_TARGET beállításának használatát, ha a meglévő adatokat csonkolni szeretné. Az alábbi kód átvált az átalakított napi adatokban a SalesFact-ben, felülírva a meglévő adatokat. 

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

## <a name="columnstore-indexes"></a>Oszlopcentrikus indexek
Alapértelmezés szerint az SQL Data Warehouse egy táblát fürtözött oszlopcentrikus indexként tárol. Az adattárolás ezen formája nagy adattömörítési és lekérdezési teljesítményt ér el nagy táblákon.  A fürtözött oszlopcentrikus index általában a legjobb választás, de bizonyos esetekben a fürtözött index vagy egy haloma a megfelelő tárolási struktúra.  A halommemória-tábla különösen hasznos lehet az átmeneti adatok, például egy végleges táblává alakított átmeneti tábla betöltéséhez.

Az oszlopcentrikus funkciók listáját az [oszlopcentrikus indexek újdonságai című témakörben találja.](/sql/relational-databases/indexes/columnstore-indexes-what-s-new) Az oszlopcentrikus index teljesítményének javításáról a [sorcsoport minőségének maximalizálása oszlopcentrikus indexek esetén című témakörben található.](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)

## <a name="statistics"></a>Statisztika
A lekérdezésoptimalizáló oszlopszintű statisztikákat használ, amikor létrehozza a lekérdezés végrehajtásának tervét. A lekérdezési teljesítmény javítása érdekében fontos, hogy az egyes oszlopok, különösen a lekérdezésillesztésekben használt oszlopok statisztikái legyenek. [A statisztikák létrehozása](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistic) automatikusan megtörténik.  A statisztikák frissítése azonban nem történik meg automatikusan. A statisztikák frissítése jelentős számú sor hozzáadása vagy módosítása után. Például frissítse a statisztikákat egy terhelés után. További információ: [Statisztika útmutató](sql-data-warehouse-tables-statistics.md).

## <a name="primary-key-and-unique-key"></a>Elsődleges kulcs és egyedi kulcs
A PRIMEP KULCS csak akkor támogatott, ha a NONCLUSTERED és a NOT ENFORCED egyaránt használatos.  A UNIQUE megkötés csak a NOT ENFORCED beállítással támogatott.  Ellenőrizze [az SQL Data Warehouse Tábla kényszereinek jelölőnégyzetét.](sql-data-warehouse-table-constraints.md)

## <a name="commands-for-creating-tables"></a>Táblázatok létrehozására szolgáló parancsok
A táblát új üres táblaként is létrehozhatja. Létrehozhat és feltölthet egy táblázatot egy select utasítás eredményeivel. Az alábbiakban a táblázat létrehozásához szükséges T-SQL parancsokat taszák meg.

| T-SQL utasítás | Leírás |
|:----------------|:------------|
| [TÁBLA LÉTREHOZÁSA](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | Üres táblát hoz létre az összes táblaoszlop és -beállítás definiálásával. |
| [KÜLSŐ TÁBLA LÉTREHOZÁSA](/sql/t-sql/statements/create-external-table-transact-sql) | Külső táblát hoz létre. A tábla definíciója az SQL Data Warehouse-ban tárolódik. A táblaadatok az Azure Blob storage vagy az Azure Data Lake Store tárolja. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | Feltölt egy új táblát egy select utasítás eredményeivel. A táblaoszlopok és adattípusok a select utasítás eredményein alapulnak. Adatok importálásához ez az utasítás külső táblából választhat. |
| [KÜLSŐ TÁBLA LÉTREHOZÁSA KIJELÖLÉSKÉNT](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | Új külső táblát hoz létre úgy, hogy egy select utasítás eredményeit külső helyre exportálja.  A hely vagy Az Azure Blob storage vagy az Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Forrásadatok igazítása az adattárházhoz

Az adattárház-táblákat egy másik adatforrásból származó adatok betöltése tölti fel. A sikeres betöltés hez a forrásadatok oszlopainak számának és adattípusainak igazodniuk kell az adatraktárban lévő tábladefinícióhoz. Az adatok igazításának beszerzése lehet a legnehezebb része a táblák tervezésének. 

Ha az adatok több adattárakból érkeznek, az adatokat beviheti az adatraktárba, és egy integrációs táblában tárolhatja. Miután az adatok az integrációs táblában vannak, az SQL Data Warehouse segítségével átalakítási műveleteket hajthat végre. Az adatok előkészítése után beillesztheti azokat a termelési táblákba.

## <a name="unsupported-table-features"></a>Nem támogatott táblaszolgáltatások
Az SQL Data Warehouse támogatja a más adatbázisok által kínált táblafunkciók közül sokat, de nem az összeset.  Az alábbi lista az SQL Data Warehouse által nem támogatott táblafunkciókat mutatja be.

- Idegen kulcs, [Táblamegkötések](/sql/t-sql/statements/alter-table-table-constraint-transact-sql) ellenőrzése
- [Számított oszlopok](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql)
- [Indexelt nézetek](/sql/relational-databases/views/create-indexed-views)
- [Sequence](/sql/t-sql/statements/create-sequence-transact-sql)
- [Ritka oszlopok](/sql/relational-databases/tables/use-sparse-columns)
- Pótló kulcsok. Megvalósítás [az identitással.](sql-data-warehouse-tables-identity.md)
- [Szinonimák](/sql/t-sql/statements/create-synonym-transact-sql)
- [Eseményindítók](/sql/t-sql/statements/create-trigger-transact-sql)
- [Egyedi indexek](/sql/t-sql/statements/create-index-transact-sql)
- [Felhasználó által definiált típusok](/sql/relational-databases/native-client/features/using-user-defined-types)

## <a name="table-size-queries"></a>Táblaméretű lekérdezések
A 60 disztribúció ban egy táblázat által felhasznált terület és sorok azonosításának egyszerű módja a [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql)használata.

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

A DBCC parancsok használata azonban meglehetősen korlátozó lehet.  A dinamikus felügyeleti nézetek (DMV-k) részletesebben jelennek meg, mint a DBCC parancsok. Először hozza létre ezt a nézetet.

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
WHERE pn.[type] = 'COMPUTE'
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

### <a name="table-space-summary"></a>Táblázatterület összegzése

Ez a lekérdezés a sorokat és a szóközt adja vissza tábla szerint.  Lehetővé teszi, hogy mely táblák a legnagyobb táblák, és hogy azok ciklikus multiplexelés, replikált, vagy hash -elosztott.  Kivonatoló elosztott táblák esetén a lekérdezés a terjesztési oszlopot jeleníti meg.  

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

### <a name="table-space-by-distribution-type"></a>Táblázattér eloszlástípus szerint

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

### <a name="table-space-by-index-type"></a>Táblázatterület indextípus szerint

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

### <a name="distribution-space-summary"></a>Terjesztési terület összegzése

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
Az adatraktár tábláinak létrehozása után a következő lépés az adatok betöltése a táblába.  Az oktatóanyag betöltési témaköre Az [adatok betöltése az SQL Data Warehouse-ba](load-data-wideworldimportersdw.md)című témakörben található.

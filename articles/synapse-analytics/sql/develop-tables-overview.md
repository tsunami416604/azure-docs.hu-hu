---
title: Táblázatok tervezése a Synapse SQL használatával
description: Bevezetés a táblázatok synapsz SQL-ben történő tervezésébe.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: f1eec76d92edc97f7e4058d3afe813f0bb2aae47
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431877"
---
# <a name="design-tables-using-synapse-sql"></a>Táblázatok tervezése a Synapse SQL használatával

Ez a dokumentum kulcsfogalmakat tartalmaz az SQL készlettel és az IGÉNY szerinti SQL-készlettel (előzetes verzió) rendelkező táblák tervezéséhez.  

[SQL on-demand (előzetes verzió)](on-demand-workspace-overview.md) egy lekérdezési szolgáltatás az adatok felett az adattó. Nem rendelkezik helyi tárolóadat-betöltés. [Az SQL-készlet](best-practices-sql-pool.md) a Synapse SQL használata során kiépített analitikus erőforrások gyűjteményét jelenti. Az SQL-készlet méretét az adatraktározási egységek (DWU) határozzák meg.

Az alábbi táblázat az SQL-készlet és az igény szerinti SQL szempontjából releváns témaköröket sorolja fel:

| Témakör                                                        | SQL-készlet | SQL igény szerint |
| ------------------------------------------------------------ | ------------------ | ----------------------- |
| [Táblázatkategória meghatározása](#determine-table-category)        | Igen                | Nem                      |
| [Sémanevek](#schema-names)                                | Igen                | Igen                     |
| [Táblanevek](#table-names)                                  | Igen                | Nem                      |
| [A táblázat megőrzése](#table-persistence)                      | Igen                | Nem                      |
| [Normál asztal](#regular-table)                              | Igen                | Nem                      |
| [Ideiglenes tábla](#temporary-table)                          | Igen                | Igen                     |
| [Külső tábla](#external-table)                            | Igen                | Igen                     |
| [Adattípusok](#data-types)                                    | Igen                | Igen                     |
| [Elosztott táblák](#distributed-tables)                    | Igen                | Nem                      |
| [Kivonat alapján elosztott táblák](#hash-distributed-tables)          | Igen                | Nem                      |
| [Replikált táblák](#replicated-tables)                      | Igen                | Nem                      |
| [Ciklikus multiplexelési táblázatok](#round-robin-tables)                    | Igen                | Nem                      |
| [Táblázatok gyakori terjesztési módszerei](#common-distribution-methods-for-tables) | Igen                | Nem                      |
| [Partíciók](#partitions)                                    | Igen                | Igen                     |
| [Oszlopcentrikus indexek](#columnstore-indexes)                  | Igen                | Nem                      |
| [Statisztika](#statistics)                                    | Igen                | Igen                     |
| [Elsődleges kulcs és egyedi kulcs](#primary-key-and-unique-key)    | Igen                | Nem                      |
| [Táblázatok létrehozására szolgáló parancsok](#commands-for-creating-tables) | Igen                | Nem                      |
| [Forrásadatok igazítása az adattárházhoz](#aligning-source-data-with-the-data-warehouse) | Igen                | Nem                      |
| [Nem támogatott táblaszolgáltatások](#unsupported-table-features)    | Igen                | Nem                      |
| [Táblaméretű lekérdezések](#table-size-queries)                    | Igen                | Nem                      |

## <a name="determine-table-category"></a>Táblázatkategória meghatározása

A [csillagséma](https://en.wikipedia.org/wiki/Star_schema) tény- és dimenziótáblákba rendezi az adatokat. Egyes táblák integrációs vagy átmeneti adatokra szolgálnak, mielőtt tény- vagy dimenziótáblára lépne. A tábla tervezésekor döntse el, hogy a táblaadatok tény-, dimenzió- vagy integrációs táblához tartoznak-e. Ez a határozat tájékoztatja a megfelelő táblaszerkezetet és -eloszlást.

- **A ténytáblák** olyan mennyiségi adatokat tartalmaznak, amelyeket általában tranzakciós rendszerben hoznak létre, majd betöltenek az adatraktárba. Egy kiskereskedelmi vállalkozás például minden nap értékesítési tranzakciókat generál, majd elemzésre betölti az adatokat egy adattárház ténytáblájába.

- **A dimenziótáblák** olyan attribútumadatokat tartalmaznak, amelyek változhatnak, de általában ritkán változnak. Például egy vevő nevét és címét egy dimenziótáblában tárolják, és csak akkor frissítik, ha a vevő profilja megváltozik. A nagy ténytábla méretének minimalizálása érdekében a vevő nevének és címének nem kell a ténytábla minden sorában lennie. Ehelyett a ténytábla és a dimenziótábla megoszthatja a vevőazonosítót. A lekérdezés a két táblához kapcsolódva társíthatja a vevő profilját és tranzakcióit.

- **Az integrációs táblák** helyet biztosítanak az adatok integrálásához vagy az előkészítéshez. Integrációs táblát létrehozhat normál táblaként, külső táblaként vagy ideiglenes táblaként. Betölthetpéldák például adatokat egy átmeneti táblába, átalakításokat hajthatunk végre az adatokon az előkészítéssorán, majd beszúrhatja az adatokat egy éles táblába.

## <a name="schema-names"></a>Sémanevek

A sémák jó módszerként csoportosítják a hasonló módon használt objektumokat. A következő kód létrehoz egy [felhasználó által definiált sémát,](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a wwi.The following code creates a user-defined séa called wwi.

```sql
CREATE SCHEMA wwi;
```

## <a name="table-names"></a>Táblanevek

Ha több adatbázist telepít át egy előzetes megoldásból az SQL-készletbe, az ajánlott eljárás az összes tény-, dimenzió- és integrációs tábla áttelepítése egyetlen SQL-készletsémába. Például a [WideWorldImportersDW](/sql/samples/wide-world-importers-dw-database-catalog?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) minta adattárház összes tábláját egy wwi nevű sémán belül tárolhatja.

Ha meg szeretné jelenmutatni a táblák sql készletben való szervezését, a táblanevek előtagjaként használhat fact, dim és int -t. Az alábbi táblázat a WideWorldImportersDW séma- és táblaneveit mutatja be.  

| WideWorldImportersDW tábla  | Táblázat típusa | SQL-készlet |
|:-----|:-----|:------|:-----|
| Város | Dimenzió | wwi. DimCity (DimCity) |
| Rendelés | Fact | wwi. FactOrder (FactOrder) |

## <a name="table-persistence"></a>A táblázat megőrzése

A táblák vagy véglegesen az Azure Storage-ban, ideiglenesen az Azure Storage-ban vagy az adatraktáron kívüli adattárban tárolják az adatokat.

### <a name="regular-table"></a>Normál asztal

A rendszeres tábla tárolja az adatokat az Azure Storage az adatraktár részeként. A tábla és az adatok megmaradnak, függetlenül attól, hogy egy munkamenet meg van-e nyitva.  Az alábbi példa egy normál táblázatot hoz létre két oszloppal.

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Ideiglenes tábla

Ideiglenes tábla csak a munkamenet időtartama alatt létezik. Az ideiglenes tábla segítségével megakadályozhatja, hogy más felhasználók ideiglenes eredményeket lássanak. Az ideiglenes táblák használata csökkenti a karbantartás szükségességét is.  Az ideiglenes táblák helyi tárolót használnak, és az SQL-készletben gyorsabb teljesítményt nyújtanak.  

Sql on-demand támogatja az ideiglenes táblákat. A használat azonban korlátozott, mivel az ideiglenes táblából választhat, de nem tud csatlakozni a tárolóban lévő fájlokkal.

További információt az [Ideiglenes táblák című témakörben](develop-tables-temporary.md)talál.

### <a name="external-table"></a>Külső tábla

[A külső táblák](develop-tables-external-tables.md) az Azure Storage blobban vagy az Azure Data Lake Storage-ban található adatokra mutatnak.

Adatok importálása külső táblákból sql készletbe a [CREATE TABLE AS SELECT](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) utasítás használatával. A betöltési oktatóanyagról a [PolyBase használata adatok betöltése az Azure blob storage-ból.](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

Az SQL igény szerinti használata a [CETAS](develop-tables-cetas.md) segítségével mentheti a lekérdezés eredményét egy külső táblába az Azure Storage-ban.

## <a name="data-types"></a>Adattípusok

Az SQL-készlet támogatja a leggyakrabban használt adattípusokat. A támogatott adattípusok listáját a CREATE TABLE utasítás CREATE TABLE hivatkozás ában található [adattípusok](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes) között található. Az adattípusok használatáról az [Adattípusok](../sql/develop-tables-data-types.md)című témakörben talál további információt.

## <a name="distributed-tables"></a>Elosztott táblák

Az SQL-készlet egyik alapvető jellemzője, hogy hogyan tud tárolni és működni a [táblákon a disztribúciók](../sql-data-warehouse/massively-parallel-processing-mpp-architecture.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#distributions)között.  Az SQL-készlet három adatterjesztési módszert támogat:

- Ciklikus időszeletelés (alapértelmezett)
- Kivonat
- Replikált

### <a name="hash-distributed-tables"></a>Kivonat alapján elosztott táblák

A kivonatoló elosztott tábla a felosztási oszlop értéke alapján osztja el a sorokat. A kivonatoló elosztott tábla nagy táblák lekérdezéseinek nagy teljesítményének elérésére szolgál. A terjesztési oszlop kiválasztásakor számos tényezőt kell figyelembe venni.

További információt az [elosztott táblák tervezési útmutatója](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)című témakörben talál.

### <a name="replicated-tables"></a>Replikált táblák

A replikált tábla rendelkezik a tábla teljes másolatát elérhető minden számítási csomóponton. A lekérdezések gyorsan futnak a replikált táblákon, mert a replikált táblák illesztése nem igényel adatmozgást. A replikáció azonban további tárhelyet igényel, és nem praktikus nagy táblák esetén.

További információt a [tervútmutató a replikált táblákhoz című témakörben talál.](../sql-data-warehouse/design-guidance-for-replicated-tables.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="round-robin-tables"></a>Ciklikus multiplexelési táblázatok

A ciklikus multiplexelési tábla egyenletesen osztja el a táblasorokat az összes disztribúció között. A sorok véletlenszerűen oszlanak el. Az adatok ciklikus multiplexelési táblába való betöltése gyors.  A lekérdezések azonban több adatáthelyezést igényelhetnek, mint a többi terjesztési módszer.

További információt az [elosztott táblák tervezési útmutatója](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)című témakörben talál.

### <a name="common-distribution-methods-for-tables"></a>Táblázatok gyakori terjesztési módszerei

A táblakategória gyakran határozza meg a táblaeloszlás optimális beállítását.

| Táblázat kategória | Ajánlott elosztási lehetőség |
|:---------------|:--------------------|
| Fact           | Kivonatoló-disztribúció használata fürtözött oszlopcentrikus indexszel. A teljesítmény akkor javul, ha két kivonattábla csatlakozik ugyanabba a terjesztési oszlophoz. |
| Dimenzió      | Kisebb táblákhoz használja a replikált at. Ha a táblák túl nagyok az egyes számítási csomópontokon való tároláshoz, használja a kivonatoló elosztott. |
| Előkészítés        | Ciklikus multiplexelés használata az átmeneti táblához. A CTAS terhelése gyors. Miután az adatok az átmeneti táblában vannak, használja az INSERT... SELECT az adatok éles táblákba való áthelyezéséhez. |

## <a name="partitions"></a>Partíciók

Az SQL-készletben a particionált tábla tárolja és végrehajtja a műveleteket a táblasorok adattartományok szerint. Egy tábla például felosztható nappal, hónap vagy év szerint. A lekérdezési teljesítmény a partíciók eltávolításával javítható, ami a partíción belüli adatokra korlátozza a lekérdezésvizsgálatát.

Az adatokat partícióváltással is karbantarthatja. Mivel az SQL-készletben lévő adatok már el vannak osztva, túl sok partíció lassíthatja a lekérdezési teljesítményt. További információ: [Particionálási útmutató](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).  

> [!TIP]
> Ha a partíció nem üres táblapartíciókra vált, fontolja meg az [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) utasítás TRUNCATE_TARGET beállításának használatát, ha a meglévő adatokat csonkolni szeretné.

Az alábbi kód az átalakított napi adatokat SalesFact partícióvá alakítja, és felülírja a meglévő adatokat.

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

Az SQL igény szerinti rendszerben korlátozhatja a lekérdezés által beolvasott fájlokat/mappákat (partíciókat). Az elérési út alapján történő particionálás a [tárolófájlok lekérdezése](develop-storage-files-overview.md)című részben ismertetett filepath és fileinfo függvényekkel támogatott. A következő példa egy mappát olvas be a 2017-es évre vonatkozó adatokkal:

```sql
SELECT
    nyc.filepath(1) AS [year],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
GROUP BY
    nyc.filepath(1),
    payment_type
ORDER BY
    nyc.filepath(1),
    payment_type
```

## <a name="columnstore-indexes"></a>Oszlopcentrikus indexek

Alapértelmezés szerint az SQL-készlet fürtözött oszlopcentrikus indexként tárolja a táblát. Az adattárolás ezen formája nagy adattömörítési és lekérdezési teljesítményt ér el nagy táblákon.  A fürtözött oszlopcentrikus index általában a legjobb választás, de bizonyos esetekben a fürtözött index vagy egy haloma a megfelelő tárolási struktúra.  

> [!TIP]
> A halommemória-tábla különösen hasznos lehet az átmeneti adatok, például egy átmeneti tábla betöltéséhez, amely végleges táblává alakul át.

Az oszlopcentrikus funkciók listáját az [oszlopcentrikus indexek újdonságai című témakörben találja.](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Az oszlopcentrikus index teljesítményének javításáról a [sorcsoport minőségének maximalizálása oszlopcentrikus indexek esetén című témakörben található.](../sql/data-load-columnstore-compression.md)

## <a name="statistics"></a>Statisztika


A lekérdezésoptimalizáló oszlopszintű statisztikákat használ, amikor létrehozza a lekérdezés végrehajtásának tervét. A lekérdezési teljesítmény javítása érdekében fontos, hogy az egyes oszlopok, különösen a lekérdezésillesztésekben használt oszlopok statisztikái legyenek. A Synapse SQL támogatja a statisztikák automatikus létrehozását. 

A statisztikai frissítés nem történik meg automatikusan. A statisztikák frissítése jelentős számú sor hozzáadása vagy módosítása után. Például frissítse a statisztikákat egy terhelés után. További információk a [Statisztika útmutatóban találhatóak.](develop-tables-statistics.md)

## <a name="primary-key-and-unique-key"></a>Elsődleges kulcs és egyedi kulcs

A PRIMEP KULCS csak akkor támogatott, ha a NONCLUSTERED és a NOT ENFORCED egyaránt használatos.  A UNIQUE megkötés csak akkor támogatott, ha nem kényszeríti a programot.  További információt az [SQL-készlettábla-korlátok](../sql-data-warehouse/sql-data-warehouse-table-constraints.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) című cikkben talál.

## <a name="commands-for-creating-tables"></a>Táblázatok létrehozására szolgáló parancsok

A táblát új üres táblaként is létrehozhatja. Létrehozhat és feltölthet egy táblázatot egy select utasítás eredményeivel. Az alábbiakban a táblázat létrehozásához szükséges T-SQL parancsokat taszák meg.

| T-SQL utasítás | Leírás |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Üres táblát hoz létre az összes táblaoszlop és -beállítás definiálásával. |
| [KÜLSŐ TÁBLA LÉTREHOZÁSA](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Külső táblát hoz létre. A tábla definíciója az SQL készletben van tárolva. A táblaadatok az Azure Blob storage vagy az Azure Data Lake Storage tárolja. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Feltölt egy új táblát egy select utasítás eredményeivel. A táblaoszlopok és adattípusok a select utasítás eredményein alapulnak. Adatok importálásához ez az utasítás külső táblából választhat. |
| [KÜLSŐ TÁBLA LÉTREHOZÁSA KIJELÖLÉSKÉNT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Új külső táblát hoz létre úgy, hogy egy select utasítás eredményeit külső helyre exportálja.  A hely vagy Az Azure Blob storage vagy az Azure Data Lake Storage. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Forrásadatok igazítása az adattárházhoz

Az adattárház-táblákat egy másik adatforrásból származó adatok betöltése tölti fel. A sikeres betöltés elérése érdekében a forrásadatok oszlopainak számának és adattípusainak igazodniuk kell az adatraktárban lévő tábladefinícióhoz.

> [!NOTE]
> Az adatok igazításának beszerzése lehet a legnehezebb része a táblák tervezésének.

Ha az adatok több adattárakból érkeznek, az adatokat az adatraktárba portolhatja, és egy integrációs táblában tárolhatja. Miután az adatok az integrációs táblában vannak, az SQL-készlet segítségével átalakítási műveleteket valósíthat meg. Az adatok előkészítése után beillesztheti azokat a termelési táblákba.

## <a name="unsupported-table-features"></a>Nem támogatott táblaszolgáltatások

Az SQL-készlet támogatja a más adatbázisok által kínált táblaszolgáltatások közül sokat, de nem az összeset.  Az alábbi lista a táblázat néhány olyan szolgáltatását tartalmazza, amelyeket az SQL-készlet nem támogat.

- Idegen kulcs, ellenőrizze [a tábla kényszereit](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Számított oszlopok](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Indexelt nézetek](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Sequence](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Ritka oszlopok](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- Helyettesítő kulcsok, megvalósítás [identitással](../sql-data-warehouse/sql-data-warehouse-tables-identity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Szinonimák](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Eseményindítók](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Egyedi indexek](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Felhasználó által definiált típusok](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="table-size-queries"></a>Táblaméretű lekérdezések

A 60 disztribúcióban egy táblázat által felhasznált terület és sorok azonosításának egyszerű módja a [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)használata.

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Ne feledje, hogy a DBCC parancsok használata meglehetősen korlátozó lehet.  A dinamikus felügyeleti nézetek (DMV-k) részletesebben jelennek meg, mint a DBCC parancsok. Először hozza létre az alábbi nézetet.

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

Ez a lekérdezés a sorokat és a szóközt adja vissza tábla szerint.  A táblázatterület összegzése lehetővé teszi, hogy megtekintse, mely táblák a legnagyobb táblák. Azt is láthatja, hogy ciklikus multiplexelés, replikálás vagy kivonatoló elosztású.  Kivonatoló elosztott táblák esetén a lekérdezés a terjesztési oszlopot jeleníti meg.  

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

Az adatraktár tábláinak létrehozása után a következő lépés az adatok betöltése a táblába.  A betöltési oktatóanyagról az [Adatok betöltése az SQL-készletbe című témakörben](../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#load-the-data-into-sql-pool)található.

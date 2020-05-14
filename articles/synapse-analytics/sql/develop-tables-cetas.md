---
title: CETAS a szinapszis SQL-ben
description: A CETAS használata a szinapszis SQL használatával
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 3bf7ff668584a78fea6e2d787e96f36a20f12e37
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197628"
---
# <a name="cetas-with-synapse-sql"></a>CETAS a szinapszis SQL-sel

Az SQL-készletben vagy az igény szerinti SQL-ben (előzetes verzió) az alábbi feladatok végrehajtásához a SELECT (CETAS) (külső tábla létrehozása) lehetőséget használhatja:  

- Külső tábla létrehozása
- A Transact-SQL SELECT utasítás eredményeinek párhuzamos exportálása a következőre:

  - Hadoop
  - Azure Storage Blob
  - 2. generációs Azure Data Lake Storage

## <a name="cetas-in-sql-pool"></a>CETAS az SQL-készletben

SQL-készlet esetén a CETAS-használat és-szintaxis beállításnál tekintse meg a [külső tábla létrehozása elemet](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) . Emellett az SQL Pool használatával történő CTAS kapcsolatos útmutatásért tekintse meg a [CREATE TABLE as Select](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) cikket.

## <a name="cetas-in-sql-on-demand"></a>Igény szerinti SQL-CETAS

Az igény szerinti SQL-erőforrás használatakor a CETAS egy külső tábla létrehozására és a lekérdezési eredmények exportálására szolgál Azure Storage Blob vagy Azure Data Lake Storage Gen2.

## <a name="syntax"></a>Szintaxis

```syntaxsql
CREATE EXTERNAL TABLE [ [database_name  . [ schema_name ] . ] | schema_name . ] table_name
    WITH (
        LOCATION = 'path_to_folder',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name  
)
    AS <select_statement>  
[;]

<select_statement> ::=  
    [ WITH <common_table_expression> [ ,...n ] ]  
    SELECT <select_criteria>
```

## <a name="arguments"></a>Argumentumok

*[[ *database_name* . [ *schema_name* ]. ] | *schema_name* . ] *table_name**

A létrehozandó tábla egy vagy három részének neve. Külső táblák esetében az igény szerinti SQL csak a tábla metaadatait tárolja. A tényleges adatok nem helyezhetők át vagy nem tárolódnak az SQL on-demandban.

LOCATION = *' path_to_folder '*

Megadja, hogy hol kell írni a SELECT utasítás eredményeit a külső adatforráson. A gyökérmappa a külső adatforrásban megadott adathely. A HELYnek egy mappára kell mutatnia, és egy záró/karakterrel kell rendelkeznie. Példa: aggregated_data/

DATA_SOURCE = *external_data_source_name*

Annak a külső adatforrás-objektumnak a nevét adja meg, amely a külső adattárolási helyet tartalmazza. Külső adatforrás létrehozásához használja a [külső adatforrás létrehozása (Transact-SQL)](develop-tables-external-tables.md#create-external-data-source)lehetőséget.

FILE_FORMAT = *external_file_format_name*

Megadja a külső fájlformátum objektumának nevét, amely a külső adatfájl formátumát tartalmazza. Külső fájlformátum létrehozásához használja a [create External File Format (Transact-SQL) formátumot](develop-tables-external-tables.md#create-external-file-format). Jelenleg csak a FORMAT = ' PARQUEt ' formátumú külső fájlformátumok támogatottak.

*<common_table_expression>*

Egy, a közös tábla kifejezésének (TÁBLAKIFEJEZÉSEK) elnevezésű ideiglenes elnevezett eredményhalmaz megadása. További információ: [common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

Válassza ki <select_criteria>

Feltölti az új táblát egy SELECT utasítás eredményeivel. *select_criteria* a SELECT utasítás törzse, amely meghatározza, hogy az új táblába milyen adatok legyenek átmásolva. További információ a SELECT utasításokról: [Select (Transact-SQL)](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

> [!NOTE]
> Az ORDER BY záradék nem támogatott a CETAS részének KIJELÖLÉSEkor.

## <a name="permissions"></a>Engedélyek

A CETAS működéséhez rendelkeznie kell a mappa tartalmának listázásához és a hely mappájához való íráshoz szükséges engedélyekkel.

## <a name="examples"></a>Példák

Ezek a példák a CETAS használatával mentik el az év és a population_ds adatforrásban található aggregated_data mappában lévő összes összesített sokaságot.

Ez a minta a korábban létrehozott hitelesítő adatokra, adatforrásra és külső fájlformátumra támaszkodik. Tekintse meg a [külső táblákat](develop-tables-external-tables.md) tartalmazó dokumentumot. Ha ugyanazon az adatforráson belül más mappába szeretné menteni a lekérdezési eredményeket, módosítsa a LOCATION argumentumot. Ha másik Storage-fiókba kívánja menteni az eredményeket, hozzon létre és használjon egy másik adatforrást DATA_SOURCE argumentumhoz.

> [!NOTE]
> A következő minták a nyilvános Azure Open Storage-fiókot használják. Csak olvasható. A lekérdezések végrehajtásához meg kell adnia azt az adatforrást, amelyhez írási engedéllyel rendelkezik.

```sql
-- use CETAS to export select statement with OPENROWSET result to  storage
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM
    OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=*/*.parquet',
    FORMAT='PARQUET') AS [r]
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

Az alábbi minta egy külső táblázatot használ a CETAS forrásaként. A korábban létrehozott hitelesítő adatokra, adatforrásra, külső fájlformátumra és külső táblára támaszkodik. Tekintse meg a [külső táblákat](develop-tables-external-tables.md) tartalmazó dokumentumot.

```sql
-- use CETAS with select from external table
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM census_external_table
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

## <a name="supported-data-types"></a>Támogatott adattípusok

A CETAS a következő SQL-adattípusokkal rendelkező eredményhalmaz tárolására használható:

- binary
- varbinary
- char
- varchar
- dátum
- time
- datetime2
- tizedes tört
- numerikus
- lebegőpontos
- valós szám
- bigint
- int
- smallint
- tinyint
- bit

A LOBs nem használható a CETAS.

A következő adattípusok nem használhatók a CETAS kiválasztása részben:

- NCHAR
- nvarchar
- dátum/idő
- idő adattípusúra
- DateTimeOffset
- pénzt
- túlcsordulási
- uniqueidentifier

## <a name="next-steps"></a>Következő lépések

Kipróbálhatja a [Spark-táblák](develop-storage-files-spark-tables.md)lekérdezését.

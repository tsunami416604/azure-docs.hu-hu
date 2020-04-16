---
title: CETAS a Szinapszis SQL-ben
description: A CETAS használata a Synapse SQL-rel
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 71bc20680467d270436e28190bb49db5b9313ca0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424026"
---
# <a name="cetas-with-synapse-sql"></a>CETAS szinapsze SQL-rel

Az SQL készletben vagy az SQL on-demand (előzetes verzióban) a CREATE EXTERNAL TABLE AS SELECT (CETAS) segítségével a következő feladatokat végezheti el:  

- Külső tábla létrehozása
- Ezzel párhuzamosan exportálja a Transact-SQL SELECT utasítás eredményeit

  - Hadoop
  - Azure Storage Blob
  - 2. generációs Azure Data Lake Storage

## <a name="cetas-in-sql-pool"></a>CETAS az SQL készletben

Az SQL-készlet, a CETAS-használat és a szintaxis esetén tekintse meg a [KÜLSŐ TÁBLA LÉTREHOZÁSA VÁLASZTÓKÉNT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) című cikket. Ezenkívül az SQL-készletet használó CTAS-ról a [TÁBLA MINT KIVÁLASZTÁSA létrehozása](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) című cikkben olvashat.

## <a name="cetas-in-sql-on-demand"></a>CETAS az SQL igény szerinti rendszerben

Az SQL igény szerinti erőforrás használatakor a CETAS egy külső tábla létrehozására és lekérdezési eredmények exportálására szolgál az Azure Storage Blob vagy az Azure Data Lake Storage Gen2 szolgáltatásba.

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

*[ [ *database_name* . [ *schema_name]* . ] | *schema_name.* ] *table_name**

A létrehozandó tábla egy-három részből álló neve. Külső tábla esetén az SQL igény szerinti csak a tábla metaadatait tárolja. A tényleges adatok at sql on-demand nem kerülnek áthelyezésre vagy tárolásra.

HELY = *'path_to_folder'*

Itt adható meg, hogy a SELECT utasítás eredményeit hol kell írni a külső adatforrásra. A gyökérmappa a külső adatforrásban megadott adathely. A HELY nek egy mappára kell mutatnia, és záró /. Példa: aggregated_data/

DATA_SOURCE = *external_data_source_name*

Megadja annak a külső adatforrás-objektumnak a nevét, amely a külső adatok tárolási helyét tartalmazza. Külső adatforrás létrehozásához használja [a CREATE EXTERNAL DATA SOURCE (Transact-SQL)](develop-tables-external-tables.md#create-external-data-source)parancsot.

FILE_FORMAT = *external_file_format_name*

Megadja a külső adatfájl formátumát tartalmazó külső fájlformátum-objektum nevét. Külső fájlformátum létrehozásához használja [a CREATE EXTERNAL FILE FORMAT (Transact-SQL)](develop-tables-external-tables.md#create-external-file-format)parancsot. Jelenleg csak a FORMAT='PARQUET' formátumú külső fájlformátumok támogatottak.

*<common_table_expression>*

Ideiglenes nevű eredményhalmazt, más néven közös táblakifejezést (CTE) ad meg. További információ: [WITH common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

SELECT <select_criteria>

Feltölti az új táblát a SELECT utasítás eredményeivel. *select_criteria* a SELECT utasítás törzse, amely meghatározza, hogy mely adatokat másolja az új táblába. A SELECT utasításokról a [SELECT (Transact-SQL)](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)című témakörben talál további információt.

## <a name="permissions"></a>Engedélyek

A CETAS működéséhez rendelkeznie kell jogosultságokkal a mappa tartalmának listázásához és a LOCATION mappába való íráshoz.

## <a name="examples"></a>Példák

Ezek a példák a CETAS segítségével menthetik az év és állapot szerint összesen összesített népességet egy aggregated_data mappába, amely a population_ds adatforrásban található.

Ez a minta a korábban létrehozott hitelesítő adatokra, adatforrásra és külső fájlformátumra támaszkodik. Tekintse meg a [külső táblák dokumentumát.](develop-tables-external-tables.md) Ha a lekérdezés eredményeit ugyanabban az adatforrásban lévő másik mappába szeretné menteni, módosítsa a LOCATION argumentumot. Ha az eredményeket egy másik tárfiókba szeretné menteni, hozzon létre és használjon egy másik adatforrást DATA_SOURCE argumentumhoz.

> [!NOTE]
> Az alábbi minták nyilvános Azure Open Data tárfiókot használnak. Csak olvasható. A lekérdezések végrehajtásához meg kell adnia azt az adatforrást, amelyhez írási engedéllyel rendelkezik.

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

Az alábbi minta egy külső táblát használ a CETAS forrásaként. A korábban létrehozott hitelesítő adatokra, adatforrásra, külső fájlformátumra és külső táblára támaszkodik. Tekintse meg a [külső táblák dokumentumát.](develop-tables-external-tables.md)

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

A CETAS a következő SQL-adattípusokkal rendelkező eredményhalmazok tárolására használható:

- binary
- varbinary között
- Char
- varchar
- dátum
- time
- datetime2
- tizedes tört
- numerikus
- lebegőpontos
- real
- bigint
- int
- smallint
- tinyint
- bit

A SE-k nem használhatók a CETAS-szal.

A CETAS SELECT részében nem használhatók a következő adattípusok:

- nchar
- nvarchar
- dátum/idő
- smalldatetime
- datetimeoffset
- Pénzt
- kis pénz
- uniqueidentifier

## <a name="next-steps"></a>További lépések

Megpróbálhatja lekérdezni a [Spark-táblákat.](develop-storage-files-spark-tables.md)

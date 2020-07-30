---
title: A parketta beágyazott típusai lekérdezése az SQL on-demand (előzetes verzió) használatával
description: Ebből a cikkből megtudhatja, hogyan lehet lekérdezni a parketta beágyazott típusait.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 60f2a1992750950b50995fcf36513e44e377004d
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386605"
---
# <a name="query-nested-types-in-parquet-and-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Beágyazott típusok lekérdezése a parketta és a JSON-fájlokban az SQL on-demand (előzetes verzió) használatával az Azure szinapszis Analyticsben

Ebből a cikkből megtudhatja, hogyan írhat egy lekérdezést az SQL on-demand (előzetes verzió) használatával az Azure szinapszis Analytics szolgáltatásban. Ez a lekérdezés a parketta beágyazott típusait olvassa be.
A beágyazott típusok olyan összetett struktúrák, amelyek objektumokat vagy tömböket jelképeznek. A beágyazott típusok a ben tárolhatók: 
- A [parketta](query-parquet-files.md) , ahol több összetett oszlop is tartalmaz tömböket és objektumokat.
- Hierarchikus [JSON-fájlok](query-json-files.md) , ahol az összetett JSON-dokumentumok egyetlen oszlopként is olvashatók.
- CosmosDB-gyűjtemény, amelyben minden dokumentum összetett beágyazott tulajdonságokat tartalmazhat (jelenleg a lezárt nyilvános előzetes verzióban érhető el).

A szinapszis SQL on-demand formátuma minden beágyazott típus JSON-objektumként és tömbökként, így az összetett objektumokat a JSON függvények használatával vagy [a JSON-adatok elemzésével openjson utasítással függvénnyel](https://docs.microsoft.com/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server)is [kinyerheti vagy módosíthatja](https://docs.microsoft.com/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server) . 

Alább látható egy példa arra a lekérdezésre, amely a [COVID-19 nyílt kutatási adatkészlet JSON-](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) fájljában a skaláris és objektumok értékeit Kinyeri a beágyazott objektumokkal. 

```sql
SELECT
    title = JSON_VALUE(doc, '$.metadata.title'),
    first_author = JSON_QUERY(doc, '$.metadata.authors[0]'),
    first_author_name = JSON_VALUE(doc, '$.metadata.authors[0].first'),
    complex_object = doc
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
        FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b'
    )
    WITH ( doc varchar(MAX) ) AS docs;
```

`JSON_VALUE`a függvény egy skaláris értéket ad vissza a megadott elérési úton lévő mezőből. `JSON_QUERY`a függvény a megadott elérési úton található mezőből JSON-ként formázott objektumot ad vissza.

> [!IMPORTANT]
> Ez a példa egy fájlt használ a [COVID-19 nyílt kutatási adatkészletből](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). Lásd: THS-licenc és az adatstruktúra ezen a lapon.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként létre kell **hoznia** egy olyan adatbázist, amely hivatkozik az adatforrásra. Ezután inicializálja az objektumokat a [telepítési parancsfájl](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) végrehajtásával az adatbázison. Ez a telepítési parancsfájl létrehozza az adatforrásokat, az adatbázis-hatókörrel rendelkező hitelesítő adatokat, valamint az ezekben a mintákban használt külső fájlformátumokat.

## <a name="project-nested-or-repeated-data"></a>Beágyazott vagy ismétlődő projekt

A parketta-fájl több, összetett típusú oszloppal is rendelkezhet. Az ezekből az oszlopokból származó értékek JSON-szövegként vannak formázva, és VARCHAR-oszlopként lesznek visszaadva. A következő lekérdezés beolvassa a *structExample. Parque* fájlt, és bemutatja, hogyan olvashatja el a beágyazott oszlopok értékeit: 

```sql
SELECT
    DateStruct, TimeStruct, TimestampStruct, DecimalStruct, FloatStruct
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        DateStruct VARCHAR(8000),
        TimeStruct VARCHAR(8000),
        TimestampStruct VARCHAR(8000),
        DecimalStruct VARCHAR(8000),
        FloatStruct VARCHAR(8000)
    ) AS [r];
```

Ez a lekérdezés a következő eredményt adja vissza, ahol a beágyazott objektumok tartalma JSON-szövegként lesz visszaadva:

| DateStruct    | TimeStruct    | TimestampStruct   | DecimalStruct | FloatStruct |
| --- | --- | --- | --- | --- |
|{"Date": "2009-04-25"}| {"Time": "20:51:54.3598000"}|    {"Időbélyeg": "5501-04-08 12:13:57.4821000"}|    {"Decimális": 11143412.25350}| {"Float": 0,5}|
|{"Date": "1916-04-29"}| {"Time": "00:16:04.6778000"}|    {"Időbélyeg": "1990-06-30 20:50:52.6828000"}|    {"Decimális": 1963545.62800}|  {"Float":-2,125}|

A következő lekérdezés beolvassa a *justSimpleArray. Parque* fájlt. A program a Parquet fájl összes oszlopát a beágyazott vagy ismétlődő adatokkal együtt feltervezi.

```sql
SELECT
    SimpleArray
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

A lekérdezés a következő eredményt fogja visszaadni:

|SimpleArray|
| --- |
|[11, 12, 13]|
|[21, 22, 23]|

## <a name="read-properties-from-nested-object-columns"></a>Tulajdonságok beolvasása a beágyazott objektumok oszlopaiból

`JSON_VALUE`a függvény lehetővé teszi, hogy a rendszer JSON-szövegként formázott oszlop értékeit állítsa vissza:

```sql
SELECT
    title = JSON_VALUE(complex_column, '$.metadata.title'),
    first_author_name = JSON_VALUE(complex_column, '$.metadata.authors[0].first'),
    body_text = JSON_VALUE(complex_column, '$.body_text.text'),
    complex_column
FROM
    OPENROWSET( BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
                FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b' ) WITH ( complex_column varchar(MAX) ) AS docs;
```

Az eredmény az alábbi táblázatban látható:

|cím  | first_author_name | body_text | complex_column |
| --- | --- | --- | --- |
| Kiegészítő információk öko-epidemiolo... | Julien   | – S1: törzsfejlődés... | `{    "paper_id": "000b7d1517ceebb34e1e3e817695b6de03e2fa78",    "metadata": {        "title": "Supplementary Information An eco-epidemiological study of Morbilli-related paramyxovirus infection in Madagascar bats reveals host-switching as the dominant macro-evolutionary mechanism",        "authors": [            {                "first": "Julien"` |

A legtöbb esetben a JSON-fájlokkal ellentétben egy összetett JSON-objektumot tartalmazó oszlopot ad vissza. A PARQUEt-fájlok több összetett fájllal rendelkezhetnek. A beágyazott oszlop tulajdonságait az `JSON_VALUE` egyes oszlopokban található függvény használatával olvashatja. `OPENROWSET`lehetővé teszi a beágyazott tulajdonságok elérési útjának közvetlen megadását a `WITH` záradékban. Az elérési utak az oszlop neveként állíthatók be, vagy az oszlop típusa után [JSON-elérésiút-kifejezést](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server) adhat hozzá.

A következő lekérdezés beolvassa a *structExample. Parque* fájlt, és bemutatja, hogyan lehet egy beágyazott oszlop felületi elemeit beolvasni. A beágyazott értékekre kétféleképpen hivatkozhat:
- A beágyazott érték elérési útjának kifejezésének megadása a típus megadása után.
- Az oszlopnév formázása beágyazott elérési úttal a do "." paranccsal a mezőkre való hivatkozáshoz.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        [DateValue] DATE '$.DateStruct.Date',
        [TimeStruct.Time] TIME,
        [TimestampStruct.Timestamp] DATETIME2,
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Elemek elérése ismétlődő oszlopokból

A következő lekérdezés beolvassa a *justSimpleArray. Parque* fájlt, és a [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) használatával beolvas egy **skaláris** elemet egy ismétlődő oszlopból, például egy tömbből vagy egy térképből:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Az eredmény az alábbi táblázatban látható:

|SimpleArray    | FirstElement  | SecondElement | ThirdElement |
| --- | --- | --- | --- |
| [11, 12, 13] | 11   | 12 | 13 |
| [21, 22, 23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>Az alárendelt objektumok elérése összetett oszlopokból

A következő lekérdezés beolvassa a *mapExample. Parque* fájlt, és a [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) használatával lekéri a **nem skaláris** elemet egy ismétlődő oszlopból, például tömbből vagy térképből:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Explicit módon hivatkozhat a záradékban visszaadni kívánt oszlopokra is `WITH` :

```sql
SELECT DocId,
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) 
    WITH (DocId bigint, MapOfPersons VARCHAR(max)) AS [r];
```

A struktúra `MapOfPersons` oszlopként lesz visszaadva, `VARCHAR` és JSON-sztringként van formázva.

## <a name="projecting-values-from-repeated-columns"></a>Értékek kivetítése ismétlődő oszlopokból

Ha egyes oszlopokban skaláris értékek (például) tömbje van `[1,2,3]` , egyszerűen kibonthatja őket, és a fő sorral csatlakoztathatja őket a következő parancsfájl használatával:

```sql
SELECT
    SimpleArray, Element
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS arrays
    CROSS APPLY OPENJSON (SimpleArray) WITH (Element int '$') as array_values
```

## <a name="next-steps"></a>Következő lépések

A következő cikk bemutatja, hogyan lehet [lekérdezni a JSON-fájlokat](query-json-files.md).

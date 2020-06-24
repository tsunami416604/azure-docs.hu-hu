---
title: Séma elemzése tömbökkel és beágyazott struktúrákkal
description: Tömbök és beágyazott struktúrák elemzése Apache Spark és SQL használatával
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 06/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: b02c3627cea5e441739c77d1882505c6b82489bc
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84908119"
---
# <a name="analyze-complex-data-types-in-synapse"></a>Összetett adattípusok elemzése a Szinapszisban

Ez a cikk az Azure-beli, Azure Cosmos DB-hez készült, az Azure-beli **szinapszis-hivatkozáshoz**tartozó Parquet-fájlok Elmagyarázza, hogy a felhasználók hogyan használhatják a Sparkot vagy az SQL-t összetett sémák, például tömbök vagy beágyazott struktúrák használatával történő olvasásra vagy átalakításra. Az alábbi példa egyetlen dokumentummal van elvégezve, de a Spark vagy az SQL használatával könnyedén méretezhetők a milliárd dokumentumok. Az alábbi kód PySpark (Python) használ.

## <a name="use-case"></a>Használati eset

A modern adattípusok esetében az összetett adattípusok gyakran gyakoriak az adatmérnökök számára feltett kihívás kezelésére és az azokra való feladatra. A beágyazott sémák és tömbök elemzése néhány kihívást jelent:
* Összetett az SQL-lekérdezések írásához
* Bonyolult a beágyazott oszlopok átnevezése/összeöntése
* Teljesítménnyel kapcsolatos problémák a mélyen beágyazott objektumokkal

Az adatmérnököknek ismerniük kell, hogyan dolgozzák fel hatékonyan ezeket az adattípusokat, és mindenki számára könnyen elérhetővé tegyék őket.

Az alábbi példában a szinapszis Spark az objektumok az adatkereteken keresztül egy egyszerű struktúrába való olvasására és átalakítására szolgál. A szinapszis SQL Server nélküli használatával közvetlenül ilyen objektumokat lehet lekérdezni, és az eredményeket normál táblázatként kell visszaadnia.

## <a name="what-are-arrays-and-nested-structures"></a>Mik azok a tömbök és beágyazott struktúrák?

A következő objektum az alkalmazás-betekintésből származik. Ebben az objektumban beágyazott struktúrák vannak, de beágyazott struktúrákat is tartalmazó tömbök is vannak.

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>A tömbök és beágyazott struktúrák sémájának példája
Amikor az objektum ( **DF**) adatkeretének sémáját kinyomtatja a **DF. printschema**paranccsal, a következő ábrázolás látható:

* a sárga szín beágyazott struktúrát jelöl
* a zöld szín két elemet tartalmazó tömböt jelöl

[![Séma forrása](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

_rid, _ts és _etag lettek hozzáadva a rendszerhez, mert a dokumentum bekerült a Azure Cosmos DB tranzakciós tárolóba.

A fenti adatkeret csak 5 oszlopra és 1 sorra számít. Az átalakítás után a kurátori adatkeret 13 oszlopból és 2 sorból áll majd táblázatos formában.

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>Beágyazott struktúrák összeolvasztása és a tömbök alábontása Apache Spark

A szinapszis Spark, a beágyazott struktúrák oszlopokba és tömbbe való átalakítása több sorba, egyszerű. Az alábbi lépéseket mindenki használhatja a saját megvalósításához.

[![A Spark átalakítások lépései](./media/how-to-complex-schema/spark-transfo-steps.png)](./media/how-to-complex-schema/spark-transfo-steps.png#lightbox)

**1. lépés**: a beágyazott séma összeolvasztására szolgáló függvény definiálása. Ez a függvény módosítás nélkül is használható. Hozzon létre egy cellát egy Pyspark-jegyzetfüzetben az adott függvénnyel:

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

**2. lépés**: használja a függvényt a **DF** beágyazott sémájának egy új adatkeretbe **df_flatához**:

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

A megjelenítési függvénynek 10 oszlopot és 1 sort kell visszaadnia. A tömb és a beágyazott elemei még mindig vannak.

**3. lépés**: most átalakítja a tömböt **context_custom_dimensions** az adatkeretben **df_flat** egy új dataframe **df_flat_explode**. Az alábbi kódban azt is megadjuk, hogy melyik oszlopot választjuk:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

A megjelenítési függvénynek a következő eredményt kell visszaadnia: 10 oszlop és 2 sor. A következő lépés a beágyazott sémák összeolvasztása az 1. lépésben meghatározott függvénnyel.

**4. lépés**: a függvény használatával a **df_flat_explode** adatkeret beágyazott sémáját egy új adatkeretbe **df_flat_explode_flat**:
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

A megjelenítési függvénynek 13 oszlopot és 2 sort kell megjelenítenie:

Az adatkeret df_flat_explode_flat függvény printSchema a következő eredményt adja vissza:

[![Séma végleges](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>Tömbök és beágyazott szerkezetek olvasása közvetlenül az SQL Server nélküli rendszerből

Az SQL Server nélküli lekérdezéssel, nézetek és táblázatok létrehozásával az ilyen objektumokon keresztül lehetséges.

Először is, az adattárolás módjától függően a felhasználóknak a következő besorolást kell használniuk. Minden nagybetűs eset a használati esetre vonatkozik:

| TÖMEGES              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |"Parquet" (ADLSg2)|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; Account = ACCOUNTNAME; adatbázis = databasename; gyűjtemény = COLLECTIONNAME; régió = REGIONTOQUERY, titkos = ' YOURSECRET ' |"CosmosDB" (szinapszis-hivatkozás)|



Az **SQL Server** nélküli az Azure szinapszis-hez készült társított szolgáltatást fogja támogatni Azure Cosmos db és HRE továbbítóhoz. A képesség jelenleg a szinapszis-hivatkozáshoz tartozó előzetes verzióban érhető el.

Cserélje le az alábbit:
* A "saját TÖMEGES fenti" érték a kapcsolódáshoz használandó adatforrás kapcsolati sztringje
* "A fenti típus" a forráshoz való kapcsolódáshoz használt formátummal

```sql
select *
FROM
openrowset(
BULK 'YOUR BULK ABOVE',
            FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

Két különböző típusú művelet végezhető el:
* Az alábbi sor a contextdataeventTime nevű oszlopot határozza meg, amely a beágyazott elemre hivatkozik: Context. eventTime
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

Ez a sor határozza meg a contextdataeventTime nevű oszlopot, amely a következő beágyazási elemre hivatkozik: Context>az adat>eventTime

* a **Cross Apply** paranccsal új sorok hozhatók létre a tömb alá tartozó egyes elemekhez, majd az összes olyan beágyazott objektum definiálható, amely az első ponthoz hasonló: 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

Ha a tömb 5 elemet tartalmaz 4 beágyazott struktúrával, az SQL Server nélküli az 5 sort és 4 oszlopot ad vissza.

Az SQL Server nélküli képes a helyi lekérdezésre, a tömböt 2 sorba leképezni, és az összes beágyazott struktúrát oszlopba megjeleníteni.

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan kérdezheti le a Spark-Azure Cosmos DB Azure szinapszis-hivatkozását](./synapse-link/how-to-query-analytical-store-spark.md)
* [A parketta beágyazott típusainak lekérdezése](./sql/query-parquet-nested-types.md) 
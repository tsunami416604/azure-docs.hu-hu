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
ms.openlocfilehash: ad6761466cc958235557609e929e641a0311ee43
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "86999177"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>Összetett adattípusok elemzése az Azure szinapszis Analyticsben

Ez a cikk a ( [Azure Cosmos db) szinapszis-hivatkozásban](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md)található Parquet-fájlokra és-tárolóra vonatkozik. Elmagyarázza, hogy a felhasználók hogyan használhatják a Sparkot vagy az SQLot az olyan összetett sémákkal való adatolvasásra vagy átalakításra, mint a tömbök vagy a beágyazott struktúrák. A következő példa egyetlen dokumentummal fejeződött be, de a Spark vagy az SQL használatával könnyedén méretezhetők több milliárd dokumentumra. A cikkben szereplő kód PySpark (Python) használ.

## <a name="use-case"></a>Használati eset

Az összetett adattípusok egyre gyakoribbak, és kihívást jelentenek az adatmérnökök számára, mivel a beágyazott sémák és tömbök elemzése általában időigényes és összetett SQL-lekérdezéseket tartalmaz. Emellett nehéz lehet átnevezni vagy átadni a beágyazott oszlopok adattípusát. A teljesítménnyel kapcsolatos problémák a mélyen beágyazott objektumok használatakor is felmerülhetnek.

Az adatmérnököknek ismerniük kell, hogyan kell hatékonyan feldolgozni az összetett adattípusokat, és könnyen elérhetővé tenni azokat mindenki számára.

A következő példában a szinapszis Spark az objektumok adatkereteken keresztüli, lapos struktúrába való olvasására és átalakítására szolgál. A szinapszis SQL Server nélküli lekérdezi az ilyen objektumokat közvetlenül, és az eredményeket normál táblázatként jeleníti meg.

## <a name="what-are-arrays-and-nested-structures"></a>Mik azok a tömbök és beágyazott struktúrák?

A következő objektum az [alkalmazás-betekintésből](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)származik. Ebben az objektumban beágyazott struktúrákat és tömböket tartalmaz, amelyek beágyazott struktúrákat tartalmaznak.

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
Az objektum adatkeretének ( **DF**) sémájának a paranccsal való nyomtatásakor a `df.printschema` következő ábrázolás látható:

* A sárga szín beágyazott struktúrát jelöl
* A zöld szín két elemet tartalmazó tömböt jelöl

[![Séma forrása](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

**_rid**, **_ts**és **_etag** lettek hozzáadva a rendszerhez, mivel a dokumentum Azure Cosmos db tranzakciós tárolóba lett betöltve.

A fenti adatkeret csak 5 oszlopra és 1 sorra számít. Az átalakítás után a kurátori adatkeret 13 oszlopból és 2 sorból áll majd táblázatos formában.

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>Beágyazott struktúrák összeolvasztása és a tömbök alábontása Apache Spark

A szinapszis Spark segítségével egyszerűen átalakíthatja a beágyazott struktúrákat oszlopokra és tömb elemekre több sorba. A következő lépések használhatók a megvalósításhoz.

[![A Spark átalakítások lépései](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

**1. lépés**: definiálunk egy függvényt a beágyazott séma összeolvasztásához. Ez a függvény módosítás nélkül is használható. Hozzon létre egy cellát egy [PySpark jegyzetfüzetben](quickstart-apache-spark-notebook.md) a következő függvénnyel:

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

**2. lépés**: használja a függvényt az adatkeret (**DF**) beágyazott sémájának egy új adatkeretbe való leválasztásához `df_flat` :

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

A megjelenítési függvénynek 10 oszlopot és 1 sort kell visszaadnia. A tömb és a beágyazott elemei még mindig vannak.

**3. lépés**: alakítsa át a tömböt `context_custom_dimensions` az adatkeretben `df_flat` egy új dataframe `df_flat_explode` . A következő kódban azt is megadhatja, hogy melyik oszlopot válassza ki:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

A megjelenítési függvénynek 10 oszlopot és 2 sort kell visszaadnia. A következő lépés a beágyazott sémák összeolvasztása az 1. lépésben meghatározott függvénnyel.

**4. lépés**: használja a függvényt az adatkeret beágyazott sémájának `df_flat_explode` egy új adatkeretbe való lelapulához `df_flat_explode_flat` :
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

A megjelenítési függvénynek 13 oszlopot és 2 sort kell megjelenítenie.

Az `printSchema` adatkeret funkciója a `df_flat_explode_flat` következő eredményt adja vissza:

[![Séma végleges](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>Tömbök és beágyazott szerkezetek olvasása közvetlenül az SQL Server nélküli rendszerből

A nézetek és a táblázatok lekérdezése és létrehozása az SQL Server nélküli szolgáltatással lehetséges.

Először is, az adattárolás módjától függően a felhasználóknak a következő besorolást kell használniuk. A nagybetűvel megjelenő minden esetben a használati esetre vonatkozik:

| TÖMEGES              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |"Parquet" (ADLSg2)|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; Account = ACCOUNTNAME; adatbázis = databasename; gyűjtemény = COLLECTIONNAME; régió = REGIONTOQUERY, titkos = ' YOURSECRET ' |"CosmosDB" (szinapszis-hivatkozás)|


> [!NOTE]
> Az SQL Server nélküli az Azure Cosmos és a HRE áteresztő szolgáltatáshoz kapcsolódó szinapszis-hivatkozáshoz tartozó társított szolgáltatást fogja támogatni. A képesség jelenleg a szinapszis-hivatkozáshoz tartozó előzetes verzióban érhető el.

Cserélje le az egyes mezőket a következőképpen:
* "A TÖMEGES fenti" = az adatforrás kapcsolati karakterlánca, amelyhez csatlakozik
* "A fenti típus" = a forráshoz való kapcsolódáshoz használt formátum

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

Két különböző típusú művelet létezik:

Az első Művelettípus a következő sorában van megadva, amely az nevű oszlopot határozza meg, amely a `contextdataeventTime` beágyazott elemre hivatkozik: Context. eventTime 
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

Ez a sor határozza meg a contextdataeventTime nevű oszlopot, amely a következő beágyazási elemre hivatkozik: Context>az adat>eventTime

A második Művelettípus a `cross apply` tömbben lévő minden elemhez új sorokat hoz létre, majd a minden olyan beágyazott objektumot definiál, amely az első ponthoz hasonló: 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

Ha a tömb 5 elemet tartalmaz 4 beágyazott struktúrával, az SQL Server nélküli az 5 sort és 4 oszlopot ad vissza. Az SQL Server nélküli képes a helyi lekérdezésre, a tömböt 2 sorba leképezni, és az összes beágyazott struktúrát oszlopba megjeleníteni.

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan kérdezheti le a Azure Cosmos DBhoz készült szinapszis-hivatkozást a Spark használatával](./synapse-link/how-to-query-analytical-store-spark.md)
* [A parketta beágyazott típusainak lekérdezése](./sql/query-parquet-nested-types.md) 
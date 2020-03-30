---
title: Egy-néhány relációs adat áttelepítése az Azure Cosmos DB SQL API-ba
description: Ismerje meg, hogyan kezelhetők az összetett adatáttelepítések az SQL API-ba irányuló, csak egy-a-kevés kapcsolathoz
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 467e9627a2623779bd808ca5aebdf76d8a5eda42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896635"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Egy-néhány relációs adat áttelepítése az Azure Cosmos DB SQL API-fiókba

Annak érdekében, hogy egy relációs adatbázisból az Azure Cosmos DB SQL API-ba, szükség lehet az adatmodell optimalizálása szükséges.

Az egyik gyakori transzformáció az adatok denormalizálása a kapcsolódó alelemek egy JSON-dokumentumba történő beágyazásával. Itt néhány lehetőséget is megvizsgálunk az Azure Data Factory vagy az Azure Databricks használatával. A Cosmos DB adatmodellezésével kapcsolatos általános útmutatásért tekintse át [az Azure Cosmos DB Adatmodellezését.](modeling-data.md)  

## <a name="example-scenario"></a>Példaforgatókönyv

Tegyük fel, hogy a következő két tábla van az SQL-adatbázisunkban, az Orders és az OrderDetails.


![Rendelés részletei](./media/migrate-relational-to-cosmos-sql-api/orders.png)

Ezt az egy-a-kevés kapcsolathoz egy JSON-dokumentumba szeretnénk egyesíteni az áttelepítés során. Ehhez létrehozhatunk egy T-SQL lekérdezést a "FOR JSON" használatával az alábbiak szerint:

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

A lekérdezés eredményei az alábbiak szerint néznek ki: 

![Rendelés részletei](./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png#lightbox)


Ideális esetben egyetlen Azure Data Factory (ADF) másolási tevékenységet szeretne használni az SQL-adatok forrásként történő lekérdezéséhez, és a kimenetet közvetlenül az Azure Cosmos DB-fogadóba szeretné írni megfelelő JSON-objektumként. Jelenleg nem lehetséges a szükséges JSON-átalakítás egyetlen másolási tevékenységben végrehajtani. Ha megpróbáljuk másolni a fenti lekérdezés eredményeit egy Azure Cosmos DB SQL API-tárolóba, az OrderDetails mezőt a dokumentum karakterlánc-tulajdonságaként fogjuk látni a várt JSON-tömb helyett.

Ezt a jelenlegi korlátozást az alábbi módokon tudjuk megkerülni:

* **Használja az Azure Data Factory két másolási tevékenység:** 
  1. JSON-formátumú adatok beszereznie az SQL-ből egy szöveges fájlba egy köztes blobtárolási helyen, és 
  2. Adatok betöltése a JSON-szövegfájlból egy azure Cosmos DB-tárolóba.

* **Az Azure Databricks használatával sql-ből olvashat, és az Azure Cosmos DB-be írhat** – itt két lehetőséget mutatunk be.


Nézzük meg részletesebben ezeket a megközelítéseket:

## <a name="azure-data-factory"></a>Azure Data Factory

Bár nem ágyazhatjuk be az OrderDetails-t JSON-tömbként a cél Cosmos DB-dokumentumba, két külön másolási tevékenység használatával megkerülhetjük a problémát.

### <a name="copy-activity-1-sqljsontoblobtext"></a>Tevékenység #1 másolása: SqlJsonToBlobText

A forrásadatok esetében egy SQL-lekérdezést használunk, hogy az eredményt egyetlen oszlopként kapjuk meg, amely soronként egy JSON-objektumot (a Sorrendet jelölve) az SQL Server OPENJSON és a FOR JSON PATH képességeinek használatával:

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

![ADF-másolat](./media/migrate-relational-to-cosmos-sql-api/adf1.png)


Az SqlJsonToBlobText másolási tevékenység fogadójában a "Tagolt szöveg" lehetőséget választjuk, és az Azure Blob Storage egy adott@concatmappájára mutatjuk dinamikusan generált egyedi fájlnévvel (például ' (pipeline(). RunId,".json").
Mivel a szöveges fájl nem igazán "tagolt", és nem akarjuk, hogy külön oszlopokba legyen elemezve vesszővel, és meg akarjuk őrizni a dupla idézőjeleket ("), az "Oszlophatároló" értéket tab ("\t") - vagy egy másik karaktert, amely nem fordul elő az adatokban - és "Idézet karakter" "Nincs idézőjel".

![ADF-másolat](./media/migrate-relational-to-cosmos-sql-api/adf2.png)

### <a name="copy-activity-2-blobjsontocosmos"></a>Tevékenység #2 másolása: BlobJsonToCosmos

Ezután módosítjuk az ADF-folyamatot a második másolási tevékenység hozzáadásával, amely az Azure Blob Storage-ban jelenik meg az első tevékenység által létrehozott szövegfájlhoz. "JSON" forrásként dolgozza fel, hogy a szövegfájlban található JSON-soronként egy dokumentumként szúrja be a Cosmos DB-fogadóba.

![ADF-másolat](./media/migrate-relational-to-cosmos-sql-api/adf3.png)

Opcionálisan hozzáadunk egy "Törlés" tevékenységet is a folyamathoz, hogy minden futtatás előtt törölje az összes korábbi fájlt, amely a /Orders/ mappában maradt. A mi ADF csővezeték most valahogy így néz ki:

![ADF-másolat](./media/migrate-relational-to-cosmos-sql-api/adf4.png)

Miután aktiválta a fenti folyamatot, megjelenik egy fájl, amely et a köztes Azure Blob Storage helyen létrehozott, amely soronként egy JSON-objektumot tartalmaz:

![ADF-másolat](./media/migrate-relational-to-cosmos-sql-api/adf5.png)

Azt is látjuk, megrendelések dokumentumok megfelelően beágyazott OrderDetails beilleszteni a Cosmos DB gyűjtemény:

![ADF-másolat](./media/migrate-relational-to-cosmos-sql-api/adf6.png)


## <a name="azure-databricks"></a>Azure Databricks

A Spark az [Azure Databricks-ben](https://azure.microsoft.com/services/databricks/) is használhatjuk az SQL Database-forrásból származó adatok másolásához az Azure Cosmos DB célhelyére anélkül, hogy létre tudnánk adni a köztes szöveges/JSON-fájlokat az Azure Blob Storage-ban. 

> [!NOTE]
> Az egyértelműség és az egyszerűség érdekében az alábbi kódrészletek explicit módon titkos adatbázis-jelszavakat tartalmaznak, de mindig használja az Azure Databricks titkos kulcsokat.
>

Először hozzuk létre és csatolja a szükséges [SQL-összekötő](https://docs.databricks.com/data/data-sources/sql-databases-azure.html) és az [Azure Cosmos DB összekötő](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) könyvtárak az Azure Databricks-fürthöz. A fürt újraindítása a könyvtárak betöltésének biztosításához.

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks1.png)

Ezután bemutatjuk a Scala és a Python két mintáját. 

### <a name="scala"></a>Scala
Itt a "FOR JSON" kimenettel rendelkező SQL-lekérdezés eredményeit kapjuk meg egy DataFrame-be:

```scala
// Connect to Azure SQL https://docs.databricks.com/data/data-sources/sql-databases-azure.html
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks2.png)

Ezután csatlakozunk a Cosmos DB adatbázisunkhoz és gyűjteményünkhöz:

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

Végül meghatározzuk a sémát, és from_json használatával alkalmazza a DataFrame-et, mielőtt a CosmosDB-gyűjteménybe mentené.

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks3.png)


### <a name="python"></a>Python

Alternatív megközelítésként előfordulhat, hogy json-átalakításokat kell végrehajtania a Sparkban (ha a forrásadatbázis nem támogatja a "For JSON" vagy hasonló műveletet), vagy párhuzamos műveleteket szeretne használni egy nagyon nagy adatkészlethez. Itt bemutatjuk a PySpark mintát. Először állítsa be a forrás- és céladatbázis-kapcsolatokat az első cellában:

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

Ezután lekérdezzük a forrásadatbázist (ebben az esetben az SQL Servert) mind a sorrend, mind a rendelési részletrekordok, és az eredményeket a Spark Dataframes-be helyezzük. Létrehozunk egy listát is, amely tartalmazza az összes rendelésazonosítót, és egy szálkészletet a párhuzamos műveletekhez:

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

Ezután hozzon létre egy függvényt a rendelések írásához a cél SQL API-gyűjteménybe. Ez a függvény szűri az adott rendelésazonosító összes rendelési részletét, jsontömbbe konvertálja őket, és beilleszti a tömböt egy JSON-dokumentumba, amelyet az adott sorrendben a cél SQL API-gyűjteménybe írunk:

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

Végül a fentieket egy térképfunkcióval hívjuk meg a szálkészleten, hogy párhuzamosan hajtsuk végre, átadva a korábban létrehozott rendelésazonosítók listáját:

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
Mindkét megközelítés, a végén, meg kell kapnia megfelelően mentett beágyazott OrderDetails belül minden rendelési dokumentum Cosmos DB gyűjtemény:

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks4.png)

## <a name="next-steps"></a>További lépések
* Információ az [azure Cosmos DB adatmodellezéséről](https://docs.microsoft.com/azure/cosmos-db/modeling-data)
* [Ismerje meg, hogyan modellezése és particionálása az Azure Cosmos DB-n](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example)

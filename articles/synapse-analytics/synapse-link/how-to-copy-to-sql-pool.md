---
title: A Azure Cosmos DB-beli SQL-készletbe másolt szinapszis-hivatkozás másolása Apache Spark használatával
description: Betöltheti az adatkészletet egy Spark-dataframe, megfordíthatja az adatgyűjtést, és betöltheti egy SQL Pool-táblába
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 08/10/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 88962d63519cfeb78be694c4f702b05ed4e7d3df
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88658508"
---
# <a name="copy-data-from-azure-cosmos-db-into-a-sql-pool-using-apache-spark"></a>Adatok másolása Azure Cosmos DBból egy SQL-készletbe Apache Spark használatával

Az Azure szinapszis hivatkozása Azure Cosmos DB lehetővé teszi, hogy a felhasználók közel valós idejű elemzést futtassanak a Azure Cosmos DBban lévő működési adaton. Vannak azonban olyan időpontok, amikor bizonyos adatokat össze kell összesíteni és bővíteni kell az adattárház-felhasználók kiszolgálásához. A kisegítő és az exportálási szinapszis-kapcsolati adat csak néhány cellával elvégezhető egy jegyzetfüzetben.

## <a name="prerequisites"></a>Előfeltételek
* [Szinapszis-munkaterület kiépítése](../quickstart-create-workspace.md) az alábbiakkal:
    * [Spark-készlet](../quickstart-create-apache-spark-pool-studio.md)
    * [SQL-készlet](../quickstart-create-sql-pool-studio.md)
* [Cosmos DB fiók kiépítése HTAP-tárolóval](../../cosmos-db/configure-synapse-link.md)
* [A Azure Cosmos DB HTAP-tároló összekötése a munkaterülettel](./how-to-connect-synapse-link-cosmos-db.md)
* [A megfelelő beállítással importálhatja az adatait egy SQL-készletbe a Sparkból](../spark/synapse-spark-sql-pool-import-export.md)

## <a name="steps"></a>Lépések
Ebben az oktatóanyagban csatlakozni fog az analitikai tárolóhoz, hogy ne legyen hatással a tranzakciós tárolóra (a rendszer nem használja fel a kérelmek egységeit). A következő lépéseket fogjuk elvégezni:
1. A Cosmos DB HTAP-tároló beolvasása Spark-dataframe
2. Eredmények összesítése új dataframe
3. Az adatgyűjtés egy SQL-készletbe

[![Spark – SQL-lépések](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png)](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png#lightbox)

## <a name="data"></a>Adatok
Ebben a példában egy **RetailSales**nevű HTAP-tárolót használunk. Egy **ConnectedData**nevű társított szolgáltatás része, és a következő sémával rendelkezik:
* _rid: sztring (null értékű = igaz)
* _ts: hosszú (null értékű = igaz)
* logQuantity: Double (null értékű = igaz)
* productCode: sztring (null értékű = igaz)
* mennyiség: hosszú (null értékű = igaz)
* Ár: hosszú (null értékű = igaz)
* azonosító: sztring (null értékű = igaz)
* hirdetés: hosszú (null értékű = igaz)
* storeId: hosszú (null értékű = igaz)
* weekStarting: hosszú (null értékű = igaz)
* _etag: sztring (null értékű = igaz)

Az értékesítéseket (*mennyiség*, *bevétel* (ár x mennyiség) *productCode* és *weekStarting* szerint összesítjük jelentéskészítési célokra. Végezetül a **dbo. productsales**nevű SQL Pool-táblába exportáljuk ezeket az adatfájlokat.

## <a name="configure-a-spark-notebook"></a>Spark-jegyzetfüzet konfigurálása
A fő nyelvként hozzon létre egy Spark-jegyzetfüzetet a Scala as Spark (Scala) néven. A jegyzetfüzet alapértelmezett beállítását használjuk a munkamenethez.

## <a name="read-the-data-in-spark"></a>A Sparkban található információk beolvasása
Olvassa el a Cosmos DB HTAP-tárolót a Sparktal egy dataframe az első cellában.

```java
val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "ConnectedData").
    option("spark.cosmos.container", "RetailSales").
    load()
```

## <a name="aggregate-the-results-in-a-new-dataframe"></a>Eredmények összesítése új dataframe

A második cellában az új dataframe szükséges átalakítást és összesítéseket futtatjuk, mielőtt betöltjük egy SQL-készlet adatbázisába.

```java
// Select relevant columns and create revenue
val df_olap_step1 = df_olap.select("productCode","weekStarting","quantity","price").withColumn("revenue",col("quantity")*col("price"))
//Aggregate revenue, quantity sold and avg. price by week and product ID
val df_olap_aggr = df_olap_step1.groupBy("productCode","weekStarting").agg(sum("quantity") as "Sum_quantity",sum("revenue") as "Sum_revenue").
    withColumn("AvgPrice",col("Sum_revenue")/col("Sum_quantity"))
```

## <a name="load-the-results-into-a-sql-pool"></a>Az eredmények betöltése SQL-készletbe

A harmadik cellában az SQL-készletbe tölti be az adathalmazt. Automatikusan létrehoz egy ideiglenes külső táblát, külső adatforrást és egy külső fájlformátumot, amelyet a rendszer a feladatok elvégzése után töröl.

```java
df_olap_aggr.write.sqlanalytics("arnaudpool.dbo.productsales", Constants.INTERNAL)
```

## <a name="query-the-results-with-sql"></a>Eredmények lekérdezése az SQL-sel

Az eredményt egy egyszerű SQL-lekérdezéssel kérdezheti le, például a következő SQL-parancsfájl használatával:
```sql
SELECT  [productCode]
,[weekStarting]
,[Sum_quantity]
,[Sum_revenue]
,[AvgPrice]
 FROM [dbo].[productsales]
```

A lekérdezés a következő eredményeket mutatja be diagram módban: [ ![ Spark – SQL lépések](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png)](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png#lightbox)

## <a name="next-steps"></a>További lépések
* [Azure Cosmos DB analitikus áruház lekérdezése Apache Spark](./how-to-query-analytical-store-spark.md)
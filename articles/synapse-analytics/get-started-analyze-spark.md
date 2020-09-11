---
title: 'Oktatóanyag: az első lépések elemzése a Sparktal'
description: Ebből az oktatóanyagból megismerheti, hogyan elemezheti az adatApache Spark
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: ebcec3907e40a8ba58aab841cd788c58ec7a94fe
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90017914"
---
# <a name="analyze-with-apache-spark"></a>Elemzés a Apache Spark

## <a name="analyze-nyc-taxi-data-in-blob-storage--using-spark"></a>NYC-taxi-adatelemzés a blob Storage-ban a Spark használatával

Ebből az oktatóanyagból megismerheti az adatok betöltését és elemzését az Azure szinapszis Apache Sparkával.

1. Az **adatközpontban** kattintson a jobb gombbal az **Azure Blob Storage > minta adatkészletek > nyc_tlc_yellow** elemre **, és**válassza az **új jegyzetfüzet kiválasztása** lehetőséget.
1. Ekkor létrejön egy új jegyzetfüzet a következő kóddal:
    ```
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```
1. A jegyzetfüzetben válasszon egy Spark-készletet a **csatolás** menüben
1. Kattintson a cella **Futtatás** parancsára

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>A New York-i taxi-szolgáltatás betöltése a Spark nyctaxi-adatbázisba

A **SQLDB1**egy táblájában elérhetők az adatkészletek. Töltse be egy **nyctaxi**nevű Spark-adatbázisba.

1. A szinapszis Studióban nyissa meg a **fejlesztés** központot.
1. Válassza a **+**  >  **Jegyzetfüzet**lehetőséget.
1. A jegyzetfüzet tetején állítsa a **csatolás** értéket **Spark1**értékre.
1. Válassza a **kód hozzáadása** lehetőséget egy jegyzetfüzet-kód cellájának hozzáadásához, majd illessze be a következő szöveget:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Nyissa meg az **adatközpontot** , kattintson a jobb gombbal az **adatbázisok**elemre, majd válassza a **frissítés**lehetőséget. Ezeket az adatbázisokat kell látnia:

    - **SQLDB1** (SQL-készlet)
    - **nyctaxi** (Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>A New York-i taxi-adat elemzése a Spark és a notebook használatával

1. Térjen vissza a jegyzetfüzetbe.
1. Hozzon létre egy új kódlapot, és írja be a következő szöveget. Ezután futtassa a cellát, hogy megjelenjenek a **nyctaxi** Spark-adatbázisba betöltött NYC-taxik adatai.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. A következő kód futtatásával végezze el ugyanezt az elemzést, amelyet korábban az SQL-készlet **SQLDB1**. Ez a kód az elemzés eredményeit egy **nyctaxi. passengercountstats** nevű táblába menti, és megjeleníti az eredményeket.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. A cella eredményei között válassza a **diagram** lehetőséget az információk vizualizációjának megtekintéséhez.

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Az adatvizualizációk testreszabása a Spark és a notebook használatával

Beállíthatja, hogy a diagramok hogyan legyenek megjelenítve jegyzetfüzetek használatával. Az alábbi kód egy egyszerű példát mutat be. A **matplotlib** és a **Seaborn**népszerű kódtárakat használja. A kód ugyanolyan típusú diagramot jelenít meg, mint a korábban futtatott SQL-lekérdezések.

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```



## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>Adatok betöltése egy Spark-táblából egy SQL-készlet táblájába

Korábban az SQL Pool táblázat **SQLDB1. dbo. Trip** fájlból másolt adatok a Spark Table **nyctaxi. Trip**. Ezután a Spark használatával összesítettük az adatokat a Spark Table **nyctaxi. passengercountstats**. Most átmásoljuk a **nyctaxi. passengercountstats** adatait az SQL Pool **SQLDB1. dbo. passengercountstats**nevű táblába.

Futtassa a következő cellát a jegyzetfüzetben. Az összesített Spark-táblázatot visszamásolja az SQL-készlet táblájába.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Adatelemzés a Storage szolgáltatásban](get-started-analyze-storage.md)



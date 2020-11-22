---
title: 'Rövid útmutató: a Spark elemzésének első lépései'
description: Ebből az oktatóanyagból megismerheti, hogyan elemezheti az adatApache Spark
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: d7b198790b1ecc884321ad42c97eb5cf0c239b7e
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2020
ms.locfileid: "95241981"
---
# <a name="analyze-with-apache-spark"></a>Elemzés a Apache Spark

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>NYC-taxi-adatelemzés a blob Storage-ban a Spark használatával

Ebből az oktatóanyagból megismerheti az adatok betöltését és elemzését az Azure szinapszis Apache Sparkával.

1. Az adatközpontban **kattintson az** **új erőforrás hozzáadása** elemre (a **csatolthoz** tartozó pluszjelre)  >> **tallózási mintákat**. 
1. Keresse meg a **New York-i taxi & limuzin Commission – Yellow taxi Trip Records** , és kattintson rá. 
1. A lap alján nyomja meg a **Folytatás** , majd az **adatkészlet hozzáadása** után gombot. 
1. Most az adatközpontban kattintson a jobb **gombbal** az **Azure Blob Storage >> minta adatkészletek elemre >> nyc_tlc_yellow** és **válassza az** **új jegyzetfüzet** elemet.
1. Ekkor létrejön egy új jegyzetfüzet a következő kóddal:
    ```
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```
1. A jegyzetfüzetben válasszon egy kiszolgáló nélküli Spark-készletet a **csatolás** menüben
1. A cella **futtatásának** kiválasztása
1. Ha csak a dataframe sémáját szeretné megtekinteni, futtasson egy cellát a következő kóddal:
    ```
    data_df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>A New York-i taxi-szolgáltatás betöltése a Spark nyctaxi-adatbázisba

A **SQLPOOL1** egy táblájában elérhetők az adatkészletek. Töltse be egy **nyctaxi** nevű Spark-adatbázisba.

1. A szinapszis Studióban nyissa meg a **fejlesztés** központot.
1. Válassza a **+**  >  **Jegyzetfüzet** lehetőséget.
1. A jegyzetfüzet tetején állítsa a **csatolás** értéket **Spark1** értékre.
1. Válassza a **kód hozzáadása** lehetőséget egy jegyzetfüzet-kód cellájának hozzáadásához, majd illessze be a következő szöveget:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Nyissa meg az **adatközpontot** , kattintson a jobb gombbal az **adatbázisok** elemre, majd válassza a **frissítés** lehetőséget. Ezeket az adatbázisokat kell látnia:

    - **SQLPOOL1 (SQL)**
    - **nyctaxi (Spark)**

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>A New York-i taxi-adat elemzése a Spark és a notebook használatával

1. Térjen vissza a jegyzetfüzetbe.
1. Hozzon létre egy új kódlapot, és írja be a következő szöveget. Ezután futtassa a cellát, hogy megjelenjenek a **nyctaxi** Spark-adatbázisba betöltött NYC-taxik adatai.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. A következő kód futtatásával végezze el ugyanezt az elemzést, amelyet korábban a dedikált SQL Pool- **SQLPOOL1** adott meg. Ez a kód az elemzés eredményeit egy **nyctaxi. passengercountstats** nevű táblába menti, és megjeleníti az eredményeket.

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





## <a name="load-data-from-a-spark-table-into-a-dedicated-sql-pool-table"></a>Adatok betöltése a Spark-táblából egy dedikált SQL Pool-táblába

Korábban a dedikált SQL Pool-táblázat **SQLPOOL1. dbo. Trip** fájlját másolta a Spark Table **nyctaxi. Trip**-ba. Ezután a Spark használatával összesítettük az adatokat a Spark Table **nyctaxi. passengercountstats**. Most átmásoljuk a **nyctaxi. passengercountstats** adatait egy dedikált SQL Pool-táblába, amelynek neve **SQLPOOL1. dbo. passengercountstats**.

Futtassa a következő cellát a jegyzetfüzetben. Az összesített Spark-táblázatot visszamásolja a dedikált SQL Pool-táblába.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az adatelemzés kiszolgáló nélküli SQL-készlettel](get-started-analyze-sql-on-demand.md)



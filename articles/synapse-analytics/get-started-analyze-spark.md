---
title: 'Rövid útmutató: a Spark elemzésének első lépései'
description: Ebből az oktatóanyagból megismerheti, hogyan elemezheti az adatApache Sparkt.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 3b5f5d64498922e9fc35942ff4570d801aa6c516
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118880"
---
# <a name="analyze-with-apache-spark"></a>Elemzés a Apache Spark

Ebből az oktatóanyagból megismerheti az adatok betöltését és elemzését az Azure szinapszis Apache Sparkával.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>NYC-taxi-adatelemzés a blob Storage-ban a Spark használatával

1. Az adatközpontban jelölje be az **új erőforrás hozzáadása** (a **csatolt** elem melletti plusz gomb) > a **Tallózás** **katalógust** .
1. Válassza a **New York-i taxi & Limousine Commission – Yellow taxi Trip Records** elemet.
1. Az oldal alján **válassza az**  >  **adatkészlet hozzáadása** elemet.
1. Az **adatközpontban** kattintson a jobb gombbal az **Azure Blob Storage** elemre, majd **válassza a** **minta adatkészletek**  >  **nyc_tlc_yellow** lehetőséget.
1. Válassza az **új jegyzetfüzet** lehetőséget, ha új jegyzetfüzetet szeretne létrehozni a következő kóddal:

    ```py
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```

1. A jegyzetfüzet **csatolás** menüjében válassza ki a kiszolgáló nélküli Spark-készletet.
1. Válassza a **Futtatás** lehetőséget a cellában.
1. Ha csak a dataframe sémáját szeretné látni, futtasson egy cellát a következő kóddal:

    ```py
    data_df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>A New York-i taxi-szolgáltatás betöltése a Spark nyctaxi-adatbázisba

Az adattábla a **SQLPOOL1** egyik táblájában érhető el. Töltse be egy **nyctaxi** nevű Spark-adatbázisba.

1. A szinapszis Studióban nyissa meg a **fejlesztés** központot.
1. Válassza a **+**  >  **Jegyzetfüzet** lehetőséget.
1. A jegyzetfüzet tetején állítsa a **csatolás** értéket **Spark1** értékre.
1. Válassza a **kód hozzáadása** lehetőséget egy jegyzetfüzet-kód cellájának hozzáadásához, majd írja be a következő szöveget:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Válassza a **Futtatás** lehetőséget a cellában.
1. Az **adatközpontban** kattintson a jobb gombbal az **adatbázisok** elemre, majd válassza a **frissítés** lehetőséget. Ezeket az adatbázisokat kell látnia:

    - **SQLPOOL1 (SQL)**
    - **nyctaxi (Spark)**

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>A New York-i taxi-adat elemzése a Spark és a notebook használatával

1. Térjen vissza a jegyzetfüzetbe.
1. Hozzon létre egy új kódlapot, és írja be a következő szöveget.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Futtassa a cellát a **nyctaxi** Spark-adatbázisba betöltött NYC-taxi adatai megjelenítéséhez.
1. Futtassa a következő kódot, és végezze el ugyanezt az elemzést, amelyet korábban a dedikált SQL Pool **SQLPOOL1** adott meg. Ez a kód menti és megjeleníti az elemzés eredményeit egy **nyctaxi. passengercountstats** nevű táblába.

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

Korábban átmásolta az adatok a dedikált SQL Pool-táblázat **SQLPOOL1. dbo. Trip** a Spark Table **nyctaxi. Trip**. Ezután összesíti az adatokat a Spark Table **nyctaxi. passengercountstats**. Most másolja az **nyctaxi. passengercountstats** adatait a dedikált SQL Pool-táblába **SQLPOOL1. dbo. passengercountstats** néven.

Futtassa a következő cellát a jegyzetfüzetben. Az összesített Spark-táblázatot visszamásolja a dedikált SQL Pool-táblába.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az adatelemzés kiszolgáló nélküli SQL-készlettel](get-started-analyze-sql-on-demand.md)

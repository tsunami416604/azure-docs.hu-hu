---
title: 'Oktatóanyag: az Azure szinapszis Analytics használatbavétele'
description: Lépések az Azure szinapszis alapvető fogalmai gyors megismeréséhez
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 05/19/2020
ms.openlocfilehash: 75c8d52a750567d3b34ad2aea236477ca8c97245
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171412"
---
# <a name="getting-started-with-azure-synapse-analytics"></a>Első lépések az Azure szinapszis Analytics használatával

Ez az oktatóanyag végigvezeti az Azure szinapszis Analytics beállításához és használatához szükséges alapvető lépéseken.

## <a name="prepare-a-storage-account-for-use-with-a-synapse-workspace"></a>Storage-fiók előkészítése a szinapszis-munkaterülettel való használatra

* A [Azure Portal](https://portal.azure.com) megnyitása
* Hozzon létre egy új Storage-fiókot a következő beállításokkal:
    |Tab|Beállítás | Ajánlott érték | Description |
    |---|---|---|---|
    |Alapvető beállítások|**Storage-fiók neve**| Bármilyen nevet megadhat.|Ebben a dokumentumban a következőre hivatkozunk: `contosolake` .|
    |Alapvető beállítások|**Fiók típusa**|A következőre kell beállítani`StorageV2`||
    |Alapvető beállítások|**Hely**|Bármelyik helyet kiválaszthatja| Javasoljuk, hogy a szinapszis-munkaterület és a Azure Data Lake Storage (ADLS) Gen2-fiók ugyanabban a régióban legyen.|
    |Felsőfokú|**Data Lake Storage Gen2**|`Enabled`| Az Azure szinapszis csak olyan Storage-fiókokkal működik, ahol ez a beállítás engedélyezve van.|

1. A Storage-fiók létrehozása után a bal oldali navigációs sávon válassza a **hozzáférés-vezérlés (iam)** lehetőséget. Ezután rendelje hozzá a következő szerepköröket, vagy győződjön meg arról, hogy azok már hozzá vannak rendelve. 
    a. * Rendelje hozzá magát a **tulajdonosi** szerepkörhöz a b Storage-fiókban. * Rendelje hozzá magát a Storage- **blob adat-tulajdonosi** szerepköréhez a Storage-fiókban
1. A bal oldali navigációs sávon válassza a **tárolók** lehetőséget, és hozzon létre egy tárolót. Bármilyen nevet megadhat. Fogadja el az alapértelmezett **nyilvános hozzáférési szintet**. Ebben a dokumentumban a tárolót fogjuk hívni `users` . Kattintson a **Létrehozás** gombra. 

## <a name="create-a-synapse-workspace"></a>Szinapszis-munkaterület létrehozása

* Nyissa meg a [Azure Portal](https://portal.azure.com) és a felső kereséssel `Synapse` .
* A **szolgáltatások**területen található keresési eredmények között válassza az **Azure szinapszis Analytics (munkaterületek előzetes verzió) lehetőséget.**
* Válassza a **+ Hozzáadás** lehetőséget, ha új munkaterületet szeretne létrehozni ezekkel a beállításokkal

    |Tab|Beállítás | Ajánlott érték | Description |
    |---|---|---|---|
    |Alapvető beállítások|**Munkaterület neve**|Bármilyen hívást megadhat.| Ebben a dokumentumban a következőt fogjuk használni:`myworkspace`|
    |Alapvető beállítások|**Régió**|A Storage-fiók régiójának egyeztetése|

1. A **2. generációs Data Lake Storage kiválasztása**területen válassza ki a korábban létrehozott fiókot és tárolót.
    > [!NOTE]
    > Az itt kiválasztott Storage-fiók a szinapszis munkaterülethez tartozó "elsődleges" Storage-fiókra hivatkozik. Ez a fiók az adattárolásra szolgál az Apache Spark-táblákban, illetve a Spark-készletek létrehozásakor vagy Spark-alkalmazások futtatásakor létrehozott naplók esetében.

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Kattintson a **Létrehozás** gombra. A munkaterület néhány percen belül elkészül.

## <a name="verify-the-synapse-workspace-msi-has-access-to-the-storage-account"></a>Annak ellenőrzése, hogy a szinapszis munkaterület MSI-je hozzáfér-e a Storage-fiókhoz

Lehetséges, hogy ezt már elvégezték. Minden esetben ellenőrizze, hogy van-e.

1. Nyissa meg a [Azure Portal](https://portal.azure.com) , és nyissa meg a munkaterülethez kiválasztott elsődleges Storage-fiókot.
1. A bal oldali navigációs sávon válassza a **hozzáférés-vezérlés (iam)** lehetőséget. Ezután rendelje hozzá a következő szerepköröket, vagy győződjön meg arról, hogy azok már hozzá vannak rendelve. 
    a. Rendelje hozzá a munkaterület identitását a Storage **blob adatközreműködői** szerepkörhöz a Storage-fiókban. A munkaterület identitásának neve megegyezik a munkaterülettel. Ebben a dokumentumban a munkaterület neve, `myworkspace` így a munkaterület identitása`myworkspaced`
1. Kattintson a **Mentés** gombra.
    
## <a name="launch-synapse-studio"></a>A szinapszis Studio elindítása

A szinapszis-munkaterület létrehozása után kétféleképpen nyithatja meg a szinapszis Studio alkalmazást:
* Nyissa meg a szinapszis munkaterületet a [Azure Portalban](https://portal.azure.com) , és az **Áttekintés** szakasz tetején válassza a **szinapszis Studio elindítása** elemet.
* Közvetlenül nyissa meg a https://web.azuresynapse.net munkaterületet, és jelentkezzen be.

## <a name="create-a-sql-pool"></a>SQL-készlet létrehozása

1. A szinapszis Studióban a bal oldali navigációs sávon válassza a **> SQL-készletek kezelése** lehetőséget.

    > [!NOTE] 
    > Az összes szinapszis-munkaterület egy előre létrehozott, **SQL on-demand**nevű készlettel rendelkezik.

1. Válassza az **+ új** lehetőséget, és adja meg a következő beállításokat:

    |Beállítás | Ajánlott érték | 
    |---|---|
    |**SQL-készlet neve**| `SQLDB1`|
    |**Teljesítményszint**|`DW100C`|

1. Válassza a **felülvizsgálat + létrehozás** , majd a **Létrehozás**lehetőséget.
1. Az SQL-készlet néhány percen belül elkészül.

    > [!NOTE]
    > A szinapszis SQL-készlet megfelel a (z) "Azure SQL Data Warehouse" néven használt

Az SQL-készletek számlázható erőforrásokat használnak, amíg fut. Így szükség esetén szüneteltetheti a készletet a költségek csökkentése érdekében.

Az SQL-készlet létrehozásakor egy SQL Pool-adatbázishoz (más néven **SQLDB1**) lesz társítva.

## <a name="create-an-apache-spark-pool"></a>Apache Spark-készlet létrehozása

1. A szinapszis Studióban a bal oldalon válassza a **kezelés > Apache Spark készletek** elemet.
1. Válassza az **+ új** lehetőséget, és adja meg a következő beállításokat:

    |Beállítás | Ajánlott érték | 
    |---|---|
    |**Apache Spark készlet neve**|`Spark1`
    |**Csomópont mérete**| `Small`|
    |**Csomópontok száma**| Állítsa be a minimumot 3 értékre, a maximumot 3 értékre|

1. Válassza a **felülvizsgálat + létrehozás** , majd a **Létrehozás**lehetőséget.
1. A Apache Spark-készlet néhány másodpercen belül elkészül.

> [!NOTE]
> A név ellenére egy Apache Spark-készlet nem olyan, mint egy SQL-készlet. Csak néhány alapszintű metaadatot használ, amely tájékoztatja a szinapszis-munkaterületet a Spark használatával kapcsolatban. 

Mivel ezek a metaadatok, a Spark-készletek nem indíthatók el és nem állíthatók le. 

Ha bármilyen Spark-tevékenységet végzett a Szinapszisban, meg kell adnia a használni kívánt Spark-készletet. A készlet tájékoztatja, hogy hány Spark-erőforrást kell használni. Csak a felhasznált erőforrások Thar kell fizetnie. Ha aktívan abbahagyja a készlet használatát, az erőforrások automatikusan időtúllépést okoznak, és újra lesznek hasznosítva.

> [!NOTE]
> A Spark-adatbázisok a Spark-készletektől függetlenül jönnek létre. A munkaterületnek mindig van egy **alapértelmezett** Spark-adatbázisa, és további Spark-adatbázisok is létrehozhatók.

## <a name="the-sql-on-demand-pool"></a>Az igény szerinti SQL-készlet

Minden munkaterülethez egy előre összeállított és nem törölhető, **SQL igény szerinti**készlet tartozik. Az igény szerinti SQL-készlet lehetővé teszi az SQL használatát anélkül, hogy létre kellene hoznia egy szinapszis SQL-készletet. A más típusú készletektől eltérően az SQL igény szerinti számlázása a lekérdezés futtatásához beolvasott adatmennyiségen alapul, a lekérdezés végrehajtásához használt erőforrások számától függetlenül.

* Az SQL on-demand saját SQL igény szerinti adatbázisokat is tartalmaz, amelyek egymástól függetlenül léteznek bármely SQL igény szerinti készletből.
* Jelenleg a munkaterületnek mindig pontosan egy SQL igény szerinti készlete van, amelyet **igény szerint SQL-** nek nevezünk.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>A New York-i taxi-mintaadatok betöltése a SQLDB1-adatbázisba

1. A szinapszis Studióban a legfelső kék menüben jelölje be a **?** ikonra.
1. Az első lépések **> első lépések központ** kiválasztása
1. A kártya **lekérdezési mintaadatok**elemnél válassza ki a nevű SQL-készletet.`SQLDB1`
1. Válassza ki a **lekérdezési adatelemet**. Ekkor megjelenik egy értesítés, amely a "mintaadatok betöltése" üzenetet jeleníti meg, majd eltűnik.
1. Megjelenik egy, a szinapszis Studio felső részén található világoskék értesítési sáv, amely azt jelzi, hogy a rendszer betölti az adatmennyiséget a SQLDB1. Várjon, amíg zöldre nem vált, majd zárja be.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Ismerkedjen meg az SQL-készletben található New York-i taxi-szolgáltatással

1. **A szinapszis** Studióban navigáljon az adatközponthoz
1. Navigáljon a **SQLDB1 > táblákhoz**. Ekkor több táblázat is be van töltve.
1. Kattintson a jobb gombbal a **dbo. Utazási** táblázat, és válassza az **új SQL-szkript > válassza a Top 100 sort**
1. A rendszer létrehoz egy új SQL-parancsfájlt, és automatikusan futtatja azt.
1. Figyelje meg, hogy az SQL-parancsfájl felső részén a **Kapcsolódás** automatikusan a SQLDB1 nevű SQL-készletre lesz beállítva.
1. Cserélje le az SQL-parancsfájl szövegét ezzel a kóddal, majd futtassa.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

1. Ez a lekérdezés azt mutatja be, hogy a teljes utazási távolság és az átlagos utazási távolság az utasok számával függ.
1. Az SQL-parancsfájl eredményének ablakában módosítsa a **nézetet** a diagramra, hogy az eredmények **táblázatos** megjelenítése megjelenjen

## <a name="load-the-nyc-taxi-sample-data-into-the-spark-nyctaxi-database"></a>A New York-i taxi-mintaadatok betöltése a Spark nyctaxi-adatbázisba

A (z) egy táblájában elérhetők az adatkészletek `SQLDB1` . Most betöltjük egy "nyctaxi" nevű Spark-adatbázisba.

1. A szinapszis Studióban navigáljon a **fejlesztés** hubhoz
1. **+** **Jegyzetfüzet** kiválasztása és kiválasztása
1. A jegyzetfüzet felső részén állítsa a **csatolás** értékre a következőre:`Spark1`
1. Válassza a **kód hozzáadása** lehetőséget egy jegyzetfüzet-kód cellájának hozzáadásához, és illessze be az alábbi szöveget:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Navigáljon az **adatközponthoz** , kattintson a jobb gombbal az **adatbázisok** elemre, és válassza a **frissítés**lehetőséget.
1. Most ezeket az adatbázisokat kell látnia:
    - SQLDB (SQL-készlet)
    - nyctaxi (Spark)
      
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>A New York-i taxi-adat elemzése a Spark és a notebook használatával

1. Visszatérés a jegyzetfüzetbe
1. Hozzon létre egy új kódlapot, írja be az alábbi szöveget, és futtassa a cellát, hogy például a Spark-ADATBÁZISba betöltött New York-i taxi-adathalmazt `nyctaxi` .

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. A következő kód futtatásával végezze el ugyanazt az elemzést, amelyet korábban az SQL-készlettel végzett `SQLDB1` . Ez a kód az elemzés eredményeit egy nevű táblába menti `nyctaxi.passengercountstats` , és megjeleníti az eredményeket.

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

1. A cella eredményei között válassza a **diagram** lehetőséget az adatvizualizáció megjelenítéséhez
 
## <a name="customize-data-visualization-data-with-spark-and-notebooks"></a>Adatvizualizációs adat testreszabása Spark és notebook használatával

A jegyzetfüzetek segítségével szabályozhatja a renderelési diagramok módját. A következő kód egy egyszerű példát mutat be a népszerű kódtárak és a használatával `matplotlib` `seaborn` . Ugyanazt a diagramot jeleníti meg, amelyet a korábbi SQL-lekérdezések futtatásakor látott.

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

Korábban egy SQL-készletből származó adatok lettek átmásolva `SQLDB1.dbo.Trip` egy Spark-táblába `nyctaxi.trip` . Ezután a Spark használatával összesítettük az adatokat a Spark-táblába `nyctaxi.passengercountstats` . Most átmásoljuk az adatait `nyctaxi.passengercountstats` egy SQL-címkészlet nevű táblába `SQLDB1.dbo.PassengerCountStats` . 

Futtassa az alábbi cellát a jegyzetfüzetben. Az összesített Spark-táblát az SQL-készlet táblába másolja vissza.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>NYC-alapú taxi-adatelemzés a Spark-adatbázisokban az SQL igény szerinti használatával 

A Spark-adatbázisok táblái automatikusan láthatók és az SQL igény szerint lekérdezhető.

1. A szinapszis Studióban navigáljon a **fejlesztés** hubhoz, és hozzon létre egy új SQL-parancsfájlt
1. Az **SQL igény szerinti** **kapcsolódásának** beállítása 
1. Illessze be a következő szöveget a parancsfájlba, és futtassa a parancsfájlt.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```
    > [!NOTE]
    > Amikor először futtat egy SQL on-demand-lekérdezést, a lekérdezés futtatásához szükséges SQL-erőforrások összegyűjtéséhez körülbelül 10 másodpercre lesz szükség az SQL igény szerinti lekérdezéséhez. A további lekérdezések nem igénylik ezt az időt, és sokkal gyorsabbak lesznek.
  
## <a name="orchestrate-activities-with-pipelines"></a>Tevékenységek koordinálása folyamatokkal

Az Azure szinapszis számos feladatát felhasználhatja. Ebben a szakaszban láthatja, milyen egyszerű.

1. A szinapszis Studióban navigáljon a **hangszerelő** hubhoz.
1. Válassza a **+** **folyamat**lehetőséget. Új folyamat jön létre.
1. Navigáljon a fejlesztés hubhoz, és keresse meg a korábban létrehozott jegyzetfüzeteket.
1. Húzza a jegyzetfüzetet a folyamatba.
1. A folyamatban válassza az **trigger hozzáadása > új/szerkesztés**lehetőséget.
1. Az **eseményindító** kiválasztása területen válassza az **új**lehetőséget, majd az ismétlődés beállításnál állítsa az eseményindítót 1 óránként futtatva.
1. Kattintson az **OK** gombra.
1. Válassza az **összes közzététele** lehetőséget, és a folyamat óránként fog futni.
1. Ha azt szeretné, hogy a folyamat most a következő órára való várakozás nélkül fusson, válassza az **trigger hozzáadása > új/szerkesztés**lehetőséget.

## <a name="working-with-data-in-a-storage-account"></a>Adatkezelés egy Storage-fiókban

Eddig az adatok a munkaterületen található adatbázisokban találhatók. Most bemutatjuk, hogyan dolgozhat a fájlokkal a Storage-fiókokban. Ebben az esetben a munkaterület és a munkaterület létrehozásakor megadott tároló elsődleges Storage-fiókját fogjuk használni.

* A Storage-fiók neve:`contosolake`
* A Storage-fiókban található tároló neve:`users`

### <a name="creating-csv-and-parquet-files-in-your-storage-account"></a>CSV-és Parquet-fájlok létrehozása a Storage-fiókban

Futtassa a következő kódot egy jegyzetfüzetben. Létrehoz egy CSV-fájlt és egy Parquet-fájlt a Storage-fiókban.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyzing-data-in-a-storage-account"></a>A Storage-fiókban tárolt adatok elemzése

1. **A szinapszis** Studióban navigáljon az adatközponthoz
1. **Csatolt** kijelölése
1. Navigáljon a **Storage-fiókok > sajátmunkaterület (elsődleges-contosolake)**
1. **Felhasználók kiválasztása (elsődleges) "**
1. Ekkor egy "NYCTaxi" nevű mappát kell megjelennie. A belsejében két "PassengerCountStats. csv" és "PassengerCountStats. Parque" nevű mappa jelenik meg.
1. Navigáljon a "PassengerCountStats. Parque" mappába.
1. Kattintson a jobb gombbal a parketta-fájlra, és válassza az **új jegyzetfüzet**lehetőséget, majd hozzon létre egy jegyzetfüzetet a következőhöz hasonló cellával:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Futtassa a cellát.
1. Kattintson a jobb gombbal a parketta-fájlra, és válassza az **új SQL-parancsfájl lehetőséget > válassza ki a TOP 100 sort**, így a következőhöz hasonló SQL-parancsfájlt fog létrehozni:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```
    
1. A szkriptben a **csatolás** mező az **igény szerinti SQL-** értékre lesz állítva.
1. Futtassa a szkriptet.

## <a name="visualize-data-with-power-bi"></a>Adatok megjelenítése Power BI használatával

A NYX-taxiból összesített adatkészleteket hoztunk létre két táblában:
* `nyctaxi.passengercountstats`
* `SQLDB1.dbo.PassengerCountStats`

Power BI munkaterületet összekapcsolhatja a szinapszis munkaterülettel. Ez lehetővé teszi, hogy egyszerűen beolvassa az adatait a Power BI munkaterületre, és közvetlenül a szinapszis-munkaterületen szerkesztheti a Power BI-jelentéseket.

### <a name="create-a-power-bi-workspace"></a>Power BI munkaterület létrehozása

1. Jelentkezzen be a [powerbi.microsoft.com](https://powerbi.microsoft.com/).
1. Hozzon létre egy nevű új Power BI-munkaterületet `NYCTaxiWorkspace1` .

### <a name="link-your-synapse-workspace-to-your-new-power-bi-workspace"></a>A szinapszis-munkaterület összekapcsolása az új Power BI munkaterülettel

1. A szinapszis Studióban navigáljon a **> társított szolgáltatások kezelése**elemre.
1. Válassza az **+ új** lehetőséget, majd a **Kapcsolódás Power bi** lehetőséget, és adja meg a következő mezőket:

    |Beállítás | Ajánlott érték | 
    |---|---|
    |**Név**|`NYCTaxiWorkspace1`|
    |**Munkaterület neve**|`NYCTaxiWorkspace1`|
        
1. Kattintson a **Létrehozás** gombra.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-synapse-workspace"></a>A szinapszis-munkaterületen lévő adatokat használó Power BI adatkészlet létrehozása

1. A szinapszis Studióban navigáljon a **fejlesztés > Power bi**.
1. Navigáljon a **NYCTaxiWorkspace1 > Power bi adatkészletek** elemre, és válassza az **új Power bi adatkészlet**lehetőséget.
1. Vigye az egérmutatót az `SQLDB1` adatbázis fölé, és válassza a **download. pbids fájlt**.
1. Nyissa meg a letöltött `.pbids` fájlt. 
1. Ez elindítja Power BI asztalt, és automatikusan csatlakozik a `SQLDB1` -hez a szinapszis-munkaterületen.
1. Ha megjelenik egy párbeszédpanel, amely az **SQL Server-adatbázis**néven jelenik meg: a. Válassza a **Microsoft-fiók**lehetőséget. 
    b. Válassza **a bejelentkezés** lehetőséget, és jelentkezzen be.
    c. Kattintson a **Csatlakozás** gombra.
1. Ekkor megnyílik a **navigátor** párbeszédpanel. Ha igen, jelölje be a **PassengerCountStats** tábla jelölőnégyzetét, és válassza a **Betöltés**lehetőséget.
1. Ekkor megjelenik A **kapcsolatbeállítások** párbeszédpanel. Válassza a **DirectQuery** elemet, majd kattintson **az OK gombra** .
1. Kattintson a bal oldali **jelentés** gombra.
1. **Diagram** hozzáadása a jelentéshez
    a. Húzza a **PasssengerCount** oszlopot a **vizualizációk > a b tengelyre** . Húzza a **SumTripDistance** és a **AvgTripDistance** oszlopokat a **vizualizációk > értékekre**.
1. A **Kezdőlap** lapon válassza a **Közzététel**lehetőséget.
1. A rendszer megkérdezi, hogy szeretné-e menteni a módosításokat. Kattintson a **Mentés** gombra.
1. A rendszer megkéri, hogy válasszon egy fájlnevet. Válassza ki `PassengerAnalysis.pbix` és válassza a **Mentés**lehetőséget.
1. Ekkor megkéri, hogy válasszon ki **egy célhelyet** , `NYCTaxiWorkspace1` és válassza a **kiválasztás**lehetőséget.
1. Várjon, amíg a közzététel befejeződik.

### <a name="configure-authentication-for-your-dataset"></a>Az adatkészlet hitelesítésének konfigurálása

1. [Powerbi.microsoft.com](https://powerbi.microsoft.com/) megnyitása és **Bejelentkezés**
1. A bal oldalon a **munkaterületek területen válassza ki** a `NYCTaxiWorkspace1` munkaterületet.
1. A munkaterületen belül látnia kell egy nevű adatkészletet `Passenger Analysis` és egy nevű jelentést `Passenger Analysis` .
1. Vigye az egérmutatót az `PassengerAnalysis` adatkészlet fölé, és válassza ki a három pontot tartalmazó ikont, és válassza a **Beállítások**lehetőséget.
1. Az **adatforrás hitelesítő**adatai területen állítsa be a **hitelesítési módszert** a **OAuth2** értékre, és válassza a **Bejelentkezés**lehetőséget.

### <a name="edit-a-report-in-synapse-studio"></a>Jelentés szerkesztése a szinapszis Studióban

1. Lépjen vissza a szinapszis studióba, és válassza a **Bezárás és frissítés** lehetőséget. 
1. Navigáljon a **fejlesztés** hubhoz 
1. Vigye az egérmutatót **Power BIre** , és kattintson az **Power bi-jelentések** csomópont frissítése elemre.
1. Most a **Power bi** alatt meg kell jelennie a következőnek: a. * A **NYCTaxiWorkspace1 > Power bi adatkészletek**alatt egy új, **PassengerAnalysis**nevű adathalmazt.
    b. * A **NYCTaxiWorkspace1 > Power bi a jelentések**területen egy új, **PassengerAnalysis**nevű jelentést.
1. Válassza ki a **PassengerAnalysis** jelentést. 
1. Ekkor megnyílik a jelentés, és most már szerkesztheti a jelentést közvetlenül a szinapszis Studio alkalmazásban.

## <a name="monitor-activities"></a>Tevékenységek monitorozása

1. A szinapszis Studióban navigáljon a figyelő központhoz.
1. Ezen a helyen megtekintheti a munkaterületen zajló összes tevékenység előzményeit, és azokat is, amelyek aktívak.
1. Ismerkedjen meg a **folyamat futtatásával**, **Apache Spark alkalmazásokkal**és **SQL-kérelmekkel** , és megtekintheti, hogy mit tett a munkaterületen.

## <a name="next-steps"></a>Következő lépések

További információ az [Azure szinapszis Analytics (előzetes verzió) szolgáltatásról](overview-what-is.md)


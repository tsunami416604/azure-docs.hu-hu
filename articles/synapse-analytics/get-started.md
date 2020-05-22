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
ms.openlocfilehash: 29e5ba149a99f350b8ad9244605470d8b9b61597
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749499"
---
# <a name="getting-started-with-azure-synapse-analytics"></a>Első lépések az Azure szinapszis Analytics használatával

Ez az oktatóanyag végigvezeti az Azure szinapszis Analytics beállításához és használatához szükséges alapvető lépéseken.

## <a name="prepare-a-storage-account-for-use-with-a-synapse-workspace"></a>Storage-fiók előkészítése a szinapszis-munkaterülettel való használatra

* A [Azure Portal](https://portal.azure.com) megnyitása
* Hozzon létre egy új Storage-fiókot a következő beállításokkal:
    * Az **alapok** lapon

    |Beállítás | Ajánlott érték | Leírás |
    |---|---|---|
    |**Storage-fiók neve**| Bármilyen nevet megadhat.|Ebben a dokumentumban a következőre hivatkozunk: `contosolake` .
    |**Fiók típusa**|A következőre kell beállítani`StorageV2`||
    |**Hely**|Bármelyik helyet kiválaszthatja| Javasoljuk, hogy a szinapszis-munkaterület és a Azure Data Lake Storage (ADLS) Gen2-fiók ugyanabban a régióban legyen.|
    ||||

    * A **speciális** lapon

    |Beállítás | Ajánlott érték | Leírás |
    |---|---|---|
    |**2. generációs Data Lake Storage**|`Enabled`| Az Azure szinapszis csak olyan Storage-fiókokkal működik, ahol ez a beállítás engedélyezve van.|
    ||||

* A Storage-fiók létrehozása után végezze el ezeket a szerepkör-hozzárendeléseket, vagy győződjön meg arról, hogy azok már hozzá vannak rendelve. A Storage-fiókban a bal oldali navigációs sávon válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
    * Rendeljen hozzá saját magát a **tulajdonosi** szerepkörhöz a Storage-fiókban
    * Rendeljen hozzá saját magát a Storage- **blob adattulajdonosi** szerepkörhöz a Storage-fiókban
* A bal oldali navigációs sávon válassza a **tárolók** lehetőséget, és hozzon létre egy tárolót. Bármilyen nevet megadhat. Fogadja el az alapértelmezett **nyilvános hozzáférési szintet**. Ebben a dokumentumban a tárolót fogjuk hívni `users` . Kattintson a **Létrehozás** gombra. 

## <a name="create-a-synapse-workspace"></a>Szinapszis-munkaterület létrehozása

* Nyissa meg a [Azure Portal](https://portal.azure.com) és a felső kereséssel `Synapse` .
* A **szolgáltatások**területen található keresési eredmények között válassza az **Azure szinapszis Analytics (munkaterületek előzetes verzió) lehetőséget.**
* Válassza a **+ Hozzáadás** lehetőséget
* **Alapbeállítások** lap:

    |Beállítás | Ajánlott érték | Leírás |
    |---|---|---|
    |**Munkaterület neve**|Bármilyen hívást megadhat.| Ebben a dokumentumban a következőt fogjuk használni:`myworkspace`
    |**Régió**|A Storage-fiók régiójának egyeztetése||
    |||

* A **2. generációs Data Lake Storage** kiválasztása területen válassza ki a korábban létrehozott fiókot és tárolót.

> [!NOTE]
> Az itt választott Storage-fiókot a szinapszis munkaterület "elsődleges" Storage-fiókjának nevezzük.

* Válassza az **Áttekintés + létrehozás** lehetőséget. Kattintson a **Létrehozás** gombra. A munkaterület néhány percen belül elkészül.

## <a name="verify-the-synapse-workspace-msi-has-access-to-the-storage-account"></a>Annak ellenőrzése, hogy a szinapszis munkaterület MSI-je hozzáfér-e a Storage-fiókhoz

Lehetséges, hogy ezt már elvégezték. Minden esetben ellenőrizze, hogy van-e.

* Nyissa meg a [Azure Portal](https://portal.azure.com) nyissa meg a munkaterülethez kiválasztott elsődleges Storage-fiókot.
* Győződjön meg arról, hogy a következő hozzárendelés létezik, vagy hozza létre, ha nem
    * Storage blob adatközreműködői szerepkör a Storage-fiókban a munkaterületen.
    * Ha ezt a szerepkört hozzá szeretné rendelni a munkaterülethez, válassza ki a Storage blob adatközreműködői szerepkört, hagyja meg az alapértelmezett **hozzárendelési jogosultságot** , és a **kiválasztás** mezőben adja meg a munkaterület nevét. Kattintson a **Mentés** gombra.
    
## <a name="launch-synapse-studio"></a>A szinapszis Studio elindítása

A szinapszis-munkaterület létrehozása után kétféleképpen nyithatja meg a szinapszis Studio alkalmazást:
* Nyissa meg a szinapszis munkaterületet a [Azure Portalban](https://portal.azure.com) , és az **Áttekintés** szakasz tetején válassza a **szinapszis Studio elindítása** elemet.
* Közvetlenül nyissa meg a https://web.azuresynapse.net munkaterületet, és jelentkezzen be.

## <a name="create-a-sql-pool"></a>SQL-készlet létrehozása

* A szinapszis Studióban a bal oldalon navigáljon **> SQL-készletek kezelése** elemre.
* Megjegyzés: az összes szinapszis-munkaterület egy előre létrehozott, **SQL on-demand**nevű készlettel rendelkezik.
* Válassza az **+ új** lehetőséget, és adja meg a következő beállításokat:

    |Beállítás | Ajánlott érték | 
    |---|---|---|
    |**SQL-készlet neve**| `SQLDB1`|
    |**Teljesítményszint**|`DW100C`|
* Válassza a **felülvizsgálat + létrehozás** , majd a **Létrehozás**lehetőséget.
* A készlet néhány percen belül elkészül.

> [!NOTE]
> A szinapszis SQL-készlet megfelel a (z) "Azure SQL Data Warehouse" néven használt

* Az SQL-készletek számlázható erőforrásokat használnak, amíg fut. Így szükség esetén szüneteltetheti a készletet a költségek csökkentése érdekében.
* Az SQL-készlet létrehozásakor egy SQL Pool-adatbázishoz (más néven **SQLDB1**) lesz társítva.

## <a name="create-an-apache-spark-pool-for-azure-synapse-analytics"></a>Apache Spark-készlet létrehozása az Azure szinapszis Analyticshez

* A szinapszis Studióban a bal oldalon válassza a **kezelés > Apache Spark készletek** elemet.
* Válassza az **+ új** lehetőséget, és adja meg a következő beállításokat:

    |Beállítás | Ajánlott érték | 
    |---|---|---|
    |**Apache Spark készlet neve**|`Spark1`
    |**Csomópont mérete**| `Small`|
    |**Csomópontok száma**| Állítsa be a minimumot 3 értékre, a maximumot 3 értékre|
    |||

* Válassza a **felülvizsgálat + létrehozás** , majd a **Létrehozás**lehetőséget.
* A Apache Spark-készlet néhány másodpercen belül elkészül.

> [!NOTE]
> A név ellenére egy Apache Spark-készlet nem olyan, mint egy SQL-készlet. Csak néhány alapszintű metaadatot használ, amely tájékoztatja a szinapszis-munkaterületet a Spark használatával kapcsolatban. 

* A metaadatokat tartalmazó Spark-készletek nem indíthatók el és nem állíthatók le. 
* Ha bármilyen Spark-tevékenységet végzett a Szinapszisban, meg kell adnia a használni kívánt Spark-készletet. A készlet tájékoztatja, hogy hány Spark-erőforrást kell használni. Csak a felhasznált erőforrások Thar kell fizetnie. Ha aktívan leállítja a készlet használatát, az erőforrások automatikusan időtúllépést és újrahasznosítást végeznek.
> [!NOTE]
> A Spark-adatbázisok a Spark-készletektől függetlenül jönnek létre. A munkaterületnek mindig van egy **alapértelmezett** Spark-adatbázisa, és további Spark-adatbázisok is létrehozhatók.

## <a name="sql-on-demand-pools"></a>Igény szerinti SQL-készletek

Az SQL on-demand olyan speciális SQL-készlet, amely mindig elérhető egy szinapszis-munkaterületen. Lehetővé teszi az SQL-sel való munkát anélkül, hogy létre kellene hoznia egy szinapszis SQL-készletet.

> [!NOTE]
> A más típusú készletektől eltérően az SQL igény szerinti számlázása a lekérdezés futtatásához beolvasott adatmennyiségen alapul, a lekérdezés végrehajtásához használt erőforrások számától függetlenül.

* Az SQL on-demand saját SQL igény szerinti adatbázisokat is tartalmaz, amelyek egymástól függetlenül léteznek bármely SQL igény szerinti készletből.
* Jelenleg a munkaterületnek mindig pontosan egy SQL igény szerinti készlete van, amelyet **igény szerint SQL-** nek nevezünk.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>A New York-i taxi-mintaadatok betöltése a SQLDB1-adatbázisba

* A szinapszis Studióban a legfelső kék menüben jelölje be a **?** ikont.
* Az első lépések **> első lépések központ** kiválasztása
* A kártya címkével ellátott **lekérdezési mintaadatok** területen válassza ki a nevű SQL-készletet.`SQLDB1`
* Válassza ki a **lekérdezési adatelemet**. Megjelenik egy értesítés, amely a "mintaadatok betöltése" üzenetet fogja látni, amely megjelenik majd.
* Megjelenik egy, a szinapszis Studio felső részén található világoskék értesítési sáv, amely azt jelzi, hogy a rendszer betölti az adatmennyiséget a SQLDB1. Várjon, amíg zöldre nem vált, majd zárja be.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Ismerkedjen meg az SQL-készletben található New York-i taxi-szolgáltatással

* **A szinapszis** Studióban navigáljon az adatközponthoz
* Navigáljon a **SQLDB1 > táblákhoz**. Ekkor több táblázat is be van töltve.
* Kattintson a jobb gombbal a **dbo. Utazási** táblázat, és válassza az **új SQL-szkript > válassza a Top 100 sort**
* A rendszer létrehoz egy új SQL-parancsfájlt, és automatikusan futtatja azt.
* Figyelje meg, hogy az SQL-parancsfájl felső részén a **Kapcsolódás** automatikusan a SQLDB1 nevű SQL-készletre lesz beállítva.
* Cserélje le az SQL-parancsfájl szövegét ezzel a kóddal, majd futtassa.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

* Ez a lekérdezés azt mutatja be, hogy a teljes utazási távolság és az átlagos utazási távolság az utasok számával függ.
* Az SQL-parancsfájl eredményei ablakban módosítsa a **nézetet** **diagramra** , hogy megjelenjenek az eredmények egy vonal diagramként való megjelenítése

## <a name="create-a-spark-database-and-load-the-nyc-taxi-data-into-it"></a>Hozzon létre egy Spark-adatbázist, és töltse be a New York-i taxi-adatkészletet

Az SQL Pool-adatbázisban elérhetők az adatkészletek. Most betöltjük egy Spark-adatbázisba.

* A szinapszis Studióban navigáljon a * * fejlesztés hubhoz
* **+** **Jegyzetfüzet** kiválasztása és kiválasztása
* A jegyzetfüzet felső részén állítsa a **csatolás** értékre a következőre:`Spark1`
* Válassza a **kód hozzáadása** lehetőséget egy jegyzetfüzet-kód cellájának hozzáadásához, és illessze be az alábbi szöveget:

    ```scala
    %% spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

 * Navigáljon az adatközponthoz, kattintson a jobb gombbal az adatbázisok elemre, és válassza a **frissítés** lehetőséget.
 * Most ezeket az adatbázisokat kell látnia:
     * SQLDB (SQL-készlet)
     * nyctaxi (Spark)
      
 ## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>A New York-i taxi-adat elemzése a Spark és a notebook használatával

 * Visszatérés a jegyzetfüzetbe
 * Hozzon létre egy új kódlapot, írja be az alábbi szöveget, és futtassa a cellát

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

 * Futtassa ezt a kódot, hogy ugyanazt az elemzést végezze el az SQL-készlettel korábban

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

 * A cella eredményei között válassza a **diagram** lehetőséget az adatvizualizáció megjelenítéséhez
 
## <a name="customize-data-visualization-data-with-spark-and-notebooks"></a>Adatvizualizációs adat testreszabása Spark és notebook használatával

A Spark-jegyzetfüzetek segítségével pontosan szabályozható a renderelési diagramok. A következő kód egy egyszerű példát mutat be a népszerű kódtárak matplotlib és a tengeri Born használatával. Ugyanezt a diagramot fogja megjeleníteni az SQL-lekérdezések korábbi futtatásakor.

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

Korábban egy SQL-készletből származó adatok lettek átmásolva egy Spark-adatbázisba. A Spark használatával összesítettük az adatokat a nyctaxi. passengercountstats. Most futtassuk az alábbi cellát egy jegyzetfüzetben, és az összesített táblát vissza kell másolni az SQL Pool-adatbázisba.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>NYC-alapú taxi-adatelemzés a Spark-adatbázisokban SQL-on igény szerint 

* A Spark-adatbázisok táblái automatikusan láthatók és az SQL igény szerint lekérdezhető
* A szinapszis Studióban navigáljon a fejlesztés hubhoz, és hozzon létre egy új SQL-parancsfájlt
* Az **SQL igény szerinti** **kapcsolódásának** beállítása 
* Illessze be a következő szöveget a szkriptbe:

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

* **Futtatás** kiválasztása
* Megjegyzés: az első futtatásakor a lekérdezés futtatásához szükséges SQL-erőforrások összegyűjtéséhez igénybe vesz körülbelül 10 másodpercet. A további lekérdezések nem igénylik ezt az időt.
  
## <a name="use-pipelines-to-orchestrate-activities"></a>Folyamatok összehangolása a tevékenységek használatával

Az Azure szinapszis számos feladatát felhasználhatja. Ebben a szakaszban láthatja, milyen egyszerű.

* A szinapszis Studióban navigáljon a hangszerelő hubhoz.
* Válassza a **+** **folyamat**lehetőséget. Új folyamat jön létre.
* Navigáljon a fejlesztés hubhoz, és keresse meg a korábban létrehozott jegyzetfüzeteket.
* Húzza a jegyzetfüzetet a folyamatba.
* A folyamatban válassza az **trigger hozzáadása > új/szerkesztés**lehetőséget.
* Az **eseményindító** kiválasztása területen válassza az **új**lehetőséget, majd az ismétlődés beállításnál állítsa az eseményindítót 1 óránként futtatva.
* Válassza az **OK** lehetőséget.
* Válassza az **összes közzététele** lehetőséget, és a folyamat óránként fog futni.
* Ha azt szeretné, hogy a folyamat azonnal fusson anélkül, hogy a következő órára kellene várnia, válassza az **aktiválás hozzáadása > új/szerkesztés**lehetőséget.

## <a name="working-with-data-in-a-storage-account"></a>Adatkezelés egy Storage-fiókban

Eddig az adatok az adatbázisokban találhatók. Most bemutatjuk, hogy az Azure szinapszis hogyan elemezheti az egyszerű fájlokat egy Storage-fiókban. Ebben a forgatókönyvben azt a Storage-fiókot és-tárolót fogjuk használni, amelyhez a munkaterületet csatoltuk.

A Storage-fiók neve: contosolake a tároló neve a Storage-fiókban: felhasználók

### <a name="creating-csv-and-parquet-files-in-your-storage-account"></a>CSV-és Parquet-fájlok létrehozása a Storage-fiókban

Futtassa a következő kódot egy jegyzetfüzetben. Létrehoz egy CSV-és egy parketta-adatkészletet a Storage-fiókban.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyzing-data-in-a-storage-account"></a>A Storage-fiókban tárolt adatok elemzése

* **A szinapszis** Studióban navigáljon az adatközponthoz
* **Csatolt** kijelölése
* Navigáljon a **Storage-fiókok > workspacename (elsődleges-contosolake)**
* **Felhasználók kiválasztása (elsődleges) "**
* Ekkor egy "NYCTaxi" nevű mappát kell megjelennie. A belsejében két "PassengerCountStats. csv" és "PassengerCountStats. Parque" nevű mappa jelenik meg.
* Navigáljon a "PassengerCountStats. Parque" mappába.
* Kattintson a jobb gombbal a parketta-fájlra, és válassza az új jegyzetfüzet lehetőséget, majd hozzon létre egy jegyzetfüzetet a következőhöz hasonló cellával:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

* Futtassa a cellát a Parquet-fájl Spark használatával történő elemzéséhez.
* Kattintson a jobb gombbal a parketta-fájlra, és válassza az új **SQL-parancsfájl lehetőséget > válassza ki a TOP 100 sort**, majd hozzon létre egy jegyzetfüzetet a következőhöz hasonló cellával:

    ```py
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```
    
* A szkript csatolva lesz az **SQL igény szerinti** futtatásához. Figyelje meg, hogy a rendszer kikövetkezteti a sémát a parketta fájlból.

## <a name="visualize-data-with-power-bi"></a>Adatok megjelenítése Power BI használatával

Az adatait mostantól könnyen elemezheti és láthatóvá teheti Power BIban. A szinapszis egyedi integrációt biztosít, amely lehetővé teszi egy Power BI munkaterület összekapcsolását a szinapszis munkaterületen. A Kezdés előtt kövesse az ebben a rövid útmutatóban ismertetett [lépéseket a Power bi](quickstart-power-bi.md) munkaterület összekapcsolásához.

### <a name="create-a-power-bi-workspace-and-link-it-to-your-synapse-workspace"></a>Power BI munkaterület létrehozása és csatolása a szinapszis-munkaterülethez

* Jelentkezzen be a [powerbi.microsoft.com](https://powerbi.microsoft.com/).
* Hozzon létre egy nevű új Power BI-munkaterületet `NYCTaxiWorkspace1` .
* A szinapszis Studióban navigáljon a **> társított szolgáltatások kezelése**elemre.
* Válassza az **+ új** lehetőséget, majd a **Kapcsolódás Power bi** lehetőséget, és adja meg a következő mezőket:

    |Beállítás | Ajánlott érték | 
    |---|---|---|
    |**Név**|`NYCTaxiWorkspace1`|
    |**Munkaterület neve**|`NYCTaxiWorkspace1`|
    |||
    
* Kattintson a **Létrehozás** gombra.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-synapse-workspace"></a>A szinapszis-munkaterületen lévő adatokat használó Power BI adatkészlet létrehozása

* A szinapszis Studióban navigáljon a **fejlesztés > Power bi**.
* Navigáljon a **NYCTaxiWorkspace1 > Power bi adatkészletek** elemre, és válassza az **új Power bi adatkészlet**lehetőséget.
* Vigye a kurzort a SQLDB1-adatbázis fölé, és válassza a **download. pbids fájlt**.
* Nyissa meg a letöltött `.pbids` fájlt. Ez elindítja Power BI asztalt, és automatikusan csatlakozik a SQLDB1-hez a szinapszis-munkaterületen.
* Ha megjelenik egy párbeszédpanel, amely az **SQL Server-adatbázis**néven látható:
    * Válassza a **Microsoft-fiók**lehetőséget. 
    * Válassza **a bejelentkezés** lehetőséget, és jelentkezzen be.
    * Kattintson a **Csatlakozás** gombra.
* Ekkor megnyílik a **navigátor** párbeszédpanel. Ha a **PassengerCountStats** tábla látható, válassza a **Betöltés**lehetőséget.
* Ekkor megjelenik A **kapcsolatbeállítások** párbeszédpanel. Válassza a **DirectQuery** elemet, majd kattintson **az OK gombra** .
* Kattintson a bal oldali **jelentés** gombra.
* **Diagram** hozzáadása a jelentéshez
    * Húzza a **PasssengerCount** oszlopot a **vizualizációk > tengelyre**
    * Húzza a **SumTripDistance** és a **AvgTripDistance** oszlopokat a **vizualizációk > értékekre**.
* A **Kezdőlap** lapon válassza a **Közzététel**lehetőséget.
* A rendszer megkérdezi, hogy szeretné-e menteni a módosításokat. Kattintson a **Mentés** gombra.
* A rendszer megkéri, hogy válasszon egy fájlnevet. Válassza ki `PassengerAnalysis.pbix` és válassza a **Mentés**lehetőséget.
* Ekkor megkéri, hogy válasszon ki **egy célhelyet** , `NYCTaxiWorkspace1` és válassza a **kiválasztás**lehetőséget.
* Várjon, amíg a közzététel befejeződik.

### <a name="configure-authentication-for-your-dataset"></a>Az adatkészlet hitelesítésének konfigurálása

* [Powerbi.microsoft.com](https://powerbi.microsoft.com/) megnyitása és **Bejelentkezés**
* A bal oldalon a **munkaterületek** területen válassza ki a `NYCTaxiWorkspace1` közzétett munkaterületet.
* A munkaterületen belül látnia kell egy nevű adatkészletet `Passenger Analysis` és egy nevű jelentést `Passenger Analysis` .
* Vigye az egérmutatót az `PassengerAnalysis` adatkészlet fölé, és válassza ki a három pontot tartalmazó ikont, és válassza a **Beállítások**lehetőséget.
* Az **adatforrás hitelesítő adatainál** állítsa be a hitelesítési módszert a **OAuth2** értékre, és válassza a **Bejelentkezés**lehetőséget.

### <a name="edit-a-report-report-in-synapse-studio"></a>Jelentés szerkesztése a szinapszis Studióban

* Lépjen vissza a szinapszis studióba, és válassza a **Bezárás és frissítés** lehetőséget, és most meg kell jelennie a következőknek:
    * **Power bi adatkészletek**alatt egy új, **PassengerAnalysis**nevű adathalmazt.
    * **Power bi adatkészletek**alatt egy új, **PassengerAnalysis**nevű jelentést.
* Kattintson a **PassengerAnalysis** jelentésre. 
    * Nem jelenik meg semmi, mert továbbra is konfigurálnia kell a hitelesítést az adatkészlethez.
* A SynapseStudio-ben navigáljon a **> Power bi fejlesztése > a munkaterület neve > Power bi-jelentésekhez**.
* Zárjunk be minden olyan ablakot, amely a Power BI jelentést mutatja.
* Frissítse a **Power bi jelentések** csomópontot.
* Válassza ki a jelentést, és most szerkesztheti a jelentést közvetlenül a szinapszis Studio alkalmazásban.

## <a name="monitor-activities"></a>Tevékenységek monitorozása

* A szinapszis Studióban navigáljon a figyelő központhoz.
* Ezen a helyen megtekintheti az összes olyan tevékenység előzményeit, amely a munkaterületen zajlik, és amelyek most aktívak.
* Ismerkedjen meg a **folyamat futtatásával**, **Apache Spark alkalmazásokkal**és **SQL-kérelmekkel** , és megtekintheti, hogy mit tett a munkaterületen.

## <a name="next-steps"></a>További lépések

További információ az [Azure szinapszis Analytics (előzetes verzió) szolgáltatásról](overview-what-is.md)


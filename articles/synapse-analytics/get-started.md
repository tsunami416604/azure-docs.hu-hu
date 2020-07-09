---
title: 'Oktatóanyag: Ismerkedés az Azure szinapszis Analytics szolgáltatással'
description: Ebből az oktatóanyagból megismerheti az Azure szinapszis Analytics beállításának és használatának alapvető lépéseit.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: daa8b594b06203c7de9a9b462be469dd71ed2e49
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791860"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Ismerkedés az Azure szinapszis Analytics szolgáltatással

Ez az oktatóanyag végigvezeti az Azure szinapszis Analytics beállításának és használatának alapszintű lépésein.

## <a name="prepare-a-storage-account"></a>Storage-fiók előkészítése

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
1. Hozzon létre egy új Storage-fiókot, amely a következő beállításokkal rendelkezik:

    |Tab|Beállítás | Ajánlott érték | Leírás |
    |---|---|---|---|
    |Alapvető beállítások|**Storage-fiók neve**| Bármilyen nevet megadhat.|Ebben a dokumentumban **contosolake**néven fogunk hivatkozni rá.|
    |Alapvető beállítások|**Fiók típusa**|**StorageV2**értékre kell állítani.||
    |Alapvető beállítások|**Hely**|Válassza ki a kívánt helyet.| Javasoljuk, hogy az Azure szinapszis Analytics-munkaterülete és Azure Data Lake Storage Gen2 fiókja ugyanabban a régióban legyen.|
    |Felsőfokú|**Data Lake Storage Gen2**|**Engedélyezve**| Az Azure szinapszis csak olyan Storage-fiókokkal működik, ahol ez a beállítás engedélyezve van.|
    |||||

1. A Storage-fiók létrehozása után a bal oldali panelen válassza a **hozzáférés-vezérlés (iam)** lehetőséget. Ezután rendelje hozzá a következő szerepköröket, vagy győződjön meg arról, hogy már hozzá van rendelve:
    1. Rendelje hozzá magát a **tulajdonosi** szerepkörhöz.
    1. Rendeljen hozzá saját magát a **Storage blob-adat tulajdonosi** szerepköréhez.
1. A bal oldali ablaktáblán válassza a **tárolók** lehetőséget, és hozzon létre egy tárolót.
1. Megadhatja a tároló nevét. Ebben a dokumentumban a Container **Users**nevet fogjuk megkeresni.
1. Fogadja el az alapértelmezett **nyilvános hozzáférési szint**beállítást, majd válassza a **Létrehozás**lehetőséget.

A következő lépésben úgy konfigurálja az Azure szinapszis-munkaterületet, hogy ezt a Storage-fiókot használja az "elsődleges" Storage-fiókként és a munkaterület adatai tárolására szolgáló tárolóként. A munkaterület Apache Spark táblákban tárolja az adattárakat. Egy **/Synapse/workspacename**nevű mappában tárolja a Spark-alkalmazás naplóit.

## <a name="create-a-synapse-workspace"></a>Szinapszis-munkaterület létrehozása

1. Nyissa meg a [Azure Portal](https://portal.azure.com), és a legfelső szintű keresés a **szinapszisban**.
1. A keresési eredmények között, a **szolgáltatások**területen válassza az **Azure szinapszis Analytics (munkaterületek előzetes verzió)** lehetőséget.
1. Válassza a **Hozzáadás** lehetőséget a munkaterület létrehozásához a következő beállítások használatával:

    |Tab|Beállítás | Ajánlott érték | Leírás |
    |---|---|---|---|
    |Alapvető beállítások|**Munkaterület neve**|Bármilyen hívást megadhat.| Ebben a dokumentumban a **sajátmunkaterület**-t fogjuk használni.|
    |Alapvető beállítások|**Régió**|A Storage-fiók régiójának egyeztetése.|

1. A **2. generációs Data Lake Storage kiválasztása**területen válassza ki a korábban létrehozott fiókot és tárolót.
1. Válassza a **felülvizsgálat +**  >  **Létrehozás**létrehozása lehetőséget. A munkaterület pár percen belül elkészül.

## <a name="verify-access-to-the-storage-account"></a>A Storage-fiókhoz való hozzáférés ellenőrzése

Előfordulhat, hogy az Azure szinapszis-munkaterülethez tartozó felügyelt identitások már hozzáférnek a Storage-fiókhoz. Az alábbi lépéseket követve győződjön meg arról, hogy:

1. Nyissa meg a munkaterülethez kiválasztott [Azure Portal](https://portal.azure.com) és elsődleges Storage-fiókot.
1. A bal oldali panelen válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
1. Rendelje hozzá a következő szerepköröket, vagy győződjön meg arról, hogy már hozzá van rendelve. Ugyanazt a nevet használjuk a munkaterület-identitáshoz és a munkaterület nevéhez.
    1. A Storage- **blob adatközreműködői** szerepköréhez a Storage-fiókban rendeljen **sajátmunkaterület** a munkaterület-identitáshoz.
    1. Rendelje hozzá a **sajátmunkaterület** a munkaterület neveként.

1. Kattintson a **Mentés** gombra.

## <a name="open-synapse-studio"></a>A szinapszis Studio megnyitása

Az Azure szinapszis-munkaterület létrehozása után kétféleképpen nyithatja meg a szinapszis Studio alkalmazást:

* Nyissa meg a szinapszis munkaterületet a [Azure Portal](https://portal.azure.com). Az **Áttekintés** szakasz tetején válassza a **szinapszis Studio elindítása**lehetőséget.
* Lépjen a https://web.azuresynapse.net munkaterületre, és jelentkezzen be.

## <a name="create-a-sql-pool"></a>SQL-készlet létrehozása

1. A szinapszis Studióban a bal oldali ablaktáblán válassza az **Manage**  >  **SQL-készletek**kezelése lehetőséget.
1. Válassza az **új** lehetőséget, és adja meg a következő beállításokat:

    |Beállítás | Ajánlott érték | 
    |---|---|---|
    |**SQL-készlet neve**| **SQLDB1**|
    |**Teljesítményszint**|**DW100C**|
    |||

1. Válassza a **felülvizsgálat +**  >  **Létrehozás**létrehozása lehetőséget. Az SQL-készlet néhány percen belül elkészül. Az SQL-készlet társítva van egy **SQLDB1**néven is ismert SQL Pool-adatbázishoz.

Az SQL-készlet számlázható erőforrásokat használ, amíg az aktív. A készletet később is szüneteltetheti a költségek csökkentése érdekében.

## <a name="create-an-apache-spark-pool"></a>Apache Spark-készlet létrehozása

1. A szinapszis Studióban a bal oldali ablaktáblán válassza a **Manage**  >  **Apache Spark készletek**kezelése lehetőséget.
1. Válassza az **új** lehetőséget, és adja meg a következő beállításokat:

    |Beállítás | Ajánlott érték | 
    |---|---|---|
    |**Apache Spark készlet neve**|**Spark1**
    |**Csomópont mérete**| **Kicsi**|
    |**Csomópontok száma**| Állítsa be a minimumot 3 értékre, a maximumot 3 értékre|

1. Válassza a **felülvizsgálat +**  >  **Létrehozás**létrehozása lehetőséget. A Apache Spark-készlet néhány másodpercen belül elkészül.

> [!NOTE]
> A név ellenére egy Apache Spark-készlet nem olyan, mint egy SQL-készlet. Ez csak néhány alapvető metaadat, amelyet az Azure szinapszis-munkaterület használatának elvégzéséhez használ a Sparktal való kommunikációhoz.

Mivel ezek a metaadatok, a Spark-készletek nem indíthatók el és nem állíthatók le.

Amikor Spark-tevékenységet hajt végre az Azure Szinapszisban, meg kell adnia a használni kívánt Spark-készletet. A készlet azt jelzi, hogy az Azure szinapszis hány Spark-erőforrást használ. Ön csak a felhasznált erőforrásokért fizet. Ha aktívan abbahagyja a készlet használatát, az erőforrások automatikusan időtúllépést és újrahasznosítást végeznek.

> [!NOTE]
> A Spark-adatbázisok a Spark-készletektől függetlenül jönnek létre. A munkaterületnek mindig van egy **alapértelmezett**nevű Spark-adatbázisa. További Spark-adatbázisokat is létrehozhat.

## <a name="the-sql-on-demand-pool"></a>Az igény szerinti SQL-készlet

Minden munkaterülethez egy előre elkészített, **SQL on-demand**nevű készlet tartozik. Ez a készlet nem törölhető. Az igény szerinti SQL-készlet lehetővé teszi az SQL használatát anélkül, hogy létre kellene hoznia egy SQL-készletet az Azure Szinapszisban.

A más típusú készletektől eltérően az SQL igény szerinti számlázása a lekérdezés futtatásához beolvasott adatmennyiségen alapul, nem a lekérdezés végrehajtásához használt erőforrások számától.

* Az SQL on-demand saját SQL igény szerinti adatbázisokat tartalmaz, amelyek egymástól függetlenül léteznek bármely SQL igény szerinti készletből.
* A munkaterületnek mindig van egy SQL igény szerinti, **igény**szerinti SQL on-demand nevű készlete.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>A New York-i taxi-mintaadatok betöltése a SQLDB1-adatbázisba

1. A szinapszis Studióban a legfelső kék menüben válassza a **?** ikonra.
1. Válassza az **első**lépéseket az első  >  **lépések központban**.
1. A **lekérdezési mintaadatok**feliratú kártyán válassza ki a **SQLDB1**nevű SQL-készletet.
1. Válassza ki a **lekérdezési adatelemet**. A "mintaadatok betöltése" értesítés röviden megjelenik. A szinapszis Studio teteje közelében lévő világoskék állapotsor azt jelzi, hogy a rendszer az SQLDB1-be tölti be az adatmennyiséget.
1. Az állapotsor zöldre váltása után zárja be a következőt:.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Ismerkedjen meg az SQL-készletben található New York-i taxi-szolgáltatással

1. A szinapszis Studióban nyissa meg **az** adatközpontot.
1. Nyissa meg a **SQLDB1**  >  **táblákat**. Ekkor több táblázat is betöltődik.
1. Kattintson a jobb gombbal a **dbo. Utazási** táblázat és válassza az **új SQL-parancsfájl**lehetőséget, majd  >  **válassza a Top 100 sort**.
1. Várjon, amíg a rendszer létrehoz egy új SQL-parancsfájlt, és futtatja azt.
1. Figyelje meg, hogy az SQL-parancsfájl felső részén a **Kapcsolódás** automatikusan a **SQLDB1**nevű SQL-készletre lesz beállítva.
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

    Ez a lekérdezés azt mutatja be, hogy a teljes utazási távolság és az átlagos utazási távolság az utasok számával függ.
1. Az SQL-parancsfájl eredményének ablakában módosítsa **View** a nézetet **diagramra** , hogy megjelenítse az eredmények vonal diagramként való megjelenítését.

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

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>NYC-alapú taxi-adatelemzés a Spark-adatbázisokban az SQL igény szerinti használatával

A Spark-adatbázisok táblái automatikusan láthatók, és az SQL igény szerint is lekérdezhető.

1. A szinapszis Studióban nyissa meg a **fejlesztés** csomópontot, és hozzon létre egy új SQL-szkriptet.
1. Állítsa be a **Kapcsolódás** az **SQL-hez igény szerint**lehetőséget.
1. Illessze be a következő szöveget a parancsfájlba, és futtassa a parancsfájlt.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > Amikor először futtat egy SQL on-demand lekérdezést, a lekérdezés futtatásához szükséges SQL-erőforrások összegyűjtéséhez körülbelül 10 másodpercre van szükség az SQL igény szerint. A további lekérdezések sokkal gyorsabban fognak megjelenni.
  
## <a name="orchestrate-activities-with-pipelines"></a>Tevékenységek koordinálása folyamatokkal

Az Azure szinapszis számos feladatát felhasználhatja.

1. A szinapszis Studióban nyissa meg a **hangszerelő** hubot.
1. Válassza **+**  >  a **folyamat** lehetőséget egy új folyamat létrehozásához.
1. Lépjen a **fejlesztés** hubhoz, és keresse meg a korábban létrehozott jegyzetfüzeteket.
1. Húzza a jegyzetfüzetet a folyamatba.
1. A folyamatban válassza a **Hozzáadás trigger**  >  **új/szerkesztés**lehetőséget.
1. Az **eseményindító kiválasztása**területen válassza az **új**lehetőséget, majd az **Ismétlődés** beállításnál állítsa az eseményindítót 1 óránként futtatva.
1. Válassza az **OK** lehetőséget.
1. Kattintson **Az összes közzététele** gombra. A folyamat óránként fut.
1. Ahhoz, hogy a folyamat azonnal fusson, a következő órában való várakozás nélkül válassza az új **trigger**  >  **/Szerkesztés**hozzáadása lehetőséget.

## <a name="work-with-data-in-a-storage-account"></a>Adattárolási fiókban tárolt adatmennyiség

Eddig olyan forgatókönyveket ismertetünk, amelyekben az adatok a munkaterület adatbázisaiban találhatók. Most bemutatjuk, hogyan dolgozhat a fájlokkal a Storage-fiókokban. Ebben a forgatókönyvben a munkaterület és a munkaterület létrehozásakor megadott tároló elsődleges Storage-fiókját fogjuk használni.

* A Storage-fiók neve: **contosolake**
* A tároló neve a Storage-fiókban: **felhasználók**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>CSV-és Parquet-fájlok létrehozása a Storage-fiókban

Futtassa a következő kódot egy jegyzetfüzetben. Létrehoz egy CSV-fájlt és egy Parquet-fájlt a Storage-fiókban.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyze-data-in-a-storage-account"></a>A Storage-fiókban tárolt adatelemzés

1. A szinapszis Studióban nyissa meg **az** adatközpontot, majd kattintson a **csatolt**elemre.
1. Nyissa meg a **Storage accounts**  >  **sajátmunkaterület (Primary-contosolake)**.
1. Válassza a **felhasználók (elsődleges)** lehetőséget. Ekkor megjelenik a **NYCTaxi** mappa. A belsejében két, **PassengerCountStats.csv** és **PassengerCountStats. Parque**nevű mappát kell látnia.
1. Nyissa meg a **PassengerCountStats. Parque** mappát. A belsejében egy olyan nevű parketta-fájl jelenik meg, mint a *00000-2638e00c-0790-496b-a523-578da9a15019-C000. Snappy. parketta*.
1. Kattintson a jobb gombbal a **. parketta**elemre, majd válassza az **új jegyzetfüzet**lehetőséget. Létrehoz egy jegyzetfüzetet, amely a következőhöz hasonló cellával rendelkezik:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Futtassa a cellát.
1. Kattintson a jobb gombbal a parketta fájlra, majd válassza az **új SQL-parancsfájl**lehetőséget, majd válassza a  >  **Top 100 sort**. A következőhöz hasonló SQL-parancsfájlt hoz létre:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

     A szkriptben a **csatolás** mező az **SQL on-demand**értékre van állítva.

1. Futtassa a szkriptet.

## <a name="visualize-data-with-power-bi"></a>Adatok megjelenítése Power BI használatával

A New York-i taxi-adatokból összesített adatkészleteket hoztunk létre két táblában:
- **nyctaxi.passengercountstats**
- **SQLDB1. dbo. PassengerCountStats**

Power BI munkaterületet az Azure szinapszis-munkaterülethez kapcsolhat. Ez lehetővé teszi, hogy egyszerűen beolvassa az adatait a Power BI munkaterületre. Az Power BI-jelentéseket közvetlenül az Azure szinapszis munkaterületen szerkesztheti.

### <a name="create-a-power-bi-workspace"></a>Power BI munkaterület létrehozása

1. Jelentkezzen be a [powerbi.microsoft.com](https://powerbi.microsoft.com/).
1. Hozzon létre egy új, **NYCTaxiWorkspace1**nevű Power bi-munkaterületet.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Az Azure szinapszis-munkaterület összekapcsolása az új Power BI munkaterülettel

1. A szinapszis Studióban lépjen a **Manage**  >  **társított szolgáltatások**kezelése elemre.
1. Válassza az **új**  >  **Kapcsolódás Power bi**lehetőséget, majd állítsa be a következő mezőket:

    |Beállítás | Ajánlott érték | 
    |---|---|
    |**Name (Név)**|**NYCTaxiWorkspace1**|
    |**Munkaterület neve**|**NYCTaxiWorkspace1**|

1. Kattintson a **Létrehozás** gombra.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Az Azure szinapszis-munkaterületen adatokat használó Power BI adatkészlet létrehozása

1. A szinapszis Studióban lépjen a **fejlesztés**  >  **Power bi**elemre.
1. Lépjen a **NYCTaxiWorkspace1**  >  **Power bi adatkészletek** elemre, és válassza az **új Power bi adatkészlet**lehetőséget.
1. Vigye a kurzort a **SQLDB1** -adatbázis fölé, és válassza a **download. pbids fájlt**.
1. Nyissa meg a letöltött **. pbids** fájlt. Power BI az asztal megnyílik, és automatikusan csatlakozik a **SQLDB1** -hez az Azure szinapszis-munkaterületen.
1. Ha megjelenik egy párbeszédpanel, amely az **SQL Server-adatbázis**néven látható:
    1. Válassza a **Microsoft-fiók**lehetőséget.
    1. Válassza a **Bejelentkezés** lehetőséget, és jelentkezzen be a fiókjába.
    1. Kattintson a **Csatlakozás** gombra.
1. A **navigátor** párbeszédpanel megnyitása után jelölje be a **PassengerCountStats** tábla jelölőnégyzetét, és válassza a **Betöltés**lehetőséget.
1. Ha megjelenik a **kapcsolatbeállítások** párbeszédpanel, válassza a **DirectQuery**  >  **OK**elemet.
1. Kattintson a bal oldalon található **jelentés** gombra.
1. **Diagram** hozzáadása a jelentéshez
    1. Húzza a **PassengerCount** oszlopot a **vizualizációk**  >  **tengelyére**.
    1. Húzza a **SumTripDistance** és a **AvgTripDistance** oszlopot a **vizualizációk**  >  **értékeire**.
1. A **Kezdőlap** lapon válassza a **Közzététel**lehetőséget.
1. A módosítások mentéséhez válassza a **Mentés** gombot.
1. Válassza ki a **PassengerAnalysis. pbix**fájl nevét, majd kattintson a **Mentés**gombra.
1. A **cél kiválasztása lapon**válassza a **NYCTaxiWorkspace1**lehetőséget, majd kattintson a **kiválasztás**elemre.
1. Várjon, amíg a közzététel befejeződik.

### <a name="configure-authentication-for-your-dataset"></a>Az adatkészlet hitelesítésének konfigurálása

1. Nyissa meg a [powerbi.microsoft.com](https://powerbi.microsoft.com/) , és **Jelentkezzen be**.
1. A bal oldalon, a **munkaterületek**területen válassza a **NYCTaxiWorkspace1** -munkaterületet.
1. A munkaterületen belül keresse meg az **utas-elemzés** nevű adatkészletet és egy **utas-elemzés**nevű jelentést.
1. Vigye a kurzort a **PassengerAnalysis** adatkészlet fölé, válassza a három pontot (...), majd válassza a **Beállítások**lehetőséget.
1. Az **adatforrás hitelesítő**adatai területen állítsa a **hitelesítési módszert** **OAuth2**értékre, majd válassza a **Bejelentkezés**lehetőséget.

### <a name="edit-a-report-in-synapse-studio"></a>Jelentés szerkesztése a szinapszis Studióban

1. Lépjen vissza a szinapszis studióba, és válassza a **Bezárás és frissítés**lehetőséget.
1. Nyissa meg a **fejlesztés** központot.
1. Mutasson a **Power bi** fölé, és válassza a **Power bi jelentések** csomópontjának frissítése lehetőséget.
1. **Power bi** alatt meg kell jelennie a következőknek:
    1. A **NYCTaxiWorkspace1**  >  **Power bi adatkészletek**alatt egy új, **PassengerAnalysis**nevű adathalmazt.
    1. A **NYCTaxiWorkspace1**  >  **Power bi-jelentések**területen egy új, **PassengerAnalysis**nevű jelentés szerepel.
1. Válassza ki a **PassengerAnalysis** jelentést. Megnyílik a jelentés, amely közvetlenül a szinapszis Studióban szerkeszthető.

## <a name="monitor-activities"></a>Tevékenységek monitorozása

1. A szinapszis Studióban nyissa meg a **figyelő** hubot.
1. Ezen a helyen megtekintheti a munkaterületen zajló összes tevékenység előzményeit, és azokat is, amelyek aktívak.
1. Ismerkedjen meg a **folyamat futtatásával**, **Apache Spark alkalmazásokkal**és **SQL-kérésekkel** , hogy megtekintse a munkaterületen már elvégzett munkát.

## <a name="next-steps"></a>További lépések

További információ az [Azure szinapszis Analytics szolgáltatásról (munkaterületek előzetes verzió)](overview-what-is.md).


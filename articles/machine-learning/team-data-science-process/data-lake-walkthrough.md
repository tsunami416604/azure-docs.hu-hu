---
title: Méretezhető adatelemzés az Azure Data Lake szolgáltatással – Csapatadat-elemzési folyamat
description: Hogyan használhatja az Azure Data Lake-et adatfeltárási és bináris besorolási feladatok elvégzésére egy adatkészleten.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9409f14b20684afa1a39d45e663ff316f405cc97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717926"
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Skálázható adatelemzés az Azure Data Lake használatával: egy végpontok között című forgatókönyv
Ez a forgatókönyv bemutatja, hogyan használhatja az Azure Data Lake adatfeltárási és bináris besorolási feladatok at a ccc taxi út és a viteldíj adatkészlet előrejelzéséhez, hogy egy tipp fizet egy viteldíjat. Végigvezeti a [csapat adatelemzési folyamat lépéseit,](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)az adatgyűjtéstől a modellbetanításig, majd a modellt közzéíró webszolgáltatás üzembe helyezéséig.

## <a name="technologies"></a>Technológiák

Ezek a technológiák ebben a forgatókönyvben használatosak.
* Azure Data Lake Analytics
* U-SQL és Visual Studio
* Python
* Azure Machine Learning
* Scripts


### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
A [Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) minden szükséges képességgel rendelkezik ahhoz, hogy az adatszakértők bármilyen méretű, alakú és sebességű adatokat tárolhassanak, valamint költséghatékony módon végezhet adatfeldolgozást, fejlett elemzéseket és gépi tanulási modellezést.   A fizetés munkakörönként történik, csak akkor, ha az adatok feldolgozása ténylegesen folyamatban van. Az Azure Data Lake Analytics tartalmazza az U-SQL nyelvet, amely az SQL deklaratív jellegét a C# kifejező erejével keveri a skálázható elosztott lekérdezési képesség biztosításához. Lehetővé teszi a strukturálatlan adatok feldolgozását séma olvasásra történő alkalmazásával, egyéni logika és felhasználó által definiált függvények (UDF-ek) beszúrásával, és bővíthetőséget is tartalmaz, hogy lehetővé tegye a részletes vezérlést a nagy méretekben történő végrehajtáshoz. Ha többet szeretne megtudni az U-SQL tervezési filozófiájáról, olvassa el a [Visual Studio blogbejegyzését.](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)

A Data Lake Analytics a Cortana Analytics Suite kulcsfontosságú része, és együttműködik az Azure SQL Data Warehouse, a Power BI és a Data Factory szolgáltatással. Ez a kombináció teljes felhőalapú big data-adatokat és fejlett elemzési platformot biztosít.

Ez a forgatókönyv az adatelemzési folyamat feladatainak elvégzéséhez szükséges előfeltételek és erőforrások telepítésének ismertetésével kezdődik. Ezután ismerteti az adatfeldolgozási lépéseket az U-SQL használatával, és befejezi, hogy bemutatja, hogyan használhatja a Python és a Hive az Azure Machine Learning Studio (klasszikus) a prediktív modellek létrehozásához és üzembe helyezéséhez.

### <a name="u-sql-and-visual-studio"></a>U-SQL és Visual Studio
Ez a forgatókönyv azt javasolja, hogy a Visual Studio segítségével szerkesztsd az U-SQL parancsfájlokat az adatkészlet feldolgozásához. Az U-SQL parancsfájlok itt vannak leírva, és külön fájlban vannak megadva. A folyamat magában foglalja az adatok betöltését, feltárását és mintavételezését. Azt is bemutatja, hogyan futtathatok egy U-SQL-parancsfájlalapú feladatot az Azure Portalon. Hive-táblák jönnek létre az adatok egy társított HDInsight-fürt, hogy megkönnyítse a létrehozása és üzembe helyezése egy bináris besorolási modell az Azure Machine Learning Studio-ban.

### <a name="python"></a>Python
Ez a forgatókönyv is tartalmaz egy szakaszt, amely bemutatja, hogyan hozhat létre és helyezhet üzembe egy prediktív modellt a Python használatával az Azure Machine Learning Studio használatával. Ez biztosítja a Jupyter notebook a Python-parancsfájlok a lépéseket ebben a folyamatban. A notebook kód néhány további funkció mérnöki lépések és modellek építése, mint például a többosztályos osztályozás és regressziómodellezés mellett a bináris osztályozási modell itt vázolt. A regressziós feladat a tipp mennyiségének előrejelzése más tippfunkciók alapján.

### <a name="azure-machine-learning"></a>Azure Machine Learning 
Az Azure Machine Learning Studio (klasszikus) a prediktív modellek létrehozásához és üzembe helyezéséhez két módszer rel használható: először Python-parancsfájlokkal, majd Hive-táblákkal egy HDInsight (Hadoop) fürtön.

### <a name="scripts"></a>Scripts
Ebben a forgatókönyvben csak a fő lépések jelennek meg. Letöltheti a teljes **U-SQL szkriptet** és a **Jupyter Notebook-ot** a [GitHubról.](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough)

## <a name="prerequisites"></a>Előfeltételek
A témakörök megkezdése előtt a következőkre van szüksége:

* Azure-előfizetés. Ha még nem rendelkezik ilyen, olvassa [el az Ingyenes Azure-próbaverzió beszereznie.](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)
* [Ajánlott] Visual Studio 2013-as vagy újabb verzió. Ha még nincs telepítve ezen verziók egyike, letöltheti az ingyenes közösségi verziót a [Visual Studio Community](https://www.visualstudio.com/vs/community/)programból.

> [!NOTE]
> A Visual Studio helyett az Azure Portalon is elküldheti az Azure Data Lake-lekérdezéseket. Ennek módjára vonatkozó utasítások mind a Visual Studióval, mind a portálon a Folyamat adatok az **U-SQL-lel**című szakaszban találhatók.
>
>


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Adatelemzési környezet előkészítése az Azure Data Lake-hez
Az adatelemzési környezet előkészítéséhez hozza létre a következő erőforrásokat:

* Azure Data Lake storage (ADLS)
* Azure Data Lake Analytics (ADLA)
* Azure Blob tárfiók
* Azure Machine Learning Studio (klasszikus) fiók
* Azure Data Lake-eszközök a Visual Studio számára (ajánlott)

Ez a szakasz az egyes erőforrások létrehozásának módját ismerteti. Ha úgy dönt, hogy a Hive-táblák az Azure Machine Learning, python helyett, egy modell létrehozásához, egy HDInsight (Hadoop) fürt kiépítése is. Ez az alternatív eljárás a 2.


> [!NOTE]
> Az **Azure Data Lake Store** létrehozható külön-külön, vagy amikor létrehozza az Azure Data Lake **Analytics** alapértelmezett tárolóként. A rendszer az egyes erőforrások külön-külön történő létrehozásához tartalmaz utasításokat, de a Data Lake tárfiókot nem kell külön létrehozni.
>
>

### <a name="create-an-azure-data-lake-storage"></a>Azure Data Lake-tárlétrehozása


Hozzon létre egy ADLS-t az [Azure Portalon.](https://portal.azure.com) További információt a [HDInsight-fürt létrehozása a Data Lake Store használatával az Azure Portal használatával című témakörben talál.](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) Ügyeljen arra, hogy állítsa be a fürt AAD-identitás a **DataSource** panelen a **választható konfiguráció** panel en leírt.

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Azure Data Lake Analytics-fiók létrehozása
Hozzon létre egy ADLA-fiókot az [Azure Portalon.](https://portal.azure.com) További részletek: [Oktatóanyag: Az Azure Data Lake Analytics használatának első lépései az Azure Portal használatával.](../../data-lake-analytics/data-lake-analytics-get-started-portal.md)

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Azure Blob-tárfiók létrehozása
Hozzon létre egy Azure Blob-tárfiókot az [Azure Portalon.](https://portal.azure.com) További információt az [Azure Storage-fiókok – például](../../storage/common/storage-create-storage-account.md)A tárfiók létrehozása című szakaszban talál.

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-classic-account"></a>Azure Machine Learning Studio (klasszikus) fiók beállítása
Regisztráljon/be az Azure Machine Learning Studio (klasszikus) az [Azure Machine Learning stúdió](https://azure.microsoft.com/services/machine-learning/) oldalán. Kattintson az **Első lépések** gombra, majd válasszon egy "Szabad munkaterületet" vagy "Normál munkaterületet". Most már készen áll a kísérletek létrehozására az Azure Machine Learning stúdióban.

### <a name="install-azure-data-lake-tools-recommended"></a>Az Azure Data Lake tools telepítése [Ajánlott]
Telepítse az Azure Data Lake Tools-t a Visual Studio verziójához az [Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)szolgáltatásból.

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

A telepítés befejezése után nyissa meg a Visual Studio alkalmazást. A Data Lake lapon a menü a tetején jelenik meg. Az Azure-erőforrások nak meg kell jelennie a bal oldali panelen, amikor bejelentkezik az Azure-fiókba.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>A NYC Taxi Trips adatkészlet
Az itt használt adatkészlet egy nyilvánosan elérhető adatkészlet - a [NYC Taxi Trips adatkészlet](https://www.andresmh.com/nyctaxitrips/). A NYC Taxi Trip adatok áll körülbelül 20 GB tömörített CSV fájlok (~ 48 GB tömörítetlen), felvétel több mint 173 millió egyéni utak és a viteldíjak fizetett minden út. Minden út rekord tartalmazza a felvételi és dropoff helyeken és időpontokban, anonimizált hack (driver's) engedély számát, és a medál (taxi egyedi azonosító) számát. Az adatok a 2013-as év összes utazására vonatkoznak, és minden hónapra vonatkozóan a következő két adatkészletben szerepelnek:

A "trip_data" CSV tartalmazza az utazás részleteit, például az utasok számát, a felvételi és leadási pontokat, az utazás időtartamát és az utazás hosszát. Íme néhány mintarekord:

       medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868



A "trip_fare" CSV tartalmazza az egyes utazásokért fizetett viteldíj részleteit, például a fizetés típusát, a viteldíj összegét, a pótdíjat és az adókat, a tippeket és az útdíjakat, valamint a teljes kifizetett összeget. Íme néhány mintarekord:

       medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A egyedülálló kulcs\_-hoz\_követ utazás adat és utazás boldogul van\_áll\_-ból következő három mezők: medál, csapkod engedély és pickup datetime. A nyers CSV-fájlok érhető el egy Azure Storage blob. Az illesztés U-SQL-parancsfájlja a [Join trip and fare tables (Csatlakozás utazás és viteldíjtáblák)](#join) szakaszban található.

## <a name="process-data-with-u-sql"></a>Adatok feldolgozása u-SQL-rel
Az ebben a szakaszban bemutatott adatfeldolgozási feladatok közé tartozik a betöltés, a minőség ellenőrzése, feltárása és az adatok mintavételezése. Hogyan lehet csatlakozni az utazáshoz és a viteldíjtáblákhoz is látható. Az utolsó szakasz ban egy U-SQL-parancsfájlalapú feladat futtatása látható az Azure Portalon. Az egyes alszakaszokra mutató hivatkozások:

* [Adatbetöltés: beolvasás a nyilvános blobból származó adatok között](#ingest)
* [Adatminőség-ellenőrzések](#quality)
* [Adatok feltárása](#explore)
* [Csatlakozzon az utazási és a viteldíjtáblákhoz](#join)
* [Adatmintavétel](#sample)
* [U-SQL-feladatok futtatása](#run)

Az U-SQL parancsfájlok itt vannak leírva, és külön fájlban vannak megadva. A teljes **U-SQL parancsfájlokat** letöltheti a [GitHubról.](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough)

Az U-SQL, Open Visual Studio végrehajtásához kattintson **a Fájl --> Új --> Project**parancsra, válassza az **U-SQL Project**lehetőséget, és mentse egy mappába.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> Lehetőség van az Azure Portal használatával az U-SQL végrehajtására a Visual Studio helyett. Az Azure Data Lake Analytics erőforrásra a portálon navigálhat, és a lekérdezéseket közvetlenül az alábbi ábrán látható módon küldheti el:
>
>

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="data-ingestion-read-in-data-from-public-blob"></a><a name="ingest"></a>Adatbetöltés: Nyilvános blobból származó adatok beolvasása

Az azure-blobban lévő adatok helye **wasb://container\_\@nevű\_\_blobtár-fiók\_name.blob.core.windows.net/blob_name** ként hivatkozik, és **az Extractors.Csv()** használatával nyerhető ki. Helyettesítse a saját tároló nevét és a\_\@tárfiók nevét a következő parancsfájlokban a tároló névblob-tárfiók\_\_\_nevéhez a wasb-címben. Mivel a fájlnevek formátumúak, az **\_utazási\_\{\*\}adatok .csv** segítségével mind a 12 trip fájlban olvashatók.

    ///Read in Trip data
    @trip0 =
        EXTRACT
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string
    // This is reading 12 trip data from blob
    FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
    USING Extractors.Csv();

Mivel az első sorban fejlécek vannak, el kell távolítania a fejléceket, és az oszloptípusokat megfelelőre kell módosítania. A feldolgozott adatokat mentheti az Azure Data Lake Storage szolgáltatásba **a swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ használatával, vagy az Azure Blob storage-fiókba **a wasb://container_name\@blob_storage_account_name.blob.core.windows.net/blob_name**használatával.

    // change data types
    @trip =
        SELECT
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        DateTime.Parse(pickup_datetime) AS pickup_datetime,
        DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
        Int32.Parse(passenger_count) AS passenger_count,
        Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
        Double.Parse(trip_distance) AS trip_distance,
        (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
        (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
        (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
        (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
    FROM @trip0
    WHERE medallion != "medallion";

    ////output data to ADL
    OUTPUT @trip
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
    USING Outputters.Csv();

    ////Output data to blob
    OUTPUT @trip
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
    USING Outputters.Csv();

Hasonlóképpen elolvashatja a viteldíj-adatkészleteket. Kattintson a jobb gombbal az Azure Data Lake Storage-ra, és megnézheti az adatokat az **Azure Portalon – > A Data Explorer** vagy a File **Explorer** a Visual Studióban.

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="data-quality-checks"></a><a name="quality"></a>Adatminőség-ellenőrzések
Az utazás- és viteldíjtáblák beolvasása után az adatminőség-ellenőrzést a következő módon lehet elvégezni. Az eredményül kapott CSV-fájlok kimenetel az Azure Blob storage vagy az Azure Data Lake Storage.

Keresse meg a medálok számát és a medálok egyedi számát:

    ///check the number of medallions and unique number of medallions
    @trip2 =
        SELECT
        medallion,
        vendor_id,
        pickup_datetime.Month AS pickup_month
        FROM @trip;

    @ex_1 =
        SELECT
        pickup_month,
        COUNT(medallion) AS cnt_medallion,
        COUNT(DISTINCT(medallion)) AS unique_medallion
        FROM @trip2
        GROUP BY pickup_month;
        OUTPUT @ex_1
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
    USING Outputters.Csv();

Keresse meg azokat a medálokat, amelyek több mint 100 utazást tettek:

    ///find those medallions that had more than 100 trips
    @ex_2 =
        SELECT medallion,
               COUNT(medallion) AS cnt_medallion
        FROM @trip2
        //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
        GROUP BY medallion
        HAVING COUNT(medallion) > 100;
        OUTPUT @ex_2
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
    USING Outputters.Csv();

Keresse meg ezeket az érvénytelen rekordokat a pickup_longitude szempontjából:

    ///find those invalid records in terms of pickup_longitude
    @ex_3 =
        SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
        FROM @trip
        WHERE
        pickup_longitude <- 90 OR pickup_longitude > 90;
        OUTPUT @ex_3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
    USING Outputters.Csv();

Egyes változók hiányzó értékeinek megkeresése:

    //check missing values
    @res =
        SELECT *,
               (medallion == null? 1 : 0) AS missing_medallion
        FROM @trip;

    @trip_summary6 =
        SELECT
            vendor_id,
        SUM(missing_medallion) AS medallion_empty,
        COUNT(medallion) AS medallion_total,
        COUNT(DISTINCT(medallion)) AS medallion_total_unique
        FROM @res
        GROUP BY vendor_id;
    OUTPUT @trip_summary6
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
    USING Outputters.Csv();



### <a name="data-exploration"></a><a name="explore"></a>Adatok feltárása
Végezze el az adatok feltárását a következő parancsfájlokkal, hogy jobban megértse az adatokat.

Keresse meg a hegytelen és a nem hegyű utak eloszlását:

    ///tipped vs. not tipped distribution
    @tip_or_not =
        SELECT *,
               (tip_amount > 0 ? 1: 0) AS tipped
        FROM @fare;

    @ex_4 =
        SELECT tipped,
               COUNT(*) AS tip_freq
        FROM @tip_or_not
        GROUP BY tipped;
        OUTPUT @ex_4
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
    USING Outputters.Csv();

Keresse meg a tipp összegének eloszlását a következő levágási értékekkel: 0, 5, 10 és 20 dollár.

    //tip class/range distribution
    @tip_class =
        SELECT *,
               (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
        FROM @fare;
    @ex_5 =
        SELECT tip_class,
               COUNT(*) AS tip_freq
        FROM @tip_class
        GROUP BY tip_class;
        OUTPUT @ex_5
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
    USING Outputters.Csv();

Keresse meg az utazás távolságának alapvető statisztikáit:

    // find basic statistics for trip_distance
    @trip_summary4 =
        SELECT
            vendor_id,
            COUNT(*) AS cnt_row,
            MIN(trip_distance) AS min_trip_distance,
            MAX(trip_distance) AS max_trip_distance,
            AVG(trip_distance) AS avg_trip_distance
        FROM @trip
        GROUP BY vendor_id;
    OUTPUT @trip_summary4
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
    USING Outputters.Csv();

Keresse meg az utazási távolság százalékos arányát:

    // find percentiles of trip_distance
    @trip_summary3 =
        SELECT DISTINCT vendor_id AS vendor,
                        PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
        FROM @trip;
       // group by vendor_id;
    OUTPUT @trip_summary3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
    USING Outputters.Csv();


### <a name="join-trip-and-fare-tables"></a><a name="join"></a>Csatlakozzon az utazási és a viteldíjtáblákhoz
Az utazási és viteldíjtáblákhoz medál, hack_license és pickup_time is csatlakozhat.

    //join trip and fare table

    @model_data_full =
    SELECT t.*,
    f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
    (f.tip_amount > 0 ? 1: 0) AS tipped,
    (f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @trip AS t JOIN  @fare AS f
    ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
    WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

    //// output to blob
    OUTPUT @model_data_full
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
    USING Outputters.Csv();

    ////output data to ADL
    OUTPUT @model_data_full
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
    USING Outputters.Csv();


Az utasok számának minden szintjére számítsa ki a rekordok számát, az átlagos tippösszeget, a tipp összegének varianciáját, a véglegelt utazások százalékos arányát.

    // contingency table
    @trip_summary8 =
        SELECT passenger_count,
               COUNT(*) AS cnt,
               AVG(tip_amount) AS avg_tip_amount,
               VAR(tip_amount) AS var_tip_amount,
               SUM(tipped) AS cnt_tipped,
               (float)SUM(tipped)/COUNT(*) AS pct_tipped
        FROM @model_data_full
        GROUP BY passenger_count;
        OUTPUT @trip_summary8
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
    USING Outputters.Csv();


### <a name="data-sampling"></a><a name="sample"></a>Adatmintavétel
Először véletlenszerűen válassza ki az adatok 0,1%-át az egyesített táblából:

    //random select 1/1000 data for modeling purpose
    @addrownumberres_randomsample =
    SELECT *,
            ROW_NUMBER() OVER() AS rownum
    FROM @model_data_full;

    @model_data_random_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_randomsample
    WHERE rownum % 1000 == 0;

    OUTPUT @model_data_random_sample_1_1000
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
    USING Outputters.Csv();

Ezután rétegzett mintavétel bináris változóval tip_class:

    //stratified random select 1/1000 data for modeling purpose
    @addrownumberres_stratifiedsample =
    SELECT *,
            ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
    FROM @model_data_full;

    @model_data_stratified_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_stratifiedsample
    WHERE rownum % 1000 == 0;
    //// output to blob
    OUTPUT @model_data_stratified_sample_1_1000
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv();
    ////output data to ADL
    OUTPUT @model_data_stratified_sample_1_1000
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv();


### <a name="run-u-sql-jobs"></a><a name="run"></a>U-SQL-feladatok futtatása
Az U-SQL-parancsfájlok szerkesztése után elküldheti őket a kiszolgálónak az Azure Data Lake Analytics-fiókjával. Kattintson **a Data Lake**menü Feladat **küldése**parancsára, válassza ki **analytics-fiókját,** válassza **a Párhuzamosság**lehetőséget, és kattintson a **Küldés** gombra.

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

Ha a feladat sikeresen teljesül, a feladat állapota megjelenik a Visual Studióban figyelésre. A feladat befejezése után akár vissza is játszhatja a feladat-végrehajtási folyamatot, és megtudhatja a szűk keresztmetszeti lépéseket a feladat hatékonyságának javítása érdekében. Az Azure Portalon is ellenőrizheti az U-SQL-feladatok állapotát.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Most már ellenőrizheti a kimeneti fájlokat az Azure Blob storage vagy az Azure Portalon. Használja a rétegzett mintaadatokat a következő lépésben a modellezéshez.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Modellek létrehozása és üzembe helyezése az Azure Machine Learningben
Két lehetőség áll rendelkezésre az Azure Machine Learningbe való adatbe bevonzáshoz, hogy

* Az első lehetőség, használja a mintavételezett adatokat, amelyek egy Azure Blob (a **fenti adatmintavételi** lépés) és a Python segítségével modelleket hozhat létre és helyezhet üzembe az Azure Machine Learning.
* A második lehetőség, az adatok lekérdezése az Azure Data Lake közvetlenül egy Hive-lekérdezés használatával. Ez a beállítás megköveteli, hogy hozzon létre egy új HDInsight-fürtöt, vagy használjon egy meglévő HDInsight-fürtöt, ahol a Hive-táblák az Azure Data Lake Storage-ban a New Angeles-i taxi adatokra mutatnak.  Mindkét lehetőséget a következő szakaszok tárgyaljuk.

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>1. lehetőség: A Python használatával gépi tanulási modelleket hozhat létre és helyezhet üzembe
Gépi tanulási modellek python használatával történő létrehozásához és üzembe helyezéséhez hozzon létre egy Jupyter-jegyzetfüzetet a helyi gépen vagy az Azure Machine Learning Studióban. A [GitHubon](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) található Jupyter notebook tartalmazza az adatok feltárásához, megjelenítéséhez, a szolgáltatástervezéshez, a modellezéshez és a telepítéshez. Ebben a cikkben csak a modellezés és a központi telepítés vonatkozik.

### <a name="import-python-libraries"></a>Python-tárak importálása
A minta Jupyter Notebook vagy a Python-parancsfájl futtatásához a következő Python-csomagok szükségesek. Ha az Azure Machine Learning notebook szolgáltatást használja, ezek a csomagok előre telepítve vannak.

    import pandas as pd
    from pandas import Series, DataFrame
    import numpy as np
    import matplotlib.pyplot as plt
    from time import time
    import pyodbc
    import os
    from azure.storage.blob import BlobService
    import tables
    import time
    import zipfile
    import random
    import sklearn
    from sklearn.linear_model import LogisticRegression
    from sklearn.cross_validation import train_test_split
    from sklearn import metrics
    from __future__ import division
    from sklearn import linear_model
    from azureml import services


### <a name="read-in-the-data-from-blob"></a>Olvasás a blobból származó adatok között
* Kapcsolati karakterlánc

        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
* Olvasás szövegként

        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))

  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)
* Oszlopnevek és különálló oszlopok hozzáadása

        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
* Egyes oszlopok módosítása numerikusra

        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>Gépi tanulási modellek létrehozása
Itt hozhat létre egy bináris osztályozási modellt, hogy megjósolja, hogy egy utazás tipped-e vagy sem. A Jupyter notebook ban további két modelltalálható: többosztályos besorolás és regressziós modellek.

* Először létre kell hozni a dummy változókat, amelyek felhasználhatók a scikit-learn modellekben

        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
* Adatkeret létrehozása a modellezéshez

        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])

        X = data.iloc[:,1:]
        Y = data.tipped
* Képzés és tesztelés 60-40 split

        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
* Logisztikai regresszió a képzési készletben

        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)

       ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)
* Pontszámvizsgálati adatkészlet

        Y_test_pred = logit_fit.predict(X_test)
* Értékelési mérőszámok számítása

        fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
        print fpr_train, tpr_train, thresholds_train

        fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred)
        print fpr_test, tpr_test, thresholds_test

        #AUC
        print metrics.auc(fpr_train,tpr_train)
        print metrics.auc(fpr_test,tpr_test)

        #Confusion Matrix
        print metrics.confusion_matrix(Y_train,Y_train_pred)
        print metrics.confusion_matrix(Y_test,Y_test_pred)

       ![c2](./media/data-lake-walkthrough/c2-py-logit-evaluation.PNG)

### <a name="build-web-service-api-and-consume-it-in-python"></a>Webszolgáltatás API-jának létrehozása és felhasználása pythonban
Szeretné üzembe a gépi tanulási modell, miután megépült. A bináris logisztikai modell itt példaként használható. Győződjön meg arról, hogy a scikit-learn verzió a helyi gépen 0.15.1 (Az Azure Machine Learning Studio már legalább ebben a verzióban).

* Keresse meg a munkaterület hitelesítő adatait az Azure Machine Learning Studio (klasszikus) beállításaiból. Az Azure Machine Learning Studio-ban kattintson a **Beállítások** --> **névengedélyezési** --> **jogtolatokra.**

    ![c3](./media/data-lake-walkthrough/c3-workspace-id.PNG)

        workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
        auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

* Webszolgáltatás létrehozása

        @services.publish(workspaceid, auth_token)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(int) #0, or 1
        def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
            return logit_fit.predict(inputArray)
* Webszolgáltatás hitelesítő adatainak beszereznie

        url = predictNYCTAXI.service.url
        api_key =  predictNYCTAXI.service.api_key

        print url
        print api_key

        @services.service(url, api_key)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(float)
        def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            pass
* Hívja meg a webszolgáltatás API-ját. Általában várjon 5-10 másodpercet az előző lépés után.

        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)

       ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>2. lehetőség: Modellek létrehozása és üzembe helyezése közvetlenül az Azure Machine Learningben
Az Azure Machine Learning Studio (klasszikus) közvetlenül az Azure Data Lake Storage-ból olvashatadatokat, majd modellek et hozhat létre és helyezhet üzembe. Ez a megközelítés egy Hive-táblát használ, amely az Azure Data Lake Storage-ban mutat. Egy külön Azure HDInsight-fürt kell kiépíteni a Hive-táblához. 

### <a name="create-an-hdinsight-linux-cluster"></a>HDInsight Linux-fürt létrehozása
Hozzon létre egy HDInsight-fürtöt (Linux) az [Azure Portalon.](https://portal.azure.com) További információt az **Azure Data Lake Storage-hoz való hozzáféréssel rendelkező HDInsight-fürt létrehozása** című témakörben talál: [HDInsight-fürt létrehozása a Data Lake Store használatával az Azure Portal használatával](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)című témakörben.

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Hive-tábla létrehozása a HDInsightban
Most hozzon létre Hive-táblákat az Azure Machine Learning Studio (klasszikus) a HDInsight-fürtben az Azure Data Lake Storage-ban tárolt adatok használatával az előző lépésben. Nyissa meg a létrehozott HDInsight-fürtöt. Kattintson **a Beállítások** --> **tulajdonságai** --> **fürt AAD Identity** --> **ADLS Access**elemre, győződjön meg arról, hogy az Azure Data Lake Storage-fiók olvasási, írási és végrehajtási jogosultságokkal van hozzáadva a listához.

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Ezután kattintson az **Irányítópult** gombra a **Beállítások** gomb mellett, és megjelenik egy ablak. Kattintson a lap jobb felső sarkában található **Hive-nézet** elemre, és megjelenik a **Lekérdezésszerkesztő**.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Illessze be a következő Hive-parancsfájlok egy tábla létrehozásához. Az adatforrás helye az Azure Data Lake Storage hivatkozási így: **adl://data_lake_store_name.azuredatalakestore.net:443/folder_name/file_name**.

    CREATE EXTERNAL TABLE nyc_stratified_sample
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string,
      payment_type string,
      fare_amount string,
      surcharge string,
      mta_tax string,
      tolls_amount string,
      total_amount string,
      tip_amount string,
      tipped string,
      tip_class string,
      rownum string
      )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';


Amikor a lekérdezés befejeződik, az eredményeket a következőkkel kell látnia:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Modellek létrehozása és üzembe helyezése az Azure Machine Learning Studio-ban
Most már készen áll egy olyan modell létrehozására és üzembe helyezésére, amely előre jelzi, hogy az Azure Machine Learning egy tippet fizet-e vagy sem. A rétegzett mintaadatok készen állnak a bináris osztályozási (csúcs vagy nem) probléma megoldásához. A többosztályos besorolást (tip_class) és regressziót (tip_amount) használó prediktív modellek az Azure Machine Learning Studio-val is létrehozhatóés üzembe helyezhetők, de itt csak azt mutatja be, hogyan kezelje az esetet a bináris besorolási modell használatával.

1. Az adatok at Azure Machine Learning Studio (klasszikus) az **Adatok importálása** modul, elérhető az **adatok bemeneti és kimeneti** szakaszban. További információt az [Adatok modul importálása modul](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) referencialapján talál.
2. Válassza a **Hive-lekérdezés** adatforrásként lehetőséget a **Tulajdonságok** panelen. **Data source**
3. Illessze be a következő Hive-parancsfájlt a **Hive adatbázis lekérdezésszerkesztőbe**

        select * from nyc_stratified_sample;
4. Adja meg a HDInsight-fürt URI-ját (ez az URI megtalálható az Azure Portalon), a Hadoop hitelesítő adatait, a kimeneti adatok helyét és az Azure Storage-fiók nevét/kulcs-tároló nevét.

   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)

Egy példa egy bináris osztályozási kísérlet adatokat olvas a Hive tábla az alábbi ábrán látható:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

A kísérlet létrehozása után kattintson a **Webszolgáltatás** --> **prediktív webszolgáltatásának beállítása gombra.**

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Futtassa az automatikusan létrehozott pontozási kísérletet, amikor befejeződik, kattintson **a Webszolgáltatás telepítése gombra**

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

A webszolgáltatás irányítópultja hamarosan megjelenik:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Összefoglalás
A forgatókönyv kitöltésével létrehozott egy adatelemzési környezetet méretezhető végpontok között megoldások létrehozásához az Azure Data Lake-ben. Ezt a környezetet egy nagy nyilvános adatkészlet elemzésére használták, az adatelemzési folyamat kanonikus lépésein keresztül, az adatgyűjtéstől a modellbetanításon keresztül, majd a modell webszolgáltatásként való üzembe helyezéséig. Az U-SQL az adatok feldolgozására, feltárására és mintavételezésére szolgál. A Python és a Hive az Azure Machine Learning Studio (klasszikus) használatával prediktív modelleket hozhat létre és helyezhet üzembe.

## <a name="whats-next"></a>A következő lépések
A [csapatadat-elemzési folyamat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) tanulási útvonala a speciális elemzési folyamat egyes lépéseit leíró témakörökre mutató hivatkozásokat tartalmaz. A [Csapatadat-elemzési folyamat forgatókönyvek](walkthroughs.md) lapján egy sor forgatókönyv található, amelyek bemutatják az erőforrások és szolgáltatások használatát különböző prediktív elemzési forgatókönyvekben:

* [A csapatadat-elemzési folyamat működés közben: az SQL Data Warehouse használata](sqldw-walkthrough.md)
* [A csapatadat-elemzési folyamat működés közben: HDInsight Hadoop-fürtök használata](hive-walkthrough.md)
* [A csapatadat-elemzési folyamat: az SQL Server használata](sql-walkthrough.md)
* [Az azure HDInsight Spark használatával iradt adatelemzési folyamat áttekintése](spark-overview.md)

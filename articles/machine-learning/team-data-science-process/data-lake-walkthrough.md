---
title: Skálázható adatelemzés Azure Data Lake – csoportos adatelemzési folyamattal
description: Az adatkészletek adatfeltárási és bináris besorolási feladatainak Azure Data Lake használata.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4f1f60ef50b65c13464e7a777e9b8ce66b5fa122
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492450"
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Skálázható adatelemzés a Azure Data Lake használatával: teljes körű útmutató
Ez az útmutató bemutatja, hogyan használhatók a Azure Data Lake az adatfeltárási és a bináris besorolási feladatok elvégzésére a New York-i taxi Trip és a viteldíj-adatkészlet mintáján, és megjósolható, hogy a tipp díjköteles-e. Végigvezeti a [csoportos adatelemzési folyamat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)lépésein, a teljes körű, az adatgyűjtés és a modell képzésének lépésein, majd egy olyan webszolgáltatás üzembe helyezésén, amely közzéteszi a modellt.

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
A [Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) minden olyan képességgel rendelkezik, amely megkönnyíti az adatszakértők számára a méret, az alakzat és a sebesség adatainak tárolását, valamint az adatfeldolgozást, a fejlett elemzési funkciókat, valamint a gépi tanulási modellezést, amely nagy skálázhatósággal rendelkezik költséghatékony módon.   A feladatokat csak akkor kell fizetnie, ha az adatok feldolgozása ténylegesen folyamatban van. A Azure Data Lake Analytics a U-SQL nyelvet is tartalmazza, amely az SQL C# deklaratív jellegét ötvözi a méretezhető elosztott lekérdezési képesség biztosításához. Lehetővé teszi a strukturálatlan adatok feldolgozását, ha a sémát az olvasás, az egyéni logika és a felhasználó által definiált függvények (UDF-EK) használatával végzi, és bővíthető, így részletesen szabályozható a méretezési műveletek végrehajtása. Ha többet szeretne megtudni az U-SQL mögötti tervezési filozófiáról, tekintse meg a [Visual Studio blogbejegyzését](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

A Data Lake Analytics kulcseleme a Cortana Analytics Suite csomagnak is, emellett az Azure SQL Data Warehouse, a Power BI és a Data Factory szolgáltatásokkal is együttműködik. Ez teljes körű felhőalapú big data és fejlett elemzési platformot biztosít.

Ez az útmutató az adatelemzési folyamatokkal kapcsolatos feladatok végrehajtásához szükséges előfeltételek és erőforrások telepítését ismerteti. Ezután az U-SQL használatával ismerteti az adatfeldolgozási lépéseket, és azt mutatja be, hogyan használható a Python és a kaptár a Azure Machine Learning Studio (klasszikus) segítségével a prediktív modellek létrehozásához és üzembe helyezéséhez.

### <a name="u-sql-and-visual-studio"></a>U-SQL és Visual Studio
Ez a forgatókönyv azt javasolja, hogy a Visual Studio használatával szerkessze az U-SQL-parancsfájlokat az adatkészlet feldolgozásához. Az U-SQL-szkripteket itt ismertetjük, és egy külön fájlban vannak megadva. A folyamat magában foglalja az adatfeldolgozást, a feltárást és a mintavételezést. Azt is bemutatja, hogyan futtathat egy U-SQL-parancsfájlból álló feladatot a Azure Portal. A rendszer létrehozza a kaptár-táblákat egy társított HDInsight-fürt adatai számára, hogy elősegítse a bináris besorolási modell kiépítését és telepítését Azure Machine Learning Studioban.

### <a name="python"></a>Python
Ez az útmutató egy olyan szakaszt is tartalmaz, amely bemutatja, hogyan hozhat létre és helyezhet üzembe egy prediktív modellt a Python és a Azure Machine Learning Studio használatával. Egy Jupyter notebookot biztosít a Python-szkriptekkel a folyamat lépéseihez. A notebook tartalmaz néhány további mérnöki lépést és modellt, például a többosztályos besorolást és a regressziós modellezést is, az itt ismertetett bináris besorolási modell mellett. A regresszió feladata, hogy előre megjósolja a tipp mennyiségét más tip-funkciók alapján.

### <a name="azure-machine-learning"></a>Azure Machine Learning
A Azure Machine Learning Studio (klasszikus) a prediktív modellek létrehozásához és üzembe helyezéséhez használható. Ezt két módszer használatával végezheti el: először a Python-szkriptekkel, majd egy HDInsight-(Hadoop-) fürthöz tartozó kaptár-táblákkal.

### <a name="scripts"></a>Scripts
Ebben az útmutatóban csak a fő lépések szerepelnek. Letöltheti a teljes **U-SQL-szkriptet** és **Jupyter notebook** a [githubról](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

## <a name="prerequisites"></a>Előfeltételek
A témakörök elkezdése előtt a következőkkel kell rendelkeznie:

* Azure-előfizetés. Ha még nem rendelkezik ilyennel, tekintse meg az [Azure ingyenes próbaverziójának beszerzése](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)című témakört.
* Ajánlott Visual Studio 2013 vagy újabb verzió. Ha még nem rendelkezik a telepített verziók egyikével sem, letöltheti az ingyenes közösségi verziót a [Visual Studio Community](https://www.visualstudio.com/vs/community/)-ból.

> [!NOTE]
> A Visual Studio helyett a Azure Portal is használhatja Azure Data Lake lekérdezések elküldéséhez. A Visual Studióval és a portálon az **adatfeldolgozás a U-SQL-** sel című szakaszban található útmutatóban talál útmutatást.
>
>


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Adatelemzési környezet előkészítése Azure Data Lake
Az adatelemzési környezet előkészítéséhez ehhez a bemutatóhoz hozza létre a következő erőforrásokat:

* Azure Data Lake Store (ADLS)
* Azure Data Lake Analytics (ADLA)
* Azure Blob Storage-fiók
* Azure Machine Learning Studio (klasszikus) fiók
* Azure Data Lake Tools for Visual Studio (ajánlott)

Ez a szakasz útmutatást nyújt az egyes erőforrások létrehozásához. Ha úgy dönt, hogy a kaptár táblákat a Python helyett a Azure Machine Learning használatával használja, a modell létrehozásához egy HDInsight-(Hadoop-) fürtöt is létre kell hoznia. Ez az alternatív eljárás a 2. lehetőség szakaszban olvasható.


> [!NOTE]
> A **Azure Data Lake Store** külön is létrehozható, vagy ha a **Azure Data Lake Analytics** alapértelmezett tárolóként hozza létre. Ezekre az erőforrásokra külön kell létrehoznia az egyes erőforrásokat, de a Data Lake Storage-fiókot külön kell létrehozni.
>
>

### <a name="create-an-azure-data-lake-store"></a>Azure Data Lake Store létrehozása


Hozzon létre egy ADLS a [Azure Portal](https://portal.azure.com). Részletekért lásd: [HDInsight-fürt létrehozása Data Lake Store használatával Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Ügyeljen arra, hogy a fürt HRE-identitását az itt leírt **opcionális konfiguráció** panel **DataSource (adatforrás** ) paneljén állítsa be.

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Azure Data Lake Analytics fiók létrehozása
Hozzon létre egy ADLA-fiókot a [Azure Portal](https://portal.azure.com). További részletek: [oktatóanyag: az Azure Data Lake Analytics használatának első lépései Azure Portal használatával](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Azure Blob Storage-fiók létrehozása
Hozzon létre egy Azure Blob Storage-fiókot a [Azure Portal](https://portal.azure.com). Részletekért lásd: Storage-fiók létrehozása című rész az [Azure Storage-fiókokról](../../storage/common/storage-create-storage-account.md).

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-classic-account"></a>Azure Machine Learning Studio (klasszikus) fiók beállítása
Regisztráljon/Azure Machine Learning Studio (klasszikus) a [Azure Machine learning Studio](https://azure.microsoft.com/services/machine-learning/) oldaláról. Kattintson az első **lépések** gombra, és válassza az "ingyenes munkaterület" vagy a "standard munkaterület" lehetőséget. Most már készen áll a kísérletek létrehozására Azure Machine Learning Studióban.

### <a name="install-azure-data-lake-tools-recommended"></a>Azure Data Lake eszközök telepítése [ajánlott]
Telepítse Azure Data Lake eszközöket a Visual Studio verziójához a [Visual studióhoz készült Azure Data Lake Tools](https://www.microsoft.com/download/details.aspx?id=49504)webhelyről.

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

A telepítés sikeres befejeződése után nyissa meg a Visual studiót. A felső menüben a Data Lake lapon kell megjelennie. Azure-erőforrásai a bal oldali panelen jelennek meg az Azure-fiókba való bejelentkezéskor.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>A New York-i taxis adatkészlete
Az itt használt adatkészlet nyilvánosan elérhető adatkészlet – a New York-i [taxis adatkészlete](https://www.andresmh.com/nyctaxitrips/). A New York-i taxi Trip-adat körülbelül 20 GB tömörített CSV-fájlból áll (~ 48 GB tömörítetlen), amely több mint 173 000 000 egyedi utazást és az egyes utazásokhoz fizetett viteldíjat rögzíti. Az egyes utazási rekordok tartalmazzák a felvételi és a lemorzsolódási helyszíneit és időpontját, a névtelen csapkod (illesztőprogram) licencének számát és a digitális medált (a taxi egyedi AZONOSÍTÓját). Az adat a 2013-as év összes utazására vonatkozik, és a következő két adatkészletben szerepel minden hónapban:

A "trip_data" CSV az utazás részleteit tartalmazza, például az utasok számát, a felvételi és a lemorzsolódási pontokat, az utazási időtartamot és a menetidő hosszát. Íme néhány példa a rekordokra:

       medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868



Az "trip_fare" CSV tartalmazza az egyes utazások díjait, például a fizetési típust, a díjszabást, a pótdíjat, az adókat, a tippeket és az autópályadíjat, valamint a teljes fizetett összeget. Íme néhány példa a rekordokra:

       medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Az utazás\_az adat és az utazás\_viteldíj egyedi kulcsa a következő három mezőből áll: emlékérem, Hack\_License és pickup\_DateTime. A nyers CSV-fájlok egy nyilvános Azure Storage-blobból érhetők el. Az ehhez az illesztéshez tartozó U-SQL-szkript a [JOIN Trip és a fare Tables](#join) szakaszban található.

## <a name="process-data-with-u-sql"></a>Adatfeldolgozás az U-SQL-sel
Az ebben a szakaszban bemutatott adatfeldolgozási feladatok közé tartozik az adatok beolvasása, a minőség ellenőrzése, a vizsgálat és a mintavételezés. Az utazáshoz és a viteldíj-táblákhoz való csatlakozás is megjelenik. Az utolsó szakaszban egy U-SQL-parancsfájl futtatására szolgáló feladatot láthat a Azure Portal. Az egyes alszakaszokra mutató hivatkozások:

* [Adatfeldolgozás: adatok beolvasása nyilvános blobból](#ingest)
* [Az adatminőség ellenőrzése](#quality)
* [Adatelemzés](#explore)
* [Csatlakozás az utazáshoz és a viteldíj-táblázatokhoz](#join)
* [Adatmintavételezés](#sample)
* [U-SQL-feladatok futtatása](#run)

Az U-SQL-szkripteket itt ismertetjük, és egy külön fájlban vannak megadva. Letöltheti a teljes **U-SQL-szkripteket** a [githubról](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

A U-SQL futtatásához nyissa meg a Visual studiót, kattintson a **file--> New--> Project**elemre, válassza a **U-SQL-projekt**, majd a név lehetőséget, és mentse egy mappába.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> A Visual Studio helyett az Azure Portal használatával is végrehajthat U-SQL-t. Navigáljon a portál Azure Data Lake Analytics erőforrásához, és közvetlenül a következő ábrán látható módon küldje el a lekérdezéseket:
>
>

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="ingest"></a>Adatfeldolgozás: adatok beolvasása nyilvános blobból

Az Azure blobban található adatok helyét a **wasb://container\_neve\@blob\_storage\_-fiók\_Name.blob.Core.Windows.net/blob_name** , és a **extracters. csv ()** használatával lehet kinyerni. Helyettesítse be a saját tároló nevét és a Storage-fiók nevét a következő parancsfájlokban a tároló\_neve\@blob\_Storage\_fiók\_neve a wasb-címben. Mivel a fájlnevek formátuma azonos formátumú, lehetséges, hogy az **utazás\_az adat\_\{\*\}. csv** fájlból az összes 12 útvonalon olvasható.

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

Mivel az első sorban vannak fejlécek, el kell távolítania a fejléceket, és módosítania kell az oszlopok típusait. A feldolgozott adatai a **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ vagy az Azure Blob Storage-fiók használatával menthetők Azure Data Lake Storage a **wasb://container_name\@blob_storage_account_name. blob. Core. Windows. net/blob_name**használatával.

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

Hasonlóképpen, a díjszabási adatkészletekben is olvasható. Kattintson a jobb gombbal a Azure Data Lake Store lehetőségre, és megtekintheti az adatait **Azure Portal-> adatkezelő** vagy a Visual Studióban található **fájlkezelőben** .

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="quality"></a>Az adatminőség ellenőrzése
Az utazás és a viteldíjak táblázatának beolvasása után a következő módon végezheti el az adatminőség-ellenőrzéseket. Az eredményül kapott CSV-fájlok kimenete lehet az Azure Blob Storage-ba vagy a Azure Data Lake Store.

A medálok számának és a medálok egyedi számának megkeresése:

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

Megtalálhatja azokat a medálokat, amelyeknek több mint 100 útja van:

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

A pickup_longitude szempontjából érvénytelen rekordok keresése:

    ///find those invalid records in terms of pickup_longitude
    @ex_3 =
        SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
        FROM @trip
        WHERE
        pickup_longitude <- 90 OR pickup_longitude > 90;
        OUTPUT @ex_3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
    USING Outputters.Csv();

Néhány változóhoz tartozó hiányzó értékek megkeresése:

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



### <a name="explore"></a>Adatelemzés
A következő szkriptekkel könnyebben megismerheti az adatelemzést.

A kitalált és a nem kimutatott utak eloszlásának megkeresése:

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

Megkeresheti a tipp összegének elosztását a cut-off értékekkel: 0, 5, 10 és 20 dollár.

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

Az utazás távolságának alapvető statisztikáinak keresése:

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

Az utazási távolság százalékos értékének megkeresése:

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


### <a name="join"></a>Csatlakozás az utazáshoz és a viteldíj-táblázatokhoz
A Trip és a viteldíj táblákat a medál, a hack_license és a pickup_time is csatlakoztathatja.

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


Az egyes utasok számainak száma, a rekordok számának kiszámítása, a tipp átlagos mennyisége, a tip-mennyiség eltérése, a kiszolgált utak százalékos aránya.

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


### <a name="sample"></a>Adatmintavételezés
Először is véletlenszerűen válassza ki az illesztett tábla adatainak 0,1%-át:

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

Ezután végezze el a rétegzett mintavételezést a bináris változó tip_class:

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


### <a name="run"></a>U-SQL-feladatok futtatása
Ha befejezte a U-SQL-parancsfájlok szerkesztését, elküldheti őket a kiszolgálónak a Azure Data Lake Analytics fiók használatával. Kattintson a **Data Lake**, a **feladatok elküldése**elemre, válassza ki az **Analytics-fiókját**, válassza a **párhuzamosság**lehetőséget, majd kattintson a **Küldés** gombra.

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

Ha a feladatot a rendszer sikeresen betartja, a rendszer a Visual Studióban jeleníti meg a feladatok állapotát a figyeléshez. A feladatok futtatásának befejeződése után visszajátszhatja a feladatok végrehajtási folyamatát, és megtekintheti a szűk keresztmetszetet a feladatok hatékonyságának növelése érdekében. Az U-SQL-feladatok állapotának ellenõrzéséhez Azure Portal is megtekintheti.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Most már megtekintheti a kimeneti fájlokat az Azure Blob Storage-ban vagy a Azure Portalban. A következő lépésben használja a rétegzett mintaadatok a modellezéshez.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Modellek létrehozása és üzembe helyezése Azure Machine Learning
Két lehetőség áll rendelkezésre az adatAzure Machine Learningba való lekéréséhez és a létrehozásához.

* Az első lehetőség az Azure-Blobba írt mintavételezési adatok (a fenti **adatmintavételezési** lépés) és a Python használata a modellek Azure Machine Learningból való létrehozásához és üzembe helyezéséhez.
* A második lehetőségnél Azure Data Lake közvetlenül a kaptár-lekérdezés használatával kérdezi le az adataikat. Ehhez a lehetőséghez létre kell hoznia egy új HDInsight-fürtöt, vagy egy meglévő HDInsight-fürtöt kell használnia, ahol a kaptár táblák a New York-i taxi Azure Data Lake Storage-adatpontra mutatnak  Mindkét lehetőséget a következő szakaszokban tárgyaljuk.

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>1\. lehetőség: gépi tanulási modellek létrehozása és üzembe helyezése a Python használatával
Gépi tanulási modellek a Python használatával történő létrehozásához és üzembe helyezéséhez hozzon létre egy Jupyter Notebook a helyi gépen vagy Azure Machine Learning Studio. A [githubon](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) megadott Jupyter notebook a teljes kódot tartalmazza a vizsgálathoz, az adat megjelenítéséhez, a szolgáltatások fejlesztéséhez, modellezéséhez és üzembe helyezéséhez. Ebben a cikkben csak a modellezést és a telepítést tárgyaljuk.

### <a name="import-python-libraries"></a>Python-kódtárak importálása
A minta Jupyter Notebook vagy a Python-parancsfájl futtatásához a következő Python-csomagok szükségesek. Ha a Azure Machine Learning notebook szolgáltatást használja, ezeket a csomagokat előre telepítették.

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


### <a name="read-in-the-data-from-blob"></a>Olvasás a blobból származó adatokból
* kapcsolatok karakterlánca

        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
* Beolvasás szövegként

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
* Oszlopok módosítása numerikusra

        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>Gépi tanulási modellek készítése
Itt létrehozhat egy bináris besorolási modellt, amely azt jelzi, hogy egy adott utazás nem megfelelő-e. A Jupyter Notebook további két modellt is megtalálhat: többosztályos besorolást és regressziós modelleket.

* Először létre kell hoznia a scikit-modellekben használható dummy-változókat.

        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
* Adatkeret létrehozása a modellezéshez

        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])

        X = data.iloc[:,1:]
        Y = data.tipped
* Képzés és tesztelés 60-40 Split

        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
* Logisztikai regresszió a betanítási készletben

        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)

       ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)
* Pontszám tesztelési adatkészlete

        Y_test_pred = logit_fit.predict(X_test)
* Értékelési mérőszámok kiszámítása

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

### <a name="build-web-service-api-and-consume-it-in-python"></a>Webszolgáltatási API létrehozása és felhasználása a Pythonban
A gépi tanulási modellt a létrehozása után szeretné működővé tenni. A bináris logisztikai modellt példaként használjuk. Győződjön meg arról, hogy a scikit-Learn verzió a helyi gépen 0.15.1. Ha Azure Machine Learning studiót használ, nem kell aggódnia.

* Keresse meg a munkaterület hitelesítő adatait Azure Machine Learning Studio (klasszikus) beállítások közül. Azure Machine Learning Studio kattintson a **beállítások** --> **név** --> **engedélyezési tokenek**elemre.

    ![C3 csomag](./media/data-lake-walkthrough/c3-workspace-id.PNG)

        workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
        auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

* Webszolgáltatás létrehozása

        @services.publish(workspaceid, auth_token)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(int) #0, or 1
        def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
            return logit_fit.predict(inputArray)
* Webszolgáltatás hitelesítő adatainak beolvasása

        url = predictNYCTAXI.service.url
        api_key =  predictNYCTAXI.service.api_key

        print url
        print api_key

        @services.service(url, api_key)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(float)
        def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            pass
* Hívja meg a Web Service API-t. Az előző lépés után 5-10 másodpercig kell várnia.

        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)

       ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>2\. lehetőség: modellek létrehozása és üzembe helyezése közvetlenül a Azure Machine Learningban
Azure Machine Learning Studio (klasszikus) az adatok közvetlenül a Azure Data Lake Storeból olvashatók be, majd a modellek létrehozásához és üzembe helyezéséhez használhatók. Ez a megközelítés egy struktúra-táblázatot használ, amely a Azure Data Lake Store mutat. Ehhez külön Azure HDInsight-fürtöt kell kiépíteni, amelyen a kaptár-tábla létrejött. A következő részekben bemutatjuk, hogyan teheti ezt meg.

### <a name="create-an-hdinsight-linux-cluster"></a>HDInsight Linux-fürt létrehozása
Hozzon létre egy HDInsight-fürtöt (Linux) a [Azure Portal](https://portal.azure.com). Részletekért lásd a **HDInsight-fürt létrehozása a Azure Data Lake Storehoz** című szakaszt a következő témakörben: a [HDInsight-fürt létrehozása a Data Lake Store használatával Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Kaptár-tábla létrehozása a HDInsight-ben
Most létre kell hoznia a HDInsight-fürtben a Azure Machine Learning Studioban (Klasszikusban) használni kívánt kaptár-táblákat az előző lépésben Azure Data Lake Store tárolt adataival. Nyissa meg a létrehozott HDInsight-fürtöt. Kattintson a **beállítások** --> **Tulajdonságok** --> a **fürt HRE-identitása** --> **ADLS hozzáférés**elemre, győződjön meg arról, hogy a Azure Data Lake Store-fiók hozzá van adva a listához olvasási, írási és végrehajtási jogosultságokkal.

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Ezután kattintson a **Beállítások** gomb melletti **irányítópultra** , és egy ablak jelenik meg. A lap jobb felső sarkában kattintson a **struktúra nézet** elemre, és a **lekérdezés-szerkesztőnek**kell megjelennie.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Illessze be a következő kaptár-szkripteket egy tábla létrehozásához. Az adatforrás helye Azure Data Lake Store hivatkozásban van: **ADL://data_lake_store_name.azuredatalakestore.net: 443/folder_name/fájlnév**.

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


Ha a lekérdezés futása befejeződött, az alábbihoz hasonló eredményeket kell látnia:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Modellek létrehozása és üzembe helyezése Azure Machine Learning Studio
Most már készen áll arra, hogy olyan modellt hozzon létre és helyezzen üzembe, amely azt jelzi, hogy a tippet Azure Machine Learning-e fizetni. A rétegzett mintaadatok készen állnak az ebben a bináris besorolásban (tip vagy not) fennálló probléma használatára. A többosztályos besorolást (tip_class) és a regressziót (tip_amount) használó prediktív modellek a Azure Machine Learning Studio használatával is felhasználhatók és üzembe helyezhetők, de itt csak azt mutatják be, hogyan kezelheti az esetet a bináris besorolási modell használatával.

1. Az adatok beolvasása a Azure Machine Learning Studioba (klasszikus) az adatok **importálása** modul használatával, amely az **adatbevitel és a kimenet** szakaszban érhető el. További információ: az [adatok importálása modul](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) referenciája lap.
2. Válassza ki a **kaptár-lekérdezést** a **Tulajdonságok** panelen lévő **adatforrásként** .
3. Illessze be a következő kaptár-szkriptet a **kaptár-adatbázis lekérdezési** szerkesztőjébe

        select * from nyc_stratified_sample;
4. Adja meg a HDInsight-fürt URI-JÁT (ez a Azure Portal található), a Hadoop hitelesítő adatai, a kimeneti adatok helye és az Azure Storage-fiók neve/kulcs/tároló neve.

   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)

Az alábbi ábrán látható egy példa arra, hogy egy bináris besorolási kísérlet az adatok a kaptár táblából való olvasását mutatja be:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

A kísérlet létrehozása után kattintson a webszolgáltatás --> **prediktív webszolgáltatás** **beállítása** elemre.

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Futtassa az automatikusan létrehozott pontozási kísérletet, ha befejeződik, kattintson a **webszolgáltatás üzembe helyezése** lehetőségre.

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

A webszolgáltatás irányítópultja hamarosan megjelenik:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Összefoglalás
Az útmutató elvégzésével létrehozta az adatelemzési környezetet a Azure Data Lake méretezhető, teljes körű megoldások létrehozásához. Ezzel a környezettel elemezhető egy nagyméretű nyilvános adatkészlet, amely az adatelemzési folyamat kanonikus lépésein, a modell betanításán keresztül az adatok beszerzésén, majd a modell webszolgáltatásként való üzembe helyezésén alapul. Az U-SQL felhasználta az adatfeldolgozást, az adatelemzést és a mintavételt. A Python és a kaptár a Azure Machine Learning Studio (klasszikus) használatával készült a prediktív modellek létrehozásához és üzembe helyezéséhez.

## <a name="whats-next"></a>Vajon mi a következő lépés?
A [csoportos adatelemzési folyamat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) képzési terve olyan témakörökre mutató hivatkozásokat tartalmaz, amelyek a speciális elemzési folyamat egyes lépéseit ismertetik. A [csoportos adatelemzési folyamat áttekintése](walkthroughs.md) oldalon számos bemutatót talál, amelyek bemutatják, hogyan használhatja az erőforrásokat és szolgáltatásokat különböző prediktív elemzési helyzetekben:

* [A csoportos adatelemzési folyamat működés közben: a SQL Data Warehouse használata](sqldw-walkthrough.md)
* [A csoportos adatelemzési folyamat működés közben: a HDInsight Hadoop-fürtök használata](hive-walkthrough.md)
* [A csoportos adatelemzési folyamat: a SQL Server használata](sql-walkthrough.md)
* [Az adatelemzési folyamat áttekintése az Azure HDInsight Spark használatával](spark-overview.md)

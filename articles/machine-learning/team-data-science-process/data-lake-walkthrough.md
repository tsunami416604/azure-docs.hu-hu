---
title: 'Az Azure Data Lake méretezhető Adattudomány: egy végpontok közötti forgatókönyv |} Microsoft Docs'
description: Hogyan használható az Azure Data Lake adatok feltárása és bináris osztályozás feladatok végrehajtására a dataset.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 91a8207f-1e57-4570-b7fc-7c5fa858ffeb
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev
ms.openlocfilehash: 6d0f889e1cc76eced172d66755a0a9275e6b7bdf
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Az Azure Data Lake méretezhető Adattudomány: egy végpont forgatókönyv
Ez a bemutató ismerteti az Azure Data Lake használata az adatok feltárása és a bináris osztályozási feladatok NYC taxi út mintán és díjszabás adatkészlet-e a tipp egy jegy ára fizeti előre. Az végigvezeti a lépéseken, a [Team adatok tudományos folyamat](http://aka.ms/datascienceprocess), végpontok, tanítási modell az adatgyűjtést, majd egy webszolgáltatás, amely közzéteszi a modell a központi telepítéshez.

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
A [Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) összes funkciókkal rendelkeznek, könnyebben adatszakértőkön szükséges adatok mérete, alakzat és sebesség tárolására, és elvégezheti az adatok feldolgozása, speciális elemzés és költséghatékony magas méretezhetőség modellezési gépi tanulás.   Kell fizetnie a feladatonként, csak akkor, ha ténylegesen feldolgozott adatokat. Az Azure Data Lake Analytics U-SQL, az egy nyelv, amely méretezhető biztosításához a C# kifejezőerejével SQL deklaratív természetét keveri elosztott lekérdezés funkció. Ez lehetővé teszi a strukturálatlan adatok feldolgozása olvasáskor séma alkalmazásával, egyéni logika és a felhasználói függvény (UDF), és bővítési részletes szabályozhatják, hogyan hajthat végre léptékű engedélyezéséhez. U-SQL mögött tervezési alapvetően kapcsolatos további információkért lásd: [Visual Studio által írt blogbejegyzés](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

A Data Lake Analytics kulcseleme a Cortana Analytics Suite csomagnak is, emellett az Azure SQL Data Warehouse, a Power BI és a Data Factory szolgáltatásokkal is együttműködik. Ez lehetővé teszi egy teljes felhőalapú big Data típusú adatok és a speciális elemzési platformot.

Ez a forgatókönyv kezdődik, hogyan telepíthet az Előfeltételek és tudományos folyamat feladatok elvégzéséhez szükséges erőforrások ismertetésével. Ezután a U-SQL használatával adatfeldolgozási lépéseit sorolja fel, és arra a következtetésre jut jelenít meg a Python és a Hive használata Azure Machine Learning Studio létrehozásához és telepítéséhez a prediktív modellek. 

### <a name="u-sql-and-visual-studio"></a>U-SQL és a Visual Studio
Ez a forgatókönyv az adatkészlet feldolgozása U-SQL parancsfájl szerkesztése a Visual Studio használatát javasolja. A U-SQL-parancsfájlok ismerteti, és egy különálló fájlban megadott. A folyamat választásával dolgozhat fel, felfedezése és az adatok mintavétele tartalmazza. Azt is bemutatja, hogyan U-SQL parancsfájl alapú végezzen Azure-portálról. Hive táblák hoz létre az épület és a bináris osztályozási modell Azure Machine Learning Studio telepítési társított HDInsight-fürtök az adatokat.  

### <a name="python"></a>Python
Ez a forgatókönyv bemutatja, hogyan létrehozásához és telepítéséhez használja a Python Azure Machine Learning Studio prediktív modellek szakasz is tartalmaz. A folyamat lépései kínál Jupyter notebook a Python-parancsfájlokkal. A notebook néhány további szolgáltatás mérnöki lépéseket és -modellek konstrukció például multiclass besorolás és a bináris osztályozási modell itt vázolt mellett modellezési regressziós kódját tartalmazza. A regresszió feladata más tip szolgáltatások alapján tipp mennyisége előre jelezni. 

### <a name="azure-machine-learning"></a>Azure Machine Learning
Az Azure Machine Learning Studio létrehozásához és telepítéséhez a prediktív modellek szolgál. Ebben az esetben két módszer használatával: első Python-parancsfájlok majd Hive táblák HDInsight (Hadoop) fürtökön.

### <a name="scripts"></a>Scripts
A bemutatóban szereplő eljárásokat csak az egyszerű lépéseket. Letöltheti a teljes **U-SQL parancsfájl** és **Jupyter Notebook** a [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

## <a name="prerequisites"></a>Előfeltételek
Ezek a témakörök elkezdéséhez az alábbiakkal kell rendelkeznie:

* Azure-előfizetés. Ha még nem rendelkezik egy, lásd: [beolvasása az Azure ingyenes próbaverzió](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Ajánlott] A Visual Studio 2013 vagy újabb verzió. Ha még nem rendelkezik ilyen verziójú telepítve, akkor egy ingyenes közösségi verziója letölthető a [Visual Studio Community](https://www.visualstudio.com/vs/community/).

> [!NOTE]
> Visual Studio helyett is használhatja az Azure-portálon az Azure Data Lake-lekérdezések. Erre a Visual Studio úgy is, és a portál című részben található útmutatás **feldolgozni az adatokat az U-SQL**. 
> 
> 


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Azure Data Lake data tudományos környezet előkészítése
Ez a forgatókönyv az adatok tudományos környezet előkészítése, hozzon létre a következőket:

* Azure Data Lake Store (ADLS) 
* Az Azure Data Lake Analytics (ADLA)
* Az Azure Blob storage-fiók
* Azure Machine Learning Studio-fiók
* Az Azure Data Lake Tools for Visual Studio (ajánlott)

Ez a szakasz útmutatást létrehozásával egyes ezeket az erőforrásokat. Ha úgy dönt, hogy a Hive táblák használata az Azure Machine Learning, Python, helyett egy modell összeállításához is szüksége (Hadoop) HDInsight-fürtök kiépítéséhez. Az alternatív eljárás a 2. lehetőség szakaszban leírt.


> [!NOTE]
> A **Azure Data Lake Store** is létrehozható, vagy külön-külön vagy létrehozásakor a **Azure Data Lake Analytics** az alapértelmezett tárolóként. Ezen erőforrások mindegyike külön-külön létrehozására vonatkozó utasításokat hivatkozott, de a Data Lake-tárfiókot kell nem kell külön hoz létre.
>
> 

### <a name="create-an-azure-data-lake-store"></a>Hozzon létre egy Azure Data Lake Store


Hozzon létre egy ADLS a a [Azure-portálon](http://portal.azure.com). További információkért lásd: [HDInsight-fürtök létrehozása az Azure-portál használatával a Data Lake Store](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Ügyeljen arra, hogy a fürt AAD-identitása beállítása a **DataSource** panel a **opcionális konfigurációs** panel leírt van. 

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Azure Data Lake Analytics-fiók létrehozása
Az ADLA-fiók létrehozása a [Azure-portálon](http://portal.azure.com). További információkért lásd: [oktatóanyag: Ismerkedés az Azure Data Lake Analytics az Azure portál használatával](../../data-lake-analytics/data-lake-analytics-get-started-portal.md). 

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Egy Azure Blob storage-fiók létrehozása
Hozzon létre egy Azure Blob storage-fiókot az a [Azure-portálon](http://portal.azure.com). További információkért lásd: a hozzon létre egy tárolási fiók szakasz [tudnivalók az Azure storage-fiókok](../../storage/common/storage-create-storage-account.md).

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-account"></a>Az Azure Machine Learning Studio fiók beállítása
Jelentkezzen be- / az Azure Machine Learning Studio a a [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) lap. Kattintson a **az induláshoz** gombra, majd válassza a "Szabad munkaterület" vagy "Szabványos munkaterület". Most már készen áll a a következők az Azure ML Studio létrehozása a mintakísérletek használatával.  

### <a name="install-azure-data-lake-tools-recommended"></a>Telepítheti az Azure Data Lake Tools [ajánlott]
Azure Data Lake Tools telepítése a Visual Studio verziójának [Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

A telepítés sikeres befejezése után nyissa meg a Visual Studio. A Data Lake lapon a menü felső részén kell megjelennie. Az Azure-erőforrások meg kell jelennie a bal oldali panelen történő bejelentkezéskor be Azure-fiókjába.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>A NYC Taxi Utazgatással adatkészlet
Az itt használt adatkészlet nyilvánosan elérhető dataset--a [NYC Taxi Utazgatással dataset](http://www.andresmh.com/nyctaxitrips/). A NYC Taxi út tartalmaz körülbelül 20 GB tömörített CSV-fájlok (tömörítetlen ~ 48 GB), minden út kifizette több mint 173 millió egyedi való adatváltások számát és a vitel rögzítése. Minden út rekord tartalmazza a felvétel és Gyűjtőtár helyeket és időpontokat, anonimizált rejthetők el (illesztőprogram) licenc száma, valamint a medallion (taxi tartozó egyedi azonosító). Az adatok minden való adatváltások számát ismerteti az év 2013, és minden hónap a következő két adatkészletet találhatók:

A "trip_data" CSV út részletek, például a utasok, a felvételi és dropoff pontok, út időtartama és út hossza tartalmazza. Íme néhány példa rekordok:

       medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868



A "trip_fare" CSV a jegy ára kifizette minden út, például a fizetési mód, jegy ára összeg, emelt díjas és adókat, tippeket és autópályadíjak, és a teljes összeg fizetős részleteit tartalmazza. Íme néhány példa rekordok:

       medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Egyedi kulcs út csatlakozni\_adatok és út\_jegy ára tevődnek össze a következő három mezőt: medallion, rejthetők el\_licenc és a felvételi\_dátum és idő. A nyers CSV-fájlok elérhetők a nyilvános Azure storage-blobba. Ezt az összekapcsolást a U-SQL-parancsfájlt a rendszer a [út és a jegy ára csatlakozás](#join) szakasz.

## <a name="process-data-with-u-sql"></a>A U-SQL adatok feldolgozása
Az adatok feldolgozása ebben a szakaszban ismertetett feladatok választásával dolgozhat fel, minőségi ellenőrzése, felfedezése és az adatok mintavétele. Hogyan lehet út és a jegy ára is látható. A végső szakaszban futtatási megjelenik egy U-SQL parancsfájl feladatot az Azure-portálon. Az alábbiakban minden alszakasz mutató hivatkozásokat:

* [Adatfeldolgozást: nyilvános blob adatainak olvasása](#ingest)
* [Adatminőségi ellenőrzése](#quality)
* [Az adatok feltárása](#explore)
* [Csatlakozás út és a jegy ára](#join)
* [Adat-mintavételezésre](#sample)
* [U-SQL feladatok futtatása](#run)

A U-SQL-parancsfájlok ismerteti, és egy különálló fájlban megadott. Letöltheti a teljes **U-SQL-parancsfájlok** a [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

U-SQL, nyissa meg a Visual Studio, végrehajtásához kattintson **fájl--> Új projekt-->**, válassza a **U-SQL projekt**, a nevet, és mentse azt egy mappát.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> Akkor lehet végrehajtani a U-SQL Visual Studio helyett az Azure portál használatával. Navigáljon a portálon az Azure Data Lake Analytics az erőforráshoz, és küldje el a lekérdezések közvetlenül, az alábbi ábrán szemléltetett:
> 
> 

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="ingest"></a>Adatfeldolgozást: A nyilvános blob adatainak olvasása
Az adatok az Azure-blobot a helyét hivatkozott **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name** és használatával kiolvasható **Extractors.Csv()**. Helyettesítse a saját tároló nevének és a tárfiók nevét az alábbi parancsfájlok a container_name@blob_storage_account_name wasb címe. Mivel ugyanazt a formátumot a fájlneveket, lehetőség **út\_data_ {\*\}.csv** összes 12 út fájl olvasásához. 

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

Mivel az első sor fejlécek szerepelnek, kell távolítsa el a fejlécek és oszloptípus átalakítása megfelelő néhányat a meglévők közül. Mentés a feldolgozott adatok Azure Data Lake Storage használatával elvégezhető **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ vagy az Azure Blob storage-fiók használatával  **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**. 

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

Hasonló módon érheti el a jegy ára adathalmaz. Kattintson a jobb gombbal az Azure Data Lake Store, ha szeretné, nézze meg az adatok **Azure-portálon--> adatkezelő** vagy **Fájlkezelőben** Visual studióban. 

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="quality"></a>Adatminőségi ellenőrzése
Miután út és a jegy ára az olvasott, adatminőségi ellenőrzése a következő módon teheti meg. Az eredményül kapott CSV-fájlok Azure Blob storage-vagy Azure Data Lake Store lehet. 

Keresse meg és medallions egyedi száma medallions száma:

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

Keresse meg azokat, amelyek 100-nál több utazgatással kellett medallions:

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

Található érvénytelen rekordokat pickup_longitude tekintetében:

    ///find those invalid records in terms of pickup_longitude
    @ex_3 =
        SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
        FROM @trip
        WHERE
        pickup_longitude <- 90 OR pickup_longitude > 90;
        OUTPUT @ex_3   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
    USING Outputters.Csv(); 

Az egyes változók hiányzó értékeinek megkeresése:

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



### <a name="explore"></a>Az adatok feltárása
Hajtsa végre az egyes adatok feltárása, a következő parancsfájlok segítségével az adatok jobb megértése.

Formabontó és nem Formabontó utak terjesztési keresése:

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

Tipp összeg lezárási értékekkel terjesztési található: 0, 5, 10 és 20 dollár.

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

Keresse meg a út távolság alapvető statisztikai adatait tartalmazza:

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

A százalékos érték út távolság keresése:

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


### <a name="join"></a>Csatlakozás út és a jegy ára
Út és a jegy ára medallion, hack_license és pickup_time társíthatók.

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


Az egyes utas száma a rekordok, átlagos tipp összeg, tipp összeg varianciáját, Formabontó utazgatással százaléka számának kiszámítása.

    // contigency table
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


### <a name="sample"></a>Adat-mintavételezésre
Első lépésként véletlenszerűen válassza ki az adatok 0,1 % a táblázatból illesztett:

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

Tegye a rétegzett mintavételi bináris változó tip_class szerint:

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


### <a name="run"></a>U-SQL feladatok futtatása
Ha a U-SQL-parancsfájlok szerkesztésének befejezése után küldheti el ezeket a kiszolgálóhoz, az Azure Data Lake Analytics-fiókkal. Kattintson a **Data Lake**, **feladat elküldése**, jelölje be a **Analytics-fiók**, válassza ki **párhuzamossági**, és kattintson a **küldje el a következőt** gombra.  

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

Ha a feladat sikeresen megfelelést, a feladat állapotának megjelenik a Visual Studio figyelésre. Miután a feladat befejezése után történik, még akkor is a feladat végrehajtási folyamatának visszajátszásos és kideríti, hogy a szűk keresztmetszetek lépéseket a feladat hatékonyság növelése érdekében. Is elvégezheti az Azure-portálhoz a U-SQL-feladatok állapotának ellenőrzéséhez.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Most már ellenőrizheti a kimeneti fájlok az Azure Blob-tároló vagy Azure-portálon. A rétegzett mintaadatok használni a modellezési a következő lépésben.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Hozza létre és telepítheti az Azure Machine Learning modellek
Két lehetőség áll rendelkezésre, hogy olvasnak be adatokat az Azure Machine Learning hozhat létre és 

* Az első lehetőség, használhatja a mintaadatokat, amely egy Azure Blob írt (a a **adat-mintavételezésre** fenti lépés), és Python létrehozásához és telepítéséhez az Azure Machine Learning modellek. 
* A második lehetőség az adatait az Azure Data Lake közvetlenül a Hive-lekérdezések használatával. Ez a beállítás megköveteli, hogy hozzon létre egy új HDInsight-fürtöt, vagy használja egy meglévő HDInsight-fürt, ahol a Hive táblák mutasson-e az NY Taxi adatokat az Azure Data Lake Storage.  Mindkét ezeket a beállításokat az alábbi szakaszok ismertetik. 

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>1. lehetőség: A gép tanulási modelljeit használja Python létrehozásához és telepítéséhez
Létrehozásához, és központi telepítéséhez a machine learning modellek pythonos környezetekben, a Jupyter Notebook létrehozása a helyi számítógépen vagy az Azure Machine Learning Studióban. A Jupyter Notebook megadott [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) vizsgálatát, adatok, a szolgáltatás mérnöki csapathoz, a modellezési és a központi telepítés megjelenítése a teljes kódját tartalmazza. Ez a cikk csak a modellezési és a központi telepítés tartoznak. 

### <a name="import-python-libraries"></a>Python könyvtárak importálása
A minta futtatásához Jupyter Notebook vagy a Python parancsfájlt, a következő csomagok szükségesek Python. Az AzureML Notebook szolgáltatást használja, ha ezeket a csomagokat előre telepített törölték.

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


### <a name="read-in-the-data-from-blob"></a>A blob adatainak olvasása
* Kapcsolati karakterlánc   
  
        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
* Szövegként olvasása
  
        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))
  
  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)    
* Oszlop neveket adhat hozzá és különálló oszlopok
  
        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
* Egyes oszlopok sorrendjének megváltoztatásához az numerikus
  
        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>Machine learning modellek létrehozása
Itt egy bináris osztályozási modell előre jelezni, hogy egy út Formabontó-e, vagy nem hoz létre. A Jupyter Notebook a található egyéb két modell: multiclass besorolási és regressziós modell.

* Először hozzon létre üres változók használható scikit kell – ismerje meg, modellek
  
        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
* A modellezési adatok kerete létrehozása
  
        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])
  
        X = data.iloc[:,1:]
        Y = data.tipped
* Modell betanítására és tesztelésére 60-40 felosztása
  
        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
* A gyakorlókészlethez logisztikai regresszió
  
        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)
  
       ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)
* Tesztelési adatkészlet pontozása
  
        Y_test_pred = logit_fit.predict(X_test)
* Értékelési-metrikák kiszámítása
  
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

### <a name="build-web-service-api-and-consume-it-in-python"></a>Webszolgáltatási API létrehozása és a Python szokásokra is
Azok a gépi tanulási modell, miután be lett építve szeretné. A bináris logisztikai a modell példa. Győződjön meg arról, hogy a scikit-további 0.15.1 verziója a helyi gépen. Nem kell foglalkoznia ez vagy Azure ML studio szolgáltatás használatakor.

* A munkaterület hitelesítő adatait az Azure ML studio beállítások megkeresése Az Azure Machine Learning Studióban, kattintson a **beállítások** --> **neve** --> **engedélyezési jogkivonatok**. 
  
    ![c3](./media/data-lake-walkthrough/c3-workspace-id.PNG)

        workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
        auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

* Hozzon létre a webszolgáltatás
  
        @services.publish(workspaceid, auth_token) 
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(int) #0, or 1
        def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
            return logit_fit.predict(inputArray)
* Webes szolgáltatás hitelesítő adatainak lekérése
  
        url = predictNYCTAXI.service.url
        api_key =  predictNYCTAXI.service.api_key
  
        print url
        print api_key
  
        @services.service(url, api_key)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(float)
        def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            pass
* Webszolgáltatási API hívása. Várnia kell, 5-10 másodperc az előző lépés után.
  
        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)
  
       ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>2. lehetőség: Hozzon létre, és közvetlenül az Azure Machine Learning modellek telepítése
Az Azure Machine Learning Studio lehet adatokat olvasni közvetlenül az Azure Data Lake Store, és akkor hozhat létre és telepíthet a modellek használható. Ezt a módszert használja, amely az Azure Data Lake Store mutat Hive táblát használ. Ehhez szükséges, hogy egy külön Azure HDInsight-fürt üzembe helyezve, az a Hive tábla létrehozása. A következő szakaszok bemutatják, hogyan ehhez. 

### <a name="create-an-hdinsight-linux-cluster"></a>HDInsight Linux-fürtök létrehozása
(Linux) HDInsight-fürtök létrehozása a [Azure-portálon](http://portal.azure.com). További információkért lásd: a **HDInsight-fürtök létrehozása az Azure Data Lake Store-hozzáféréssel rendelkező** szakasz [HDInsight-fürtök létrehozása az Azure-portál használatával a Data Lake Store](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Hdinsight Hive tábla létrehozásához
Most hozzon létre Hive táblák a HDInsight-fürtjéhez az előző lépésben az Azure Data Lake Store-ban tárolt adatok használhatók az Azure Machine Learning Studióban. Nyissa meg a létrehozott HDInsight-fürthöz. Kattintson a **beállítások** --> **tulajdonságok** --> **fürt AAD-identitása** --> **ADLS-hozzáférés**, győződjön meg arról, hogy az Azure Data Lake Store-fiók kerül a listában az olvasási, írási és végrehajtási jogokat. 

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Kattintson a **irányítópult** mellett a **beállítások** gombra, és egy ablakban jelenik meg. Kattintson a **Hive View** , és a lap jobb felső sarokban kell megjelennie a **Lekérdezésszerkesztő**.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Illessze be a következő Hive parancsfájlok tábla létrehozásához. Adatforrás helye a ily módon az Azure Data Lake Store-dokumentáció: **adl://data_lake_store_name.azuredatalakestore.net:443/mappanév/Fájlnév**.

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


A lekérdezés végeztével kell megjelennie az eredmények ehhez hasonló:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Hozza létre és telepítheti az Azure Machine Learning Studióban modellek
Most már készen áll létrehozásához és telepítéséhez modell, amely képes-e a tipp van fizetős Azure Machine Learning segítségével. A bináris osztályozási használható készen áll a rétegzett mintaadatok (tipp vagy sem) a probléma. A prediktív modellek multiclass besorolás (tip_class) és regressziós (tip_amount) használatával is beépített és az Azure Machine Learning Studio telepített, de itt csak kimutatható hogyan kezelje a helyzet, a bináris osztályozási modell használatával.

1. Az Azure gépi tanulás használatával az adatok beolvasása a **és adatokat importálhat** modul érhető el a **bemeneti és kimeneti** szakasz. További információkért lásd: a [és adatokat importálhat modul](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) referencialapja.
2. Válassza ki **Hive-lekérdezések** , a **adatforrás** a a **tulajdonságok** panel.
3. Illessze be a következő Hive parancsfájl a **adatbázis-lekérdezés Hive** szerkesztő
   
        select * from nyc_stratified_sample;
4. Adja meg az URI a HDInsight-fürt (Ez található Azure-portálon), a Hadoop hitelesítő adatokat, a helyét, valamint kimeneti adatokat az Azure storage fiók nevét vagy kulcstároló neve.
   
   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)  

A bináris osztályozási kísérlet olvasási Hive tábla adatait az alábbi ábrán látható példát:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

A kísérlet létrehozása után kattintson **webes szolgáltatások beállítása** --> **prediktív webszolgáltatás**

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Az automatikusan létrehozott futtatásához kattintson a Befejezés után, pontozási kísérletet, **webes szolgáltatás telepítése**

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

A webes szolgáltatás irányítópultját hamarosan jeleníti meg:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Összegzés
Ez a forgatókönyv végrehajtásával létrehozott egy adatok tudományos környezet méretezhető végpontok közötti megoldások Azure Data Lake készítéséhez. Ebben a környezetben használt elemzése a nagy nyilvános adatkészlet véve azt a tudományos folyamat, a modell képzés útján, majd a központi telepítéshez a modell webszolgáltatásként adatgyűjtést kanonikus lépésein. U-SQL használatával feldolgozni, vizsgálatát, és az az adatokat. Python és a Hive használt Azure Machine Learning Studio build és üzembe prediktív modelleket.

## <a name="whats-next"></a>A következő lépések
A képzési terv a [Team adatok tudományos folyamat (TDSP)](http://aka.ms/datascienceprocess) a speciális elemzés folyamat egyes lépéseit ismertető témakörökre mutató hivatkozásokat tartalmaz. Nincsenek felsorolva a forgatókönyvek sorozata a [Team adatok tudományos folyamat forgatókönyvek](walkthroughs.md) oldal, amely megjelenítve a különböző prediktív elemzési forgatókönyvekben erőforrások és szolgáltatások használata:

* [Az Team tudományos folyamat működés közben: az SQL Data Warehouse](sqldw-walkthrough.md)
* [A művelet az Team tudományos folyamat: HDInsight Hadoop-fürtök használata](hive-walkthrough.md)
* [Az Team tudományos folyamat: SQL Server használata](sql-walkthrough.md)
* [A Spark on Azure HDInsight használatának adatok tudományos folyamat áttekintése](spark-overview.md)

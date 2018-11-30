---
title: 'Méretezhető adatelemzés az Azure Data Lake: egy végpontok közötti forgatókönyv |} A Microsoft Docs'
description: Hogyan használható az Azure Data Lake adatok feltárása és a bináris osztályozási feladatokat egy adatkészleten.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 52518f8c9295f00b93dee0ea356513605450aed7
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52443559"
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Méretezhető adatelemzés az Azure Data Lake: egy végpontok közötti forgatókönyv
Ez az útmutató bemutatja, hogyan használható az Azure Data Lake adatáttekintés és a bináris osztályozási feladatok NYC taxi út mintán és adatkészlet előrejelzési e tipp egy diszkont fizeti díjszabás. Emellett végigvezeti a lépéseken, a [csoportos adatelemzési folyamat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), a végpontok közötti, az adatgyűjtés a tanítási modell, majd a központi telepítését egy webszolgáltatás, amelyet a modell tesz közzé.

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
A [a Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) minden képessége az adatszakértők elérhetővé bármilyen méretű, alakzat és sebességű adatok tárolására, és elvégezheti az adatok feldolgozási, fejlett elemzési és gépi tanulási modellezés nagy az a költséghatékony méretezhetősége.   A díjfizetés Feladatonkénti alapon, csak a ténylegesen feldolgozott adatok esetén. Az Azure Data Lake Analytics U-SQL tartalmazza, egy nyelv, amely az SQL deklaratív jellegét a biztosít méretezhető C# kifejezőerejével az elosztott lekérdezési képességek. Ez lehetővé teszi az olvasási séma alkalmazásával strukturált adatok feldolgozására, egyéni logikát és a felhasználó által definiált függvények (UDF) beszúrása, és ahhoz, hogy szabályozásához hogyan hajtható végre, ipari méretekben bővíthetőséget. A U-SQL mögött tervezési filozófia kapcsolatos további információkért lásd: [Visual Studio blogbejegyzés](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

A Data Lake Analytics kulcseleme a Cortana Analytics Suite csomagnak is, emellett az Azure SQL Data Warehouse, a Power BI és a Data Factory szolgáltatásokkal is együttműködik. Ez lehetővé teszi egy átfogó felhőalapú big data és a speciális analitikai platformot.

Ez a bemutató első lépése arról, hogyan telepítse az előfeltételeket és az adatelemzési folyamat feladatok végrehajtásához szükséges erőforrásokat. Azt ismerteti, adatfeldolgozási U-SQL használatával, és azt állapítja meg, hogyan használható a Python és a Hive megjelenítésével, majd az Azure Machine Learning Studio használatával hozhat létre, és a prediktív modellek üzembe helyezése. 

### <a name="u-sql-and-visual-studio"></a>U-SQL és a Visual Studióban
Ez a forgatókönyv azt javasolja, hogy az adatkészlet feldolgozása U-SQL-parancsfájlok szerkesztése a Visual Studio használatával. A U-SQL-szkriptek az itt leírtak szerint, és a egy különálló fájlban megadott. A folyamat fürtjét, felfedezése és az adatok mintavételezésének tartalmaz. Azt is bemutatja, hogyan futtathat U-SQL-parancsprogram-feladatokat az Azure Portalról. Hive táblák létrehozása és központi telepítését az Azure Machine Learning Studio egy bináris osztályozási modell megkönnyítése kapcsolódó HDInsight-fürtben az adatok jön létre.  

### <a name="python"></a>Python
Ez a forgatókönyv egy szakasz bemutatja, hogyan készíthet és helyezhet üzembe egy prediktív modellt, a Python használata Azure Machine Learning Studio is tartalmaz. A folyamat lépései biztosítja egy Jupyter notebookot a Python-szkriptet. A notebook néhány további funkciót mérnöki lépéseket és a modellek konstrukció például többosztályos osztályozási és modellezés mellett a bináris osztályozási modell leírása itt található regressziós kódját tartalmazza. A regresszió feladata előre jelezni a funkciókat a tip alapján tipp mennyisége. 

### <a name="azure-machine-learning"></a>Azure Machine Learning
Az Azure Machine Learning Studio segítségével hozhat létre, és a prediktív modellek üzembe helyezése. Ebben az esetben két módszer használata: első Python-szkriptekkel és a egy HDInsight (Hadoop) fürtön található Hive-táblákat.

### <a name="scripts"></a>Scripts
Ebben az útmutatóban leírt csak az egyszerű lépéseket. Letöltheti a teljes **U-SQL parancsfájl** és **Jupyter Notebook** a [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

## <a name="prerequisites"></a>Előfeltételek
Ezek a témakörök elkezdéséhez az alábbiakkal kell rendelkeznie:

* Azure-előfizetés. Ha Ön még nem rendelkezik egy, [lekérése az Azure ingyenes próbaverzió](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Ajánlott] A Visual Studio 2013-as vagy újabb. Ha Ön még nem rendelkezik ilyen verziójú telepítve, akkor egy ingyenes közösségi verziója letölthető a [Visual Studio Community](https://www.visualstudio.com/vs/community/).

> [!NOTE]
> A Visual Studio helyett használhatja az Azure Portalon az Azure Data Lake-lekérdezések elküldéséhez. Ezért mindkét a Visual Studióval való és a portál című részben található utasításokat **U-SQL-adatok feldolgozása**. 
> 
> 


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Olyan adatelemezési környezetet az Azure Data Lake előkészítése
Ebben a bemutatóban az adatelemzési környezetet előkészítése, hozzon létre az alábbi forrásanyagokat:

* Az Azure Data Lake Store (ADLS) 
* Az Azure Data Lake Analytics (ADLA)
* Az Azure Blob storage-fiók
* Azure Machine Learning Studio-fiók
* Az Azure Data Lake Tools for Visual Studio (ajánlott)

Ez a szakasz útmutatást ezen erőforrások mindegyike létrehozásával. Ha úgy dönt, hogy a Hive-táblák használata az Azure Machine Learning, Python, helyett támaszkodva készítenek olyan modellt is szeretné HDInsight (Hadoop)-fürt üzembe helyezése. Ez az alternatív eljárást a 2. lehetőség szakasz ismerteti.


> [!NOTE]
> A **Azure Data Lake Store** lehet létrehozni külön-külön, vagy ha hoz létre a **Azure Data Lake Analytics** az alapértelmezett tárolóként. Ezen erőforrások mindegyike külön létrehozására vonatkozó utasításokat hivatkozott, de a Data Lake storage-fiók nem kell külön kell létrehozni.
>
> 

### <a name="create-an-azure-data-lake-store"></a>Hozzon létre egy Azure Data Lake Store


Hozzon létre egy ADLS a [az Azure portal](http://portal.azure.com). További információkért lásd: [egy HDInsight-fürt létrehozása a Data Lake Store az Azure portal használatával](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Ügyeljen arra, hogy állítsa be a fürt az AAD-identitását a **DataSource** paneljén a **opcionális konfigurációs** panelen leírt hiba. 

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Az Azure Data Lake Analytics-fiók létrehozása
Az ADLA fiók létrehozása a [az Azure portal](http://portal.azure.com). További információkért lásd: [oktatóprogram: Ismerkedés az Azure Data Lake Analytics az Azure portal használatával](../../data-lake-analytics/data-lake-analytics-get-started-portal.md). 

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Azure Blob storage-fiók létrehozása
Hozzon létre egy Azure Blob storage-fiókjában az [az Azure portal](http://portal.azure.com). További információkért lásd: a hozzon létre egy tárolási fiók szakaszhoz [tudnivalók az Azure storage-fiókok](../../storage/common/storage-create-storage-account.md).

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-account"></a>Az Azure Machine Learning Studio-fiók létrehozása
Regisztrál / az Azure Machine Learning studióba, a [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) lapot. Kattintson a **első lépések** gombra, majd válassza a "Ingyenes munkaterület" vagy "Standard munkaterület". A rendszer most már készen áll, kísérletek létrehozására az Azure Machine Learning Studióban.  

### <a name="install-azure-data-lake-tools-recommended"></a>[Ajánlott] az Azure Data Lake Tools telepítése
Az Azure Data Lake Tools telepítése a Visual Studio-verzióra vonatkozó [Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

A telepítés sikeres befejezése után nyissa meg a Visual Studio. A Data Lake a felső menüben lapon kell megjelennie. Az Azure-erőforrások kell jelennek meg a bal oldali panelen, amikor bejelentkezik az Azure-fiókjával.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>A NYC Taxi lelassítja adatkészlet
Az itt használt adatkészlet a nyilvánosan elérhető adatkészlet--a [NYC Taxi lelassítja adatkészlet](http://www.andresmh.com/nyctaxitrips/). A NYC Taxi útadatok körülbelül 20 GB tömörített CSV-fájlok (~ 48 GB tömörítetlen) áll, minden egyes út 173 milliónál egyes utak és a díjakat fizetni. Minden egyes út rekord a begyűjtés és dropoff helyek és időpontok, licencszám anonimizált feltörés (illesztőprogramok) és medallion (taxi az egyedi azonosító) száma tartalmazza. Az adatok minden lelassítja ismerteti az év 2013-hoz, és minden hónapban megtalálható a következő két adatkészletet:

"Trip_data" CSV trip részleteit, például az utasok, begyűjtést és dropoff pontok, út időtartama és út hossza számát tartalmazza. Az alábbiakban néhány példa rekordokat:

       medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868



A "trip_fare" CSV a diszkont fizetett minden út, például a fizetési típus, diszkont összeg, pótdíj és adók, tippek és útdíjak, és a teljes összeg fizetős részleteit tartalmazza. Az alábbiakban néhány példa rekordokat:

       medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Utazás csatlakozni egyedi kulcsa\_adatokat és utazás\_diszkont épül fel a következő három mezőt: medallion céltudatos\_licenc, és felvétel\_datetime. A nyers CSV-fájlok elérhetők a nyilvános Azure tárolóblobból. A U-SQL parancsfájl az illesztési operátor szerepel a [utazást és diszkont táblák](#join) szakaszban.

## <a name="process-data-with-u-sql"></a>U-SQL-adatok feldolgozása
Az ebben a szakaszban bemutatott adatfeldolgozási feladatok feldolgozására, minőségi ellenőrzése, felfedezése és az adatok mintavételezésének tartalmazza. Hogyan utazást és diszkont táblák is megjelennek. Az utolsó szakaszban futtatási bemutatja a U-SQL parancsfájl feladat az Azure Portalról. Az alábbiakban az egyes alszakasz mutató hivatkozásokat:

* [Adatbetöltés: a nyilvános blob adatainak olvasása](#ingest)
* [Adatok minőségi ellenőrzése](#quality)
* [Az adatok feltárása](#explore)
* [Utazás és diszkont táblák](#join)
* [Adat-mintavételezés](#sample)
* [U-SQL-feladatok futtatása](#run)

A U-SQL-szkriptek az itt leírtak szerint, és a egy különálló fájlban megadott. Letöltheti a teljes **U-SQL-parancsfájlok** a [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Hajtsa végre a U-SQL használatával, nyissa meg a Visual Studióban kattintson a **fájl--> Új--> projekt**, válassza a **U-SQL projekt**, a nevet, és mentse azt egy mappába.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> Az Azure Portal használatával hajtható végre a U-SQL Visual Studio helyett lehetőség. Keresse meg az Azure Data Lake Analytics-erőforrás a portálon, és közvetlenül, az alábbi ábrán szemléltetett-lekérdezések elküldéséhez:
> 
> 

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="ingest"></a>Adatbetöltés: A nyilvános blob adatainak olvasása
Az Azure blobban lévő adatok helyét hivatkozott **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name** használatával képes kinyerni és **Extractors.Csv()**. Helyettesítse be a saját tároló neve és a tárfiók nevét az alábbi parancsfájlok a container_name@blob_storage_account_name wasb-címét. Mivel ugyanazt a formátumot a fájlneveket, lehetőség használni **út\_data_ {\*\}.csv** , olvassa el az összes 12 útadatokat tartalmazó fájlt. 

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

Mivel az első sorban a fejlécek, távolítsa el a fejlécek, és megfelelő azokat az oszlop típusának módosítása szüksége. Mentés a feldolgozott adatokat az Azure Data Lake Storage használatával elvégezhető **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ vagy az Azure Blob storage-fiók használatával  **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**. 

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

Hasonlóképpen olvashat diszkont adatkészletek. Kattintson a jobb gombbal az Azure Data Lake Store, ha szeretné, tekintse meg az adatok **az Azure portal adatkezelő-->** vagy **fájlkezelő** Visual Studión belül. 

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="quality"></a>Adatok minőségi ellenőrzése
Utazás és diszkont táblák a olvasása, miután adatminőségi ellenőrzése a következő módon teheti meg. Az eredményül kapott CSV-fájlok a kimenet az Azure Blob storage vagy az Azure Data Lake Store lehet. 

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

Keresse meg azokat, amelyek több mint 100 lelassítja kellett medallions:

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

Keresse meg azokat a tekintetében pickup_longitude érvénytelen rekordokat:

    ///find those invalid records in terms of pickup_longitude
    @ex_3 =
        SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
        FROM @trip
        WHERE
        pickup_longitude <- 90 OR pickup_longitude > 90;
        OUTPUT @ex_3   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
    USING Outputters.Csv(); 

Keresse meg a hiányzó értékek az egyes változók:

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
Hajtsa végre az alábbi parancsfájlok jobban megismerheti az adatok néhány adatvizsgálatot.

Keresse meg a Formabontó, és nem Formabontó lelassítja eloszlása:

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

Keresse meg a terjesztési tipp összeg levágva jelenhetnek értékekkel: 0, 5, 10 és 20 dollár.

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

Keresse meg az utazás távolság alapszintű statisztikákat:

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

Keresse meg az utazás távolság percentilisei:

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


### <a name="join"></a>Utazás és diszkont táblák
Utazás és diszkont táblák medallion, hack_license és pickup_time összekapcsolható.

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


Utas száma az egyes fenyegetési, a rekordokat, átlagos tipp összeg, tipp összeg varianciáját, Formabontó lelassítja százaléka számának kiszámítása.

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


### <a name="sample"></a>Adat-mintavételezés
Első lépésként véletlenszerűen kiválasztása 0,1 %-át az adatokat a táblából kell csatlakozniuk:

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

Ezután tegye a rétegzett mintavételi bináris változó tip_class szerint:

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
Amikor befejezte a szerkesztést a U-SQL-parancsfájlok, elküldheti őket a kiszolgálóhoz az Azure Data Lake Analytics-fiók használatával. Kattintson a **Data Lake**, **feladat elküldése**, jelölje be a **Analytics-fiók**, válassza a **párhuzamosság**, kattintson **küldés**  gombra.  

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

Ha a feladat a sikeresen teljesül, a feladat állapota megjelenik a Visual Studióban figyelésre. A feladat végeztével még a feladat-végrehajtási folyamat visszajátszásával és ismerje meg, a szűk keresztmetszetet lépéseket a feladat hatékonyság növelése. Ha ellátogat az Azure Portalra az U-SQL-feladatok állapotának ellenőrzéséhez.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Most ellenőrizheti a kimeneti fájlokat az Azure Blob storage vagy az Azure Portalon. A modellezés, a következő lépésben a rétegzett mintaadatok használatával.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Hozhat létre, és az Azure Machine Learning modellek üzembe helyezése
Két lehetőség áll rendelkezésre, hogy az adatok lekérése az Azure Machine Learningbe hozhat létre és 

* Az első lehetőség, használhatja az Azure Blob lett írva a mintavételezett adatok (az a **adat-mintavételezés** . a fenti lépés) és a Python használatával hozhat létre, és az Azure Machine Learning modellek üzembe helyezése. 
* A második lehetőség, a lekérdezheti, ha az Azure Data Lake az adatokat egy Hive-lekérdezést közvetlenül használatával. Ez a beállítás megköveteli, hogy hozzon létre egy új HDInsight-fürtöt, vagy használjon egy meglévő HDInsight-fürtöt, mutasson a Hive-táblákat az Azure Data Lake Storage NY i taxik adatait.  Mindkét ezek a beállítások az alábbi szakaszok ismertetik. 

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>1. lehetőség: A machine learning-modellek létrehozása és üzembe helyezése a Python használatával
Hozhat létre, és a Python használata a machine learning-modellek üzembe helyezése, a Jupyter Notebook létrehozása a helyi gépen, vagy az Azure Machine Learning Studióban. A Jupyter Notebook megadott [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) ismerje meg, megjelenítheti az adatokat, funkciófejlesztési, modellezés és üzembe helyezés a teljes kódját tartalmazza. Ez a cikk csak a modellezésre és a központi telepítési terjed ki. 

### <a name="import-python-libraries"></a>Python-kódtárakat importálása
Annak érdekében, hogy a minta futtatása Jupyter Notebook vagy a Python-szkriptfájlt, a következő Python-csomagok szükségesek. Az AzureML Notebook szolgáltatás használja, ha ezek a csomagok előre telepítve lett.

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


### <a name="read-in-the-data-from-blob"></a>Olvassa el az adatok a blobból
* Kapcsolati sztring   
  
        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
* Szöveg olvasás
  
        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))
  
  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)    
* Adja hozzá az oszlopok neveit és különálló oszlopok
  
        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
* Módosíthatja az egyes oszlopok numerikus típusúvá
  
        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>Machine learning-modellek létrehozása
Itt hozhat létre egy bináris osztályozási modell előre jelezni, hogy belépőt Formabontó-e, vagy sem. A Jupyter Notebookot található egyéb két modell: többosztályos osztályozási vagy regressziós modelleket.

* Meg kell hoznia a helyőrző változókat, amelyek segítségével a scikit-modellek további
  
        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
* A modellezés adatkeretbe létrehozása
  
        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])
  
        X = data.iloc[:,1:]
        Y = data.tipped
* Betanítására és tesztelésére 60 – 40 felosztása
  
        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
* A gyakorlókészlethez logisztikai regresszió
  
        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)
  
       ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)
* Tesztelési adatkészlet pontszám
  
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

### <a name="build-web-service-api-and-consume-it-in-python"></a>Webszolgáltatási API készítése és használata, a Pythonban
Szeretné üzembe helyezése a gépi tanulási modell, miután állították össze. A bináris logisztikai modell használható példa. Győződjön meg arról, hogy a scikit-további 0.15.1 verziószáma a helyi gépen. Nem kell foglalkoznia ezzel vagy az Azure Machine Learning studio szolgáltatás használatakor.

* Keresse meg a munkaterület hitelesítő adatait az Azure ML studio beállítások. Az Azure Machine Learning Studióban, kattintson **beállítások** --> **neve** --> **hitelesítési jogkivonatok**. 
  
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
* Webszolgáltatási API hívása. 5 – 10 másodperc az előző lépés után várnia kell.
  
        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)
  
       ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>2. lehetőség: Hozzon létre, és közvetlenül az Azure Machine Learning modellek üzembe helyezése
Az Azure Machine Learning Studio tudja olvasni az adatokat közvetlenül az Azure Data Lake Store, és hozzon létre és helyezhet üzembe modelleket majd használni. Ez a megközelítés használja a Hive-tábla, amely az Azure Data Lake Store mutat. Ehhez az szükséges, hogy egy külön Azure HDInsight-fürt építhető ki, amelyen létrehozták a Hive-táblában. A következő szakaszok bemutatják, hogyan teheti ezt. 

### <a name="create-an-hdinsight-linux-cluster"></a>HDInsight Linux-fürt létrehozása
Hozzon létre egy HDInsight-fürt (Linux) a [az Azure portal](http://portal.azure.com). További információkért lásd: a **egy HDInsight-fürt létrehozása az Azure Data Lake Store-hozzáféréssel rendelkező** szakasz [egy HDInsight-fürt létrehozása a Data Lake Store az Azure portal használatával](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>A HDInsight Hive-tábla létrehozásához
Most hozzon létre a HDInsight-fürt használata az Azure Data Lake Store az előző lépésben tárolt adatokat az Azure Machine Learning Studióban használandó Hive-táblákat. Nyissa meg a létrehozott HDInsight-fürt. Kattintson a **beállítások** --> **tulajdonságok** --> **AAD-identitását a fürt** --> **ADLS hozzáférés**, Győződjön meg arról, hogy az Azure Data Lake Store-fiók kerül a listában az olvasási, írási és végrehajtási jogokat. 

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Kattintson a **irányítópult** mellett a **beállítások** gombra, és a egy ablakban jelenik meg. Kattintson a **Hive-nézet** és az oldal jobb felső sarokban a következő: a **Lekérdezésszerkesztő**.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Illessze be a következő Hive-parancsprogramok hozzon létre egy táblát. Adatforrás helye. ily módon az Azure Data Lake Store-hivatkozás: **adl://data_lake_store_name.azuredatalakestore.net:443/mappa_neve/Fájlnév**.

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


A lekérdezés befejeztével az eredmények ehhez hasonló kell megjelennie:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Hozhat létre és helyezhet üzembe modelleket az Azure Machine Learning Studióban
Most már készen áll készíthet és helyezhet üzembe egy modellt az e tipp: az Azure Machine Learning fizetős. A rétegzett mintaadatok használhatók a bináris osztályozási készen áll (tipp vagy sem) a probléma. A prediktív modellek többosztályos osztályozási (tip_class) és regressziós (tip_amount) használatával is létrehozott és telepített Azure Machine Learning Studióban, de itt, csak akkor jelenik meg az esetet a bináris osztályozási modell kezelése.

1. Az adatok lekérése az Azure ML használatával a **adatok importálása** modul elérhető a **adatok bemeneti és kimeneti** szakaszban. További információkért lásd: a [adatok importálása modullal](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) referenciájának oldalát.
2. Válassza ki **Hive-lekérdezés** , a **adatforrás** a a **tulajdonságok** panel.
3. Illessze be a következő Hive-szkriptet a **adatbázis-lekérdezés Hive** szerkesztő
   
        select * from nyc_stratified_sample;
4. Adja meg az URI-t a HDInsight-fürt (Ez található Azure Portalon), a Hadoop hitelesítő adatokat, a kimeneti adatok és az Azure storage-fióknév neve/kulcs/tároló helyét.
   
   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)  

Egy bináris osztályozási kísérlet olvasási adatok Hive-táblából az alábbi ábrán látható példát:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

A kísérlet létrehozása után kattintson a **webszolgáltatás beállítása** --> **prediktív webszolgáltatás**

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Futtassa az automatikusan létrehozott nézhetik a pontozás a kísérlethez, kattintson a **webszolgáltatás üzembe helyezése**

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

A webszolgáltatás irányítópultján hamarosan jeleníti meg:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Összegzés
Ez a forgatókönyv végrehajtásával létrehozott egy olyan adatelemezési környezetet az Azure Data Lake méretezhető végpontok közötti megoldások kialakításához. Ebben a környezetben használt elemzése egy nagy méretű nyilvános adathalmazt vesz igénybe, a adatelemzési folyamat, az adatok beszerzési modell betanítása keresztül, majd a modellt webszolgáltatásként üzembe helyezés a canonical lépésein. U-SQL dolgozza fel, ismerje meg, és a mintaadatok lett megadva. Python- és Hive használták az Azure Machine Learning Studio készíthet és helyezhet üzembe prediktív modelleket.

## <a name="whats-next"></a>A következő lépések
A képzési terv a [csoportos adatelemzési folyamat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) leíró a fejlett analitikai folyamat minden lépése témakörökre mutató hivatkozásokat tartalmaz. Nincsenek a forgatókönyvek az esethez sorozata a [csoportos adatelemzési folyamat forgatókönyvek](walkthroughs.md) mutatni, hogyan használhatja az erőforrásokat és szolgáltatásokat a prediktív elemzések különböző forgatókönyvekben oldal:

* [A csoportos adatelemzési folyamat működés közben: az SQL Data Warehouse használatával](sqldw-walkthrough.md)
* [A csoportos adatelemzési folyamat működés közben: HDInsight Hadoop-fürtök használata](hive-walkthrough.md)
* [A csoportos adatelemzési folyamat: az SQL Server használata](sql-walkthrough.md)
* [A Spark használatával az Azure HDInsight adatelemzési folyamat áttekintése](spark-overview.md)

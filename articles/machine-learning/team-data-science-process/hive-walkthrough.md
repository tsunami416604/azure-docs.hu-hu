---
title: Adatok feltárása Hadoop-fürtben – Csapatadat-elemzési folyamat
description: A team data science folyamat használatával egy teljes körű forgatókönyv, egy HDInsight Hadoop-fürt használatával egy modell létrehozásához és üzembe helyezéséhez.
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
ms.openlocfilehash: bf69786f56f52874bd9358ae44a6b88b466e77f4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677462"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>A csapatadat-elemzési folyamat működés közben: Azure HDInsight Hadoop-fürtök használata
Ebben a forgatókönyvben a [csapat adatelemzési folyamat (TDSP)](overview.md) egy végpontok között forgatókönyv. Egy [Azure HDInsight Hadoop-fürtöt](https://azure.microsoft.com/services/hdinsight/) használunk a nyilvánosan elérhető [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) adatkészletből származó adatok tárolásához, feltárásához és szolgáltatástervezéséhez, valamint az adatok lemintavételezéséhez. A bináris és többosztályos besorolási és regressziós prediktív feladatok kezeléséhez az Azure Machine Learning segítségével modelleket hozunk létre az adatokból. 

Egy forgatókönyv, amely bemutatja, hogyan kell kezelni egy nagyobb adatkészletet, lásd: [Csapat adatelemzési folyamat – Az Azure HDInsight Hadoop fürtök használata egy 1 TB-os adatkészleten.](hive-criteo-walkthrough.md)

Az IPython-jegyzetfüzetek segítségével is elvégezheti az 1 TB-os adatkészletet használó forgatókönyvben bemutatott feladatokat. További információ: [Criteo forgatókönyv Hive ODBC-kapcsolat használatával.](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb)

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>NYC Taxi Trips adatkészlet leírása
A NYC Taxi Trip adatok körülbelül 20 GB tömörített vesszővel elválasztott értékek (CSV) fájlok (~ 48 GB tömörítetlen). Ez több mint 173 millió egyéni utak, és magában foglalja a viteldíjak fizetett minden út. Minden út rekord tartalmazza a pick-up és dropoff helyét és idejét, anonimizált hack (driver's) engedély számát, és medallion száma (a taxi egyedi azonosító). Az adatok a 2013-as év összes utazására vonatkoznak, és minden hónapra vonatkozóan a következő két adatkészletben szerepelnek:

- A trip_data CSV fájlok tartalmazzák az utazás részleteit: az utasok számát, a felvételi és leadási pontokat, az utazás időtartamát és az utazás hosszát. Íme néhány mintarekord:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
- A trip_fare CSV fájlok tartalmazzák az egyes utazásokért fizetett viteldíj részleteit: fizetés típusa, viteldíj összege, pótdíj és adók, tippek és útdíjak, valamint a teljes kifizetett összeg. Íme néhány mintarekord:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Az egyedülálló kulcs-hoz\_követ\_utazás adat és utazás boldogul\_van áll\_-ból mezők: medál, csapkod engedély, és pickup datetime. Ahhoz, hogy az adott utazáshoz kapcsolódó összes részletet megkapja, elegendő csatlakozni a három kulcshoz.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Példák előrejelzési feladatokra
Határozza meg, hogy milyen típusú előrejelzéseket szeretne készíteni az adatelemzés alapján, hogy segítsen tisztázni a szükséges folyamatfeladatokat. Íme három példa az előrejelzési problémákra, amelyekkel ebben a forgatókönyvben foglalkozunk, mindezt a *tipp\_összege*alapján:

- **Bináris besorolás**: Tipp elvárható, hogy egy tipp et fizettek-e egy utazásért. Ez azt jelenti, hogy a 0 0 000-nál nagyobb *tippösszeg\_* pozitív példa, míg *a\_* 000 000 000 000 000 000 000 000 000 000 000 000 Dollár.
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0
- **Többosztályos besorolás**: Az utazásért fizetett tippösszegek tartományának előrejelzése. A *borravaló\_összegét* öt osztályra osztjuk:
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0 and tip_amount <= $5
        Class 2: tip_amount > $5 and tip_amount <= $10
        Class 3: tip_amount > $10 and tip_amount <= $20
        Class 4: tip_amount > $20
- **Regressziós feladat:** Tippelje meg az utazásért fizetett tipp összegét.  

## <a name="set-up-an-hdinsight-hadoop-cluster-for-advanced-analytics"></a><a name="setup"></a>HDInsight Hadoop-fürt beállítása speciális elemzésekhez
> [!NOTE]
> Ez általában egy rendszergazdai feladat.
> 
> 

Beállíthat egy Azure-környezetet a speciális elemzésekhez, amely három lépésben alkalmaz egy HDInsight-fürtöt:

1. [Tárfiók létrehozása:](../../storage/common/storage-account-create.md)Ez a tárfiók az Azure Blob storage-ban tárolt adatok tárolására szolgál. A HDInsight-fürtökben használt adatok is itt találhatók.
2. [Az Azure HDInsight Hadoop-fürtök testreszabása a fejlett elemzési folyamathoz és technológiához.](customize-hadoop-cluster.md) Ez a lépés létrehoz egy HDInsight Hadoop-fürtöt 64 bites Anaconda Python 2.7-es kódokkal az összes csomóponton. A HDInsight-fürt testreszabása során két fontos lépést kell megjegyeznie.
   
   * Ne felejtse el összekapcsolni az 1. Ez a tárfiók a fürtön belül feldolgozott adatokhoz fér hozzá.
   * A fürt létrehozása után engedélyezze a távoli hozzáférést a fürt fő csomópontjához. Tallózással keresse meg a **Konfiguráció** lapot, és válassza **a Távoli szolgáltatás engedélyezése**lehetőséget. Ez a lépés a távoli bejelentkezéshez használt felhasználói hitelesítő adatokat adja meg.
3. [Hozzon létre egy Azure Machine Learning-munkaterületet:](../studio/create-workspace.md)Ezzel a munkaterülettel gépi tanulási modelleket hozhat létre. Ez a feladat a HDInsight-fürt használatával a kezdeti adatfeltárás és a sikertelen mintavételezés befejezése után történik.

## <a name="get-the-data-from-a-public-source"></a><a name="getdata"></a>Az adatok beszereznie nyilvános forrásból
> [!NOTE]
> Ez általában egy rendszergazdai feladat.
> 
> 

A [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) adatkészletet a számítógépnyilvános helyéről történő másolásához használja az Adatok áthelyezése az Azure Blob [storage-ba és onnan című dokumentumban](move-azure-blob.md)ismertetett módszerek bármelyikét.

Itt bemutatjuk, hogyan kell az AzCopy segítségével átvinni az adatokat tartalmazó fájlokat. Az AzCopy letöltéséhez és telepítéséhez kövesse [az Első lépések az AzCopy parancssori segédprogramjával](../../storage/common/storage-use-azcopy.md)című témakörutasításait.

1. A parancssorablakban futtassa a következő AzCopy parancsokat, és cserélje * \<le path_to_data_folder>* a kívánt célra:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. Amikor a másolat befejeződik, összesen 24 tömörített fájlt fog látni a kiválasztott adatmappában. Csomagolja ki a letöltött fájlokat a helyi számítógép ugyanazon könyvtárába. Jegyezze fel azt a mappát, amelyben a tömörítetlen fájlok találhatók. Ezt a mappát * \<a\_\_következő\_\> fájlok unzipped_data elérési útjaként* nevezik.

## <a name="upload-the-data-to-the-default-container-of-the-hdinsight-hadoop-cluster"></a><a name="upload"></a>Az adatok feltöltése a HDInsight Hadoop-fürt alapértelmezett tárolójára
> [!NOTE]
> Ez általában egy rendszergazdai feladat.
> 
> 

A következő AzCopy-parancsokban cserélje le a következő paramétereket a Hadoop-fürt létrehozásakor és az adatfájlok kibontásakor megadott tényleges értékekre.

* *** \<path_to_data_folder>*** A számítógép azon könyvtára (az elérési úttal együtt), amely a kibontott adatfájlokat tartalmazza.  
* Hadoop-fürt>neve *** \<*** A HDInsight-fürthöz társított tárfiók.
* ***A Hadoop-fürt alapértelmezett tárolója>\<*** A fürt által használt alapértelmezett tároló. Az alapértelmezett tároló neve általában megegyezik magával a fürttel. Ha például a fürt neve "abc123.azurehdinsight.net", az alapértelmezett tároló az abc123.
* tárfiók kulcsának>*** \<*** A fürt által használt tárfiók kulcsa.

A parancssorból vagy egy Windows PowerShell-ablakból futtassa a következő két AzCopy-parancsot.

Ez a parancs feltölti az utazási adatokat a ***nyctaxitripraw*** könyvtárba a Hadoop-fürt alapértelmezett tárolójában.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Ez a parancs feltölti a viteldíjadatokat a hadoop-fürt alapértelmezett tárolójában lévő ***nyctaxifareraw*** könyvtárba.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Az adatoknak mostantól a Blob storage-ban kell lenniük, és készen kell lenniük a HDInsight-fürtön belüli felhasználásra.

## <a name="sign-in-to-the-head-node-of-hadoop-cluster-and-prepare-for-exploratory-data-analysis"></a><a name="#download-hql-files"></a>Jelentkezzen be a Hadoop-fürt fő csomópontjára, és készüljön fel a feltáró adatok elemzésére
> [!NOTE]
> Ez általában egy rendszergazdai feladat.
> 
> 

A fürt főcsomópontjának feltáró adatelemzéshez és az adatok lefelé mintavételezéséhez való eléréséhez kövesse a [Hadoop-fürt főcsomópontjának elérése](customize-hadoop-cluster.md)című részben ismertetett eljárást.

Ebben a forgatókönyvben elsősorban [a Hive-](https://hive.apache.org/)ban írt lekérdezéseket használjuk, egy SQL-szerű lekérdezési nyelvet, előzetes adatfeltárások végrehajtásához. A Hive-lekérdezések '.hql' fájlokban tárolódnak. Ezután le-minta ezeket az adatokat kell használni a Machine Learning modellek létrehozásához.

A fürt előkészítése a feltáró adatok elemzésére, töltse le a megfelelő Hive-parancsfájlokat tartalmazó ".hql" fájlokat a [GitHubról](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) egy helyi könyvtárba (C:\temp) a fő csomóponton. Nyissa meg a parancssort a fürt fő csomópontján belül, és futtassa a következő két parancsot:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Ez a két parancs letölti az összes ".hql" fájlt, amelyre szükség van ebben a forgatókönyvben, a központi csomópontban lévő ***C:\temp&#92;*** helyi könyvtárba.

## <a name="create-hive-database-and-tables-partitioned-by-month"></a><a name="#hive-db-tables"></a>Hive-adatbázis és hónap szerint particionált táblák létrehozása
> [!NOTE]
> Ez a feladat általában egy rendszergazda.
> 
> 

Most már készen áll a Hive-táblák létrehozására a NYC taxi adatkészlethez.
A Hadoop-fürt főcsomópontjában nyissa meg a Hadoop parancssort a főcsomópont asztalán. Írja be a Hive könyvtárat a következő parancs futtatásával:

    cd %hive_home%\bin

> [!NOTE]
> Futtassa az összes Hive-parancsokebben a forgatókönyvben a Hive bin/ könyvtár parancs. Ez automatikusan kezeli az elérési út problémáit. Ebben a forgatókönyvben a "Hive könyvtársor", a "Hive bin/ directory prompt" és a "Hadoop parancssor" kifejezéseket használjuk szinonimaként.
> 
> 

A Hive könyvtárparancsból futtassa a következő parancsot a Hive-adatbázist és táblákat létrehozó főcsomópont Hadoop parancssorában:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Itt van a **\_c:\temp\mintastruktúra\_\_létrehozása\_\_db és tables.hql** fájl, amely létrehozza a Hive adatbázis **nyctaxidb**, és a táblák **utazás** és **a viteldíj**.

    create database if not exists nyctaxidb;

    create external table if not exists nyctaxidb.trip
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double)  
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

    create external table if not exists nyctaxidb.fare
    (
        medallion string,
        hack_license string,
        vendor_id string,
        pickup_datetime string,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double)
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

Ez a Hive-parancsfájl két táblát hoz létre:

* Az **utazási** táblázat tartalmazza az egyes utazások részleteit (a vezető adatai, a felvételi idő, az utazási távolság és az időpontok).
* A **viteldíjtábla** tartalmazza a viteldíj részleteit (a viteldíj összege, a borravaló összege, az útdíjak és a pótdíjak).

Ha további segítségre van szüksége ezekkel az eljárásokkal kapcsolatban, vagy alternatívalekérdezéseket szeretne megvizsgálni, olvassa el a [Hive-lekérdezések küldése közvetlenül a Hadoop parancssorból](move-hive-tables.md#submit)című szakaszt.

## <a name="load-data-to-hive-tables-by-partitions"></a><a name="#load-data"></a>Adatok betöltése Struktúra-táblákba partíciók szerint
> [!NOTE]
> Ez a feladat általában egy rendszergazda.
> 
> 

A NYC taxi adatkészlet egy természetes particionálás hónap szerint, amelyet az általunk használt, hogy gyorsabb feldolgozási és lekérdezési idő. A következő PowerShell-parancsok (a Hive-címtárból a Hadoop parancssorhasználatával) adatokat töltenek be az utazás és a viteldíj Hive-táblák, havonta particionálva.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

A **\_mintastruktúra\_\_\_betöltési adatai partitions.hql\_** fájl szerint a következő **LOAD** parancsokat tartalmazzák:

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

A feldolgozási folyamat során használt Hive-lekérdezések száma csak egy vagy két partíciót tartalmaz. De ezek a lekérdezések futtathatók a teljes adatkészleten.

### <a name="show-databases-in-the-hdinsight-hadoop-cluster"></a><a name="#show-db"></a>Adatbázisok megjelenítése a HDInsight Hadoop-fürtben
A HDInsight Hadoop-fürtben a Hadoop parancssori ablakban létrehozott adatbázisok megjelenítéséhez futtassa a következő parancsot a Hadoop parancssorában:

    hive -e "show databases;"

### <a name="show-the-hive-tables-in-the-nyctaxidb-database"></a><a name="#show-tables"></a>A Hive-táblák megjelenítése a **nyctaxidb** adatbázisban
A **nyctaxidb** adatbázis tábláinak megjelenítéséhez futtassa a következő parancsot a Hadoop parancssorában:

    hive -e "show tables in nyctaxidb;"

Megerősíthetjük, hogy a táblák particionálva vannak a következő parancs futtatásával:

    hive -e "show partitions nyctaxidb.trip;"

Itt van a várt kimenet:

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 2.075 seconds, Fetched: 12 row(s)

Hasonlóképpen biztosíthatjuk, hogy a viteldíjtábla particionálva legyen a következő parancs futtatásával:

    hive -e "show partitions nyctaxidb.fare;"

Itt van a várt kimenet:

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 1.887 seconds, Fetched: 12 row(s)

## <a name="data-exploration-and-feature-engineering-in-hive"></a><a name="#explore-hive"></a>Adatfeltárás és szolgáltatástervezés a Hive-ban
> [!NOTE]
> Ez általában egy adattudós feladat.
> 
> 

A Hive-lekérdezések segítségével adatfeltárási és szolgáltatásmérnöki feladatokat végezhet a Hive-táblákba betöltött adatokhoz. Íme néhány példa az ilyen feladatokra:

* Mindkét tábla első 10 rekordjának megtekintése.
* Néhány mező adateloszlásának megismerése különböző időablakokban.
* Vizsgálja meg a hosszúsági és szélességi mezők adatminőségét.
* Bináris és többosztályos besorolási címkék létrehozása a tipp összege alapján.
* A közvetlen úttávolságok kiszámításával hozhat létre funkciókat.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Feltárás: Tekintse meg a top 10 rekordok asztali kirándulás
> [!NOTE]
> Ez általában egy adattudós feladat.
> 
> 

Az adatok megjelenésének megtekintéséhez vizsgálja meg az egyes tábla 10 rekordját. A rekordok vizsgálatához futtassa a következő két lekérdezést a Hadoop parancssori konzol Hive könyvtárparancssori parancssori parancssori parancssori parancssori parancssori parancssori parancssori parancssori parancssori parancssori parancssori parancssori parancssori parancssori parancssori parancssori parancssori parancssori parancssori parancssori

Az első hónap első 10 rekordjának bekerülése az utazás táblában:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Ahhoz, hogy a top 10 rekordot a viteldíj táblázatban az első hónapban:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

A rekordokat fájlba mentheti, hogy az előző lekérdezés kis módosítással kényelmesen megtekinthesse őket:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Feltárás: A rekordok számának megtekintése mind a 12 partíción
> [!NOTE]
> Ez általában egy adattudós feladat.
> 
> 

Érdekes, hogy az utazások száma hogyan változik a naptári év során. A hónap szerint csoportosítva mutatja az utazások eloszlását.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Ez a parancs a következő kimenetet adja elő:

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 283.406 seconds, Fetched: 12 row(s)

Itt az első oszlop a hónap, a második pedig az adott hónapra vonatkozó utazások száma.

Az utazási adatkészletünkben lévő rekordok teljes számát úgy is meg számolhatjuk, hogy a következő parancsot futtatjuk a Hive könyvtárparancssorában:

    hive -e "select count(*) from nyctaxidb.trip;"

Ez a parancs a következőt eredményezi:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Az utazási adatkészlethez hasonló parancsok használatával a Hive-címtárból származó Hive-lekérdezéseket adhatunk ki a viteldíj-adatkészlethez a rekordok számának ellenőrzéséhez.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Ez a parancs a következő kimenetet hozza létre:

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 253.955 seconds, Fetched: 12 row(s)

Mindkét adatkészletesetében pontosan ugyanannyi út kerül vissza, amely biztosítja az adatok helyes betöltésének első érvényesítését.

A viteldíj-adatkészlet rekordjainak teljes számát megszámolhatja a Hive könyvtárparancs következő parancsával:

    hive -e "select count(*) from nyctaxidb.fare;"

Ez a parancs a következőt eredményezi:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

A rekordok száma mindkét táblában megegyezik, így a második ellenőrzés, hogy az adatok megfelelően töltődtek be.

### <a name="exploration-trip-distribution-by-medallion"></a>Feltárás: Utazás elosztása medál szerint
> [!NOTE]
> Ez az elemzés általában egy adatelemző feladat.
> 
> 

Ez a példa azonosítja azokat a medálokat (taxiszámokat), amelyek egy adott időszakon belül több mint 100 utat tesznek meg. A lekérdezés számára előnyös a particionált tábla-hozzáférés, mivel a **hatos**partícióváltozó feltétele. A lekérdezés eredményei egy helyi fájlba kerülnek( **queryoutput.tsv**), a `C:\temp` főcsomóponton.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Itt van a **tartalom\_a\_\_minta kaptár út száma\_\_medallion.hql** fájl ellenőrzésre.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

A new yorki taxi adatkészletében lévő medál egy egyedi fülkét azonosít. Azonosíthatja, hogy mely fülkék viszonylag forgalmasak, ha megkérdezi, hogy egy adott időszakban melyik vezető út több, mint egy bizonyos számú utazás. A következő példa azonosítja azokat a fülkéket, amelyek az első három hónapban több mint száz utat tettek meg, és a lekérdezés eredményeit egy helyi fájlba mentik, **a C:\temp\queryoutput.tsv**fájlba.

Itt van a **tartalom\_a\_\_minta kaptár út száma\_\_medallion.hql** fájl ellenőrzésre.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

A Hive könyvtárparancsból futtassa a következő parancsot:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Feltárása: Trip forgalmazás medál és csapkod engedély
> [!NOTE]
> Ez a feladat általában egy adattudós.
> 
> 

Egy adatkészlet feltárása során gyakran szeretnénk megvizsgálni az értékcsoportok eloszlását. Ez a rész egy példát mutat be, hogyan kell ezt az elemzést a taxik és a járművezetők.

A **\_minta\_\_kaptár\_út\_\_száma medallion license.hql** fájl szerint csoportosítja a viteldíj-adatkészletet **a medálon** és **a hack_license,** és az egyes kombinációk számát adja vissza. Itt van a tartalma:

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Ez a lekérdezés a vezető- és illesztőprogram-kombinációkat adja vissza, csökkenő számú utazás szerint rendezve.

A Hive könyvtárból parancs sora futtassa a következőt:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

A lekérdezés eredménye egy helyi fájlba íródik( **C:\temp\queryoutput.tsv**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Feltárás: Az adatok minőségének felmérése az érvénytelen hosszúsági vagy szélességi rekordok ellenőrzésével
> [!NOTE]
> Ez általában egy adattudós feladat.
> 
> 

A feltáró adatelemzés egyik közös célja az érvénytelen vagy hibás rekordok kivonása. Ebben a szakaszban a példa határozza meg, hogy a hosszúsági vagy szélességi mezők tartalmaznak-e olyan értéket, amely messze kívül esik a NYC területen. Mivel valószínű, hogy az ilyen rekordok hibás hosszúsági szélességi értékkel rendelkeznek, meg akarjuk szüntetni őket a modellezéshez használt adatokból.

Itt van a tartalom **minta\_kaptár\_tulajdonság\_assessment.hql** fájl ellenőrzésre.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


A Hive könyvtárból parancs sora futtassa a következőt:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

A parancsban található *-S* argumentum letiltja a Hive-térkép/Csökkentés feladatok állapotképernyő-nyomtatását. Ez a parancs azért hasznos, mert a Hive-lekérdezés kimenetének képernyőnyomtatását olvashatóbbá teszi.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Feltárás: Bináris osztály eloszlása utazási tippek
> [!NOTE]
> Ez általában egy adattudós feladat.
> 
> 

Az [előrejelzési feladatok példái](hive-walkthrough.md#mltasks) szakaszban ismertetett bináris besorolási probléma esetén érdemes tudni, hogy kapott-e tippet. Ez a forgalmazás a tippeket bináris:

* tipp adott (Class\_1, tipp összege > $ 0)  
* nincs tipp (Class\_0, tipp összege = $0)

A következő **mintastruktúra\_\_tipped\_frequencies.hql** fájl mutatja a parancsot futtatni:

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

A Hive könyvtárból parancs sora futtassa a következőt:

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Feltárás: Osztályeloszlások a többosztályos beállításban
> [!NOTE]
> Ez általában egy adattudós feladat.
> 
> 

Az [előrejelzési feladatok példái](hive-walkthrough.md#mltasks) szakaszban ismertetett többosztályos besorolási probléma esetében ez az adatkészlet egy természetes besorolást is biztosít a megadott tippek mennyiségének előrejelzéséhez. A raktárhelyeket használhatjuk a lekérdezés tipptartományai meghatározásához. A különböző csúcstartományok osztályeloszlásának lekérni, használja a **mintahiváz-tip\_\_\_\_tartomány frequencies.hql** fájlt. Itt van a tartalma.

    SELECT tip_class, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount=0, 0,
            if(tip_amount>0 and tip_amount<=5, 1,
            if(tip_amount>5 and tip_amount<=10, 2,
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

Futtassa a következő parancsot a Hadoop parancssori konzolról:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Feltárás: Számítsa ki a közvetlen távolságot két hosszúsági szélességi hely között
> [!NOTE]
> Ez általában egy adattudós feladat.
> 
> 

Érdemes tudni, hogy van-e különbség a közvetlen távolság két hely között, és a tényleges utazási távolság a taxi. Az utas kevésbé valószínű, hogy borravalót, ha rájönnek, hogy a vezető szándékosan vette őket egy hosszabb útvonalon.

A tényleges úttávolság és a Két hosszúsági szélességi pont (a "nagy kör" távolság) közötti [barátközötti](https://en.wikipedia.org/wiki/Haversine_formula) összehasonlításmegtekintéséhez használhatja a Hive-ban elérhető trigonometriai funkciókat:

    set R=3959;
    set pi=radians(180);

    insert overwrite directory 'wasb:///queryoutputdir'

    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
     *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
     *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
     /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
     +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
     pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
    from nyctaxidb.trip
    where month=1
    and pickup_longitude between -90 and -30
    and pickup_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
    and dropoff_latitude between 30 and 90;

Az előző lekérdezésben Az R a Föld sugara mérföldben, a pi pedig radiánná alakul át. A program szűri a hosszúsági szélességi pontokat, hogy eltávolítsa a NYC területtől távolodó értékeket.

Ebben az esetben az eredményeket egy **queryoutputdir**nevű könyvtárba írjuk. A következő parancsok sorrendje először létrehozza ezt a kimeneti könyvtárat, majd futtatja a Hive parancsot.

A Hive könyvtárból parancs sora futtassa a következőt:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


A lekérdezés idoletere kilenc Azure-blobba **(queryoutputdir/000000\_0** **queryoutputdir/000008\_0)** íródik a Hadoop-fürt alapértelmezett tárolója alatt.

Az egyes blobok méretének megtekintéséhez futtassa a következő parancsot a Hive könyvtárból:

    hdfs dfs -ls wasb:///queryoutputdir

Egy adott fájl tartalmának megtekintéséhez tegyük a **000000\_0-t,** használja a Hadoop parancsát. `copyToLocal`

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> `copyToLocal`lehet nagyon lassú a nagy fájlokat, és nem ajánlott velük.  
> 
> 

Az Azure-blobban található adatok egyik fő előnye, hogy az [Adatok importálása][import-data] modul használatával megismerhetjük a Machine Learningben található adatokat.

## <a name="down-sample-data-and-build-models-in-machine-learning"></a><a name="#downsample"></a>Leadott mintaadatok és buildmodellek a Machine Learningben
> [!NOTE]
> Ez általában egy adattudós feladat.
> 
> 

A feltáró adatelemzési fázis után készen állunk arra, hogy lemintasszük a Machine Learning modellek készítésére vonatkozó adatokat. Ebben a szakaszban bemutatjuk, hogyan használhatja a Hive-lekérdezést az adatok lemintavételezéséhez. A Machine Learning ezután az [Adatok importálása][import-data] modulból éri el azt.

### <a name="down-sampling-the-data"></a>Az adatok lemintavételezése
Az eljárás két lépésből áll. Először is csatlakozunk a **nyctaxidb.trip** és **nyctaxidb.fare** asztalok három kulcs, amelyek jelen vannak minden rekordot: **medál**, **hack\_engedély**, és **a pickup\_datetime**. Ezután létrehozunk egy bináris besorolási címkét, **a hegyezi**és egy többosztályos besorolási címkét, **tip\_osztályt.**

Ahhoz, hogy az adatok importálása a Machine Learningben az adatok importálása modulból közvetlenül használhassa az adatok [at,][import-data] az előző lekérdezés eredményeit egy belső Hive-táblában kell tárolnia. A következőkben létrehozunk egy belső Hive-táblát, és feltöltjük annak tartalmát az egyesített és a mintavételezett adatokkal.

A lekérdezés a szabványos Hive-függvényeket alkalmazza közvetlenül a következő időparaméterek létrehozásához a **\_felvételi datetime** mezőből:
- napóra
- az év hetében
- hétköznap (az "1" a hétfőt, a "7" pedig a vasárnapot jelenti)

A lekérdezés a felvételi és a leadási helyek közötti közvetlen távolságot is generálja. Az ilyen függvények teljes listáját a [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

A lekérdezés majd lefelé mintákat az adatokat, hogy a lekérdezés eredményei elférnek az Azure Machine Learning Studio.The query then down-samples the data that the query results can fit into Azure Machine Learning Studio. Az eredeti adatkészletnek csak körülbelül 1 százaléka kerül importálásra a stúdióba.

A **\_mintastruktúra tartalma\_az\_\_aml\_full.hql fájlra való felkészülést,** amely előkészíti az adatokat a machine learning modellépületéhez:

        set R = 3959;
        set pi=radians(180);

        create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
        )
        row format delimited fields terminated by ','
        lines terminated by '\n'
        stored as textfile;

        --- now insert contents of the join into the above internal table

        insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
        select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

        from
        (
        select
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
        ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
        rand() as sample_key

        from nyctaxidb.trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from nyctaxidb.fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01

A lekérdezés futtatása a Hive könyvtárból:

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

Most már van egy belső tábla, **nyctaxidb.nyctaxi_downsampled_dataset**, amely elérhető az [Adatok importálása][import-data] modul a Machine Learning. Továbbá használhatja ezt az adatkészletet machine learning-modellek létrehozásához.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Az Adatok importálása modul használata a Machine Learningben az lefelé mintavételezett adatok eléréséhez
A Machine Learning [Adatok importálása][import-data] modulban hive-lekérdezéseket kell kiadnia, és hozzá kell férnem egy Machine Learning-munkaterülethez. A fürt hitelesítő adataihoz és a hozzá tartozó tárfiókhoz is hozzá kell férnie.

Íme néhány részlet az [Adatok importálása][import-data] modulról és a betáplálandó paraméterekről:

**HCatalog-kiszolgáló URI:** Ha a fürt neve **abc123**, majd használja: https:\//abc123.azurehdinsight.net.

**Hadoop felhasználói fiók neve**: A fürthöz választott felhasználónév (nem a távelérés felhasználói neve).

**Hadoop felhasználói fiók jelszava**: A fürthöz választott jelszó (nem a távelérési jelszó).

**Kimeneti adatok helye**: Azure-nak választott.

**Azure Storage-fiók neve:** A fürthöz társított alapértelmezett tárfiók neve.

**Azure-tároló neve:** A fürt alapértelmezett tárolóneve, és általában megegyezik a fürt nevével. **Az abc123**nevű fürt neve abc123 név.

> [!IMPORTANT]
> Minden olyan táblának, amelyet a Machine Learning [Adatok importálása][import-data] moduljával szeretnénk lekérdezni, belső táblának kell lennie.
> 
> 

Az alábbiakban azt állapíthatja meg, hogy a **D.db** adatbázisBan lévő **T** tábla belső tábla-e. A Hive könyvtárparancsból futtassa a következő parancsot:

    hdfs dfs -ls wasb:///D.db/T

Ha a tábla belső tábla, és ki van töltve, a tartalmát itt kell megjeleníteni.

Egy másik módja annak megállapítására, hogy egy tábla egy belső tábla az Azure Storage Explorer használata. Segítségével megnyitható a fürt alapértelmezett tárolóneve, majd a tábla neve szerint szűr. Ha a tábla és annak tartalma megjelenik, ez megerősíti, hogy belső tábla.

Íme egy képernyőkép a Hive-lekérdezésről és az [Adatok importálása][import-data] modulról:

![Képernyőkép: Az Adatok importálása modul Hive-lekérdezése](./media/hive-walkthrough/1eTYf52.png)

Mivel a lepusztult adatok az alapértelmezett tárolóban találhatók, a Machine Learning ből származó hive-lekérdezés egyszerű. Ez csak egy **SELECT * FROM nyctaxidb.nyctaxi\_downsampled\_adatok**.

Az adatkészlet most már használható a Machine Learning-modellek létrehozásának kiindulópontjaként.

### <a name="build-models-in-machine-learning"></a><a name="mlmodel"></a>Modellek készítése a Machine Learningben
Most már folytathatja a modellépítést és a modell üzembe helyezését a [Machine Learning ben.](https://studio.azureml.net) Az adatok készen állnak arra, hogy felhasználjuk a korábban azonosított előrejelzési problémák megoldását:

- **Bináris besorolás**: Annak előrejelzése, hogy egy tipp et fizettek-e egy utazásért.

  **Tanuló használt:** Kétosztályos logisztikai regresszió

  a. Ehhez a problémához a cél (vagy osztály) címke **billent .** Az eredeti, lefelé mintavételezett adatkészlet néhány oszlopot tartalmaz, amelyek a besorolási kísérlet célszivárgásai. Különösen a **\_tipposztály,** **a\_tipp összege**és a teljes **\_összeg** a tesztelési időpontban nem elérhető célcímkéről tartalmaz információkat. Ezeket az oszlopokat az [Adatkészlet modul Oszlopok kijelölése][select-columns] modullal távolítjuk el a megfontolásból.

  Az alábbi ábra azt mutatja, hogy a kísérlet ünk azt jelzi, hogy egy adott utazásért fizettek-e borravalót:

  ![Kísérlet diagramja annak előrejelzésére, hogy a borravalót kifizették-e](./media/hive-walkthrough/QGxRz5A.png)

  b. Ehhez a kísérlethez a célcímke-eloszlásunk nagyjából 1:1 volt.

   A következő diagram a bináris osztályozási probléma tip osztálycímkéinek eloszlását mutatja be:

  ![A tipposztály-címkék eloszlásának diagramja](./media/hive-walkthrough/9mM4jlD.png)

    Ennek eredményeképpen 0,987-es görbén (AUC) lévő területet kapunk, az alábbi ábrán látható módon:

  ![Az AUC értékének diagramja](./media/hive-walkthrough/8JDT0F8.png)

- **Többosztályos besorolás:** Az utazásért fizetett tippösszegek tartományának előrejelzése a korábban meghatározott osztályok használatával.

  **Tanuló használt:** Többosztályos logisztikai regresszió

  a. Ehhez a problémához a cél (vagy osztály) címke **tipp\_osztály**, amely öt érték egyikét veheti igénybe (0,1,2,3,4). Csakúgy, mint a bináris besorolási esetben, van néhány oszlop, amelyek a kísérlet cél szivárgás. Különösen a **borravaló**, **a borravaló\_összege**és a teljes **\_összeg** tárja fel a célcímkéről szóló olyan információkat, amelyek a tesztelés időpontjában nem állnak rendelkezésre. Ezeket az oszlopokat az [Adatkészlet oszlopok kijelölése][select-columns] modullal távolítjuk el.

  Az alábbi ábra azt mutatja, hogy a kísérlet megjósolni, hogy melyik bin egy tipp valószínűleg esik. A raktárhelyek a következők: Class 0: tip = $0, Class 1: tip > $0 és tip <= $5, Class 2: tip > $5 és tipp <= $10, Class 3: tip > $10 és tip <= $20, és Class 4: tip > $20.

  ![Kísérlet diagramja a raktárhely tippjének előrejelzésére](./media/hive-walkthrough/5ztv0n0.png)

  Most megmutatjuk, hogy néz ki a tesztosztály tényleges eloszlása. A 0 és 1 osztály elterjedt, a többi osztály pedig ritkák.

  ![A vizsgálati osztály eloszlásának diagramja](./media/hive-walkthrough/Vy1FUKa.png)

  b. Ehhez a kísérlethez egy zűrzavaros mátrixot használunk az előrejelzési pontosságok szemével, ahogy az itt látható:

  ![Zavartsági mátrix](./media/hive-walkthrough/cxFmErM.png)

  Míg az osztály pontosságát a elterjedt osztályok jó, a modell nem csinál egy jó munkát a "tanulás" a ritkább osztályok.

- **Regressziós feladat:** Az utazásért fizetett tipp összegének előrejelzése.

  **Tanuló használt:** Kiemelt döntési fa

  a. Ehhez a problémához a cél (vagy osztály) címke **a tipp\_összege**. A cél szivárgás ebben az esetben a következők: **billenő**, **\_tip osztály**, és **a teljes\_összeg**. Mindezek a változók felfedik a tipp összegét, amely általában nem érhető el a tesztelési idő. Ezeket az oszlopokat az [Adatkészlet oszlopok kijelölése][select-columns] modullal távolítjuk el.

  A következő ábra a kísérlet et mutatja be az adott tipp mennyiségének előrejelzésére:

  ![Kísérlet diagramja a tipp mennyiségének előrejelzésére](./media/hive-walkthrough/11TZWgV.png)

  b. Regressziós problémák esetén az előrejelzés pontosságát az előrejelzések négyzetes hibájának és a meghatározási együtthatónak a megvizsgálásával mérjük:

  ![Előrejelzési statisztika képernyőképe](./media/hive-walkthrough/Jat9mrz.png)

  Itt a meghatározási együttható 0,709, ami azt jelenti, hogy a variancia körülbelül 71 százalékát a modell együtthatók magyarázzák.

> [!IMPORTANT]
> Ha többet szeretne megtudni a Machine Learningről, valamint a hozzáférésről és annak használatáról, olvassa [el a Mi a Gépi tanulás.](../studio/what-is-machine-learning.md) Emellett az [Azure AI-galériában](https://gallery.cortanaintelligence.com/) a kísérletek egy skáláját, és egy alapos bevezetést nyújt a gépi tanulás képességeinek.
> 
> 

## <a name="license-information"></a>Licencadatai
Ezt a mintaforgatókönyvet és a hozzá tartozó parancsfájlokat a Microsoft az MIT-licenc alatt osztja meg. További információ: **LICENSE.txt** fájl a GitHub mintakódjának könyvtárában.

## <a name="references"></a>Referencia
• [Andrés Monroy NYC Taxi utak Letöltés oldal](https://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC's Taxi Trip adatok Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi és Limuzin Bizottság Kutatási és Statisztikai](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/




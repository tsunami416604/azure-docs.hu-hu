---
title: Egy Hadoop-fürt – a csoportos adatelemzési folyamat az adatok megismerése
description: A csoportos adatelemzési folyamat használatával egy végpontok közötti forgatókönyv, alkalmazó egy HDInsight Hadoop-fürtöt hozhat létre és helyezhet üzembe modelleket.
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
ms.openlocfilehash: 005d4fe1b6ec59e7f05be3dd2ab3e72d0e7aa8e0
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720571"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>A csoportos adatelemzési folyamat működés közben: használata Azure HDInsight Hadoop-fürtök
Ebben az útmutatóban egy végpontok közötti forgatókönyvben használjuk a [csoportos adatelemzési folyamatot (TDSP)](overview.md) . Egy Azure HDInsight Hadoop- [fürtöt](https://azure.microsoft.com/services/hdinsight/) használunk a nyilvánosan elérhető [NYC-taxis](https://www.andresmh.com/nyctaxitrips/) adatkészletből származó adatok tárolására, megismerésére és szolgáltatására, valamint az adatok leállására. Bináris és többosztályos besorolási és regressziós prediktív feladatok kezelésére, hogy ki, hogy az adatok az Azure Machine Learning modellek. 

A nagyobb adatkészletek kezelésére szolgáló bemutatóhoz lásd: [csoportos adatelemzési folyamat – Azure HDInsight Hadoop fürtök használata 1 TB-os adatkészleten](hive-criteo-walkthrough.md).

Az 1 TB-os adatkészletet használó bemutatóban bemutatott feladatok elvégzéséhez IPython notebookot is használhat. További információ: Criteo- [útmutató a kaptár ODBC-kapcsolatok használatával](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="dataset"></a>NYC taxi TRIPS adatkészlet leírása
A NYC Taxi útadatok körülbelül 20 GB tömörített vesszővel elválasztott értékeket (CSV) fájl (~ 48 GB tömörítetlen). 173 milliónál egyes lelassítja rendelkezik, és a fizetett egyes utazás a díjakat tartalmazza. Minden egyes út rekord tartalmazza a felvétel és dropoff hely és idő, anonimizált feltörés (illesztőprogramok) licencszám és medallion number (egyedi azonosító a taxi). Az adatok terjed ki minden lelassítja az év 2013-hoz, és minden hónapban megtalálható a következő két adatkészletet:

- A trip_data CSV-fájlok az utazás részleteit tartalmazzák: az utasok számát, a beléptetést és a lemorzsolódási, az utazási időtartamot és a menetidő hosszát. Az alábbiakban néhány példa rekordokat:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
- Az trip_fare CSV-fájlok tartalmazzák az egyes utazások díjait, a fizetés típusát, a díjszabást, a pótdíjat, az adókat, a tippeket és az autópályadíjat, valamint a teljes fizetett összeget. Az alábbiakban néhány példa rekordokat:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Az utazás\_az adat-és az\_utazáshoz való csatlakozáshoz szükséges egyedi kulcs a következő mezőkből áll: emlékérem, Hack\_License, és pickup\_DateTime. Beolvasni az összes részletes adat kapcsolódik egy adott utazást, is elegendő az, hogy ezek a kulcsok csatlakozik a szolgáltatáshoz.

## <a name="mltasks"></a>Példák az előrejelzési feladatokra
Határozza meg, hogy milyen előrejelzések alapján kívánja végrehajtani az adatelemzést a szükséges folyamat-feladatok tisztázásához. Íme három példa az útmutatóban ismertetett előrejelzési problémákra, mindezt a *tipp\_összeg*alapján:

- **Bináris besorolás**: megjósolhatja, hogy fizetett-e borravalót egy útra. Ez egy olyan *tipp\_* , amely nagyobb, mint $0 pozitív példa, míg a *tip\_mennyiség* $0 értéke negatív példa.
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0
- **Többosztályos besorolás**: az útra kifizetett tip-összegek tartományának előrejelzése. A *tipp\_összegét* öt osztályra osztjuk:
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0 and tip_amount <= $5
        Class 2: tip_amount > $5 and tip_amount <= $10
        Class 3: tip_amount > $10 and tip_amount <= $20
        Class 4: tip_amount > $20
- **Regressziós feladat**: előre megjósolható, hogy a tipp mennyi összeget fizetett ki egy útra.  

## <a name="setup"></a>HDInsight Hadoop-fürt beállítása a speciális elemzésekhez
> [!NOTE]
> Ez az általában egy rendszergazdai tevékenységhez.
> 
> 

Beállíthatja a speciális elemzésekhez, amely egy HDInsight-fürtöt három lépésben alkalmaz az Azure-környezet:

1. [Storage-fiók létrehozása](../../storage/common/storage-account-create.md): ezt a Storage-fiókot használjuk az Azure Blob Storage-ban tárolt adattároláshoz. A HDInsight-fürtök használt adatokat is itt található.
2. [Testreszabhatja Azure HDInsight Hadoop fürtöket a fejlett elemzési folyamathoz és technológiához](customize-hadoop-cluster.md). Ebben a lépésben létrehoz egy HDInsight Hadoop-fürt összes csomópontjára telepítse a 64 bites Anaconda Python 2.7. Ne feledje, a HDInsight-fürt testreszabása során két fontos lépésből áll.
   
   * Ne feledje, a storage-fiók létrehozásakor, a HDInsight-fürt az 1. lépésben létrehozott. Ehhez a tárfiókhoz fér hozzá a fürtön belül feldolgozott adatok.
   * Miután a fürt létrehozása távoli hozzáférés engedélyezése a fürt fő csomópontjának. Keresse meg a **konfiguráció** lapot, és válassza a **távoli engedélyezése**lehetőséget. Ebben a lépésben meghatározza a távoli bejelentkezéshez használt felhasználói hitelesítő adatokat.
3. [Azure Machine learning munkaterület létrehozása](../studio/create-workspace.md): ezt a munkaterületet gépi tanulási modellek létrehozásához használhatja. Ez a feladat egy kezdeti adatfeltárás befejezése és lefelé-mintavétel után képes kezelni a HDInsight-fürt használatával.

## <a name="getdata"></a>Adatok beolvasása nyilvános forrásból
> [!NOTE]
> Ez az általában egy rendszergazdai tevékenységhez.
> 
> 

Ha a [New York-i taxis](https://www.andresmh.com/nyctaxitrips/) adatkészletet a saját gépén szeretné átmásolni a gépre, használja az [adatok áthelyezése az Azure Blob Storage-ba és az-ból](move-azure-blob.md)című témakörben leírt módszerek egyikét.

Itt azt ismertetjük, hogyan vihetők át az adatokat tartalmazó fájlok AzCopy használatával. A AzCopy letöltéséhez és telepítéséhez kövesse az [első lépések a AzCopy parancssori segédprogrammal](../../storage/common/storage-use-azcopy.md)című témakör útmutatását.

1. A parancssori ablakban futtassa a következő AzCopy-parancsokat, és cserélje le a *\<path_to_data_folder >* a kívánt célra:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. A másolás befejeződése után megjelenik a kiválasztott data 24 tömörített fájlok összesen. Csomagolja ki a letöltött fájlok ugyanabban a könyvtárban, a helyi gépen. Jegyezze fel a mappa, ahol a kibontott fájlok találhatók. Ennek a mappának a neve a *\<elérési útja\_\_unzipped_data\_fájlok\>* a mi következik.

## <a name="upload"></a>Az adatok feltöltése a HDInsight Hadoop-fürt alapértelmezett tárolójába
> [!NOTE]
> Ez az általában egy rendszergazdai tevékenységhez.
> 
> 

A következő AzCopy-parancsokat, cserélje le az alábbi paramétereket a tényleges értékek, amelyek a Hadoop-fürt létrehozásakor megadott, és az adatfájlokat kicsomagolta.

* ***\<path_to_data_folder >*** A kibontott adatfájlokat tartalmazó könyvtár (az elérési úttal együtt) a gépen.  
* ***a Hadoop-fürt\<Storage-fiókjának neve >*** A HDInsight-fürthöz társított Storage-fiók.
* ***a Hadoop-fürt alapértelmezett tárolójának\<>*** A fürt által használt alapértelmezett tároló. Az alapértelmezett tároló neve általában megegyezik a fürt nevével. Például ha a fürt neve a "abc123.azurehdinsight.net", az alapértelmezett tároló abc123.
* ***\<Storage-fiók kulcsa >*** A fürt által használt Storage-fiók kulcsa.

A parancssorba vagy egy Windows PowerShell-ablakot futtassa a következő két AzCopy-parancsot.

Ezzel a paranccsal feltöltheti az utazási adatok a ***nyctaxitripraw*** könyvtárba a Hadoop-fürt alapértelmezett tárolójában.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Ezzel a paranccsal feltöltheti a díjszabási adatok a ***nyctaxifareraw*** könyvtárba a Hadoop-fürt alapértelmezett tárolójában.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Az adatok most kell lennie, a Blob storage szolgáltatással, és készen áll a HDInsight-fürt fel kell használni.

## <a name="#download-hql-files"></a>Jelentkezzen be a Hadoop-fürt fő csomópontjára, és készüljön fel a felderítő adatelemzésre
> [!NOTE]
> Ez az általában egy rendszergazdai tevékenységhez.
> 
> 

A fürt fő csomópontjának a felderítő adatelemzéshez és az adat-leállási mintavételezéshez való hozzáféréséhez kövesse az [Hadoop-fürt fő csomópontjának eléréséhez](customize-hadoop-cluster.md)című témakörben leírt eljárást.

Ebben az útmutatóban elsősorban a [kaptárban](https://hive.apache.org/), egy SQL-szerű lekérdezési nyelven írt lekérdezéseket használjuk az előzetes adatfeltárások végrehajtásához. A kaptár-lekérdezések a ". HQL" fájlban tárolódnak. Hogy majd lefelé-minta ezeket az adatokat a Machine Learning modellek készítéséhez használható.

Ha a fürtöt a felderítő adatok elemzéséhez szeretné előkészíteni, töltse le a megfelelő kaptár-parancsfájlokat tartalmazó ". HQL" fájlokat a [githubról](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) egy helyi könyvtárba (C:\Temp) a fő csomóponton. Nyissa meg a parancssort a fürt fő csomópontján belül, és futtassa a következő két parancsot:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Ez a két parancs letölti az ebben a bemutatóban szükséges összes ". HQL" fájlt a fő csomópont helyi ***C:\Temp&#92;***  .

## <a name="#hive-db-tables"></a>Struktúra-adatbázis és-táblázatok létrehozása hónap szerint particionálva
> [!NOTE]
> Ez a feladat általában rendszergazdák számára készült.
> 
> 

Most már készen áll az NYC taxi adatkészlet Hive táblák létrehozása.
A Hadoop-fürt fő csomópontjának nyissa meg a Hadoop parancssor az átjárócsomóponthoz, az asztalon. Adja meg a Hive-könyvtár a következő parancs futtatásával:

    cd %hive_home%\bin

> [!NOTE]
> Ez a forgatókönyv minden Hive parancsait futtatja a Hive bin / directory parancssort. Ez automatikusan kezeli az elérési út problémákat. A "Hive directory kérdés" kifejezéseket használjuk "Hive bin / directory prompt", és a "Hadoop parancssor" felcserélhető az ebben a bemutatóban.
> 
> 

A kaptár-könyvtár parancssorában futtassa a következő parancsot a kaptár-adatbázist és-táblákat létrehozó fő csomópont Hadoop parancssorában:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Itt látható a **C:\temp\sample\_struktúra tartalma\_a\_db\_és\_Tables. HQL fájlt hozza létre** , amely létrehozza a kaptár-adatbázis **nyctaxidb**, valamint **a táblákat és a** **viteldíjat**.

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

A Hive-szkript két táblát hoz létre:

* Az **utazási** táblázat az egyes Ride-túrák részleteit (az illesztőprogram részleteit, a kivételezési időt, az utazási távolságot és az időpontokat) tartalmazza.
* A **díjszabási** táblázat tartalmazza a viteldíjak részleteit (viteldíj összege, Tipp összege, autópályadíj és pótdíj).

Ha további segítségre van szüksége ezekkel az eljárásokkal kapcsolatban, vagy alternatívát szeretne kivizsgálni, tekintse meg a [kaptár-lekérdezések elküldése közvetlenül a Hadoop parancssorból](move-hive-tables.md#submit)című szakaszt.

## <a name="#load-data"></a>Adatgyűjtés a struktúra tábláiba partíciók szerint
> [!NOTE]
> Ez a feladat általában rendszergazdák számára készült.
> 
> 

A NYC taxi adatkészlet tartalmaz, egy természetes particionálás havi bontásban, amit használunk feldolgozásával és lekérdezéseivel gyorsabb engedélyezéséhez. Adatok betöltése az utazás az a következő PowerShell-parancsokat (a Hive-könyvtár által kiadott Hadoop parancssor segítségével), és Hive-táblákat, hónapok szerint particionált díjszabás.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

A **minta\_struktúrában\_\_a\_\_Partitions. HQL** -fájl a következő **betöltési** parancsokat tartalmazza:

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

A feltárási folyamat során használt kaptár-lekérdezések száma csak egy vagy két partíciót érint. Azonban ezeket a lekérdezéseket futtathatja a teljes adatkészleten.

### <a name="#show-db"></a>Adatbázisok megjelenítése a HDInsight Hadoop-fürtben
A létrehozott HDInsight Hadoop-fürtön belül a Hadoop parancssori ablakban adatbázisok megjelenítéséhez futtassa a következő parancsot a Hadoop parancssor:

    hive -e "show databases;"

### <a name="#show-tables"></a>A **nyctaxidb** -adatbázisban lévő kaptár táblák megjelenítése
A **nyctaxidb** -adatbázisban lévő táblák megjelenítéséhez futtassa a következő parancsot a Hadoop parancssorban:

    hive -e "show tables in nyctaxidb;"

Azt is ellenőrizze, hogy a táblák a következő parancs futtatásával particionáltak:

    hive -e "show partitions nyctaxidb.trip;"

Íme a várt kimenet:

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

Hasonlóképpen azt is győződjön meg arról, hogy a diszkont tábla particionálva van-e a következő parancs futtatásával:

    hive -e "show partitions nyctaxidb.fare;"

Íme a várt kimenet:

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

## <a name="#explore-hive"></a>Adatfelderítési és-funkciós mérnöki struktúra
> [!NOTE]
> Ez általában az adatok adatszakértő feladat.
> 
> 

Hive-lekérdezések segítségével adatáttekintés és a Hive-táblákat betöltött adatok feladatok mérnöki funkció végrehajtásához. Az alábbiakban példákat az ilyen feladatok:

* A két tábla első 10 rekordok megtekintése.
* Fedezze fel az adatokat a különböző időtartományok néhány mezőt disztribúciók.
* Vizsgálja meg a szélességi és hosszúsági mezők adatok minőségét.
* Hozzon létre bináris és többosztályos osztályozási címkék tipp alapul.
* Hozzon létre a szolgáltatások úgy számítástechnika a közvetlen trip távolságot.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Feltárás: Tábla, utazás az első 10 rekordok megtekintése
> [!NOTE]
> Ez általában az adatok adatszakértő feladat.
> 
> 

Jelenik meg, hogy az adatok néz ki, vizsgálja meg az egyes táblákból 10 rekordot. A rekordok vizsgálata, futtassa a következő két lekérdezéseket külön-külön az a Hadoop parancssori konzolt, a Hive könyvtár használatával.

Az első 10 rekord lekérése a trip tábla első:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Az első 10 rekord lekérése a diszkont tábla első:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Mentheti a rekordokat egy fájlba, hogy az előző lekérdezés kis módosításával kényelmesen megtekinthető legyen:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Feltárás: Rekordok száma megtekintheti az egyes partíciók 12
> [!NOTE]
> Ez általában az adatok adatszakértő feladat.
> 
> 

A lényeges hogyan változtak az lelassítja a száma a naptári év alatt van. Hónap szerint csoportosítási lelassítja elosztását mutatja.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

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
    Time taken: 283.406 seconds, Fetched: 12 row(s)

Itt az első oszlop a hónap, pedig a második lelassítja a száma az adott hónapban.

Hogy is számíthatunk rekordok teljes számát az útadatokat adatkészletben található a Hive könyvtárban a parancssorba a következő parancs futtatásával:

    hive -e "select count(*) from nyctaxidb.trip;"

Ez a parancs a következőket eredményezi:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Látható az útadatokat adatkészlet hasonló parancs használatával is ad a Hive-lekérdezéseket a Hive-könyvtár használatával diszkont adatkészlet érvényesítése rekordok száma.

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

A havonta megegyező számú utazást mindkét adatkészletnél visszaadja a rendszer, így biztosítva az első érvényesítést, hogy az adatokat helyesen töltötték be.

A Hive-könyvtár használatával a következő paranccsal megszámlálható diszkont adatkészlet rekordok teljes száma:

    hive -e "select count(*) from nyctaxidb.fare;"

Ez a parancs a következőket eredményezi:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

A két táblában található rekordok teljes száma szintén azonos, és egy második érvényesítést biztosít az adatok megfelelő betöltéséhez.

### <a name="exploration-trip-distribution-by-medallion"></a>Feltárás: Utazás eloszlás medallion szerint
> [!NOTE]
> Ez az elemzés általában egy adattudós-feladat.
> 
> 

Ebben a példában a medallions (-i taxik számokat) nagyobb, mint 100 lelassítja egy adott időtartamon belül azonosít. A lekérdezés a particionált tábla-hozzáférés előnyeit élvezheti, mert a partíció változó **hónapja**feltétele. A lekérdezési eredményeket egy helyi fájlba ( **queryoutput. TSV)** írja a rendszer, `C:\temp` a fő csomóponton.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Itt látható a **minta\_struktúra\_az utazás\_a\_\_medalion. HQL** -fájl alapján.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

A medallion NYC taxi adatkészlet egy egyedi cab azonosítja. Melyik kabinetfájlok viszonylag foglalt feltevésével, melyeket egy adott időszakban végrehajtott több mint egy bizonyos számú lelassítja azonosíthatja. Az alábbi példa azokat a megfelelőségértékelő-ket azonosítja, amelyek az első három hónapban több mint száz utazást hajtottak végre, és a lekérdezési eredményeket egy helyi fájlba, a **C:\temp\queryoutput.TSV**menti.

Itt látható a **minta\_struktúra\_az utazás\_a\_\_medalion. HQL** -fájl alapján.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

A Hive-könyvtár használatával futtassa a következő parancsot:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Feltárás: Utazás terjesztési medallion és feltörés licenc
> [!NOTE]
> Ez a feladat általában egy adattudós számára készült.
> 
> 

Az adathalmazok feltárásakor gyakran szeretnénk megvizsgálni az értékek csoportjai eloszlását. Ez a szakasz bemutatja, hogyan végezheti el ezt az elemzést a vezetőfülkék és az illesztőprogramok számára.

A **minta\_struktúra\_trip\_\_\_medalion\_License. HQL** file groups a díjszabási adatkészletet a **medál** és a **hack_license**esetében, és az egyes kombinációk számát adja vissza. Az alábbiakban a tartalmát:

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Ez a lekérdezés a cab-fájl és az illesztőprogram kombinációit, csökkenő utak száma szerint rendezve adja vissza.

A Hive-könyvtár használatával futtassa:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

A lekérdezés eredményei egy helyi fájlba íródnak, a **C:\temp\queryoutput.TSV**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Feltárás: Adatminőség érvénytelen hosszúsági és szélességi rekordok ellenőrzésével felméréséhez.
> [!NOTE]
> Ez általában az adatok adatszakértő feladat.
> 
> 

Egy közös feltáró jellegű adatok elemzése célja, hogy ki érvénytelen vagy helytelen rekordot gyomláláskor. A jelen szakaszban ismertetett példa határozza meg, hogy tartalmaznak-e szélesség vagy hosszúság mezők egy értéket, amennyiben a NYC területen kívül. Valószínű, hogy ilyen rekord van-e egy hibás hosszúsági szélességérték, mert szeretnénk kiküszöbölésének kívánja használni a modellezési adatot elemezhet.

Itt látható a **minta\_struktúra\_minőségi\_Assessment. HQL** -fájl a vizsgálathoz.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


A Hive-könyvtár használatával futtassa:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

A parancsban szereplő *-S* argumentum letiltja a struktúra-hozzárendelési Térkép/feladatok csökkentése állapot képernyőjét. Ez a parancs azért hasznos, mert a kaptár-lekérdezés kimenete olvashatóbb formában jelenik meg.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Feltárás: Bináris osztály disztribúciók trip tippek
> [!NOTE]
> Ez általában az adatok adatszakértő feladat.
> 
> 

Az [előrejelzési feladatok](hive-walkthrough.md#mltasks) című szakaszban ismertetett bináris besorolási probléma esetén hasznos lehet megállapítani, hogy van-e megadva tipp. Ehhez a terjesztéshez tippeket bináris:

* Tipp megadva (1. osztály, Tipp\_mennyiség > $0)  
* nincs tipp (0. osztály, Tipp\_összeg = $0)

A következő **minta\_struktúra\_kimutatott\_frekvenciákon. a HQL** -fájl a futtatandó parancsot mutatja:

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

A Hive-könyvtár használatával futtassa:

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Feltárás: Osztály disztribúciók multiclass beállításban
> [!NOTE]
> Ez általában az adatok adatszakértő feladat.
> 
> 

Az [előrejelzési feladatok](hive-walkthrough.md#mltasks) című szakaszban ismertetett többosztályos besorolási probléma esetén ez az adatkészlet természetes besorolást is biztosít, hogy előre megjósolja a megadott tippek mennyiségét. Dobozok használatával tipp tartományok megadása a lekérdezésben. A különböző tip-tartományok osztály-eloszlásának beszerzéséhez használja a **minta\_struktúra\_tipp\_tartomány\_frekvenciákon. HQL** fájlt. Az alábbiakban a tartalmát.

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

Futtassa a következő parancsot a Hadoop parancssori konzolból:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Feltárás: Számítási közvetlen hosszúság-szélesség végre két hely közötti távolság
> [!NOTE]
> Ez általában az adatok adatszakértő feladat.
> 
> 

Előfordulhat, hogy meg szeretné ismerni, hogy van-e a közvetlen távolság között két helyen, és a tényleges trip távolság a taxi különbség. Előfordulhat, hogy utas kevésbé valószínű, hogy ha, döntse el, hogy az illesztőprogram szándékosan vette azokat egy hosszabb útvonal tipp.

A tényleges utazási távolság és a [Haversine közötti távolság](https://en.wikipedia.org/wiki/Haversine_formula) (a "nagy kör" távolság) közötti különbség megtekintéséhez használhatja a kaptáron belül elérhető trigonometriai függvényeket:

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

Az előző lekérdezés az R a föld mérföldre lévő százalékában, és pi radians alakítja át. A földrajzi hosszúság – szélesség pontok úgy vannak szűrve, hogy eltávolítsanak a New York-i régiótól távol lévő értékeket.

Ebben az esetben az eredményeket egy **queryoutputdir**nevű könyvtárba írjuk. A következő parancsok sorozatát először létrehozza a kimeneti könyvtárba, és a Hive parancsot futtatja, majd.

A Hive-könyvtár használatával futtassa:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


A lekérdezés eredményét kilenc Azure-blobra (**queryoutputdir/000000 kódot\_0** – **queryoutputdir/000008\_0**), a Hadoop-fürt alapértelmezett tárolójában kell írni.

Az egyes blobok méretének megtekintéséhez futtassa a következő parancsot a Hive-könyvtár használatával:

    hdfs dfs -ls wasb:///queryoutputdir

Ha egy adott fájl tartalmát szeretné megtekinteni, mondjuk a **000000 kódot\_0-ra**, használja a Hadoop `copyToLocal` parancsát.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> a `copyToLocal` nagyon lassú lehet a nagyméretű fájlok esetében, és nem ajánlott velük használni.  
> 
> 

Ennek az adatnak az egyik fő előnye az, hogy az [adatimportálási][import-data] modul használatával megvizsgáljuk Machine Learningon belüli adataikat.

## <a name="#downsample"></a>Leállt – mintaadatok és modellek készítése Machine Learning
> [!NOTE]
> Ez általában az adatok adatszakértő feladat.
> 
> 

Az adatokat feltáró jellegű elemzési fázis után azt most már készen áll le-minta az adatokat a Machine Learning modellek készítéséhez. Ebben a szakaszban bemutatjuk, hogyan használható a Hive-lekérdezések való az adatok. Machine Learning ezután az [adatok importálása][import-data] modulból fér hozzá.

### <a name="down-sampling-the-data"></a>Adatok lefelé-mintavétel
Ebben az eljárásban két lépésből áll. Először csatlakoztassa a **nyctaxidb. Trip** és a **nyctaxidb. fare** táblát az összes rekordban található három kulcshoz: **emlékérem**, **Hack\_License**és **pickup\_datetime**. Ezután a bináris besorolási címkét, **a**kijelzett és a többosztályos besorolási címkét, a **Tipp\_osztályt**állítjuk elő.

Ahhoz, hogy közvetlenül a Machine Learning [adatimportálási][import-data] modulból lehessen használni a lefelé vett mintákat, az előző lekérdezés eredményét egy belső struktúra-táblába kell menteni. Az alábbiak hozunk létre egy belső Hive-táblába, és annak tartalmát a tartományhoz, és lefelé mintavételezés adatokkal feltölteni.

A lekérdezés a standard kaptár függvényeit közvetlenül alkalmazza a következő időparaméterek létrehozásához a **pickup\_datetime (dátum** /idő) mezőjéből:
- nap órája
- év hete
- a hétköznap (1) a hétfő, a "7" pedig a vasárnapot jelenti.

A lekérdezés is hoz létre a közvetlen a felvétel és dropoff helyek közötti távolság. Az ilyen függvények teljes listáját lásd: [LANGUAGEMANUAL UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

A lekérdezés majd lefelé-minták az adatokat, hogy a lekérdezés eredményeit az Azure Machine Learning studióba illeszkednek. Az eredeti adathalmazból csak körülbelül 1 százalékát a rendszer importálja a studióba.

Itt látható a **minta\_struktúrájának tartalma\_felkészülés\_\_pénzmosás\_teljes. HQL** -fájlra, amely előkészíti az adatmodell-létrehozási Machine learning:

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

Ez a lekérdezés futtatása a Hive-könyvtár használatával:

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

Most már van egy belső tábla, a **nyctaxidb. nyctaxi_downsampled_dataset**, amely a Machine learning [adatok importálása][import-data] moduljának használatával érhető el. Továbbá a Machine Learning-modellek létrehozása ehhez az adatkészlethez is használjuk.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>A Machine Learning, a lefelé mintavételezés adatok eléréséhez használja az adatok importálása modullal
A Machine Learning adatimportálási moduljában a kaptár-lekérdezések [kiküldéséhez][import-data] hozzáféréssel kell rendelkeznie egy Machine learning-munkaterülethez. Emellett a fürt és a társított storage-fiók hitelesítő adatait a hozzáférést.

Íme néhány információ az [adatimportálási][import-data] modulról és a bemeneti paraméterekről:

**HCatalog-kiszolgáló URI-ja**: Ha a fürt neve **abc123**, használja a következőket: https://abc123.azurehdinsight.net.

**Hadoop felhasználói fiók neve**: a fürthöz választott Felhasználónév (nem a távelérés felhasználóneve).

**Hadoop felhasználói fiók jelszava**: a fürthöz választott jelszó (nem a távelérési jelszó).

**Kimeneti adatokat tároló hely**: az Azure-ra lett kiválasztva.

**Azure Storage-fiók neve**: a fürthöz társított alapértelmezett Storage-fiók neve.

**Azure-tároló neve**: a fürt alapértelmezett tárolójának neve, és általában ugyanaz, mint a fürt neve. A **abc123**nevű fürt esetében a név a következő: abc123.

> [!IMPORTANT]
> Minden olyan tábla, amelyet a Machine Learning [adatimportálási][import-data] moduljának használatával szeretne lekérdezni, belső táblának kell lennie.
> 
> 

Itt megtudhatja, hogyan állapíthatja **meg, hogy** a **D. db** adatbázis egy belső tábla-e. A Hive-könyvtár használatával futtassa a következő parancsot:

    hdfs dfs -ls wasb:///D.db/T

Ha a táblázat egy belső táblázatban, és fel van töltve, a tartalmát itt kell megjelennie.

Ellenőrizze, hogy a táblázat egy belső tábla másik módja, használhatja az Azure Storage Explorer. Használatával keresse meg a fürt alapértelmezett tároló nevére, és szűrjön rá a tábla neve. Ha a táblázat és annak tartalma jelenik meg, Ez megerősíti, hogy egy belső táblázatban.

Itt látható a kaptár-lekérdezés és az [adatimportálási][import-data] modul képernyőképe:

![Képernyőkép a Hive-lekérdezés az adatok importálása modullal](./media/hive-walkthrough/1eTYf52.png)

Mivel a rendszer az alapértelmezett tárolóban tárolja a levett mintavételen alapuló adatforrásokat, a Machine Learningből származó kaptár-lekérdezés egyszerű. Ez csak egy **select * from nyctaxidb. nyctaxi\_downsampled\_adatok**.

Az adatkészlet a kiindulási pontként most már használható a Machine Learning-modellek készítéséhez.

### <a name="mlmodel"></a>Modellek készítése Machine Learning
Most már folytathatja a [Machine learning](https://studio.azureml.net)-ben való üzembe helyezési és modell-telepítési modell kialakítását. Az adatokat, ahhoz, hogy a korábban azonosított előrejelzési problémák megoldásához használja:

- **Bináris besorolás**: megjósolhatja, hogy fizetett-e borravalót egy útra.

  **Használt tanuló:** Kétosztályos logisztikai regresszió

  a. Ehhez a problémához a cél (vagy osztály) **felirat van**kijelölve. Az eredeti lefelé mintavételezés adatkészlet tartalmaz néhány olyan oszlopot, amelyek ehhez a kísérlethez besorolási cél adatszivárgás. A **tip\_osztály**, a **Tipp\_a mennyiség**és a **teljes\_összeg** felfedi a tesztelési idő alatt nem elérhető cél címkével kapcsolatos információkat. Ezeket az oszlopokat az [adatkészletek kiválasztása][select-columns] modulban az oszlopok kijelölése elem használatával távolítjuk el.

  Az alábbi ábrán látható-e egy adott út tipp kifizetett megjósolni a kísérlet:

  ![Előre jelezni, ha tipp fizették kísérlet diagram](./media/hive-walkthrough/QGxRz5A.png)

  b. Ehhez a kísérlethez a cél címke disztribúciók is körülbelül 1:1.

   Az alábbi ábra a bináris osztályozási probléma osztály címkék tipp elosztását mutatja:

  ![Tipp osztály címkék terjesztési diagramja](./media/hive-walkthrough/9mM4jlD.png)

    Ennek eredményeképpen célpontot 0.987, a görbe (AUC) alatti terület az alábbi ábrán látható módon:

  ![Diagram AUC érték](./media/hive-walkthrough/8JDT0F8.png)

- **Többosztályos besorolás**: a korábban definiált osztályok használatával előre megjósolhatja az utazáshoz kifizetett borravalók tartományát.

  **Használt tanuló:** Többosztályos logisztikai regresszió

  a. Ennél a problémánál a cél (vagy osztály) címkéje a **tipp\_osztály**, amely 5 érték (0, 1, 2, 3, 4) egyikét veheti fel. Bináris osztályozás gazdabuszadaptereken van néhány olyan oszlopot, amelyek a cél adatszivárgás ehhez a kísérlethez. Különösen **a kitűzött,** a **Tipp\_a mennyiség**és a **teljes\_összeg** a tesztelési idő alatt nem elérhető cél címkével kapcsolatos információkat jeleníti meg. Ezeket az oszlopokat az [adatkészlet kijelölése oszlopban található oszlopok][select-columns] használatával távolítjuk el.

  Az alábbi ábrán látható a kísérlet előre jelezni, melyik van tipp valószínű, hogy tartoznak. A bins vannak: osztály 0: tipp = 0, 1. osztályú: > $0 és tipp tipp < = $5, 2. osztályú: > $5 és tipp tipp < = 10 $ osztály 3: > $ 10-es és tipp tipp < $20 és osztály 4 =: > $20 tipp.

  ![Tipp a bin előrejelzésére kísérlet diagram](./media/hive-walkthrough/5ztv0n0.png)

  Most megmutatjuk, a tényleges vizsgálati osztály terjesztési néz ki. Osztály 0 és 1. osztályú elterjedt, és az egyéb osztályok ritkák.

  ![Teszt osztály terjesztési diagramja](./media/hive-walkthrough/Vy1FUKa.png)

  b. Ebben a kísérletben egy zavart mátrixot használunk az előrejelzési pontosság az itt látható módon:

  ![Keveredési mátrix](./media/hive-walkthrough/cxFmErM.png)

  Míg a pontosság osztály jó, a modell nem végez jó munkát a ritkább osztályokon a "learning".

- **Regressziós feladat**: az utazáshoz fizetett tipp mennyiségének előrejelzése.

  **Használt tanuló:** Megnövelt döntési fa

  a. Ehhez a problémához a cél (vagy osztály) címkéje a **tipp\_mennyisége**. A cél szivárgások ebben az esetben a következők **:** kitűzött, **Tipp\_osztály**, valamint a **teljes\_mennyiség**. Ezek a változók tipp mennyiség általában nem érhető el, a tesztelési idő a kapcsolatos információk felfedése. Ezeket az oszlopokat az [adatkészlet kijelölése oszlopban található oszlopok][select-columns] használatával távolítjuk el.

  Az alábbi ábrán látható a kísérlet előre jelezni az adott tipp mennyiségét:

  ![Tipp – összeg előrejelzésére kísérlet diagram](./media/hive-walkthrough/11TZWgV.png)

  b. Regresszió kapcsolatos problémák esetén a az előrejelzési pontosság megnézzük a squared hiba az előrejelzéseket, valamint a determinációs együttható mérjük:

  ![Előrejelzés statisztikai képernyőképe](./media/hive-walkthrough/Jat9mrz.png)

  Itt a determinációs együttható 0.709, ami azt jelenti, hogy az eltérés körülbelül 71 százaléka van magyarázza a modell együttható.

> [!IMPORTANT]
> Ha többet szeretne megtudni a Machine Learningről, valamint arról, hogyan érheti el és használhatja azt, tekintse [meg a mi a Machine learning](../studio/what-is-machine-learning.md). Emellett a [Azure AI Gallery](https://gallery.cortanaintelligence.com/) kísérletek széles skáláját fedi le, és alapos bevezetést biztosít a Machine learning képességeinek körébe.
> 
> 

## <a name="license-information"></a>Licencinformációk
Ez a minta forgatókönyv és a hozzájuk tartozó szkriptek által megosztott Microsoft alatt az MIT-licenccel. További információ: **License. txt** fájl a githubon található mintakód könyvtárában.

## <a name="references"></a>Referencia
• [Andrés MONROY NYC taxi TRIPS letöltési oldal](https://www.andresmh.com/nyctaxitrips/)  
• [A New York-i taxis utazási adatvédelme Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• A [New York-i taxi és a limuzin Bizottság kutatási és statisztikai adatai](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/




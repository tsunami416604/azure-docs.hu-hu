---
title: Egy Hadoop-fürt – a csoportos adatelemzési folyamat az adatok megismerése
description: A csoportos adatelemzési folyamat használatával egy végpontok közötti forgatókönyv, alkalmazó egy HDInsight Hadoop-fürtöt hozhat létre és helyezhet üzembe modelleket.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a0b4c41d347ba556e737d422af22fb5a47c7c4ff
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108721"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>A csoportos adatelemzési folyamat működés közben: Az Azure HDInsight Hadoop-fürtök használata
Ez az útmutató használjuk a [csoportos adatelemzési folyamat (TDSP)](overview.md) egy teljes körű forgatókönyvben. Használjuk egy [Azure HDInsight Hadoop-fürt](https://azure.microsoft.com/services/hdinsight/) tárolását, ismerje meg, és a nyilvánosan elérhető a szolgáltatás-mérnök adatainak [NYC Taxi lelassítja](http://www.andresmh.com/nyctaxitrips/) adatkészlet, és való az adatokat. Bináris és többosztályos besorolási és regressziós prediktív feladatok kezelésére, hogy ki, hogy az adatok az Azure Machine Learning modellek. 

Ez az útmutató bemutatja, hogyan legyen kezelve a nagyobb adatkészletet, lásd: [csoportos adatelemzési folyamat – az Azure HDInsight Hadoop-fürtök az 1 TB-os adatkészlet](hive-criteo-walkthrough.md).

Az IPython notebook használatával a forgatókönyv, amely használja az 1 TB-os adatkészlet megjelenik a feladatok elvégzését. További információkért lásd: [Hive ODBC-kapcsolattal Criteo forgatókönyv](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="dataset"></a>NYC Taxi lelassítja adatkészlet leírása
A NYC Taxi útadatok körülbelül 20 GB tömörített vesszővel elválasztott értékeket (CSV) fájl (~ 48 GB tömörítetlen). 173 milliónál egyes lelassítja rendelkezik, és a fizetett egyes utazás a díjakat tartalmazza. Minden egyes út rekord tartalmazza a felvétel és dropoff hely és idő, anonimizált feltörés (illesztőprogramok) licencszám és medallion number (egyedi azonosító a taxi). Az adatok terjed ki minden lelassítja az év 2013-hoz, és minden hónapban megtalálható a következő két adatkészletet:

- A trip_data CSV-fájlok trip részleteket tartalmaz. Ez magában foglalja az utasok, felvétel és dropoff pontok, út időtartama és út hossza száma. Az alábbiakban néhány példa rekordokat:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
- Trip_fare CSV-fájlok az egyes út fizetett diszkont részleteit tartalmazza. Ez magában foglalja a fizetési típus, diszkont összeg, pótdíj és adók, tippek és útdíjak, és a fizetett teljes összeget. Az alábbiakban néhány példa rekordokat:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Utazás csatlakozni egyedi kulcsa\_adatokat és utazás\_diszkont tevődik össze a mezők: medallion céltudatos\_licenc, és felvétel\_datetime. Beolvasni az összes részletes adat kapcsolódik egy adott utazást, is elegendő az, hogy ezek a kulcsok csatlakozik a szolgáltatáshoz.

## <a name="mltasks"></a>Példák az előrejelzés
Határozza meg, és az előrejelzések azt szeretné, hogy milyen adatok elemzése alapján. Ez segít tisztázni a feladatokat, meg kell adni a folyamat. Az alábbiakban három előrejelzési problémákat, hogy oldja meg a forgatókönyv példája. Ezek alapján a *tipp\_összeg*:

- **Bináris osztályozás**: Előrejelezheti e tipp fizették útnak. Azt jelenti egy *tipp\_összeg* nagyobb több, mint 0 USD pozitív példa, miközben egy *tipp\_összeg* negatív példa az 0 USD.
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0
- **Többosztályos osztályozási**: Előre fizetett az utazás a tip összegek tartományán. Hogy osztani a *tipp\_összeg* öt osztályokba:
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0 and tip_amount <= $5
        Class 2: tip_amount > $5 and tip_amount <= $10
        Class 3: tip_amount > $10 and tip_amount <= $20
        Class 4: tip_amount > $20
- **Regresszió feladat**: Előrejelezheti a tipp egy utazást fizetett mennyisége.  

## <a name="setup"></a>Fejlett analitikai egy HDInsight Hadoop-fürt beállítása
> [!NOTE]
> Ez az általában egy rendszergazdai tevékenységhez.
> 
> 

Beállíthatja a speciális elemzésekhez, amely egy HDInsight-fürtöt három lépésben alkalmaz az Azure-környezet:

1. [Hozzon létre egy tárfiókot](../../storage/common/storage-quickstart-create-account.md): Ezt a tárfiókot az Azure Blob storage-adatok tárolására szolgál. A HDInsight-fürtök használt adatokat is itt található.
2. [Az Azure HDInsight Hadoop-fürtök testreszabása a fejlett analitikai folyamat és technológia](customize-hadoop-cluster.md). Ebben a lépésben létrehoz egy HDInsight Hadoop-fürt összes csomópontjára telepítse a 64 bites Anaconda Python 2.7. Ne feledje, a HDInsight-fürt testreszabása során két fontos lépésből áll.
   
   * Ne feledje, a storage-fiók létrehozásakor, a HDInsight-fürt az 1. lépésben létrehozott. Ehhez a tárfiókhoz fér hozzá a fürtön belül feldolgozott adatok.
   * Miután a fürt létrehozása távoli hozzáférés engedélyezése a fürt fő csomópontjának. Keresse meg a **konfigurációs** lapot, majd **távoli engedélyezése**. Ebben a lépésben meghatározza a távoli bejelentkezéshez használt felhasználói hitelesítő adatokat.
3. [Az Azure Machine Learning-munkaterület létrehozása](../studio/create-workspace.md): Ez a munkaterület használatával hozhat létre a machine learning-modellek. Ez a feladat egy kezdeti adatfeltárás befejezése és lefelé-mintavétel után képes kezelni a HDInsight-fürt használatával.

## <a name="getdata"></a>Beolvassa az adatokat nyilvános forráskódú
> [!NOTE]
> Ez az általában egy rendszergazdai tevékenységhez.
> 
> 

Másolása a [NYC Taxi lelassítja](http://www.andresmh.com/nyctaxitrips/) adatkészletet, a gép a nyilvános helyéről ismertetett módszerek bármelyikét használhatja [adatok áthelyezése Azure Blob storage szolgáltatásba vagy onnan](move-azure-blob.md).

Itt azt ismertetjük, hogyan vihetők át az adatokat tartalmazó fájlok AzCopy használatával. Töltse le és telepítse az AzCopy, utasításai [Bevezetés az AzCopy parancssori segédprogram](../../storage/common/storage-use-azcopy.md).

1. Egy parancssori ablakban futtassa a következő AzCopy-parancsokat, és cserélje le *< path_to_data_folder >* a kívánt cél:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. A másolás befejeződése után megjelenik a kiválasztott data 24 tömörített fájlok összesen. Csomagolja ki a letöltött fájlok ugyanabban a könyvtárban, a helyi gépen. Jegyezze fel a mappa, ahol a kibontott fájlok találhatók. Ez a mappa a neve a *< elérési út\_való\_unzipped_data\_fájlok\>*  a következőkben.

## <a name="upload"></a>Az adatok feltöltése a a HDInsight Hadoop-fürt alapértelmezett tárolója
> [!NOTE]
> Ez az általában egy rendszergazdai tevékenységhez.
> 
> 

A következő AzCopy-parancsokat, cserélje le az alábbi paramétereket a tényleges értékek, amelyek a Hadoop-fürt létrehozásakor megadott, és az adatfájlokat kicsomagolta.

* ***< Path_to_data_folder >*** a könyvtár (együtt az elérési utat) a gépen, amely a kicsomagolt adatokat fájlokat tartalmazza.  
* ***<storage account name of Hadoop cluster>*** A HDInsight-fürthöz társított storage-fiók.
* ***<default container of Hadoop cluster>*** Az a fürt által használt alapértelmezett tároló. Vegye figyelembe, hogy az alapértelmezett tároló neve általában magának a fürtnek azonos néven. Például ha a fürt neve a "abc123.azurehdinsight.net", az alapértelmezett tároló abc123.
* ***<storage account key>*** A fürt által használt tárfiók kulcsa.

A parancssorba vagy egy Windows PowerShell-ablakot futtassa a következő két AzCopy-parancsot.

Ez a parancs feltölti az utazási adatokon, a ***nyctaxitripraw*** könyvtárat a a a Hadoop-fürt alapértelmezett tárolója.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Ez a parancs feltölti az diszkont adatokat, hogy a ***nyctaxifareraw*** könyvtárat a a a Hadoop-fürt alapértelmezett tárolója.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Az adatok most kell lennie, a Blob storage szolgáltatással, és készen áll a HDInsight-fürt fel kell használni.

## <a name="#download-hql-files"></a>Jelentkezzen be a Hadoop-fürt fő csomópontjának és feltáró jellegű adatok elemzése előkészítése
> [!NOTE]
> Ez az általában egy rendszergazdai tevékenységhez.
> 
> 

Feltáró jellegű adatok elemzése a fürt fő csomópontjának és lefelé-mintavétel az adatok eléréséhez kövesse az ismertetett eljárást [Hadoop-fürt fő csomópontjának eléréséhez](customize-hadoop-cluster.md).

Ez az útmutató elsősorban használjuk nyelven írt lekérdezések [Hive](https://hive.apache.org/), egy SQL-szerű lekérdezési nyelvet, és hajtsa végre az adatok előzetes explorations. A Hive-lekérdezések .hql fájlokban van tárolva. Hogy majd lefelé-minta ezeket az adatokat a Machine Learning modellek készítéséhez használható.

A fürt az feltáró jellegű adatok elemzésére előkészítéseként töltse le a .hql fájlokat, a megfelelő Hive parancsfájlokat tartalmazó [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) egy helyi könyvtárába (C:\temp) a fő csomópontot. Ehhez nyissa meg a parancssort a fürt fő csomópontjának belül, és futtassa az alábbi két parancsot:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Ez a két parancs ebben az útmutatóban a helyi könyvtárba szükséges összes .hql fájlok letöltésére ***C:\temp&#92;***  a a fő csomópontot.

## <a name="#hive-db-tables"></a>Hive-adatbázis és a hónap szerint particionált táblák létrehozása
> [!NOTE]
> Ez az általában egy rendszergazdai tevékenységhez.
> 
> 

Most már készen áll az NYC taxi adatkészlet Hive táblák létrehozása.
A Hadoop-fürt fő csomópontjának nyissa meg a Hadoop parancssor az átjárócsomóponthoz, az asztalon. Adja meg a Hive-könyvtár a következő parancs futtatásával:

    cd %hive_home%\bin

> [!NOTE]
> Ez a forgatókönyv minden Hive parancsait futtatja a Hive bin / directory parancssort. Ez automatikusan kezeli az elérési út problémákat. A "Hive directory kérdés" kifejezéseket használjuk "Hive bin / directory prompt", és a "Hadoop parancssor" felcserélhető az ebben a bemutatóban.
> 
> 

A Hive-könyvtár használatával futtassa a következő parancsot a fő csomópontot, a Hadoop parancssorban. Ezzel elküldi a Hive-adatbázis és tábla létrehozása Hive-lekérdezést:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

A következő tartalmától a **C:\temp\sample\_hive\_létrehozása\_db\_és\_tables.hql** fájlt. Ez létrehozza a Hive-adatbázis **nyctaxidb**, és a táblák **út** és **diszkont**.

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

* A **út** tábla minden egyes indításáról (illesztőprogram adatai, felvétel időpontja, trip távolság és időpontok) trip részleteit tartalmazza.
* A **diszkont** tábla (diszkont összeg, tipp összeg, útdíjak és pótdíjak) diszkont részleteit tartalmazza.

Ha ezek az eljárások bármilyen további segítségre van szüksége, vagy újakat alternatív vizsgálni kívánt, tekintse meg a szakasz [küldje el a Hive-lekérdezések a Hadoop parancssor közvetlenül a](move-hive-tables.md#submit).

## <a name="#load-data"></a>Adatok betöltése a Hive-táblák partíció
> [!NOTE]
> Ez az általában egy rendszergazdai tevékenységhez.
> 
> 

A NYC taxi adatkészlet tartalmaz, egy természetes particionálás havi bontásban, amit használunk feldolgozásával és lekérdezéseivel gyorsabb engedélyezéséhez. Adatok betöltése az utazás az a következő PowerShell-parancsokat (a Hive-könyvtár által kiadott Hadoop parancssor segítségével), és Hive-táblákat, hónapok szerint particionált díjszabás.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

A **minta\_hive\_betöltése\_adatok\_által\_partitions.hql** fájl tartalmazza a következő **betöltése** parancsokat:

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Vegye figyelembe, hogy egy feltárási folyamata az itt használt Hive-lekérdezések száma is magában foglalja, csak egy vagy két partíció található. Azonban ezeket a lekérdezéseket futtathatja a teljes adatkészleten.

### <a name="#show-db"></a>Adatbázisok jelennek meg a HDInsight Hadoop-fürt
A létrehozott HDInsight Hadoop-fürtön belül a Hadoop parancssori ablakban adatbázisok megjelenítéséhez futtassa a következő parancsot a Hadoop parancssor:

    hive -e "show databases;"

### <a name="#show-tables"></a>A Hive-táblákat megjelenítése a **nyctaxidb** adatbázis
A táblázatok megjelenítéséhez a **nyctaxidb** adatbázis, a Hadoop-parancssorban a következő parancsot:

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

## <a name="#explore-hive"></a>Az adatok feltárása és a Hive funkciófejlesztési feladatok
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

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Feltárás: A tábla, utazás az első 10 rekordok megtekintése
> [!NOTE]
> Ez általában az adatok adatszakértő feladat.
> 
> 

Jelenik meg, hogy az adatok néz ki, vizsgálja meg az egyes táblákból 10 rekordot. A rekordok vizsgálata, futtassa a következő két lekérdezéseket külön-külön az a Hadoop parancssori konzolt, a Hive könyvtár használatával.

Az első 10 rekord lekérése a trip tábla első:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Az első 10 rekord lekérése a diszkont tábla első:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

A rekordok egy kényelmes összetevőjeként fájlt mentheti. Az előző lekérdezés, módosítsa ezt a feladatot el:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Feltárás: A nézet az egyes 12 partícióra rekordok száma
> [!NOTE]
> Ez általában az adatok adatszakértő feladat.
> 
> 

A lényeges hogyan változtak az lelassítja a száma a naptári év alatt van. Hónap szerint csoportosítási lelassítja elosztását mutatja.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Ez olyan biztosít, amely a következő kimenet:

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

Ez eredményez:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Látható az útadatokat adatkészlet hasonló parancs használatával is ad a Hive-lekérdezéseket a Hive-könyvtár használatával diszkont adatkészlet érvényesítése rekordok száma.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Ez olyan biztosít, amely a következő kimenet:

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

Vegye figyelembe, hogy pontosan azonos számú lelassítja havonként visszaadott mindkét olyan adatkészlettel. Ez lehetővé teszi, hogy az adatok megfelelően betölteni az első ellenőrzést.

A Hive-könyvtár használatával a következő paranccsal megszámlálható diszkont adatkészlet rekordok teljes száma:

    hive -e "select count(*) from nyctaxidb.fare;"

Ez eredményez:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

A két tábla rekordok száma is megegyezik. Ez lehetővé teszi, hogy az adatok megfelelően betölteni egy második ellenőrzési.

### <a name="exploration-trip-distribution-by-medallion"></a>Feltárás: Utazás eloszlás medallion szerint
> [!NOTE]
> Ez általában az adatok adatszakértő feladat.
> 
> 

Ebben a példában a medallions (-i taxik számokat) nagyobb, mint 100 lelassítja egy adott időtartamon belül azonosít. A lekérdezés számos előnyt biztosít az a particionált tábla hozzáférést, mert megfelel a partíció változó **hónap**. A lekérdezés eredményeit egy helyi fájlba íródnak **queryoutput.tsv**, a `C:\temp` a központi csomóponton.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

A következő tartalmától a **minta\_hive\_út\_száma\_által\_medallion.hql** fájlt a vizsgálathoz.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

A medallion NYC taxi adatkészlet egy egyedi cab azonosítja. Melyik kabinetfájlok viszonylag foglalt feltevésével, melyeket egy adott időszakban végrehajtott több mint egy bizonyos számú lelassítja azonosíthatja. Az alábbi példa azonosítja, hogy a több mint száz lelassítja az első három hónap, és a lekérdezés eredménye egy helyi fájlba menti a kabinetfájlok **C:\temp\queryoutput.tsv**.

A következő tartalmától a **minta\_hive\_út\_száma\_által\_medallion.hql** fájlt a vizsgálathoz.

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
> Ez általában az adatok adatszakértő feladat.
> 
> 

Ha az adatkészlet feltárása, gyakran szeretnénk vizsgálja meg az értékek csoportok társ előfordulások száma. Ez a szakasz azt szemlélteti, hogyan teheti ezt a kabinetfájlok és illesztőprogramok.

A **minta\_hive\_út\_száma\_által\_medallion\_license.hql** fájl diszkont adatkészlet csoportjait a **medallion** és **hack_license**, és egyes kombinációk számát adja vissza. Az alábbiakban a tartalmát:

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Ez a lekérdezés a cab-fájl és az illesztőprogram kombinációit, csökkenő utak száma szerint rendezve adja vissza.

A Hive-könyvtár használatával futtassa:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

A lekérdezés eredményeit egy helyi fájlba íródnak **C:\temp\queryoutput.tsv**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Feltárás: Érvénytelen hosszúsági és szélességi rekordok ellenőrzésével adatminőség felmérése
> [!NOTE]
> Ez általában az adatok adatszakértő feladat.
> 
> 

Egy közös feltáró jellegű adatok elemzése célja, hogy ki érvénytelen vagy helytelen rekordot gyomláláskor. A jelen szakaszban ismertetett példa határozza meg, hogy tartalmaznak-e szélesség vagy hosszúság mezők egy értéket, amennyiben a NYC területen kívül. Valószínű, hogy ilyen rekord van-e egy hibás hosszúsági szélességérték, mert szeretnénk kiküszöbölésének kívánja használni a modellezési adatot elemezhet.

A következő tartalmától **minta\_hive\_minőségi\_assessment.hql** fájlt a vizsgálathoz.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


A Hive-könyvtár használatával futtassa:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

A *-S* argumentumot, ez a parancs tartalmazza a Hive-Map/Reduce-feladatok a állapota képernyő nyomtatott letiltja. Ez akkor hasznos, mert azt a képernyőn, a Hive-lekérdezés kimenete a nyomtatási olvashatóbbá teszi.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Feltárás: Bináris osztály disztribúciók trip tippek
> [!NOTE]
> Ez általában az adatok adatszakértő feladat.
> 
> 

A bináris osztályozási probléma leírt a [példák az előrejelzési](hive-walkthrough.md#mltasks) szakaszban hasznos lehet tudni, hogy e tipp hozták-e vagy sem. Ehhez a terjesztéshez tippeket bináris:

* adott TIP (1. osztályú, tipp\_összeget > 0 USD)  
* Nincs tip (0 osztály, tipp\_összeg = 0 USD)

A következő **minta\_hive\_Formabontó\_frequencies.hql** fájl ezt hajtja végre:

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

A leírt többosztályos osztályozási probléma a [példák az előrejelzési](hive-walkthrough.md#mltasks) szakaszban ez az adatkészlet is adatmodelljeinek előre jelezni az adott tippek mennyisége természetes besorolást. Dobozok használatával tipp tartományok megadása a lekérdezésben. Az osztály disztribúciók esetében a különböző tartományok tipp lekéréséhez használja a **minta\_hive\_tipp\_tartomány\_frequencies.hql** fájlt. Az alábbiakban a tartalmát.

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

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Feltárás: COMPUTE közvetlen hosszúság-szélesség végre két hely közötti távolság
> [!NOTE]
> Ez általában az adatok adatszakértő feladat.
> 
> 

Előfordulhat, hogy meg szeretné ismerni, hogy van-e a közvetlen távolság között két helyen, és a tényleges trip távolság a taxi különbség. Előfordulhat, hogy utas kevésbé valószínű, hogy ha, döntse el, hogy az illesztőprogram szándékosan vette azokat egy hosszabb útvonal tipp.

Tényleges trip távolság összehasonlítása megtekintéséhez és a [Haversine távolság](http://en.wikipedia.org/wiki/Haversine_formula) két hosszúság-szélesség pontja (a "nagy kör" távolság), használhatja a rendelkezésre álló trigonometriai függvények Hive belül:

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

Az előző lekérdezés az R a föld mérföldre lévő százalékában, és pi radians alakítja át. Vegye figyelembe, hogy a hosszúság-szélesség pontok eltávolítja az értéket, amely messze a NYC területen vannak szűrve.

Ebben az esetben az eredmények írható nevű könyvtárba **queryoutputdir**. A következő parancsok sorozatát először létrehozza a kimeneti könyvtárba, és a Hive parancsot futtatja, majd.

A Hive-könyvtár használatával futtassa:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


A lekérdezés eredményeinek írt kilenc Azure-blobok (**queryoutputdir/000000\_0** való **queryoutputdir/000008\_0**), az alapértelmezett tároló, a Hadoop-fürt alatt.

Az egyes blobok méretének megtekintéséhez futtassa a következő parancsot a Hive-könyvtár használatával:

    hdfs dfs -ls wasb:///queryoutputdir

Tegyük fel, egy adott fájl tartalmának megtekintéséhez **000000\_0**, használhatja a Hadoop `copyToLocal` parancsot.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> `copyToLocal` nagy méretű fájlok esetén nagyon lassú lehet, és nem ajánlott használni őket.  
> 
> 

A fő előnyt, hogy ezeket az adatokat egy Azure-blobban találhatók, hogy is tárgyaljuk Machine Learning, az adatok használatával a [adatok importálása] [ import-data] modul.

## <a name="#downsample"></a>Adatok lefelé-minta és a gépi tanulási modelleket
> [!NOTE]
> Ez általában az adatok adatszakértő feladat.
> 
> 

Az adatokat feltáró jellegű elemzési fázis után azt most már készen áll le-minta az adatokat a Machine Learning modellek készítéséhez. Ebben a szakaszban bemutatjuk, hogyan használható a Hive-lekérdezések való az adatok. A Machine Learning majd fér hozzá a [adatok importálása] [ import-data] modul.

### <a name="down-sampling-the-data"></a>Adatok lefelé-mintavétel
Ebben az eljárásban két lépésből áll. Először csatlakozzon a Microsoft a **nyctaxidb.trip** és **nyctaxidb.fare** táblákat, amelyek szerepelnek az összes rekordot három kulcsokon: **medallion**, **hack\_ licenc**, és **begyűjtés\_datetime**. Hogy előállít egy bináris osztályozási címke **Formabontó**, és a egy többosztályos osztályozási címke **tipp\_osztály**.

Kell tudni használni a lefelé mintavételezett adatok közvetlenül a [adatok importálása] [ import-data] modul a Machine Learninghez tárolja egy belső Hive-táblába az előző lekérdezés eredményeit. Az alábbiak hozunk létre egy belső Hive-táblába, és annak tartalmát a tartományhoz, és lefelé mintavételezés adatokkal feltölteni.

A lekérdezés érvényes szabványos Hive funkciók közvetlenül az alábbi létrehozásához a **begyűjtés\_datetime** mező:
- nap órája
- év hete
- hét napja (hétfő 1 összhangban és 7 összhangban vasárnap)

A lekérdezés is hoz létre a közvetlen a felvétel és dropoff helyek közötti távolság. Az ilyen függvények teljes listáját lásd: [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

A lekérdezés majd lefelé-minták az adatokat, hogy a lekérdezés eredményeit az Azure Machine Learning studióba illeszkednek. Az eredeti adathalmazból csak körülbelül 1 százalékát a rendszer importálja a studióba.

Az alábbiakban a tartalmát **minta\_hive\_előkészítése\_a\_aml\_full.hql** fájlt, amely előkészíti az adatokat a modell létrehozásához a Machine Learning szolgáltatásban:

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

Most már van egy belső tábla **nyctaxidb.nyctaxi_downsampled_dataset**, használatával is elérhetők a [adatok importálása] [ import-data] a gépi tanulás modul. Továbbá a Machine Learning-modellek létrehozása ehhez az adatkészlethez is használjuk.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>A Machine Learning, a lefelé mintavételezés adatok eléréséhez használja az adatok importálása modullal
A Hive-lekérdezések kiadására a [adatok importálása] [ import-data] a gépi tanulás modul, hozzá kell férnie egy Machine Learning-munkaterület. Emellett a fürt és a társított storage-fiók hitelesítő adatait a hozzáférést.

Az alábbiakban néhány részletek kapcsolatban a [adatok importálása] [ import-data] modul és a bemeneti paramétereket:

**HCatalog kiszolgáló URI**: Ha a fürt neve **abc123**, ez egyszerűen a: https://abc123.azurehdinsight.net.

**Hadoop-felhasználói fiók nevét**: A felhasználónév, a fürt (nem a távelérési felhasználónév) választott.

**Hadoop ser fiók jelszava**: A fürt (nem a távelérési jelszó) választott jelszó.

**Kimeneti adatok helyének**: Ezt akkor kell kiválasztani, Azure lennie.

**Az Azure storage-fiók neve**: A fürthöz társított alapértelmezett tárfiók neve.

**Az Azure container name**: Ez a fürt alapértelmezett tároló neve, és általában ugyanaz, mint a fürt nevét. Fürt nevű **abc123**, ez az abc123.

> [!IMPORTANT]
> Minden tábla kívánjuk osztani a lekérdezés használatával a [adatok importálása] [ import-data] a Machine Learning modul egy belső táblázatban kell lennie.
> 
> 

Íme, miként állapítható meg, ha egy tábla **T** adatbázisban **D.db** egy belső tábla. A Hive-könyvtár használatával futtassa a következő parancsot:

    hdfs dfs -ls wasb:///D.db/T

Ha a táblázat egy belső táblázatban, és fel van töltve, a tartalmát itt kell megjelennie.

Ellenőrizze, hogy a táblázat egy belső tábla másik módja, használhatja az Azure Storage Explorer. Használatával keresse meg a fürt alapértelmezett tároló nevére, és szűrjön rá a tábla neve. Ha a táblázat és annak tartalma jelenik meg, Ez megerősíti, hogy egy belső táblázatban.

Íme egy Képernyőkép a Hive-lekérdezés és a [adatok importálása] [ import-data] modul:

![Képernyőkép a Hive-lekérdezés az adatok importálása modullal](./media/hive-walkthrough/1eTYf52.png)

Mivel a lefelé mintát venni az adatok az alapértelmezett tárolóban található, az eredményül kapott Hive-lekérdezést a gépi tanulás, nagyon egyszerű. Ez csak egy **kiválasztása * a nyctaxidb.nyctaxi\_felbontáscsökkentésének\_adatok**.

Az adatkészlet a kiindulási pontként most már használható a Machine Learning-modellek készítéséhez.

### <a name="mlmodel"></a>A Machine Learning modellek készítése
Most már folytathatja a modell létrehozásának és a modell üzembe helyezése [Machine Learning](https://studio.azureml.net). Az adatokat, ahhoz, hogy a korábban azonosított előrejelzési problémák megoldásához használja:

- **Bináris osztályozás**: Előrejelzési e tipp fizették útnak.

  **Learner használt:** Kétosztályos logisztikai regresszió

  a. A probléma, a cél (vagy osztály) címke van **Formabontó**. Az eredeti lefelé mintavételezés adatkészlet tartalmaz néhány olyan oszlopot, amelyek ehhez a kísérlethez besorolási cél adatszivárgás. Különösen **tipp\_osztály**, **tipp\_összeg**, és **teljes\_összeg** fedik fel információkat a célként megadott címkével nem áll rendelkezésre a tesztelési idő. Hogy eltávolítása ezeket az oszlopokat veszi figyelembe a [Select Columns in Dataset] [ select-columns] modul.

  Az alábbi ábrán látható-e egy adott út tipp kifizetett megjósolni a kísérlet:

  ![Előre jelezni, ha tipp fizették kísérlet diagram](./media/hive-walkthrough/QGxRz5A.png)

  b. Ehhez a kísérlethez a cél címke disztribúciók is körülbelül 1:1.

   Az alábbi ábra a bináris osztályozási probléma osztály címkék tipp elosztását mutatja:

  ![Tipp osztály címkék terjesztési diagramja](./media/hive-walkthrough/9mM4jlD.png)

    Ennek eredményeképpen célpontot 0.987, a görbe (AUC) alatti terület az alábbi ábrán látható módon:

  ![Diagram AUC érték](./media/hive-walkthrough/8JDT0F8.png)

- **Többosztályos osztályozási**: Tipp összegek számos előre fizetett az utazás a korábban definiált osztályok használatával.

  **Learner használt:** Multiclass logisztikai regresszió

  a. A probléma, a cél (vagy osztály) címke van **tipp\_osztály**, tarthat (0,1,2,3,4) öt érték valamelyikét. Bináris osztályozás gazdabuszadaptereken van néhány olyan oszlopot, amelyek a cél adatszivárgás ehhez a kísérlethez. Különösen **Formabontó**, **tipp\_összeg**, és **teljes\_összeg** a cél-címke, amely nem érhető el információ felfedése tesztelési idő. Ezekben az oszlopokban használatával eltávolítjuk a [Select Columns in Dataset] [ select-columns] modul.

  Az alábbi ábrán látható a kísérlet előre jelezni, melyik van tipp valószínű, hogy tartoznak. A bins a következők: 0. osztály: tipp = 0, 1. osztályú: > $0 és tipp tipp < = $5, 2. osztályú: > $5 és tipp tipp < = 10 $ osztály 3: > $ 10-es és tipp tipp < $20 és osztály 4 =: > $20 tipp.

  ![Tipp a bin előrejelzésére kísérlet diagram](./media/hive-walkthrough/5ztv0n0.png)

  Most megmutatjuk, a tényleges vizsgálati osztály terjesztési néz ki. Osztály 0 és 1. osztályú elterjedt, és az egyéb osztályok ritkák.

  ![Teszt osztály terjesztési diagramja](./media/hive-walkthrough/Vy1FUKa.png)

  b. Ehhez a kísérlethez a keveredési mátrix és tekintse meg az előrejelzési pontosság használjuk. Ez itt látható:

  ![Keveredési mátrix](./media/hive-walkthrough/cxFmErM.png)

  Vegye figyelembe, hogy elég jó a osztály pontosságú az elterjedt osztályokat, amelyek a modell nem végzik jó "tanulás" az egyes osztályokra.

- **Regresszió feladat**: Előre fizetett belépőt a tip mennyisége.

  **Learner használt:** gyorsított döntési fa

  a. A probléma, a cél (vagy osztály) címke van **tipp\_összeg**. Ebben az esetben vannak a cél adatszivárgás: **Formabontó**, **tipp\_osztály**, és **teljes\_összeg**. Ezek a változók tipp mennyiség általában nem érhető el, a tesztelési idő a kapcsolatos információk felfedése. Ezekben az oszlopokban használatával eltávolítjuk a [Select Columns in Dataset] [ select-columns] modul.

  Az alábbi ábrán látható a kísérlet előre jelezni az adott tipp mennyiségét:

  ![Tipp – összeg előrejelzésére kísérlet diagram](./media/hive-walkthrough/11TZWgV.png)

  b. Regresszió kapcsolatos problémák esetén a az előrejelzési pontosság megnézzük a squared hiba az előrejelzéseket, valamint a determinációs együttható mérjük:

  ![Előrejelzés statisztikai képernyőképe](./media/hive-walkthrough/Jat9mrz.png)

  Itt a determinációs együttható 0.709, ami azt jelenti, hogy az eltérés körülbelül 71 százaléka van magyarázza a modell együttható.

> [!IMPORTANT]
> Machine Learning és elérése és a vele kapcsolatos további tudnivalókért lásd: [Mi a Machine Learning](../studio/what-is-machine-learning.md). Emellett a [Azure AI-katalógusban](https://gallery.cortanaintelligence.com/) kísérletek színtartomány ismerteti, és alapos bemutatása, a számos Machine Learning biztosít.
> 
> 

## <a name="license-information"></a>Licencinformációk
Ez a minta forgatókönyv és a hozzájuk tartozó szkriptek által megosztott Microsoft alatt az MIT-licenccel. További részletekért tekintse meg a **LICENSE.txt** fájl a könyvtárban mintakódot a Githubon.

## <a name="references"></a>Referencia
• [Andrés Monroy NYC Taxi lelassítja letöltési oldala](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC Útadatok taxiköltség Chris Whong szerint](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi és Limousine Bizottság kutatási és a statisztika](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/




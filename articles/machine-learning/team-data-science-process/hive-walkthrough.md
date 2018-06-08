---
title: Hadoop-fürthöz az adatokba, és az Azure Machine Learning modellek létrehozása |} Microsoft Docs
description: Egy végpontok közötti alkalmazó egy HDInsight Hadoop-fürt létrehozása és a modell rendszerbe állítása a forgatókönyvben az Team tudományos folyamat használja.
services: machine-learning,hdinsight
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: e9e76c91-d0f6-483d-bae7-2d3157b86aa0
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: deguhath
ms.openlocfilehash: beb1c2f88eed8fc38bd32de113835122cfd4e9a4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837327"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>A művelet az Team tudományos folyamat: használata Azure HDInsight Hadoop-fürtök
Ebben a bemutatóban használjuk a [Team adatok tudományos folyamat (TDSP)](overview.md) egy végpont forgatókönyvben. Használjuk egy [Azure HDInsight Hadoop-fürt](https://azure.microsoft.com/services/hdinsight/) tárolását, vizsgálatát, és a szolgáltatás-visszafejtés adatok a nyilvánosan elérhető [NYC Taxi való adatváltások számát](http://www.andresmh.com/nyctaxitrips/) adatkészletet, és lefelé-minta az adatokat. A multiclass és bináris osztályozás és regressziós prediktív feladatok, azt az adatok az Azure Machine Learning modellek létrehozása. 

Ez a forgatókönyv bemutatja, hogyan kezeli a nagyobb adatkészletet, lásd: [Team adatok tudományos folyamat - használata Azure HDInsight Hadoop-fürtök az 1 TB-os dataset](hive-criteo-walkthrough.md).

Egy IPython notebook használatával jelenik meg a forgatókönyv az 1 TB-os adatkészletet használó feladatok elvégzését. További információkért lásd: [Criteo forgatókönyv Hive ODBC-kapcsolat használatával](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="dataset"></a>NYC Taxi Utazgatással adatkészlet leírása
A NYC Taxi út adata körülbelül 20 GB tömörített vesszővel tagolt (CSV) fájl (tömörítetlen ~ 48 GB). Ez több mint 173 millió egyedi való adatváltások számát, és a minden út kifizette vitel is tartalmaz. Minden út rekord tartalmazza a felvétel és Gyűjtőtár hely és idő, anonimizált rejthetők el (illesztőprogram) licencszám és medallion száma (a taxi egyedi azonosítója). Az adatok minden való adatváltások számát ismerteti az év 2013, és minden hónap a következő két adatkészletet találhatók:

- A trip_data CSV-fájlok út részleteit tartalmazza. Ez magában foglalja az utasok, felvétel és Gyűjtőtár pontok, út időtartama és út hossza száma. Íme néhány példa rekordok:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
- A trip_fare CSV-fájlok a jegy minden út kifizette ára részleteit tartalmazza. Ebbe beletartoznak a fizetési mód, jegy ára összeg, emelt díjas és adók, tippeket és autópályadíjak, és a teljes összeg fizetett. Íme néhány példa rekordok:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Egyedi kulcs út csatlakozni\_adatok és út\_jegy ára tevődnek össze a mezők: medallion, rejthetők el\_licenc, és a felvételi\_dátum és idő. Ahhoz, hogy egy adott út kapcsolódik az összes részletet, is használhatók, ezek a kulcsok csatlakoztatni.

## <a name="mltasks"></a>Előrejelzés feladatok példák
Határozza meg, hogy szeretné előrejelzéseket típusú adatok elemzése alapján. Ezzel a megoldással egyértelművé vált a feladatokat, meg kell adnia a folyamat. Itt példák három ebben a bemutatóban oldjuk előrejelzés problémákat. Ezek alapján a *tipp\_összeg*:

- **Bináris osztályozási**: előre jelezni, függetlenül attól, tipp egy út kifizetett. Ez azt jelenti, hogy egy *tipp\_összeg* nagyobb, mint 0 egy pozitív példában látható, miközben egy *tipp\_összeg* $ 0 egy negatív példában látható.
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0
- **Multiclass besorolási**: út kifizette tipp összegek számos előre jelezni. Azt a osztani a *tipp\_összeg* öt osztályokba:
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0 and tip_amount <= $5
        Class 2: tip_amount > $5 and tip_amount <= $10
        Class 3: tip_amount > $10 and tip_amount <= $20
        Class 4: tip_amount > $20
- **Regressziós feladat**: megjósolható a fizetős útnak tipp mennyisége.  

## <a name="setup"></a>Állítson be egy HDInsight Hadoop-fürt speciális elemzésekre
> [!NOTE]
> Ez a művelet rendszerint egy felügyeleti feladatot.
> 
> 

Speciális elemzésekre három lépést a HDInsight-fürtök által környezetet az Azure állíthat be:

1. [Hozzon létre egy tárfiókot](../../storage/common/storage-create-storage-account.md): ezt a tárfiókot az Azure Blob storage adatok tárolására szolgál. A HDInsight-fürtök adatait is itt található.
2. [A speciális elemzés folyamat és a technológia Azure HDInsight Hadoop-fürtök testreszabása](customize-hadoop-cluster.md). Ebben a lépésben egy HDInsight Hadoop-fürt összes csomópontján telepítve 64 bites Anaconda Python 2.7 hoz létre. Jegyezze meg a HDInsight-fürt testreszabása során két fontos lépésből áll.
   
   * Ne felejtse el a storage-fiók létrehozása az 1. lépésben a HDInsight-fürthöz létrehozásakor hivatkozásra. Ez a tárfiók a fürtön belül feldolgozott adatokat éri el.
   * A fürt létrehozása után engedélyezze a távelérést az átjárócsomóponthoz, a fürt. Keresse meg a **konfigurációs** lapot, és jelölje be **távoli engedélyezése**. Ebben a lépésben adja meg a távoli bejelentkezéshez használt felhasználói hitelesítő adatokat.
3. [Hozzon létre egy Azure Machine Learning munkaterülettel](../studio/create-workspace.md): a munkaterület machine learning modellek létrehozásához használhat. Ez a feladat címzettjei után egy kezdeti adatfeltárás befejezése és a mintavételi le, a HDInsight-fürt használatával.

## <a name="getdata"></a>Beolvassa az adatokat nyilvános forráskódú
> [!NOTE]
> Ez a művelet rendszerint egy felügyeleti feladatot.
> 
> 

Másolja a [NYC Taxi Utazgatással](http://www.andresmh.com/nyctaxitrips/) nyilvános helyéről, a gép adatkészlet használja ismertetett módszerek valamelyikét [helyezze át az adatokat, és az Azure Blob Storage tárolóban](move-azure-blob.md).

Itt azt ismerteti, hogyan adatokat tartalmazó fájlok átvitele az AzCopy segítségével. Töltse le és telepítse az AzCopy, kövesse az utasításokat, [Bevezetés az AzCopy parancssori segédprogram](../../storage/common/storage-use-azcopy.md).

1. Az egy parancssori ablakot, a következő parancsokat AzCopy, cseréje *< path_to_data_folder >* a kívánt cél:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. A Másolás befejezése után megjelenik a kiválasztott data 24 tömörített fájlok összesen. Bontsa ki a helyi számítógépen a letöltött fájlok ugyanabba a könyvtárba. Jegyezze fel a kibontott fájlokat tároló mappa. Ez a mappa neve a *< elérési út\_való\_unzipped_data\_fájlok\>*  a következőkben.

## <a name="upload"></a>Az adatok feltöltése a alapértelmezett tárolót a HDInsight Hadoop-fürt
> [!NOTE]
> Ez a művelet rendszerint egy felügyeleti feladatot.
> 
> 

A következő AzCopy parancsok, cserélje le a következő paraméterek a tényleges, a Hadoop-fürt létrehozásakor megadott értékeket, és az adatfájlokat kicsomagolás.

* ***< Path_to_data_folder >*** a könyvtár (és az elérési utat) a számítógépen, amely a tömörítetlen adatfájlokat tartalmaz.  
* ***<storage account name of Hadoop cluster>*** A tárfiók a HDInsight-fürthöz társított.
* ***<default container of Hadoop cluster>*** Az alapértelmezett tároló, a fürt által használt. Vegye figyelembe, hogy az alapértelmezett tároló neve általában a neve megegyezik a fürthöz. Például ha a fürt neve a "abc123.azurehdinsight.net", az alapértelmezett tároló abc123.
* ***<storage account key>*** A fürt által használt tárfiók kulcsa.

A parancssorba vagy egy Windows PowerShell-ablakot futtassa a következő két AzCopy parancs.

Ez a parancs az út adatokat tölt az ***nyctaxitripraw*** könyvtárhoz, az alapértelmezett tárolóban, a Hadoop-fürt.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Ez a parancs a jegy ára adatokat tölt az ***nyctaxifareraw*** könyvtárhoz, az alapértelmezett tárolóban, a Hadoop-fürt.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Az adatok kell a Blob Storage tárolóban, és készen áll a HDInsight-fürtön belül használható.

## <a name="#download-hql-files"></a>Hadoop-fürt átjárócsomópontjához bejelentkezni, és készítse elő a felderítő adatelemzés
> [!NOTE]
> Ez a művelet rendszerint egy felügyeleti feladatot.
> 
> 

A felderítő adatelemzéshez fürt átjárócsomópontjához és lefelé-mintavétel az adatok megnyitásához hajtsa végre a ezt az eljárást [hozzáférni a Hadoop-fürt átjárócsomópontjához](customize-hadoop-cluster.md).

Ez a forgatókönyv elsősorban használjuk írt lekérdezések [Hive](https://hive.apache.org/), egy SQL-szerű lekérdező nyelv, előzetes adatok explorations végrehajtásához. A Hive-lekérdezések .hql fájlok tárolják. A Microsoft majd lefelé-minta ezeket az adatokat a Machine Learning modellek készítéséhez használható.

Készítse elő a fürt felderítő adatelemzéshez, töltse le a .hql fájlokat a megfelelő Hive parancsfájlokat tartalmazó [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) az átjárócsomópont egy helyi könyvtárába (C:\temp). Ehhez nyissa meg a parancssort a fürt átjárócsomópontjához belül, és futtassa az alábbi két parancsot:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

A két parancsok ebben a bemutatóban a helyi könyvtárba szükséges összes .hql fájlok letöltésére ***C:\temp&#92;***  központi csomópontjában.

## <a name="#hive-db-tables"></a>Hive-adatbázis és hónaponként particionált táblák létrehozása
> [!NOTE]
> Ez a művelet rendszerint egy felügyeleti feladatot.
> 
> 

Most már készen áll a következőt: taxi adatkészlet Hive táblák létrehozása.
A Hadoop-fürt átjárócsomópontjához nyissa meg a Hadoop parancssor az átjárócsomópont az asztalon. Adja meg a Hive könyvtárat a következő parancs futtatásával:

    cd %hive_home%\bin

> [!NOTE]
> Ez a forgatókönyv minden Hive parancsot futtatja a Hive bin / directory kérdés. Ez automatikusan kezeli az elérési út probléma merül fel. A "Struktúra directory prompt", kifejezéseket használjuk a "struktúra bin / directory prompt", és a "Hadoop parancssori" azonos értelemben ebben a bemutatóban.
> 
> 

A Hive directory parancssorból futtassa a következő parancsot a átjárócsomópontjához Hadoop parancssorban. Ez elküldi a Hive-lekérdezést a Hive-adatbázis és a táblák létrehozásához:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Ez a tartalom a **C:\temp\sample\_hive\_létrehozása\_db\_és\_tables.hql** fájlt. Ez a struktúra adatbázist hoz létre **nyctaxidb**, és a tábla **út** és **jegy ára**.

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

A Hive parancsfájl hoz létre két tábla:

* A **út** tábla minden egyes menethelyzetben (illesztőprogram adatai, felvétel időpontja, út távolság és időpontok) út részleteit tartalmazza.
* A **jegy ára** tábla (jegy ára összeg, tipp összeg, autópályadíjak és pótdíjak) jegy ára részleteit tartalmazza.

Ha ezekkel az eljárásokkal további segítségre van szüksége, vagy alternatív ők vizsgálni kívánt, című részében [elküldeni a Hive lekérdezések közvetlenül a Hadoop parancssorból](move-hive-tables.md#submit).

## <a name="#load-data"></a>Adatok betöltése a Hive táblákat partíció
> [!NOTE]
> Ez a művelet rendszerint egy felügyeleti feladatot.
> 
> 

A következőt: taxi dataset adatkészletben, természetes particionálás havonta, amelyek feldolgozásával és lekérdezéseivel gyorsabb engedélyezése használatára. A következő PowerShell-parancsokat (a Hive-könyvtár által kiadott Hadoop parancssor segítségével) út adatokat betölteni, és díjszabás Hive táblák, havonta particionálva.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

A **minta\_hive\_betölteni\_adatok\_által\_partitions.hql** fájl tartalmazza a következő **betöltése** parancsokat:

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Vegye figyelembe, hogy a Hive-lekérdezéseket a feltárási folyamatban itt használt számos tartalmaz, amely csak egy vagy két partíció megnézi. De ezeket a lekérdezéseket is futtatható között a teljes adatkészletet.

### <a name="#show-db"></a>A HDInsight Hadoop-fürt adatbázisok megjelenítése
A HDInsight Hadoop-fürt a Hadoop parancssori ablakban belül létrehozott adatbázisokat megjelenítéséhez futtassa a következő parancsot a Hadoop parancssorban:

    hive -e "show databases;"

### <a name="#show-tables"></a>A Hive táblák megjelenítése a **nyctaxidb** adatbázis
A táblázatok megjelenítése a **nyctaxidb** adatbázis, a Hadoop parancssorban futtassa a következő parancsot:

    hive -e "show tables in nyctaxidb;"

Azt is meggyőződhet arról, hogy a táblázatok particionáltak-e a következő parancs futtatásával:

    hive -e "show partitions nyctaxidb.trip;"

Ez várható kimenete:

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

Hasonlóképpen azt is győződjön meg arról, hogy a jegy ára tábla particionálva van-e a következő parancs futtatásával:

    hive -e "show partitions nyctaxidb.fare;"

Ez várható kimenete:

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

## <a name="#explore-hive"></a>Az adatok feltárása és Hive mérnöki szolgáltatás
> [!NOTE]
> Ez általában az adatok tudósok feladat.
> 
> 

Hive-lekérdezések segítségével az adatok feltárása és a szolgáltatás műszaki osztály az adatokat a Hive táblák betölti a feladatok elvégzéséhez. Az alábbiakban példákat az ilyen feladatok:

* Az első 10 rekordok megtekintése a két tábla.
* Megismerkedhet a különböző idő windows néhány mezőinek disztribúciók adatok.
* Vizsgálja meg a szélességi és hosszúsági mezők adatok minőségét.
* Tipp mennyisége alapján multiclass és bináris besorolási címkék létrehozása.
* Szolgáltatások elő a közvetlen út távolság számítástechnikai.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Feltárása: A tábla út első 10 adatainak megtekintése
> [!NOTE]
> Ez általában az adatok tudósok feladat.
> 
> 

Tekintse meg az adatok néz, vizsgálja meg az egyes 10 rögzíti. Vizsgálja meg a rekordokat, futtassa a következő két lekérdezések külön-külön a Hadoop parancssori konzol Hive directory parancssorból.

Az első 10 rögzíti a út tábla lekérése az első olyan hónap:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Az első 10 rögzíti a jegy ára tábla lekérése az első olyan hónap:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

A rekordok kényelmes megtekintésre fájlba mentheti. Az előző lekérdezés kis módosítja ezt a feladatot el:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Feltárása: A rekordok száma tekintse meg minden olyan 12 partíció
> [!NOTE]
> Ez általában az adatok tudósok feladat.
> 
> 

Az érdeklődési hogyan utak száma függ a naptári év során. Havonta csoportosítási utazgatással eloszlását mutatja.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Ezen a következő velünk a következő az alábbiakat:

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

Itt, az első oszlopban a hónap és a második pedig utak száma az adott hónapban.

Azt is is száma rekordok teljes száma az út adathalmaz a Hive könyvtár parancssorába a következő parancs futtatásával:

    hive -e "select count(*) from nyctaxidb.trip;"

Ez eredményez:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Hasonló út adatkészlet látható parancsok azt kiadhatnak Hive-lekérdezéseket a Hive directory parancssorból a jegy ára DataSet ellenőrzése rekordok száma.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Ezen a következő velünk a következő az alábbiakat:

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

Vegye figyelembe, hogy havonta utazgatással pontos azonos számú mindkét adatkészletek ad vissza. Ez lehetővé teszi, hogy az adatok megfelelően betölteni az első ellenőrzést.

A jegy ára adatkészlet-rekordok teljes száma lépésköz a Hive directory parancssorból a következő parancsot:

    hive -e "select count(*) from nyctaxidb.fare;"

Ez eredményez:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

A két tábla rekordok száma is megegyezik. Ez lehetővé teszi, hogy az adatok megfelelően betölteni a második ellenőrzési.

### <a name="exploration-trip-distribution-by-medallion"></a>Feltárása: Út elosztása medallion szerint
> [!NOTE]
> Ez általában az adatok tudósok feladat.
> 
> 

Ebben a példában a medallions (taxi számok) nagyobb, mint 100 való adatváltások számát egy adott időtartamon belül azonosítja. A lekérdezés a particionált tábla hozzáférés előnyökkel jár, mert ez a partíció változóban van annak **hónap**. A lekérdezés eredményei egy helyi fájlba íródnak **queryoutput.tsv**, a `C:\temp` a head csomóponton.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Ez a tartalom a **minta\_hive\_út\_száma\_által\_medallion.hql** fájl a vizsgálathoz.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

A medallion NYC taxi adatkészlet egy egyedi cab azonosítja. Azonosíthatja, hogy mely kabinetfájlok viszonylag foglalt azzal, hogy melyik végzett több mint egy bizonyos számú való adatváltások számát egy adott időszakban. Az alábbi példa azonosítja az első három, és a lekérdezés eredményeinek egy helyi fájlba menti egynél több száz helymódosításra utazgatással végrehajtott kabinetfájlok **C:\temp\queryoutput.tsv**.

Ez a tartalom a **minta\_hive\_út\_száma\_által\_medallion.hql** fájl a vizsgálathoz.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

A Hive directory parancssorból futtassa a következő parancsot:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Feltárása: Út terjesztési medallion és rejthetők el licenc által
> [!NOTE]
> Ez általában az adatok tudósok feladat.
> 
> 

Ha dataset felfedezése, gyakran szeretnénk vizsgálja meg a csoportok értékek co előfordulásainak száma. Ez a témakör egy példát erre a kabinetfájlok és illesztőprogramokat.

A **minta\_hive\_út\_száma\_által\_medallion\_license.hql** fájl a jegy ára dataset csoportjait a **medallion** és **hack_license**, és az egyes kombinációk számát adja vissza. Az alábbiakban tartalma:

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

A lekérdezés által visszaadott cab-illesztőprogram kombinációkat, csökkenő utak száma alapján rendezve.

A Hive directory parancssorból futtassa:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

A lekérdezés eredményei egy helyi fájlba íródnak **C:\temp\queryoutput.tsv**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Feltárása: Értékeli az adatminőségi által az érvénytelen hosszúsági és szélességi rekordok ellenőrzése
> [!NOTE]
> Ez általában az adatok tudósok feladat.
> 
> 

A közös felderítő adatelemzés célja érvénytelen vagy helytelen rekordot kimenő gyomláláskor. A jelen szakaszban ismertetett példa meghatározza, hogy a hosszúság vagy a szélesség mezők tartalmaznak-e egy értéket, amennyiben a következőt: területen kívül. Valószínű, hogy az ilyen bejegyzések egy hibás hosszúság-szélesség értékekkel rendelkeznek, mivel azt szeretnénk kiküszöbölheti a található bármely modellezési használt adatokat.

Ez a tartalom **minta\_hive\_minőségi\_assessment.hql** fájl a vizsgálathoz.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


A Hive directory parancssorból futtassa:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

A *-S* argumentum, a parancsban benne mellőzi a állapota képernyőn nyomtatás Hive térkép vagy csökkentse a feladatot. Ez akkor hasznos, mert így a képernyőn a Hive lekérdezés kimeneti nyomtatási olvashatóbb.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Feltárása: Út tippek bináris osztály disztribúciók
> [!NOTE]
> Ez általában az adatok tudósok feladat.
> 
> 

A bináris osztályozási problémához leírt a [előrejelzés feladatok példái](hive-walkthrough.md#mltasks) szakaszban érdemes tisztában lenni, hogy egy rövid adott-e. A terjesztés tippek az bináris:

* Tipp megadott (osztály 1, tipp\_összeg > $0)  
* nincs ötlet (osztály: 0, tipp\_összeg = 0)

A következő **minta\_hive\_Formabontó\_frequencies.hql** fájl ezt végzi:

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

A Hive directory parancssorból futtassa:

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Feltárása: A multiclass beállításban osztály disztribúciók
> [!NOTE]
> Ez általában az adatok tudósok feladat.
> 
> 

A témakörben ismertetett multiclass besorolás probléma a [előrejelzés feladatok példái](hive-walkthrough.md#mltasks) szakaszban, ez az adatkészlet is adatmodelljeinek előre megadott tippek mennyisége természetes besorolást. Használjuk bins tipp tartományok megadása a lekérdezésben. Ahhoz, hogy az osztály azokat a terjesztéseket, a különböző tartományok tipp, használja a **minta\_hive\_tipp\_tartomány\_frequencies.hql** fájlt. Az alábbiakban annak tartalmát.

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

Futtassa a következő parancsot a parancssori Hadoop-konzolon:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Feltárása: Számítási közvetlen hosszúság-szélesség két hely közötti távolság
> [!NOTE]
> Ez általában az adatok tudósok feladat.
> 
> 

Előfordulhat, hogy meg szeretné ismerni, hogy van-e a különbség a között két helyen, és a tényleges út távolsága a taxi közvetlen távolságát. Előfordulhat, hogy utas kevésbé valószínű tipp, ha azok mérje fel, hogy az illesztőprogram szándékosan tartott őket hosszabb útvonalanként.

A tényleges út távolság összehasonlítása megtekintéséhez és a [Haversine távolság](http://en.wikipedia.org/wiki/Haversine_formula) között két hosszúság-szélesség pontok (a "nagy kör" távolság), a rendelkezésre álló trigonometriai belül használható struktúra:

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

Az előző lekérdezés az R miles a föld sugara, és pi radiánban megadott szög alakítja át. Vegye figyelembe, hogy az a földrajzi hosszúság hosszúság pontok szűrve eltávolítja a következőt: terület messze értékeket.

Ebben az esetben azt írni az eredmények nevű **queryoutputdir**. A következő parancssorrend először hoz létre a kimeneti könyvtár, és a Hive viszont.

A Hive directory parancssorból futtassa:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


A lekérdezés eredményeinek írt kilenc Azure-blobokat (**queryoutputdir/000000\_0** való **queryoutputdir/000008\_0**), az alapértelmezett tároló, a Hadoop-fürt alatt.

Az egyes blobok méretének megtekintéséhez futtassa a következő parancsot a Hive directory parancssorból:

    hdfs dfs -ls wasb:///queryoutputdir

Egy adott fájl tartalmának megtekintéséhez fel **000000\_0**, használja a Hadoop által `copyToLocal` parancsot.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> `copyToLocal` nagyon nagy méretű fájlok esetén lassú lehet, és nem ajánlott azokat való használatra.  
> 
> 

A fő az adatok találhatók az Azure blob előnye, hogy azt használatával felfedezheti a Machine Learning adatainak a [és adatokat importálhat] [ import-data] modul.

## <a name="#downsample"></a>DOWN kétmintás adatok és a gépi tanulási modellek létrehozása
> [!NOTE]
> Ez általában az adatok tudósok feladat.
> 
> 

A felderítő adatok elemzési fázis után azt most már készen lefelé-minta az adatokat a Machine Learning modellek készítéséhez. Ebben a szakaszban megmutatjuk, hogyan Hive-lekérdezések segítségével le-minta az adatokat. Azt a Machine Learning majd fér hozzá a [és adatokat importálhat] [ import-data] modul.

### <a name="down-sampling-the-data"></a>Az adatok le-mintavétel
Az alábbi eljárással két lépésben történik. Csatlakoztassa azt a **nyctaxidb.trip** és **nyctaxidb.fare** táblák összes rekord található három kulcsok: **medallion**, **ellophatja\_ licenc**, és **a felvételi\_datetime**. A Microsoft létrehozza a bináris besorolási címkék **Formabontó**, és a multiclass besorolási címkék **tipp\_osztály**.

A fogja tudni használni a régebbi mintát adatok közvetlenül a [és adatokat importálhat] [ import-data] modul a Machine Learning szolgáltatásban tárolja egy belső Hive tábla az előző lekérdezés eredményeit. A következő azt egy belső Hive tábla létrehozásához és annak tartalmát a csatlakoztatott és lefelé mintát adatokkal feltölteni.

A lekérdezés érvényes szabványos Hive funkciók közvetlenül létrehozásához a következők egyikét a **a felvételi\_datetime** mező:
- Nap órája
- Az év hete
- milyen napra esik (hétfő jelző 1 és 7 jelző vasárnap)

A lekérdezés is állít elő, a közvetlen távolság a felvétel és a gyűjtőtár között. Az ilyen függvények teljes listáját lásd: [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

A lekérdezés majd lefelé-mintákat az adatokat, hogy a lekérdezés eredményében is illeszthető be az Azure Machine Learning Studio. Az eredeti adathalmazból csak körülbelül 1 százaléka a studio importálta.

Az alábbiakban tartalmát **minta\_hive\_előkészítése\_a\_aml\_full.hql** fájl, amely adatokat előkészíti a Machine Learning felépítése modell:

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

Ez a lekérdezés futtatása a Hive directory parancssorból:

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

Most már van egy belső tábla **nyctaxidb.nyctaxi_downsampled_dataset**, használatával is elérhetők a [és adatokat importálhat] [ import-data] a Machine Learning modulban. Ezenkívül a Machine Learning modellek létrehozása ehhez az adatkészlethez is használjuk.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Az adatok importálása modul használata a Machine Learning le mintavételezett adatokat
A Hive-lekérdezések ki a [és adatokat importálhat] [ import-data] Machine Learning-munkaterület hozzáférés szükséges a Machine Learning modulban. Hozzáférés a hitelesítő adatok a fürt és a kapcsolódó tárfiók is szükséges.

Az alábbiakban néhány részletek kapcsolatos a [és adatokat importálhat] [ import-data] modul és a bemeneti paramétereket:

**HCatalog kiszolgáló URI azonosítója**: Ha a fürt neve **abc123**, ami egyszerűen: https://abc123.azurehdinsight.net.

**Hadoop felhasználói fiók nevét**: A felhasználó nevét, a fürt (nem a távelérési felhasználónév) kiválasztása.

**Hadoop ser fiók jelszava**: az a fürt (nem a távelérési jelszó) választott a jelszót.

**Az kimeneti adatok**: Ez kell lennie az Azure van kiválasztva.

**Az Azure storage-fiók neve**: az alapértelmezett tárfiók neve a fürthöz rendelt.

**Az Azure Tárolónév**: Ez az alapértelmezett tároló a fürt nevét, és általában ugyanaz, mint a fürt nevét. A fürt neve **abc123**, ez az abc123.

> [!IMPORTANT]
> Bármely táblájának jelenítsük használatával lekérdezni a [és adatokat importálhat] [ import-data] a Machine Learning modulban lehet egy belső tábla.
> 
> 

Annak meghatározásához, hogy a tábla a következőképpen **T** adatbázisban **D.db** egy belső tábla. A Hive directory parancssorból futtassa a következő parancsot:

    hdfs dfs -ls wasb:///D.db/T

Ha a tábla egy belső tábla és a telepítéskor, a tartalom itt kell mutatnia.

Annak megállapításához, hogy egy tábla egy belső tábla úgy, hogy Azure Tártallózó használja. Segítségével keresse meg a fürt alapértelmezett tároló nevére, és majd szűrés a tábla neve. Ha a tábla és annak tartalmát jelenik meg, Ez megerősíti, hogy egy belső tábla.

Íme egy Képernyőkép a Hive-lekérdezések és a [és adatokat importálhat] [ import-data] modul:

![Képernyőfelvétel a Hive-lekérdezések és adatokat importálhat modul](./media/hive-walkthrough/1eTYf52.png)

Az alapértelmezett tárolóban le mintát adataink helyezkedik el, az eredményül kapott Hive-lekérdezést a Machine Learning nagyon egyszerű. Most már egy **válasszon * a nyctaxidb.nyctaxi\_felbontáscsökkentésének\_adatok**.

A dataset már használható kiindulási pontként gépi tanulási modellek készítéséhez.

### <a name="mlmodel"></a>A Machine Learning modellek létrehozása
Most már folytathatja a modell létrehozásának és a modell telepítési [Machine Learning](https://studio.azureml.net). Az adatok készen áll a korábban azonosított előrejelzés problémák megoldásához használhatunk:

- **Bináris osztályozási**: előre jelezni-e tipp kifizetett egy út.

  **Használt tanuló:** két osztályú logisztikai regresszió

  a. A probléma a célként megadott (vagy osztály) címke van **Formabontó**. Az eredeti le mintát dataset adatkészletben néhány az oszlopokat, amelyek a cél kiszivárgásának a besorolási kísérleti fázisú funkciókat. Különösen **tipp\_osztály**, **tipp\_összeg**, és **teljes\_összeg** fedik fel információt a célként megadott címkével Nincs elérhető tesztelési idő. Ezekben az oszlopokban azon használatával eltávolítása a [Select Columns in Dataset] [ select-columns] modul.

  Az alábbi ábrán látható-e egy adott út tipp kifizetett előre a kísérlet:

  ![Kísérlet ábrája](./media/hive-walkthrough/QGxRz5A.png)

  b. Ehhez a kísérlethez a cél címke azokat a terjesztéseket volt körülbelül 1:1.

   Az alábbi ábra a bináris osztályozási problémához osztály címkéit tipp eloszlását mutatja:

  ![Tipp osztály címkék eloszlás diagram](./media/hive-walkthrough/9mM4jlD.png)

    Ennek eredményeképpen azt szerezze be alatti (AUC) 0.987, egy olyan területre, az alábbi ábrán látható módon:

  ![Diagram AUC érték](./media/hive-walkthrough/8JDT0F8.png)

- **Multiclass besorolási**: tipp összegek fizetett út, a korábban meghatározott osztályok használatával számos előre jelezni.

  **Használt tanuló:** Multiclass logisztikai regresszió

  a. A probléma az cél (vagy osztály) címke van **tipp\_osztály**, amely (0,1,2,3,4) öt érték valamelyikét hajthatja végre. Bináris osztályozási gazdabuszadaptereken van néhány az oszlopokat, amelyek a cél kiszivárgásának a kísérleti fázisú funkciókat. Különösen **Formabontó**, **tipp\_összeg**, és **teljes\_összeg** a cél-címke, amely nem érhető el információ felfedése tesztelési idő. Ezekben az oszlopokban használatával eltávolítása a [Select Columns in Dataset] [ select-columns] modul.

  Az alábbi ábrán látható a kísérlet megjósolható melyik van tipp várhatóan tartoznak. A bins vannak: osztály: 0: tipp = 0, 1 osztály: > $0 és tipp tipp < $5, osztály 2 =: > $5 és tipp tipp < = 10 $ osztály 3: > $10 és tipp tipp < $20 és osztály 4 =: tipp > $20.

  ![Kísérlet ábrája](./media/hive-walkthrough/5ztv0n0.png)

  Most megjelenítése a tényleges vizsgálati osztály terjesztési néz. Osztály 0 és 1 osztály elterjedt, és a más osztályok ritkák.

  ![Teszt osztály eloszlás diagram](./media/hive-walkthrough/Vy1FUKa.png)

  b. Ehhez a kísérlethez használjuk a félreértések mátrix nézze meg az előrejelzési pontosság. Ez itt látható:

  ![Zavart mátrix](./media/hive-walkthrough/cxFmErM.png)

  Vegye figyelembe, hogy az osztály pontosság elterjedt osztályokon meglehetősen megfelelőek, amíg a modell nem "tanulás" szép munka meg az egyes osztályok.

- **Regressziós feladat**: megjósolható a fizetős útnak tipp mennyisége.

  **Használt tanuló:** Boosted döntési fája

  a. A probléma a célként megadott (vagy osztály) címke van **tipp\_összeg**. A cél kiszivárgásának ebben az esetben is: **Formabontó**, **tipp\_osztály**, és **teljes\_összeg**. Ezeket a változókat, amelyek általában nem érhető el a tesztelési idő tipp mennyi információ felfedése. Ezekben az oszlopokban használatával eltávolítása a [Select Columns in Dataset] [ select-columns] modul.

  Az alábbi ábrán látható a kísérletet, és előre jelezni az adott tipp mennyisége:

  ![Kísérlet ábrája](./media/hive-walkthrough/11TZWgV.png)

  b. A regresszió problémákat a az előrejelzési pontosság az előrejelzés és döntési együttható squared hiba megtekintésével mérjük:

  ![Előrejelzés statisztikai képernyőképe](./media/hive-walkthrough/Jat9mrz.png)

  Itt döntési együttható 0.709, ami azt jelenti, hogy varianciáját körülbelül 71 százaléka van magyarázza a modell együttható.

> [!IMPORTANT]
> Gépi tanulási és elérése, és ezzel kapcsolatos további információkért lásd: [Mi az Machine Learning](../studio/what-is-machine-learning.md). Emellett a [Azure Eszközintelligencia-katalógus](https://gallery.cortanaintelligence.com/) kísérletek színtartomány ismerteti, és a szolgáltatások körét a Machine Learning alapos bevezetést tartalmaz.
> 
> 

## <a name="license-information"></a>Licencinformációk
Ez a minta a forgatókönyv és a hozzá tartozó szkriptek osztanak meg a Microsoft a MIT licence. További részletekért lásd: a **LICENSE.txt** fájl a mintakódot a Githubon.

## <a name="references"></a>Referencia
• [Andrés Monroy NYC Taxi utak letöltési oldala](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC taxiköltség út adatok Chris Whong által](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi és Limousine Bizottság kutatási és statisztika](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/




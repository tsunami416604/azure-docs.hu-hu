---
title: "Hadoop-fürthöz az adatokba, és az Azure Machine Learning modellek létrehozása |} Microsoft Docs"
description: "Egy végpont forgatókönyv egy HDInsight Hadoop-fürt létrehozásához és telepítéséhez egy nyilvánosan elérhető adatkészlet modell alkalmazó az Team tudományos folyamat használja."
services: machine-learning,hdinsight
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: e9e76c91-d0f6-483d-bae7-2d3157b86aa0
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: bradsev
ms.openlocfilehash: ad7bc8bb65a3395599a4de9a9954ff203fa624c6
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>A művelet az Team tudományos folyamat: használata Azure HDInsight Hadoop-fürtök
Ebben a bemutatóban használjuk a [Team adatok tudományos folyamat (TDSP)](overview.md) egy végpont forgatókönyv használatával egy [Azure HDInsight Hadoop-fürt](https://azure.microsoft.com/services/hdinsight/) tárolására, vizsgálatát, és a beállítást, a visszafejtés adatait a nyilvánosan rendelkezésre álló [NYC Taxi Utazgatással](http://www.andresmh.com/nyctaxitrips/) adatkészletet, és lefelé az az adatokat. Az adatok modellek beépített Azure Machine Learning segítségével multiclass és bináris osztályozás és regressziós prediktív feladatok kezelésére.

Ez a forgatókönyv bemutatja, hogyan kezeli a nagyobb (1 terabájtnál) adatkészletet egy hasonló példa használata a HDInsight Hadoop-fürtök az adatfeldolgozás, lásd: [Team adatok tudományos folyamat - használata Azure HDInsight Hadoop-fürtök az 1 TB-os dataset](hive-criteo-walkthrough.md) .

Akkor is egy IPython notebook használatával a feladatok jelenik meg az 1 TB-os adatkészlet forgatókönyv végrehajtásához. Konzultáljon a felhasználók számára szeretné próbálni ezt a módszert használja a [Criteo forgatókönyv Hive ODBC-kapcsolat használatával](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) témakör.

## <a name="dataset"></a>NYC Taxi Utazgatással adatkészlet leírása
A következőt: Taxi út adatok körülbelül 20GB tömörített vesszővel tagolt (CSV) fájl (tömörítetlen ~ 48GB), több mint 173 millió egyedi való adatváltások számát és a vitel kifizette minden út. Minden út rekord tartalmazza a felvétel és Gyűjtőtár hely és idő, anonimizált rejthetők el (illesztőprogram) engedély száma és medallion (taxi tartozó egyedi azonosító) számát. Az adatok minden való adatváltások számát ismerteti az év 2013, és minden hónap a következő két adatkészletet találhatók:

1. A "trip_data" CSV-fájlok tartalmazzák a út részletek, például a utasok, a felvételi és dropoff pontok, út időtartama és út hossza. Íme néhány példa rekordok:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. A "trip_fare" CSV fájlok tartalmazzák a jegy ára kifizette minden út, például a fizetési mód, jegy ára összeg, emelt díjas és adókat, tippeket és autópályadíjak, és a teljes összeg fizetős részleteit. Íme néhány példa rekordok:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Egyedi kulcs út csatlakozni\_adatok és út\_jegy ára tevődnek össze a mezők: medallion, rejthetők el\_engedély és a felvételi\_dátum és idő.

Ahhoz, hogy az összes részletet egy adott út vonatkozó, megfelelő kapcsolódása három kulcsok: "medallion", "ellophatja\_licenc" és "a felvételi\_datetime".

Néhány további részleteket az adatok azt írják le, ha tároljuk őket a Hive táblák hamarosan.

## <a name="mltasks"></a>Előrejelzés feladatok példák
Amikor eléri az adatok, milyen típusú szeretné előrejelzéseket meghatározása a elemzésén alapul elmagyarázza a feladatokat, meg kell adnia a folyamat segítségével.
Három példa a előrejelzés problémák, amely azon alapul, amelynek létrehozását a forgatókönyv oldjuk az *tipp\_összeg*:

1. **Bináris osztályozási**: előre jelezni, függetlenül attól, tipp kifizetett utazás, azaz egy *tipp\_összeg* nagyobb, mint 0 egy pozitív példában látható, miközben egy *tipp\_Összeg* $ 0 egy negatív példában látható.
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0
2. **Multiclass besorolási**: út kifizette tipp összegek számos előre jelezni. Azt a osztani a *tipp\_összeg* öt bins vagy osztályok:
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0 and tip_amount <= $5
        Class 2: tip_amount > $5 and tip_amount <= $10
        Class 3: tip_amount > $10 and tip_amount <= $20
        Class 4: tip_amount > $20
3. **Regressziós feladat**: megjósolható a fizetős útnak tipp mennyisége.  

## <a name="setup"></a>Állítson be egy HDInsight Hadoop-fürt speciális elemzésekre
> [!NOTE]
> Ez a művelet rendszerint egy **Admin** feladat.
> 
> 

Speciális elemzésekre három lépést a HDInsight-fürtök által környezetet az Azure állíthat be:

1. [Hozzon létre egy tárfiókot](../../storage/common/storage-create-storage-account.md): ezt a tárfiókot az Azure Blob Storage adatok tárolására szolgál. A HDInsight-fürtök adatait is itt található.
2. [A speciális elemzés folyamat és a technológia Azure HDInsight Hadoop-fürtök testreszabása](customize-hadoop-cluster.md). Ebben a lépésben létrehoz egy Azure HDInsight Hadoop 64 bites Anaconda Python 2.7 a fürt összes csomópontján telepíteni. Jegyezze meg a HDInsight-fürt testreszabása során két fontos lépésből áll.
   
   * Ne felejtse el a tárfiók létrehozásakor az 1. lépésben a HDInsight-fürthöz létrehozott hivatkozásra. Ez a tárfiók a fürtön belül feldolgozott adatok elérésére szolgál.
   * A fürt létrehozása után engedélyezze a távelérést az átjárócsomóponthoz, a fürt. Keresse meg a **konfigurációs** fülre, és kattintson **távoli engedélyezése**. Ebben a lépésben adja meg a távoli bejelentkezéshez használt felhasználói hitelesítő adatokat.
3. [Hozzon létre egy Azure Machine Learning munkaterülettel](../studio/create-workspace.md): munkaterület az Azure Machine Learning segítségével hozhatók létre machine learning modellek. Ez a feladat címzettjei egy kezdeti adatfeltárás befejezése után, és a HDInsight-fürtöt használ mintavételi le.

## <a name="getdata"></a>Beolvassa az adatokat nyilvános forráskódú
> [!NOTE]
> Ez a művelet rendszerint egy **Admin** feladat.
> 
> 

A beolvasandó a [NYC Taxi való adatváltások számát](http://www.andresmh.com/nyctaxitrips/) dataset nyilvános helyéről, előfordulhat, hogy használja ismertetett módszerek valamelyikét [adatok áthelyezése, és az Azure Blob Storage](move-azure-blob.md) adatok másolása a számítógépen.

Itt azt írják le hogyan az AzCopy vihet át adatokat tartalmazó fájlok. Töltse le és telepítse az AzCopy kövesse [első lépések az AzCopy parancssori segédprogram](../../storage/common/storage-use-azcopy.md).

1. Egy parancssori ablakot, a probléma az alábbi AzCopy parancsok cseréje *< path_to_data_folder >* a kívánt cél:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. A Másolás befejezése után összesen 24 tömörített fájlok vannak, a kiválasztott adatok mappában. Bontsa ki a helyi számítógépen a letöltött fájlok ugyanabba a könyvtárba. Jegyezze fel a kibontott fájlokat tároló mappa. Ebben a mappában kell lennie az úgynevezett a *< elérési út\_való\_unzipped_data\_fájlok\>*  van következik.

## <a name="upload"></a>Az adatok feltöltése a az alapértelmezett tároló Azure HDInsight Hadoop-fürt
> [!NOTE]
> Ez a művelet rendszerint egy **Admin** feladat.
> 
> 

A következő AzCopy parancsok, cserélje le a következő paraméterek a tényleges, a Hadoop-fürt létrehozásakor megadott értékeket, és az adatfájlokat kicsomagolás.

* ***&#60; path_to_data_folder >*** a könyvtár (együtt elérési utat) a számítógépen, a tömörítetlen adatfájljait tartalmazó  
* ***&#60; Hadoop-fürt tárfióknév >*** a HDInsight-fürthöz társított storage-fiók
* ***&#60; alapértelmezett tároló Hadoop-fürt >*** az alapértelmezett tároló, a fürt által használt. Vegye figyelembe, hogy az alapértelmezett tároló neve általában a neve megegyezik a fürthöz. Például ha a fürt neve a "abc123.azurehdinsight.net", az alapértelmezett tároló abc123.
* ***&#60; tárfiók kulcsa >*** a fürt által használt tárfiók kulcsa

A parancssorba vagy egy Windows PowerShell-ablakot a gépen futtassa a következő két AzCopy parancs.

Ez a parancs az út adatokat tölt ***nyctaxitripraw*** könyvtárhoz, az alapértelmezett tárolóban, a Hadoop-fürt.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Ez a parancs a jegy ára adatokat tölt ***nyctaxifareraw*** könyvtárhoz, az alapértelmezett tárolóban, a Hadoop-fürt.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Az adatok most kell az Azure Blob Storage és a rendszer készen áll a HDInsight-fürtön belül használható.

## <a name="#download-hql-files"></a>Jelentkezzen be a Hadoop-fürt átjárócsomópontjához és és a felderítő adatelemzés előkészítése
> [!NOTE]
> Ez a művelet rendszerint egy **Admin** feladat.
> 
> 

Felderítő adatelemzés és az adatok mintavételi le a fürt átjárócsomópontjához szeretne használni, kövesse a témakörben ismertetett eljárás [a Head csomópont a Hadoop-fürt eléréséhez](customize-hadoop-cluster.md).

Ez a forgatókönyv elsősorban használjuk írt lekérdezések [Hive](https://hive.apache.org/), egy SQL-szerű lekérdező nyelv, előzetes adatok explorations végrehajtásához. A Hive-lekérdezések .hql fájlok tárolják. Azt majd lefelé az az adatokat az Azure Machine Learning modellek készítéséhez használható.

A fürt előkészítése felderítő adatelemzés, azt a megfelelő Hive parancsfájlokat tartalmazó .hql fájlok letöltésére [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) az átjárócsomópont egy helyi könyvtárába (C:\temp). Ehhez nyissa meg a **parancssor** a a fürt átjárócsomópontjához belül, és ki a következő két parancsot:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

A két parancsok letölti azokat ebben a bemutatóban a helyi könyvtárba szükséges fájlokat .hql ***C:\temp &#92;*** központi csomópontjában.

## <a name="#hive-db-tables"></a>Hive-adatbázis és hónaponként particionált táblák létrehozása
> [!NOTE]
> Ez a művelet rendszerint egy **Admin** feladat.
> 
> 

A Microsoft most már készen áll a következőt: taxi adatkészlet Hive táblák létrehozása.
A Hadoop-fürt átjárócsomópontjához, nyissa meg a ***Hadoop parancssori*** az átjárócsomóponthoz asztalán, és írja be a Hive directory parancs beírásával

    cd %hive_home%\bin

> [!NOTE]
> **Ez a forgatókönyv minden Hive parancsot futtatja a fenti Hive bin / directory kérdés. Ez az elérési út probléma merül fel automatikusan kezeli. A "Struktúra directory prompt", kifejezéseket használjuk a "struktúra bin / directory prompt", és a "Hadoop parancssori" azonos értelemben ebben a bemutatóban.**
> 
> 

A Hive directory parancssorból adja meg a következő parancsot a Hadoop parancssorát az átjárócsomópont elküldeni a Hive-lekérdezés Hive adatbázis és a táblák létrehozásához:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Ez a tartalom a ***C:\temp\sample\_hive\_létrehozása\_db\_és\_tables.hql*** fájl, amelyhez Hive adatbázist hoz létre ***nyctaxidb*** és táblák ***út*** és ***jegy ára***.

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

* a "út" tábla minden egyes menethelyzetben (illesztőprogram adatai, felvételi idő, út távolság és időpontok) út részleteit tartalmazza.
* a "jegy ára" tábla (jegy ára, tipp összegre, autópályadíjak és pótdíjak) jegy ára részleteit tartalmazza.

Ha ezekkel az eljárásokkal semmilyen további segítségre van szüksége, vagy vizsgálja meg az alternatív néhányat a meglévők közül, című részében [közvetlenül a Hadoop parancssori elküldeni a Hive-lekérdezések ](move-hive-tables.md#submit).

## <a name="#load-data"></a>Adatok betöltése a Hive táblákat partíció
> [!NOTE]
> Ez a művelet rendszerint egy **Admin** feladat.
> 
> 

A következőt: taxi dataset adatkészletben, természetes particionálás havonta, amelyek feldolgozásával és lekérdezéseivel gyorsabb engedélyezése használatára. Az alábbi PowerShell-parancsok (a Hive directory a kibocsátott a **Hadoop parancssori**) adatok hónap szerint particionálva "út" és "jegy ára" Hive táblákat betöltése.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

A *minta\_hive\_betölteni\_adatok\_által\_partitions.hql* fájl tartalmazza a következő **betöltése** parancsok.

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Vegye figyelembe, hogy egy itt használjuk a feltárási folyamata Hive-lekérdezések száma is magában foglalja, csak egyetlen partícióra vagy csak néhány partíciókat. De ezeket a lekérdezéseket futtathat a teljes adatok között.

### <a name="#show-db"></a>A HDInsight Hadoop-fürt adatbázisok megjelenítése
A HDInsight Hadoop-fürt a Hadoop parancssori ablakban létrehozott adatbázisokat megjelenítéséhez futtassa a következő parancsot a Hadoop parancssor:

    hive -e "show databases;"

### <a name="#show-tables"></a>A Hive táblák nyctaxidb adatbázis megjelenítése
A táblák nyctaxidb adatbázis megjelenítése, futtassa a következő parancsot a Hadoop parancssorban:

    hive -e "show tables in nyctaxidb;"

Azt is meggyőződhet arról, hogy a táblázatok particionáltak-e az alábbi parancsot:

    hive -e "show partitions nyctaxidb.trip;"

A várt kimeneti alább találja:

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

Hasonlóképpen azt is győződjön meg arról, hogy a jegy ára tábla particionálva van-e az alábbi parancsot:

    hive -e "show partitions nyctaxidb.fare;"

A várt kimeneti alább találja:

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
> Ez a művelet rendszerint egy **adatok tudósok** feladat.
> 
> 

A feladatok az adatokat a Hive táblák betölti a mérnöki adatok feltárása és Hive-lekérdezések segítségével kell meghatározni. Az alábbiakban például olyan feladatokat, hogy azt végigvezetik Önt ebben a szakaszban:

* Az első 10 rekordok megtekintése a két tábla.
* Megismerkedhet a különböző idő windows néhány mezőinek disztribúciók adatok.
* Vizsgálja meg a szélességi és hosszúsági mezők adatok minőségét.
* Multiclass és bináris besorolási címkék alapján készítése a **tipp\_összeg**.
* Szolgáltatások elő a közvetlen út távolság számítástechnikai.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Feltárása: A tábla út első 10 adatainak megtekintése
> [!NOTE]
> Ez a művelet rendszerint egy **adatok tudósok** feladat.
> 
> 

Az adatok néz megtekintéséhez azt minden tábla 10 rekordjai vizsgálja meg. A következő két lekérdezések külön-külön futtassa a Hadoop parancssori konzolt a vizsgálandó rekordok Hive directory parancssorból.

Az első 10 rögzíti a következő táblázatban: "út" lekérése az első olyan hónap:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Lekérése a felső 10 rekordokat a tábla "jegy ára" az első olyan hónap:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Gyakran érdemes kényelmes megtekintésre fájlba mentése a rekordok. A fentebbi lekérdezés kis módosítva ezt éri el:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Feltárása: A rekordok száma tekintse meg minden olyan 12 partíció
> [!NOTE]
> Ez a művelet rendszerint egy **adatok tudósok** feladat.
> 
> 

Fontos, a hogyan utak száma függ a naptári év során. Havonta csoportosítási lehetővé teszi, hogy mutatni ehhez a terjesztéshez utak néz.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Ez biztosítanak számunkra kimenete:

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

Itt az első oszlopban a hónap és a második pedig utak száma az adott hónapban.

Azt is is száma rekordok teljes száma a út adatkészlet a következő parancsot a Hive könyvtár parancssorába.

    hive -e "select count(*) from nyctaxidb.trip;"

Ez eredményez:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Hasonló út adatkészlet látható parancsok azt kiadhatnak Hive-Lekérdezések érvényesítése rekordok száma jegy ára adatkészlet Hive directory parancssorból.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Ez biztosítanak számunkra kimenete:

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

Vegye figyelembe, hogy mindkét adatkészletek visszaküldött pontos ugyanannyi havonta való adatváltások számát. Ez lehetővé teszi, hogy az adatok megfelelően betölteni az első ellenőrzést.

A jegy ára adatkészlet-rekordok teljes száma számbavételi végezhető a Hive directory parancssorból alábbi paranccsal:

    hive -e "select count(*) from nyctaxidb.fare;"

Ez eredményez:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

A két tábla rekordok száma is megegyezik. Ez lehetővé teszi, hogy az adatok megfelelően betölteni a második ellenőrzési.

### <a name="exploration-trip-distribution-by-medallion"></a>Feltárása: Út elosztása medallion szerint
> [!NOTE]
> Ez a művelet rendszerint egy **adatok tudósok** feladat.
> 
> 

Ebben a példában a medallion (taxi számok) azonosítja a 100-nál több való adatváltások számát egy adott időtartamon belül. A lekérdezés a particionált tábla hozzáférés előnyökkel jár, mivel ez a partíció változóban van annak **hónap**. A lekérdezés eredményeit a rendszer egy helyi fájl queryoutput.tsv a ír `C:\temp` a head csomóponton.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Ez a tartalom *minta\_hive\_út\_száma\_által\_medallion.hql* fájl a vizsgálathoz.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

A következő NYC taxi adatkészletben medallion egyedi cab azonosítja. Azt is azonosíthatja, hogy mely kabinetfájlok "foglalt" azzal, hogy melyik végzett több mint egy bizonyos számú való adatváltások számát egy adott időszakban. Az alábbi példában az első három, és menti a lekérdezés eredményeinek egy helyi fájlba, C:\temp\queryoutput.tsv egynél több száz helymódosításra utazgatással végrehajtott kabinetfájlok azonosítja.

Ez a tartalom *minta\_hive\_út\_száma\_által\_medallion.hql* fájl a vizsgálathoz.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

A Hive directory parancssorból adja ki az alábbi parancsot:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Feltárása: Út terjesztési medallion és hack_license
> [!NOTE]
> Ez a művelet rendszerint egy **adatok tudósok** feladat.
> 
> 

Ha dataset felfedezése, gyakran szeretnénk vizsgálja meg a csoportok értékek co előfordulásainak száma. Ez a szakasz példa bemutatja, hogyan kabinetfájlok és illesztőprogramok ehhez adjon meg.

A *minta\_hive\_út\_száma\_által\_medallion\_license.hql* fájlcsoportok "medallion" és "hack_license" a jegy ára adatkészlet és az egyes kombinációk számát adja vissza. Az alábbiakban annak tartalmát.

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

A lekérdezés által visszaadott cab-fájl és az adott illesztőprogram kombinációját utazgatással csökkenő száma alapján rendezve.

A Hive directory parancssorból futtassa:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

A lekérdezés eredményei egy helyi fájl C:\temp\queryoutput.tsv kerülnek.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitudelatitude-records"></a>Feltárása: Értékeli az adatminőségi érvénytelen hosszúság vagy szélességi rekordok ellenőrzésével
> [!NOTE]
> Ez a művelet rendszerint egy **adatok tudósok** feladat.
> 
> 

A közös felderítő adatelemzés célja érvénytelen vagy helytelen rekordot kimenő gyomláláskor. A jelen szakaszban ismertetett példa meghatározza, hogy a hosszúság vagy a szélesség mezők tartalmaznak-e egy értéket, amennyiben a következőt: területen kívül. Valószínű, hogy az ilyen bejegyzések rendelkezik-e egy hibás hosszúság-szélességi értékeknek, mivel azt szeretnénk kiküszöbölheti a található bármely modellezési használt adatokat.

Ez a tartalom *minta\_hive\_minőségi\_assessment.hql* fájl a vizsgálathoz.

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
> Ez a művelet rendszerint egy **adatok tudósok** feladat.
> 
> 

A bináris osztályozási problémához leírt a [előrejelzés feladatok példái](hive-walkthrough.md#mltasks) szakaszban érdemes tisztában lenni, hogy egy rövid adott-e. A terjesztés tippek az bináris:

* Tipp megadott (osztály 1, tipp\_összeg > $0)  
* nincs ötlet (osztály: 0, tipp\_összeg = 0).

A *minta\_hive\_Formabontó\_frequencies.hql* fájlsablont ezt végzi.

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
> Ez a művelet rendszerint egy **adatok tudósok** feladat.
> 
> 

A leírt multiclass besorolás probléma a [előrejelzés feladatok példái](hive-walkthrough.md#mltasks) szakasz adatkészlet is adatmodelljeinek ahol szeretnénk előre jelezni az adott tippek mennyisége természetes besorolást. Használjuk bins tipp tartományok megadása a lekérdezésben. Ahhoz, hogy az osztály azokat a terjesztéseket, a különböző tartományok tipp, használjuk a *minta\_hive\_tipp\_tartomány\_frequencies.hql* fájlt. Az alábbiakban annak tartalmát.

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

A következő parancsot a Hadoop parancssori konzolt:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-direct-distance-between-two-longitude-latitude-locations"></a>Feltárása: Számítási közvetlen hosszúság-szélesség két hely közötti távolság szerint
> [!NOTE]
> Ez a művelet rendszerint egy **adatok tudósok** feladat.
> 
> 

A közvetlen távolság mérték segítségével velünk, hogy megtudja, és a tényleges út távolság közötti eltérés. Ez a szolgáltatás jelzésével, hogy utas előfordulhat, hogy valószínűleg kevesebb tipp, ha azok mérje fel, hogy az illesztőprogram szándékosan tartott őket egy sokkal hosszabb útvonalanként rávenni azt.

A tényleges út távolság összehasonlítása megtekintéséhez és a [Haversine távolság](http://en.wikipedia.org/wiki/Haversine_formula) között két hosszúság-szélesség pontok (a "nagy kör" távolság), használjuk a rendelkezésre álló trigonometriai belül struktúra, így:

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

A fenti lekérdezés R miles a föld sugara, és pi radiánban megadott szög alakítja át. Vegye figyelembe, hogy az a földrajzi hosszúság hosszúság pontok "kiszűrt" eltávolítja a következőt: terület messze értékeket.

Ebben az esetben azt írni az eredmények "queryoutputdir" nevű könyvtár. Az alábbi parancssorozat először hoz létre a kimeneti könyvtárhoz, és a Hive viszont.

A Hive directory parancssorból futtassa:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


A lekérdezés eredményeinek írt 9 Azure-blobokat ***queryoutputdir/000000\_0*** való ***queryoutputdir/000008\_0*** az alapértelmezett tároló, a Hadoop-fürt alatt.

Az egyes blobok méretének megtekintéséhez a Hive directory parancssorból azt futtassa a következő parancsot:

    hdfs dfs -ls wasb:///queryoutputdir

Egy adott fájl tartalmának megtekintéséhez tegyük fel például 000000\_0, a Hadoop által használjuk `copyToLocal` parancsban, így.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> `copyToLocal`nagyon nagy méretű fájlok esetén lassú lehet, és nem ajánlott azokat való használatra.  
> 
> 

A kulcs az adatok találhatók az Azure blob előnye, hogy azt is adatokba az Azure Machine Learning segítségével belül a [és adatokat importálhat] [ import-data] modul.

## <a name="#downsample"></a>Adatok és -buildek mintákat az Azure Machine Learning le
> [!NOTE]
> Ez a művelet rendszerint egy **adatok tudósok** feladat.
> 
> 

A felderítő adatok elemzési fázis után azt most már készen áll a minta az adatokat az Azure Machine Learning modellek készítéséhez le. Ebben a szakaszban megmutatjuk, hogyan lefelé minta Hive-lekérdezések segítségével a adatait, amely majd elérése a [és adatokat importálhat] [ import-data] az Azure Machine Learning modulban.

### <a name="down-sampling-the-data"></a>Leállt az adatok mintavétele
Az alábbi eljárással két lépésben történik. Csatlakoztassa azt a **nyctaxidb.trip** és **nyctaxidb.fare** táblák összes rekord található három kulcs: "medallion", "ellophatja\_licenc", és "a felvételi\_datetime". A Microsoft létrehozza a bináris besorolási címkék **Formabontó** és több osztály besorolási címkék **tipp\_osztály**.

Fogja tudni használni a le adatminta közvetlenül a [és adatokat importálhat] [ import-data] az Azure Machine Learning modulban szükség egy belső Hive táblát a fenti lekérdezés eredményét. A következik azt egy belső Hive tábla létrehozásához és annak tartalmát a csatlakoztatott és le a mintaadatokat feltöltéséhez.

A lekérdezés érvényes szabványos Hive funkciók közvetlenül az óra, nap, hétköznap (hétfő jelző 1 és 7 jelző vasárnap) év hete létrehozásához a "felvétel\_dátum és idő" mező, és a közvetlen távolság a felvétel és a dropoff között. Felhasználók hivatkozhat [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) ilyen funkciók teljes listáját.

A lekérdezés majd lefelé minták az adatokat, hogy a lekérdezés eredményeit az Azure Machine Learning Studio is illeszkedik. A Studio importálta az eredeti adathalmazból csak körülbelül 1 %-át.

Az alábbiakban tartalmát *minta\_hive\_előkészítése\_a\_aml\_full.hql* fájlt, amely adatokat előkészíti a modell létrehozása az Azure Machine Learning.

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

Most már van egy belső tábla "nyctaxidb.nyctaxi_downsampled_dataset" használatával is elérhetők a [és adatokat importálhat] [ import-data] az Azure Machine Learning modulban. Továbbá a Machine Learning modellek létrehozása ehhez az adatkészlethez előfordulhat, hogy használjuk.  

### <a name="use-the-import-data-module-in-azure-machine-learning-to-access-the-down-sampled-data"></a>Az adatok importálása modul használata az Azure Machine Learning le a mintaadatokat eléréséhez
Hive kiállító előfeltételként lekérdezi a a [és adatokat importálhat] [ import-data] az Azure Machine Learning modulban, igazolnia kell a hozzáférést az Azure Machine Learning a munkaterület és a hitelesítő adatok a fürt eléréséhez és a kapcsolódó tárfiók.

Egyes adatok a [és adatokat importálhat] [ import-data] modul és a bemeneti paramétereket:

**HCatalog kiszolgáló URI azonosítója**: Ha a fürtnév abc123, akkor egyszerűen: https://abc123.azurehdinsight.net

**Hadoop felhasználói fiók nevét**: A felhasználó nevét, a fürthöz kiválasztott (**nem** a távelérés felhasználónév)

**Hadoop ser fiók jelszava**: A jelszó a fürthöz kiválasztott (**nem** a távelérés jelszó)

**Az kimeneti adatok**: Ez kell lennie az Azure van kiválasztva.

**Az Azure storage-fiók neve**: az alapértelmezett tárfiók neve a fürthöz rendelt.

**Az Azure Tárolónév**: Ez az alapértelmezett tároló a fürt nevét, és általában ugyanaz, mint a fürt nevét. A fürt "abc123" nevű ez pedig csak abc123.

> [!IMPORTANT]
> **Bármely táblájának jelenítsük lekérdezés használatával a [és adatokat importálhat] [ import-data] modul az Azure Machine Learning egy belső tábla kell lennie.** Tipp: a meghatározhatja, hogy a tábla T D.db adatbázisban egy belső tábla a következőképpen történik.
> 
> 

A Hive directory parancssorból adja ki a parancsot:

    hdfs dfs -ls wasb:///D.db/T

Ha a tábla egy belső tábla és a telepítéskor, a tartalom itt kell mutatnia. Annak megállapításához, hogy egy tábla egy belső tábla másik módja, hogy az Azure Tártallózó használja. Segítségével keresse meg a fürt alapértelmezett tároló nevére, és majd szűrés a tábla neve. Ha a tábla és annak tartalmát jelenik meg, Ez megerősíti, hogy egy belső tábla.

Íme a pillanatkép készítése a Hive-lekérdezések és a [és adatokat importálhat] [ import-data] modul:

![Adatokat importálhat modul Hive-lekérdezések](./media/hive-walkthrough/1eTYf52.png)

Vegye figyelembe, hogy a mintaadatokat az alapértelmezett tárolóban található le, mert az eredményül kapott Hive-lekérdezést az Azure Machine Learning nagyon egyszerű, csak a "Válasszon * a nyctaxidb.nyctaxi\_felbontáscsökkentésének\_adatok".

A dataset előfordulhat, hogy most már használható kiindulási pontként gépi tanulási modellek létrehozásához.

### <a name="mlmodel"></a>Az Azure Machine Learning modellek létrehozása
Most már dolgozunk a modell létrehozásának és a modell központi telepítést, a folytatáshoz [Azure Machine Learning](https://studio.azureml.net). Az adatok készen áll a fenti azonosított előrejelzés problémák megoldásához használhatunk:

**1. Bináris osztályozási**: előre jelezni-e tipp kifizetett egy út.

**Használt tanuló:** két osztályú logisztikai regresszió

a. A probléma az cél (vagy osztály) címke van "Formabontó". Az eredeti le mintát adatkészletet rendelkezik néhány az oszlopokat, amelyek a cél kiszivárgásának a besorolási kísérleti fázisú funkciókat. Különösen: tipp\_osztály, tipp\_mennyiségét, és a végösszeg\_összeg fedik fel a cél-címke, amely nem érhető el a tesztelési idő információt. Ezekben az oszlopokban szempont használatával eltávolítása a [Select Columns in Dataset] [ select-columns] modul.

Az alábbi pillanatkép a kísérletet, és előre jelezni, függetlenül attól, tipp egy adott út kifizetett jeleníti meg.

![Kísérlet pillanatkép](./media/hive-walkthrough/QGxRz5A.png)

b. Ehhez a kísérlethez a cél címke azokat a terjesztéseket volt körülbelül 1:1.

A pillanatkép, az alábbi tipp eloszlását mutatja a bináris osztályozási problémához osztály címkéit.

![Tipp osztály címkék terjesztése](./media/hive-walkthrough/9mM4jlD.png)

Ennek eredményeképpen azt szerezzen be egy AUC a 0.987, az alábbi ábrán látható módon.

![AUC érték](./media/hive-walkthrough/8JDT0F8.png)

**2. Multiclass besorolási**: út, a korábban meghatározott osztályokat kifizette tipp összegek számos előre jelezni.

**Használt tanuló:** Multiclass logisztikai regresszió

a. A probléma van az cél (vagy osztály) címke "Tipp\_osztály" amely (0,1,2,3,4) öt érték valamelyikét hajthatja végre. Bináris osztályozási gazdabuszadaptereken van néhány az oszlopokat, amelyek a cél kiszivárgásának a kísérleti fázisú funkciókat. Különösen: Formabontó, tipp\_teljes összeg\_összeg fedik fel a cél-címke, amely nem érhető el a tesztelési idő információt. Ezekben az oszlopokban használatával eltávolítása a [Select Columns in Dataset] [ select-columns] modul.

A pillanatkép, az alábbi jeleníti meg a kísérletben megjósolható melyik van tipp várhatóan tartoznak (osztály: 0: tipp = 0, 1 osztály: > $0 és tipp tipp < $5, osztály 2 =: > $5 és tipp tipp < = 10 $ osztály 3: > $10 és tipp tipp < = $20 4. osztály: tipp > $20)

![Kísérlet pillanatkép](./media/hive-walkthrough/5ztv0n0.png)

Most megjelenítése a tényleges vizsgálati osztály terjesztési néz. Látható, hogy amíg osztály 0 és 1-osztály nem elterjedt, a más osztályok ritkák.

![Osztály terjesztési tesztelése](./media/hive-walkthrough/Vy1FUKa.png)

b. Ehhez a kísérlethez használjuk a félreértések mátrix nézze meg az előrejelzési pontosság. Ez az alábbiakban látható.

![Zavart mátrix](./media/hive-walkthrough/cxFmErM.png)

Vegye figyelembe, hogy az osztály pontosság elterjedt osztályokon meglehetősen megfelelőek, amíg a modell nem "tanulás" szép munka meg az egyes osztályok.

**3. Regressziós feladat**: megjósolható a fizetős útnak tipp mennyisége.

**Használt tanuló:** Boosted döntési fája

a. A probléma van az cél (vagy osztály) címke "Tipp\_összeg". A cél kiszivárgásának ebben az esetben is: Formabontó, tipp\_osztály, teljes\_összeg; minden változókhoz általában nem érhető el a tesztelési idő tipp összeg információk felfedése. Ezekben az oszlopokban használatával eltávolítása a [Select Columns in Dataset] [ select-columns] modul.

A pillanatkép belows a kísérletet, és előre jelezni az adott tipp mennyiségét jeleníti meg.

![Kísérlet pillanatkép](./media/hive-walkthrough/11TZWgV.png)

b. A regresszió problémákat a az előrejelzési pontosság az előrejelzés, döntési együttható és hasonló squared hiba megtekintésével mérjük. Megmutatjuk, ezek az alábbi.

![Előrejelzés statisztikai](./media/hive-walkthrough/Jat9mrz.png)

Azt látja, hogy a együttható kapcsolatos 0.709, körülbelül 71 % varianciája úgy magyarázza a modell együttható.

> [!IMPORTANT]
> Az Azure Machine Learning és elérése, és ezzel kapcsolatos további tudnivalókért tekintse meg [Mi az Machine Learning](../studio/what-is-machine-learning.md). A Machine Learning kísérleteket Azure Machine Learning szolgáltatásban egy csoportját játszik nagyon hasznos erőforrás a [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com/). A gyűjtemény egy kísérletek skáláját ismerteti, és az Azure Machine Learning képességek körének való alapos bevezetést tartalmaz.
> 
> 

## <a name="license-information"></a>Licencinformációk
Ez a minta a forgatókönyv és a hozzá tartozó szkriptek osztanak meg a Microsoft a MIT licence. Ellenőrizze a LICENSE.txt fájl a mintakódot a Githubon további részleteket.

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

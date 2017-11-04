---
title: "Hozza létre, és a gépi tanulási modellek használata az SQL Server egy Azure virtuális gépen telepíteni |} Microsoft Docs"
description: "Bővített Analitikát folyamat és a technológia, működés közben"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 6066b083-262c-4453-a712-a5c05acc3df8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: fashah;bradsev
ms.openlocfilehash: d42377a55b1decc0918932b3ecc13cf575f934a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>A művelet az Team tudományos folyamat: SQL Server használata
Az oktatóanyag ismerteti a folyamatot, amely létrehozása és telepítése a gépi tanulási modellek SQL Server és a nyilvánosan elérhető dataset – a [NYC Taxi Utazgatással](http://www.andresmh.com/nyctaxitrips/) adatkészlet. Az eljárást követi a szabványos adatelemezési munkafolyamatot: betöltési, és áttekintheti az adatokat, a visszafejtés szolgáltatások megkönnyítése tanulási, majd építsenek, és a modell rendszerbe állítása.

## <a name="dataset"></a>NYC Taxi utazás közben adatkészlet leírása
A következőt: Taxi út adatok körülbelül 20GB tömörített CSV-fájlok (tömörítetlen ~ 48GB), több mint 173 millió egyedi való adatváltások számát és a vitel kifizette minden út. Minden út rekord tartalmazza a felvétel és Gyűjtőtár hely és idő, anonimizált rejthetők el (illesztőprogram) engedély száma és medallion (taxi tartozó egyedi azonosító) számát. Az adatok minden való adatváltások számát ismerteti az év 2013, és minden hónap a következő két adatkészletet találhatók:

1. A "trip_data" CSV út részletek, például a utasok, a felvételi és dropoff pontok, út időtartama és út hossza tartalmazza. Íme néhány példa rekordok:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. A "trip_fare" CSV a jegy ára kifizette minden út, például a fizetési mód, jegy ára összeg, emelt díjas és adókat, tippeket és autópályadíjak, és a teljes összeg fizetős részleteit tartalmazza. Íme néhány példa rekordok:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Egyedi kulcs út csatlakozni\_adatok és út\_jegy ára tevődnek össze a mezők: medallion, rejthetők el\_engedély és a felvételi\_dátum és idő.

## <a name="mltasks"></a>Előrejelzés feladatok példák
Azt fogalmaz meg három előrejelzés problémák alapján a *tipp\_összeg*, nevezetesen:

1. Bináris osztályozás: előre jelezni, függetlenül attól, tipp kifizetett utazás, azaz egy *tipp\_összeg* nagyobb, mint 0 egy pozitív példában látható, miközben egy *tipp\_összeg* $ 0 van egy negatív példa.
2. Multiclass osztályozás: a út kifizette tipp számos előre jelezni. Azt a osztani a *tipp\_összeg* öt bins vagy osztályok:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. Regressziós feladat: megjósolható a fizetős útnak tipp mennyisége.  

## <a name="setup"></a>A beállítás mentése az Azure data tudományos környezet speciális elemzésekre
A látható a [a környezet megtervezése](plan-your-environment.md) útmutató, több lehetőség működéséhez a NYC Taxi való adatváltások számát az adatkészlethez az Azure-ban:

* Az Azure-blobokat adatokat, majd a modellt az Azure Machine Learning használata
* Az adatok betöltése az SQL Server-adatbázist, majd a modellt az Azure Machine Learning

Ebben az oktatóanyagban a párhuzamos tömeges importálással az adatok egy SQL Server, az adatok feltárása, a szolgáltatás bemutatjuk mérnöki mintavételi le SQL Server Management Studio használatával, valamint IPython Notebook használatával. [Minta parancsfájlok](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) és [IPython notebookok](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) megosztott a Githubon. Az adatok az Azure-blobokat minta IPython jegyzetfüzet érhető el ugyanazon a helyen.

Az Azure Adattudomány környezet beállítása:

1. [Tárfiók létrehozása](../../storage/common/storage-create-storage-account.md)
2. [Az Azure Machine Learning-munkaterület létrehozása](../studio/create-workspace.md)
3. [Rendszerű virtuális gép adatok tudományos](../data-science-virtual-machine/setup-sql-server-virtual-machine.md), amely biztosítja, hogy egy SQL Server és IPython Notebook kiszolgáló.
   
   > [!NOTE]
   > A mintaparancsfájlok és IPython notebookok a rendszer letölti a Adattudomány virtuális géphez a telepítés során. A virtuális gép telepítés utáni parancsfájl befejezése után a virtuális gép dokumentumtár lehet, a mintákat:  
   > 
   > * Minta parancsfájlok:`C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * A minta IPython notebookok:`C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   Ha `<user_name>` a virtuális gép Windows bejelentkezési név. A minta mappák hivatkozik **mintaparancsfájlok** és **minta IPython notebookok**.
   > 
   > 

Az adatkészlet méretének adatforrásról és a kijelölt Azure célkörnyezet alapján, ebben a forgatókönyvben hasonlít [forgatókönyv \#5: nagy adatkészlet egy helyi fájlok, SQL Server Azure virtuális gép cél](plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Beolvassa az adatokat nyilvános forráskódú
A beolvasandó a [NYC Taxi Utazgatással](http://www.andresmh.com/nyctaxitrips/) dataset nyilvános helyéről, előfordulhat, hogy használja ismertetett módszerek valamelyikét [adatok áthelyezése, és az Azure Blob Storage](move-azure-blob.md) az adatok másolása az új virtuális gép.

Másolja az AzCopy segítségével adatokat:

1. Jelentkezzen be a virtuális gép (VM)
2. Hozzon létre egy új könyvtárat a virtuális gép adatlemez (Megjegyzés: ne használja a ideiglenes lemez, amely a virtuális Gépet állítsanak adatlemezt).
3. Egy parancssori ablakot futtassa a következő Azcopy parancssori, a (2) létrehozott Adatmappa < path_to_data_folder > cseréje:
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    Az AzCopy befejezését követően a rendszer összesen 24 zip-e a CSV-fájlok (12-út\_adatok és a 12-út\_jegy ára) az adatok mappában kell lennie.
4. Bontsa ki a letöltött fájlokat. Vegye figyelembe a mappát, ahol a kibontott fájlok találhatók. Ebben a mappában kell lennie az úgynevezett az < elérési út\_való\_adatok\_fájlok\>.

## <a name="dbload"></a>A tömeges adatok importálása az SQL Server-adatbázisba
Betöltés/átvitele a nagy mennyiségű adat egy SQL-adatbázis és a lekérdezések teljesítményét használatával növelhető *particionált táblák és nézetek*. Ebben a szakaszban leírt utasításokat követi azt [párhuzamos tömeges adatok importálása használatával SQL partíciós táblákba](parallel-load-sql-partitioned-tables.md) hozzon létre egy új adatbázist, és az adatok betöltése az párhuzamosan particionált táblákat.

1. A virtuális Géphez való bejelentkezés, indítsa el a **SQL Server Management Studio**.
2. Csatlakozás a Windows-hitelesítés használatával.
   
    ![SSMS csatlakozás][12]
3. Ha még nem változott az SQL Server hitelesítési mód, és létrehozott egy új SQL-bejelentkezési felhasználójának, nyissa meg a parancsfájl nevű **módosítása\_auth.sql** a a **mintaparancsfájlok** mappát. Az alapértelmezett felhasználónév és jelszó módosítása Kattintson a **! Végrehajtás** az eszköztáron a parancsfájl futtatásához.
   
    ![Parancsfájlok végrehajtását][13]
4. Győződjön meg arról, és/vagy mappák módosítása, az SQL Server alapértelmezett adatbázishoz és naplófájlokhoz biztosításához az újonnan létrehozott adatbázisok adatlemezt fogja tárolni. Az SQL Server Virtuálisgép-lemezkép datawarehousing terhelések optimalizált előre konfigurált adatainak és naplókönyvtárainak lemezzel. Ha a virtuális gép nem tartalmazza az adatlemezt, és a Virtuálisgép-beállítási folyamata során hozzáadott új virtuális merevlemezeket, módosítsa az alábbiak szerint az alapértelmezett mappák:
   
   * Kattintson a jobb gombbal a bal oldali panelen az SQL Server nevét, és kattintson a **tulajdonságok**.
     
       ![SQL-kiszolgáló tulajdonságai][14]
   * Válassza ki **adatbázis beállításainak** a a **oldal kijelölése** a bal oldali listában.
   * Ellenőrizze és/vagy módosításához a **adatbázis alapértelmezett helyek** való a **adatlemez** az Ön által választott helyen. Ez azért, ahol új adatbázisok találhatók, ha az alapértelmezett hely beállításokkal hozza létre.
     
       ![SQL-adatbázis alapértelmezett értéke][15]  
5. Hozzon létre egy új adatbázist és a fájlcsoport ahhoz, hogy a particionált táblák egy készlete, nyissa meg a parancsfájlpéldát **létrehozása\_db\_default.sql**. A parancsfájl létrehoz egy új adatbázist nevű **TaxiNYC** és az alapértelmezett hely a 12 fájlcsoportokat. Minden fájlcsoport tárolására egy hónap út\_adatok és út\_díjszabás adatokat. Ha szükséges, módosítsa az adatbázis nevét. Kattintson a **! Végrehajtás** a parancsfájl futtatásához.
6. Ezután hozzon létre egyet a út két partíciós táblákba\_adatokat, majd egy másikat a út\_jegy ára. Nyissa meg a parancsfájlpéldát **létrehozása\_particionált\_table.sql**, amelynél:
   
   * Az adatok hónap szerint leválasztására partíciós függvény létrehozása.
   * Minden hónap adatok leképezése más fájlcsoportba partícióséma létrehozása.
   * Hozzon létre két particionált tábla partíciós sémája leképezve: **nyctaxi\_út** út tárolására\_adatok és **nyctaxi\_jegy ára** úttárolására\_díjszabás adatokat.
     
     Kattintson a **! Végrehajtás** futtassa a parancsfájlt, és hozzon létre a particionált táblákat.
7. Az a **mintaparancsfájlok** mappa, két minta PowerShell-parancsfájlok párhuzamos tömeges importálja az adatokat a SQL Server-táblákra bemutatásához megadott van.
   
   * **BCP\_párhuzamos\_generic.ps1** egy általános parancsprogram párhuzamos tömeges adatok importálása egy táblába. Módosítsa ezt a parancsfájlt a bemeneti és a cél változók megadása a Megjegyzés sorok, a parancsfájl jelöltük.
   * **BCP\_párhuzamos\_nyctaxi.ps1** parancsfájl előre konfigurált verziójú, a két tábla a NYC Taxi Utazgatással adatok betöltése a használható.  
8. Kattintson a jobb gombbal a **bcp\_párhuzamos\_nyctaxi.ps1** parancsfájl nevét, és kattintson **szerkesztése** PowerShell megnyitható. Az előre változóival áttekintheti és módosíthatja a kijelölt adatbázis neve, a bemeneti adatok mappa, a célmappa napló és a a minta formátumú fájlok elérési útjait alapján **nyctaxi_trip.xml** és **nyctaxi\_fare.xml** (a megadott a **mintaparancsfájlok** mappa).
   
    ![A tömeges adatok importálása][16]
   
    A hitelesítési módot is kiválaszthat, alapértelmezett Windows-hitelesítés. A zöld nyílra az eszköztárban futtatásához. A parancsfájl 24 tömeges importálási műveletek párhuzamos, 12 particionált táblák elindul. Nyissa meg az SQL Server alapértelmezett Adatmappa fenti beállított figyelheti előfordulhat, hogy az adatok importálása folyamatban.
9. A PowerShell parancsfájl azt jelenti a kezdési és befejezési időpontja. Összes tömeges teljes importálja, amikor a befejezési időt jelenti. Ellenőrizze a célmappában naplóban ellenőrizheti, hogy a tömeges importálása sikeres volt, azaz, nincs hiba a napló célmappában.
10. Az adatbázis készen áll a feltárása, a szolgáltatás tervezés és egyéb műveletek. Mivel a táblák használata particionálja a következők szerint a **a felvételi\_datetime** mezőben, a lekérdezések, többek között **a felvételi\_dátum és idő** feltételek a **ahol** záradékot használják ki a partíciós sémája előnyeit.
11. A **SQL Server Management Studio**, megismerkedhet a megadott minta parancsfájlt **minta\_queries.sql**. A lekérdezés futtatásához jelölje ki a lekérdezési sorok, majd kattintson az **! Végrehajtás** az eszköztáron.
12. A NYC Taxi Utazgatással adatok betöltése két külön táblázatban. Összekapcsolási műveletek javítása érdekében javasoljuk a táblák indexelésre. A parancsfájlpéldát **létrehozása\_particionált\_index.sql** hoz létre a particionált indexek összetett illesztési kulcs **medallion, rejthetők el\_licenc, és a felvételi\_ dátum és idő**.

## <a name="dbexplore"></a>Az adatok feltárása és az SQL Server szolgáltatás manipuláció
Ebben a szakaszban végezzük el adatok feltárása és a szolgáltatás generálása közvetlenül az SQL-lekérdezések futtatásával a **SQL Server Management Studio** korábban létrehozott az SQL Server-adatbázis segítségével. Egy minta parancsfájlt nevű **minta\_queries.sql** találhatók a **mintaparancsfájlok** mappát. Módosítsa a parancsfájlt úgy módosítsa az adatbázisnevet, ha az alapértelmezettől eltérő: **TaxiNYC**.

Ebben a gyakorlatban a következő történik:

* Csatlakozás **SQL Server Management Studio** vagy Windows-hitelesítést vagy SQL-hitelesítést és az SQL-bejelentkezési név és jelszó használatával.
* Megismerkedhet a különböző idő windows néhány mezőinek disztribúciók adatok.
* Vizsgálja meg a szélességi és hosszúsági mezők adatok minőségét.
* Multiclass és bináris besorolási címkék alapján készítése a **tipp\_összeg**.
* Szolgáltatások létrehozása és számítási/összehasonlítása út távolság.
* Csatlakozás a két tábla, és bontsa ki a modellek létrehozásához használt véletlenszerű minta.

Ha készen áll az Azure Machine Learning folytatja, akkor előfordulhat, hogy vagy:  

1. A végső SQL lekérdezés kibontása és az az adatokat és a másolás-beillesztés közvetlenül a lekérdezés mentéséhez egy [és adatokat importálhat] [ import-data] modul az Azure Machine Learning, vagy
2. A mintában szereplő megmaradnak, és a modell egy új adatbázis létrehozásához használni kívánt visszafejtett adatok tábla, és az új táblázat a [és adatokat importálhat] [ import-data] az Azure Machine Learning modulban.

Ebben a szakaszban bontsa ki, és az adatokat az utolsó lekérdezési menti azt. A második módszer mutatják be a [adatok feltárása és IPython jegyzetfüzet mérnöki szolgáltatás](#ipnb) szakasz.

A sorok és oszlopok a táblázatokban fel korábban segítségével párhuzamos tömeges importálással számának gyors ellenőrzés

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Feltárása: Út elosztása medallion szerint
Ebben a példában a medallion (taxi számok) azonosítja a 100-nál több való adatváltások számát egy adott időtartamon belül. A lekérdezés előnyös a particionált tábla hozzáférés óta, akkor annak partíciós sémája által **a felvételi\_datetime**. A teljes adatkészlet lekérdezése is teszi a particionált tábla használja, és/vagy a vizsgálat index.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Feltárása: Út terjesztési medallion és hack_license
    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Adatok vizsgálatának: Helytelen hosszúsági és/vagy szélességi rekordjainak ellenőrzése
Ez a példa pedig megvizsgálja, ha a hosszúsági és/vagy szélességi mezőinek vagy érvénytelen értéket tartalmazza (radián fok -90 és 90 között kell lennie), vagy (0, 0) koordinátarendszerében.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Feltárása: A vs Formabontó. Nem Formabontó Utazgatással terjesztési
Ebben a példában megkeresi a száma, amelyek volt Formabontó és időszak (vagy a teljes adatkészletet, ha a teljes évre vonatkozó) egy adott idő alatt nem Formabontó való adatváltások számát. Ehhez a terjesztéshez a bináris címke terjesztési használandó később bináris osztályozási modellezési tükrözi.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Feltárása: Az osztály vagy tartomány terjesztési tipp
Ebben a példában kiszámítja a terjesztési tipp tartományait egy adott időszakban (vagy a teljes adatkészletet, ha a teljes évre vonatkozó). Ez az a terjesztési később használandó multiclass besorolás modellezési címke osztályok.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

#### <a name="exploration-compute-and-compare-trip-distance"></a>Feltárása: A számítási, és hasonlítsa össze a út távolság
Ebben a példában a felvétel és Gyűjtőtár hosszúság alakítja át, és SQL a földrajzi szélesség mutat, út távolság SQL geográfiai pontok különbség használatával kiszámítja és visszaadja az eredmények összehasonlítása a véletlenszerű minta. A példa az eredményeket a minőségi assessment lekérdezést kezelt korábban csak a érvényes koordináták korlátozza.

    SELECT
    pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
    ,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
    ,trip_distance
    ,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
    FROM nyctaxi_trip
    tablesample(0.01 percent)
    WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### <a name="feature-engineering-in-sql-queries"></a>Az SQL-lekérdezések mérnöki szolgáltatás
A címke és a földrajzi hely átalakítás feltárása lekérdezések is használható a számlálási eltávolításával címkék/szolgáltatások létrehozásához. A szolgáltatás további mérnöki SQL példák találhatók: a [adatok feltárása és IPython jegyzetfüzet mérnöki szolgáltatás](#ipnb) szakasz. Sokkal hatékonyabban, a szolgáltatás generációs lekérdezések futtatását a teljes adatkészlethez vagy egy nagy részét, közvetlenül a SQL Server adatbázis-példány az SQL-lekérdezések használatával. Előfordulhat, hogy a lekérdezések végrehajtani **SQL Server Management Studio**, IPython Notebook vagy semmilyen eszköz/fejlesztőkörnyezet amely helyileg vagy távolról hozzáférhessen az adatbázishoz.

#### <a name="preparing-data-for-model-building"></a>Adatok előkészítése az modell létrehozásának
Az alábbi lekérdezés illesztések a **nyctaxi\_út** és **nyctaxi\_jegy ára** táblák, állít elő, a bináris besorolási címkék **Formabontó**, egy több osztály besorolási címke **tipp\_osztály**, és 1 % véletlenszerűen kibontja a teljes illesztett adatkészletből. Ez a lekérdezés másolja, majd a beillesztett közvetlenül a [Azure Machine Learning Studio](https://studio.azureml.net) [és adatokat importálhat] [ import-data] közvetlen adatfeldolgozást az SQL Server adatbázis-modul az Azure-példányt. A lekérdezés nem tartalmazza a rekordok helytelen (0, 0) koordinátarendszerében.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 percent)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="ipnb"></a>Az adatok feltárása, a szolgáltatás fejlesztés IPython jegyzetfüzet
Ebben a szakaszban végezzük el a Python és az SQL lekérdezések írásában, korábban létrehozott SQL Server-adatbázis használata a szolgáltatás generálása és az adatok feltárása. Egy minta IPython notebook nevű **machine-Learning-data-science-process-sql-story.ipynb** találhatók a **minta IPython notebookok** mappa. A notebook is rendelkezésre áll, a [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

Ajánlott sorrendje a következő big Data típusú adatok használata esetén:

* Olvassa el az adatok kis mintában egy a memóriában levő keretbe.
* Néhány képi megjelenítések és explorations a mintaadatokat használatával hajtható végre.
* Szolgáltatás műszaki osztály használata a mintaadatokat kísérletezhet.
* Nagyobb az adatok feltárása, adatkezelési és a szolgáltatás mérnöki csapathoz használja az SQL-lekérdezések ki az Azure virtuális gép az SQL Server adatbázison Python.
* Döntse el, a minta mérete az Azure Machine Learning modell létrehozásának használandó.

Ha készen áll az Azure Machine Learning folytatja, akkor előfordulhat, hogy vagy:  

1. A végső SQL lekérdezés kibontása és az az adatokat és a másolás-beillesztés közvetlenül a lekérdezés mentéséhez egy [és adatokat importálhat] [ import-data] az Azure Machine Learning modulban. Ez a módszer mutatják be a [épület modellek az Azure Machine Learning](#mlmodel) szakasz.    
2. Továbbra is fennáll a mintában szereplő és visszafejtett adatokat az új adatbázistábla létrehozása modell használatát tervezi, akkor az új táblázat a [és adatokat importálhat] [ import-data] modul.

Néhány adatok feltárása, adatábrázolási és a szolgáltatás műszaki osztály példák az alábbiakban. További példákért lásd: a minta SQL IPython jegyzetfüzetet a **minta IPython notebookok** mappa.

#### <a name="initialize-database-credentials"></a>Adatbázis-hitelesítő adatok inicializálása
Az adatbázis-kapcsolati beállításokat a következő változók inicializálása:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Adatbázis-kapcsolat létrehozása
    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Sorok és oszlopok a tábla nyctaxi_trip jelentés száma
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('nyctaxi_trip')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('nyctaxi_trip')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* A sorok teljes számát = 173179759  
* Az oszlopok teljes száma = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Olvassa el a kisméretű minta az SQL Server-adatbázisból
    t0 = time.time()

    query = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (0.05 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

6.492000 másodpercben a mintatáblázat olvasási idő  
A beolvasott sorok és oszlopok száma = (84952, 21)

#### <a name="descriptive-statistics"></a>Leíró statisztika
Most már készen áll a mintában szereplő adatokba. Először a leíró statisztika megnézi a **út\_távolság** (vagy bármely más) mezőből:

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>A képi megjelenítés: Rajzot – példa
Ezután úgy tekintünk a Dobozdiagram a megjelenítéséhez a quantiles út távolság

    df1.boxplot(column='trip_distance',return_type='dict')

![Tőzsdei #1][1]

#### <a name="visualization-distribution-plot-example"></a>A képi megjelenítés: Terjesztési rajzot – példa
    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Tőzsdei #2][2]

#### <a name="visualization-bar-and-line-plots"></a>A képi megjelenítés: Sáv és sor előkészítésére
Ebben a példában azt az öt bins út távolság bin és a binning eredményeinek képi megjelenítése.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

A Microsoft a fenti bin terjesztési egy sávon megrajzolásához vagy sor a következő ábra

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![#3 ábrázolása][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![#4 ábrázolása][4]

#### <a name="visualization-scatterplot-example"></a>A képi megjelenítés: Scatterplot – példa
Pontdiagram rajzot közötti megmutatjuk **út\_idő\_a\_másodperc** és **út\_távolság** van-e bármilyen korrelációs

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![#6 ábrázolása][6]

Hasonlóképpen azt is ellenőrizze közötti kapcsolat **arány\_kód** és **út\_távolság**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![#8 ábrázolása][8]

### <a name="sub-sampling-the-data-in-sql"></a>Mintavétel a SQL-adatok
Adatok létrehozása modell előkészítésekor [Azure Machine Learning Studio](https://studio.azureml.net), vagy dönthet a a **SQL-lekérdezésben használandó közvetlenül az adatok importálása modul** vagy megőrizni a visszafejtett és mintavételezett adatokat az új táblázatot, amely a használhatja a [és adatokat importálhat] [ import-data] modul egy egyszerű **kiválasztása * FROM < a\_új\_tábla\_neve >** .

Ebben a szakaszban létrehozunk egy új tábla a mintában szereplő és visszafejtett adatok tárolásához. Például egy közvetlen SQL-lekérdezés a modell létrehozásának megtalálható a [adatok feltárása és az SQL Server mérnöki szolgáltatás](#dbexplore) szakasz.

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Hozzon létre egy minta tábla és 1 % illesztett tábla feltöltéséhez. Eldobni a tábla első, ha van ilyen.
Ez a szakasz azt csatlakoztassa a táblák **nyctaxi\_út** és **nyctaxi\_jegy ára**, bontsa ki a 1 % véletlenszerűen és megőrizni a mintaadatokat egy új tábla nevében  **nyctaxi\_egy\_százalék**:

    cursor = conn.cursor()

    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''

    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
        INTO nyctaxi_one_percent
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (1 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
        AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''

    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Az SQL-lekérdezések IPython Notebook használatával az adatok feltárása
Ebben a részben azt megismerkedhet a mintát 1 % adatait, amely a fenti létrehozott új tábla megőrződjenek használatával adatokat terjesztéseket. Vegye figyelembe, hogy hasonló explorations használatával végezheti el az eredeti táblák, külön kérésre mintázatot használ **TABLESAMPLE** korlátozni a feltárási minta vagy, ha a eredményeket egy adott időszak használatával korlátozza a **felvételi\_dátum és idő** partíciók, amint a [adatok feltárása és az SQL Server mérnöki szolgáltatás](#dbexplore) szakasz.

#### <a name="exploration-daily-distribution-of-trips"></a>Feltárása: Való adatváltások számát napi eloszlásáról
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Feltárása: Utazás per medallion terjesztési
    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Az SQL-lekérdezések használatával IPython jegyzetfüzet szolgáltatás létrehozása
Ez a szakasz az új címkék fog létrehozni, és közvetlenül az SQL-lekérdezések használatával szolgáltatások, az 1 % mintatáblázat működő létrehozott az előző szakaszban.

#### <a name="label-generation-generate-class-labels"></a>Címke generációs: Osztály címkék létrehozása
A következő példában azt két készlet modellezési használandó címkék létrehozása:

1. Bináris osztály címkék **Formabontó** (ha tipp kapnak becslése)
2. Multiclass címkék **tipp\_osztály** (becslése a tipp bin vagy a tartomány)
   
        nyctaxi_one_percent_add_col = '''
            ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
        '''
   
        cursor.execute(nyctaxi_one_percent_add_col)
        cursor.commit()
   
        nyctaxi_one_percent_update_col = '''
            UPDATE nyctaxi_one_percent
            SET
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''
   
        cursor.execute(nyctaxi_one_percent_update_col)
        cursor.commit()

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>A szolgáltatás műszaki osztály: Száma szolgáltatások Kategorikus oszlopokhoz
Ebben a példában egy numerikus mezőbe kategorikus mező átalakítja azáltal, hogy minden kategória az adatok az előfordulások száma.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B AS
        (
            SELECT medallion, hack_license,
                SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
                SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
            FROM nyctaxi_one_percent
            GROUP BY medallion, hack_license
        )

        UPDATE nyctaxi_one_percent
        SET nyctaxi_one_percent.cmt_count = B.cmt_count,
            nyctaxi_one_percent.vts_count = B.vts_count
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion AND A.hack_license = B.hack_license
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>A szolgáltatás műszaki osztály: Bin szolgáltatások numerikus oszlopokhoz
Ebben a példában egy folyamatos számmező átalakítja az előre definiált kategória tartományokat, azaz átalakító számmező kategorikus mező be azokat.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
        (
            SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
            NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
        )

        UPDATE nyctaxi_one_percent
        SET trip_time_bin = B.BinNumber
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion
        AND A.hack_license = B.hack_license
        AND A.pickup_datetime = B.pickup_datetime
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>A szolgáltatás műszaki osztály: Hely szolgáltatások kinyerése decimális szélesség/hosszúság
Ebben a példában megszakad a szélességi és/vagy a hosszúság mezője decimális ábrázolása több régióban mezőibe eltérő a granularitási, többek között ország város, város, letiltása, stb. Vegye figyelembe, hogy az új földrajzi mezők nincsenek leképezve: a tényleges helyekre. Leképezési geocode helyeken további információkért lásd: [Bing Maps REST szolgáltatások](https://msdn.microsoft.com/library/ff701710.aspx).

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent
        ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
            l5 varchar(3), l6 varchar(3), l7 varchar(3)
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET l1=round(pickup_longitude,0)
            , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
            , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
            , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
            , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
            , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
            , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Ellenőrizze a featurized tábla végleges formájában
    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Azt készen áll a modell létrehozásának és a modell telepítési [Azure Machine Learning](https://studio.azureml.net). Az adatok készen áll a korábban azonosított nevezetesen előrejelzés problémák bármelyikét:

1. Bináris osztályozás: előre jelezni-e tipp kifizetett egy út.
2. Multiclass besorolási: megjósolható a fizetős, a korábban meghatározott osztályba tipp tartományán.
3. Regressziós feladat: megjósolható a fizetős útnak tipp mennyisége.  

## <a name="mlmodel"></a>Az Azure Machine Learning modellek létrehozása
A modellezési gyakorlat megkezdéséhez jelentkezzen be az Azure Machine Learning munkaterülettel. Ha még nem hozott létre machine learning-munkaterület, lásd: [hozzon létre egy Azure Machine Learning munkaterülettel](../studio/create-workspace.md).

1. Ismerkedés az Azure Machine Learning, lásd: [Mi az Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Jelentkezzen be [Azure Machine Learning Studio](https://studio.azureml.net).
3. A Studio kezdőlap modulok hivatkozást, és más erőforrások számos olyan információt, videók, oktatóanyagok, valamint hivatkozásokat biztosít. Fore Azure Machine Learning kapcsolatos további információkért tekintse meg a [Azure Machine Learning dokumentációs központban](https://azure.microsoft.com/documentation/services/machine-learning/).

Egy tipikus tanítási kísérletet a következőkből áll:

1. Hozzon létre egy **+ új** kipróbálásához.
2. Az Azure Machine Learning adatokat beolvasni.
3. Előre feldolgozzák, átalakítására, és szükség esetén az adatok kezelését.
4. Szolgáltatások létrehozása, igény szerint.
5. Az adatok felosztása adatkészletek képzési/érvényesítési/tesztelése (külön adatkészleteket, vagy az egyes).
6. Válasszon egy vagy több gépi tanulási algoritmusok attól függően, hogy a tanulási probléma megoldására. Például bináris osztályozási multiclass besorolás, regressziós.
7. A képzési adatkészlet egy vagy több modell betanításához.
8. Az érvényesítési adatkészletet a betanított modellek segítségével pontozása.
9. Értékelje ki a megfelelő metrikákat a tanulási probléma kiszámításához modellek.
10. Konfigurálva finomhangolhatják a modellek, és válassza ki a legjobb központilag telepítendő modell.

Ebben a gyakorlatban azt már megismerkedett és fejthetők vissza az adatokat az SQL Server, és a minta mérete a úgy döntött, hogy az Azure Machine Learning betöltési. Egy vagy több döntöttünk előrejelzési modellek létrehozásához:

1. Az adatok Azure Machine Learning segítségével a [és adatokat importálhat] [ import-data] modul érhető el a **bemeneti és kimeneti** szakasz. További információkért lásd: a [és adatokat importálhat] [ import-data] modul referencialapja.
   
    ![Az Azure Machine Learning-adatok beolvasása][17]
2. Válassza ki **Azure SQL Database** , a **adatforrás** a a **tulajdonságok** panel.
3. Az adatbázis DNS-nevét adja meg a **adatbázis-kiszolgáló neve** mező. Formátum:`tcp:<your_virtual_machine_DNS_name>,1433`
4. Adja meg a **adatbázisnév** a megfelelő mezőben.
5. Adja meg a **SQL felhasználónév** a a ** Server aqccount felhasználónevet és a jelszót a **kiszolgáló felhasználói fiók jelszavát**.
6. Ellenőrizze **fogadja el a kiszolgálói tanúsítvány** lehetőséget.
7. Az a **adatbázis-lekérdezés** szöveg terület szerkesztése, illessze be a lekérdezést, amely a szükséges adatbázis-mezők (beleértve a számított mezőket a címkéket például) és régebbi minták a kívánt mintájának méretét az adatokat.

Az alábbi ábra egy példát egy bináris osztályozási kísérletet, közvetlenül az SQL Server adatbázisából az adatok olvasása van. Hasonló kísérletek multiclass besorolás és a visszavonási lehet létrehozni.

![Az Azure Machine Learning vonat][10]

> [!IMPORTANT]
> A modellezési adatok kinyerése és a mintavételi lekérdezés példák korábbi szakaszokban találhatók **összes címkéket a három modellezési gyakorlatok szerepelnek a lekérdezés**. Minden a modellezési gyakorlatok (kötelező) fontos lépés, hogy **kizárása** a szükségtelen címkék a más két problémákat, és bármely más **céloz kiszivárgásának**. Például bináris osztályozási használata esetén használja a címke **Formabontó** , és amelyeket a mezők **tipp\_osztály**, **tipp\_összeg**, és **teljes\_összeg**. Az utóbbi cél kiszivárgásának óta azt tartalmazzák a tipp fizetett.
> 
> Zárja ki a felesleges oszlopok és/vagy a cél kiszivárgásának, használhatja a [Select Columns in Dataset] [ select-columns] modul vagy a [szerkesztése metaadatok][edit-metadata]. További információkért lásd: [Select Columns in Dataset] [ select-columns] és [szerkesztése metaadatok] [ edit-metadata] lapok hivatkoznak.
> 
> 

## <a name="mldeploy"></a>Az Azure Machine Learning modellek telepítéséről
Amikor készen áll a modell, könnyedén telepítheti azt egy webszolgáltatás-ről a kísérlet. További információ az Azure Machine Learning webszolgáltatások üzembe helyezése: [központi telepítése az Azure Machine Learning webszolgáltatás](../studio/publish-a-machine-learning-web-service.md).

Egy új webszolgáltatás-bővítmény telepítéséhez kell:

1. A pontozási kísérlet létrehozása.
2. A webszolgáltatás üzembe helyezése.

Egy pontozási kísérletet a létrehozásához egy **befejezett** betanítása kísérletet, kattintson a **létrehozása pontozási KÍSÉRLETEZHET** alacsonyabb műveletsávon.

![Pontozás az Azure][18]

Az Azure Machine Learning megpróbál létrehozni egy pontozási kísérletet, a tanítási kísérletet összetevői alapján. Különösen a következőket hajtja végre:

1. A betanított modell elmentésével, és távolítsa el a modell képzési modulok.
2. Azonosítsa a logikai **bemeneti port** a szükséges bemeneti adatok séma képviseli.
3. Azonosítsa a logikai **kimeneti port** képviselő a várt webes szolgáltatás kimeneti sémával.

A pontozási kísérlet jön létre, amikor ellenőrzésre, majd szükség szerint állítsa be. Egy tipikus módosítása, hogy cserélje le a bemeneti adatkészlet és/vagy lekérdezési egyet, amely kizárja a címke mezők, mivel ezek nem lesz elérhető a szolgáltatás meghívásakor. Azt is néhány rögzíti, hogy a bemeneti adatkészlet és/vagy lekérdezési méretének csökkentése érdekében célszerű éppen elegendő mértékű jelzi a bemeneti sémát. A kimeneti port esetében gyakori, hogy kizárja az összes beviteli mezőt, és csak a **pontozott címkék** és **program pontozza a mennyiségeket valószínűség** a kimeneti használatával a [Select Columns in Dataset] [ select-columns] modul.

Az alábbi ábra kísérlet pontozási minta van. Amikor készen áll a központi telepítése, kattintson a **WEBSZOLGÁLTATÁS közzététele** alacsonyabb műveletsávon gombra.

![Az Azure gépi tanulás közzététele][11]

A recap, ez a forgatókönyv az oktatóanyagban az Azure data tudományos környezethez, egészen a modellek betanítása és egy Azure Machine Learning webszolgáltatás üzembe helyezése adatgyűjtést nagy nyilvános dataset dolgozott hozott létre.

### <a name="license-information"></a>Licencinformációk
Ez a minta forgatókönyv és a mellékelt parancsfájlok és IPython notebook(s) által megosztott Microsoft alatt a MIT licenccel. Ellenőrizze a LICENSE.txt fájlt további részletekért a Githubon mintakódot a címtárban.

### <a name="references"></a>Referencia
• [Andrés Monroy NYC Taxi utak letöltési oldala](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC taxiköltség út adatok Chris Whong által](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi és Limousine Bizottság kutatási és statisztika](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

[1]: ./media/sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sql-walkthrough/azuremltrain.png
[11]: ./media/sql-walkthrough/azuremlpublish.png
[12]: ./media/sql-walkthrough/ssmsconnect.png
[13]: ./media/sql-walkthrough/executescript.png
[14]: ./media/sql-walkthrough/sqlserverproperties.png
[15]: ./media/sql-walkthrough/sqldefaultdirs.png
[16]: ./media/sql-walkthrough/bulkimport.png
[17]: ./media/sql-walkthrough/amlreader.png
[18]: ./media/sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

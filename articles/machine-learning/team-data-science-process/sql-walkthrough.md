---
title: Létrehozása és üzembe helyezése egy gépi tanulási modellt az SQL Server-beli virtuális gépen |} A Microsoft Docs
description: Fejlett analitikai folyamat és technológia, működés közben
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 6066b083-262c-4453-a712-a5c05acc3df8
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: deguhath
ms.openlocfilehash: 89fb805ac64be358ed4f52865d2e89ab16fe2b17
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005798"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>A csoportos adatelemzési folyamat működés közben: az SQL Server használata
Ebben az oktatóanyagban vezeti végig a folyamat létrehozásának és üzembe helyezésének egy gépi tanulási modellt az SQL Server és a egy nyilvánosan elérhető adatkészlet használatával – a [NYC Taxi lelassítja](http://www.andresmh.com/nyctaxitrips/) adatkészlet. Az eljárást követi a szokásos adatelemzési munkafolyamathoz: fogadni, és Fedezze fel az adatokat, Funkciók tervezése, tanulás egyszerűbbé tételével majd hozhat létre és helyezhet üzembe modelleket.

## <a name="dataset"></a>NYC Taxi léptető adatkészlet leírása
A NYC Taxi útadatok körülbelül 20GB tömörített CSV-fájlok (~ 48GB tömörítetlen), minden egyes út 173 milliónál egyes utak és a vitel magában foglaló fizetni. Minden egyes út rekord tartalmazza a begyűjtést és Gyűjtőtár hely és idő, anonimizált feltörés (illesztőprogramok) licencszám és medallion (taxi az egyedi azonosító) számát. Az adatok minden lelassítja ismerteti az év 2013-hoz, és minden hónapban megtalálható a következő két adatkészletet:

1. "Trip_data" CSV trip részleteit, például az utasok, begyűjtést és dropoff pontok, út időtartama és út hossza számát tartalmazza. Az alábbiakban néhány példa rekordokat:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. A "trip_fare" CSV a diszkont fizetett minden út, például a fizetési típus, diszkont összeg, pótdíj és adók, tippek és útdíjak, és a teljes összeg fizetős részleteit tartalmazza. Az alábbiakban néhány példa rekordokat:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Utazás csatlakozni egyedi kulcsa\_adatokat és utazás\_diszkont tevődik össze a mezők: medallion céltudatos\_engedélyt, és felvétel\_datetime.

## <a name="mltasks"></a>Példák az előrejelzés
Hogy fogalmaz meg három előrejelzési problémák alapján a *tipp\_összeg*, nevezetesen:

1. Bináris osztályozás: előrejelzése e tipp fizették útnak, azaz egy *tipp\_összeg* nagyobb több, mint 0 USD pozitív példa, miközben egy *tipp\_összeg* 0 USD, de egy a példában negatív.
2. Többosztályos osztályozási: előre fizetett az utazás a tip tartományán. Hogy osztani a *tipp\_összeg* öt bins vagy osztályok:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. Regresszió. feladat: előre fizetett belépőt a tip mennyisége.  

## <a name="setup"></a>Olyan adatelemezési környezetet beállítás mentése az Azure fejlett elemzésekhez
Amint láthatja, hogy az a [a környezet megtervezése](plan-your-environment.md) útmutató, többféle módon való használata az Azure-ban a NYC Taxi lelassítja adatkészlet:

* Az adatok Azure-blobokban, majd a modellt az Azure Machine Learning használata
* Az adatok betöltése az SQL Server-adatbázist, majd a modellt az Azure Machine Learning

Ebben az oktatóanyagban egy SQL Server, az adatok feltárása, a szolgáltatás az adatok párhuzamos tömeges importálás bemutatjuk mérnöki mintavételi le az SQL Server Management Studio használatával, valamint IPython Notebook használatával. [Szkript minták](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) és [IPython-jegyzetfüzeteket](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) a Githubon vannak megosztva. Egy mintául szolgáló IPython notebook az Azure-blobokban adatokkal való munka érhető el ugyanazon a helyen.

Az Azure Data Science környezet beállításához:

1. [Tárfiók létrehozása](../../storage/common/storage-create-storage-account.md)
2. [Az Azure Machine Learning-munkaterület létrehozása](../studio/create-workspace.md)
3. [A Data Science virtuális gép kiépítése](../data-science-virtual-machine/setup-sql-server-virtual-machine.md), amely egy SQL Server és a egy IPython Notebook kiszolgálót biztosít.
   
   > [!NOTE]
   > A mintaszkriptek és IPython-jegyzetfüzeteket a rendszer letölti a Data Science virtuális gép a telepítés során. A virtuális gép telepítés utáni parancsfájl lefutásakor a minták szerepelni fog a virtuális gép dokumentumtár:  
   > 
   > * Példa parancsfájl: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Minta IPython-jegyzetfüzeteket: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   ahol `<user_name>` a virtuális gép Windows bejelentkezési név. A mintául szolgáló mappákat, fogunk hivatkozni **Mintaszkriptek** és **minta IPython-jegyzetfüzeteket**.
   > 
   > 

Az adatkészlet méretét, adatforrás helye és a célként kiválasztott Azure-környezet alapján, ez a forgatókönyv hasonlít a [forgatókönyv \#5: nagy méretű adathalmazt a helyi fájlok a cél SQL Server Azure-beli virtuális gépen](plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Beolvassa az adatokat nyilvános forráskódú
Az első a [NYC Taxi lelassítja](http://www.andresmh.com/nyctaxitrips/) adatkészlet a nyilvános helyéről, előfordulhat, hogy használja ismertetett módszerek valamelyikét [áthelyezése adat- és az Azure Blob Storage-ból](move-azure-blob.md) az adatok másolása az új virtuális gépet.

Az AzCopy használatával adatok másolása:

1. Jelentkezzen be a virtuális gép (VM)
2. Hozzon létre egy új könyvtárat a Virtuálisgép-adatlemez (Megjegyzés: ne használja az ideiglenes lemez, amely a virtuális gép adatlemezként).
3. Egy parancssori ablakban futtassa a következő Azcopy parancssort, és cserélje le az adatokat (2) létrehozott mappa < path_to_data_folder >:
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    Az AzCopy befejezését követően 24 összesen zip CSV-fájlok (12-trip\_adatok és a 12-trip\_diszkont) az adatok mappában kell lennie.
4. Csomagolja ki a letöltött fájlokat. Vegye figyelembe a mappára, ahol a kibontott fájlok találhatók. Ez a mappa hivatkozni fog az < elérési út\_a\_adatok\_fájlok\>.

## <a name="dbload"></a>Tömeges adatimportálás SQL Server-adatbázisba
Betöltés/átvitele a nagy mennyiségű adat az SQL database és a lekérdezések teljesítményének javítása érdekében a *particionált táblák és nézetek*. Ebben a szakaszban leírt utasításokat követjük [párhuzamos tömeges adatok importálása használata SQL-táblák partíció](parallel-load-sql-partitioned-tables.md) hozzon létre egy új adatbázist és az adatok betöltése az párhuzamosan a particionált táblákat.

1. A virtuális Géphez való bejelentkezés, indítsa el a **SQL Server Management Studio**.
2. Csatlakozás a Windows-hitelesítés használatával.
   
    ![Csatlakozás ssms használatával][12]
3. Ha még nem módosította az SQL Server-hitelesítési módot, és létrehozott egy új SQL-bejelentkezési felhasználójának, nyissa meg a parancsfájl nevű **módosítása\_auth.sql** a a **Mintaszkriptek** mappát. Módosítsa az alapértelmezett felhasználónév és jelszó. Kattintson a **! Hajtsa végre** a parancsfájl futtatásához az eszköztáron.
   
    ![Szkript végrehajtása][13]
4. Győződjön meg arról, és/vagy mappák módosítása, az SQL Server alapértelmezett adatbázis és naplófájlok annak biztosítása érdekében az újonnan létrehozott adatbázisok adatlemez lesz tárolva. Az SQL Server VM-lemezképet, amelyet a datawarehousing terhelések van optimalizálva előre konfigurálva, az adat- és naplófájlok lemezeket. Ha a virtuális gép nem tartalmazza az adatlemezt, és a virtuális gép telepítési folyamat során hozzáadott új virtuális merevlemezeket, módosítsa az alapértelmezett mappák az alábbiak szerint:
   
   * Kattintson a jobb gombbal az SQL Server neve a bal oldali panelen, és kattintson a **tulajdonságok**.
     
       ![SQL Server tulajdonságai][14]
   * Válassza ki **adatbázis-beállítások** származó a **oldal kijelölése** a bal oldali listában.
   * Ellenőrizze és/vagy módosításához a **alapértelmezett helyek adatbázis-** , a **adatlemez** tetszőleges helyen. Ez az új adatbázis-ket, ha az alapértelmezett hely beállításokkal létrehozott.
     
       ![Az SQL Database alapértelmezett értéke][15]  
5. Hozzon létre egy új adatbázist és a fájlcsoportokat, amely tárolja a particionált táblák egy készlete, nyissa meg a minta parancsfájl **létrehozása\_db\_default.sql**. A szkript létrehoz egy új adatbázist **TaxiNYC** és az adatok alapértelmezett hely 12 fájlcsoportokat. Minden egyes fájlcsoport feladatelemeket az út egy hónap\_adatokat és trip\_adatok díjszabás. Ha szükséges, módosítsa az adatbázis nevét. Kattintson a **! Hajtsa végre** a parancsfájl futtatásához.
6. Ezután hozzon létre egy az utazás a két partíció táblázat\_adatok és a egy másik az utazás a\_diszkont. Nyissa meg a minta parancsfájl **létrehozása\_particionált\_table.sql**, ahol:
   
   * Hozzon létre egy partíciós függvény kell felosztani az adatokat a hónap szerint.
   * Hozzon létre egy partícióséma, különböző fájlcsoportba minden hónapban adatok leképezése.
   * Hozzon létre két particionált tábla partíciós sémája leképezve: **nyctaxi\_út** feladatelemeket az utazás\_adatokat és **nyctaxi\_diszkont** feladatelemeket az utazás\_adatok díjszabás.
     
     Kattintson a **! Hajtsa végre** futtassa a szkriptet, és a particionált táblákat hozhat létre.
7. Az a **Mintaszkriptek** mappában vannak a két minta PowerShell-parancsfájlok párhuzamos tömeges importálja az SQL Server-táblákra adatok bemutatásához megadott.
   
   * **BCP\_párhuzamos\_generic.ps1** egy általános parancsfájl párhuzamos tömeges adatok importálása egy táblába. Módosítsa ezt a szkriptet a bemeneti és a cél változók beállítása a szkriptben a Megjegyzés sorok jelzett.
   * **BCP\_párhuzamos\_nyctaxi.ps1** az általános parancsfájl előre konfigurált változata és betölteni a mindkét táblázatot a NYC Taxi utak adatokhoz való használható.  
8. Kattintson a jobb gombbal a **bcp\_párhuzamos\_nyctaxi.ps1** parancsfájl nevét, és kattintson **szerkesztése** a PowerShell való megnyitásához. Az előre definiált változókat, és módosítsa megfelelően a kijelölt adatbázis neve, a bemeneti adatok egy mappáján, log célmappa és a minta formátumú fájlok elérési útjai **nyctaxi_trip.xml** és **nyctaxi\_fare.xml** (a megadott a **Mintaszkriptek** mappát).
   
    ![Adatok tömeges importálása][16]
   
    Is kiválaszthat a hitelesítési mód, alapértelmezett érték a Windows-hitelesítés. Kattintson a zöld nyílra, kattintson az eszköztár Futtatás. A szkript elindít 24 tömeges importálási műveletek párhuzamosan, 12 egyes particionált táblára vonatkozóan. Az adatok importálása folyamatban van az SQL Server alapértelmezett adat mappában nyissa meg a fenti beállított is figyelheti.
9. A PowerShell-parancsfájlt a kezdési és befejezési idejét jelzi. Ha az összes tömeges importálás befejeződött, a befejezési időpont jelentett. Ellenőrizze a cél naplómappában, győződjön meg arról, hogy a tömeges importálás sikerült-e, azaz a nem jelentett hibát a célmappában napló.
10. Az adatbázis most már készen áll a feltárás funkciófejlesztési és egyéb műveletek. Mivel a táblák a következők szerint vannak particionálva a **begyűjtést\_dátum és idő** mező, lekérdezések, többek között **begyűjtés\_dátum és idő** való a **ahol** záradék partícióséma élvezheti.
11. A **SQL Server Management Studio**, Fedezze fel a megadott minta parancsfájl **minta\_queries.sql**. A mintalekérdezések futtatásához jelölje ki a lekérdezés sorokat, majd kattintson az **! Hajtsa végre** az eszköztáron.
12. A NYC Taxi lelassítja adatok betöltése két külön táblázatban. Összekapcsolási műveletek javítása érdekében javasoljuk a tábla indexelése. A minta parancsfájl **létrehozása\_particionált\_index.sql** particionált indexeket az összetett illesztési kulcsot hoz létre **medallion céltudatos\_licenc és a felvétel\_ dátum és idő**.

## <a name="dbexplore"></a>Az adatok feltárása és az SQL Server Funkciófejlesztési feladatok
Ebben a szakaszban végezzük el adatok feltárása és a szolgáltatás generálása SQL-lekérdezéseket közvetlenül futtatásával a **SQL Server Management Studio** korábban létrehozott SQL Server-adatbázis használatával. Egy mintaszkriptet nevű **minta\_queries.sql** megtalálható a **Mintaszkriptek** mappát. Módosítsa a parancsfájlt úgy, hogy módosítsa az adatbázis nevét, ha az alapértelmezettől eltérő: **TaxiNYC**.

Ebben a gyakorlatban a következő történik:

* Csatlakozás **SQL Server Management Studio** vagy Windows-hitelesítés használatával, vagy SQL-hitelesítés és az SQL-bejelentkezési név és jelszó használatával.
* Fedezze fel az adatokat a különböző időtartományok néhány mezőt disztribúciók.
* Vizsgálja meg a szélességi és hosszúsági mezők adatok minőségét.
* Bináris és többosztályos osztályozási címkék alapján készítése a **tipp\_összeg**.
* Szolgáltatások készítése és trip távokat számítási/összehasonlítása.
* Csatlakozzon a két táblázatot, és csomagolja ki egy véletlenszerűen vett minta modellek létrehozásához használt.

Amikor elkészült, lépjen az Azure Machine Learning, a következő lehetőségekkel vagy:  

1. A végső SQL-lekérdezés kibontása és a mintaadatok és másolás és beillesztés közvetlenül a lekérdezés mentéséhez egy [adatok importálása] [ import-data] modul az Azure Machine Learning, vagy
2. A mintavételezett megmaradnak, és a visszafejtett azt tervezi, hogy az új adatbázis létrehozásához használja az adattábla és az új táblázat a [adatok importálása] [ import-data] az Azure Machine Learning modul.

Ebben a szakaszban az adatok kinyeréséhez és az utolsó lekérdezési menti azt. A második módszer mutatják be a [Adatáttekintés és az IPython Notebook mérnöki funkció](#ipnb) szakaszban.

A sorok és oszlopok korábban segítségével a párhuzamos tömeges importálás, a táblák számát egy gyors ellenőrzés

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Feltárás: Utazás eloszlás medallion szerint
Ebben a példában a medallion (-i taxik számokat) azonosít, több mint 100 lelassítja egy adott időtartamon belül. A lekérdezés kiaknázhatják a particionált tábla hozzáférés, mivel megfelel a partíciós sémája **begyűjtés\_datetime**. A teljes adatkészlet lekérdezése is teszi a particionált tábla használja, és/vagy a vizsgálat index.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Feltárás: Utazás terjesztési medallion és hack_license
    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Adatok vizsgálatának: Helytelen hosszúsági és/vagy szélességi rekordok ellenőrzésére
Ebben a példában folytat, ha a hosszúsági és/vagy szélességi mezők vagy érvénytelen értéket tartalmazza (radián fok – 90 és 90 között kell lennie), vagy rendelkezik (0, 0) koordinátáit.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Feltárás: Formabontó vs. Nem Formabontó lelassítja terjesztési
Ebben a példában találja, hogy voltak Formabontó és időszak (vagy ha a teljes évre vonatkozó teljes adatkészlet) egy adott idő alatt nem Formabontó lelassítja a száma. Ehhez a terjesztéshez tükrözi a bináris címke terjesztése bináris osztályozási modellezési később használható.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Feltárás: Osztály és címtartomány terjesztési tipp
Ebben a példában kiszámítja a terjesztési tipp címtartományok egy adott időtartamon (vagy a teljes adatkészlethez, ha a teljes évre vonatkozó). Ez a később fogja használni a modellezési többosztályos osztályozási címke osztályok eloszlása.

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

#### <a name="exploration-compute-and-compare-trip-distance"></a>Feltárás: Számítás, és hasonlítsa össze a Trip távolság
Ebben a példában a begyűjtés és Gyűjtőtár hosszúsági alakítja át, és SQL földrajzi szélesség mutat, kiszámítja az SQL a földrajzi pontokat különbség használatával trip távolság és egy véletlenszerűen vett minta az eredmények az összehasonlításhoz adja vissza. A példában az eredményeket, és csak a a minőségi értékelés lekérdezési korábban tartozó érvényes koordináták korlátozza.

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

#### <a name="feature-engineering-in-sql-queries"></a>Az SQL-lekérdezések Funkciófejlesztési
A címke és a geography átalakítás feltárás lekérdezéseket is használható a számlálási eltávolításával címkék/szolgáltatások létrehozásához. A szolgáltatás további mérnöki SQL példák találhatók a [Adatáttekintés és az IPython Notebook mérnöki funkció](#ipnb) szakaszban. Legyen hatékonyabb a szolgáltatás generálása lekérdezések futtatására a teljes adatkészletet vagy közvetlenül az SQL Server adatbázispéldány rendszeren futó SQL-lekérdezések használatával egy nagy részét. Előfordulhat, hogy a lekérdezések hajtható **SQL Server Management Studio**, IPython Notebook vagy semmilyen eszköz/fejlesztőkörnyezet amely hozzáférhessen az adatbázishoz, helyileg vagy távolról.

#### <a name="preparing-data-for-model-building"></a>A modell létrehozásához az adatok előkészítése
Az alábbi lekérdezés csatlakozik a **nyctaxi\_út** és **nyctaxi\_diszkont** táblák, létrehoz egy bináris osztályozási címke **Formabontó**, amely egy többcsoportos besorolási címke **tipp\_osztály**, és a egy 1 % véletlenszerűen vett minta kigyűjti a teljes csatlakozó adatkészlet. Ez a lekérdezés másolja, majd a beillesztett közvetlenül a [Azure Machine Learning Studio](https://studio.azureml.net) [adatok importálása] [ import-data] modul a közvetlen adatbetöltés, az SQL Server-adatbázisból példány az Azure-ban. A lekérdezés nem tartalmazza a helytelen rekordot (0, 0) koordinátáit.

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


## <a name="ipnb"></a>Az adatok feltárása és az IPython Notebook Funkciófejlesztési feladatok
Ebben a szakaszban végezzük el a adatáttekintés és a szolgáltatás létrehozása Python és az SQL-lekérdezéseket futtassanak a korábban létrehozott SQL Server-adatbázis használatával. Egy mintául szolgáló IPython notebook nevű **machine-Learning-data-science-process-sql-story.ipynb** megtalálható a **minta IPython-jegyzetfüzeteket** mappát. Ez a jegyzetfüzet is érhető el az [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

A javasolt feladatütemezés, a big Data típusú adatok használata során, a következő:

* Olvassa el az adatok néhányat példaként egy memórián belüli adatok keretbe.
* Hajtsa végre az egyes Vizualizációk és explorations mintavételezett adatok használatával.
* A mintavételezett adatok funkciófejlesztési kísérletezhet.
* A nagyobb méretű adatfeltárás, adatkezelés és funkciófejlesztési a Python használatával SQL-lekérdezéseket közvetlenül kiadni az SQL Server-adatbázist az Azure-beli virtuális gépen.
* Döntse el, az Azure Machine Learning-modell létrehozásának minta mérete.

Ha elkészült, lépjen tovább az Azure Machine Learning, a következő lehetőségekkel vagy:  

1. A végső SQL-lekérdezés kibontása és a mintaadatok és másolás és beillesztés közvetlenül a lekérdezés mentéséhez egy [adatok importálása] [ import-data] az Azure Machine Learning modul. Ez a módszer mutatják be a [épület modellek az Azure Machine Learning](#mlmodel) szakaszban.    
2. Azt tervezi, hogy egy új adatbázis-tábla létrehozásához használja a mintavételezett és visszafejtett adatok megőrzése, majd az új táblázat a [adatok importálása] [ import-data] modul.

Az alábbiakban a néhány adatfeltárás, adatvizualizáció és példák mérnöki funkció. További példák: a minta SQL IPython notebook az a **minta IPython-jegyzetfüzeteket** mappát.

#### <a name="initialize-database-credentials"></a>Adatbázis-hitelesítő adatok inicializálása
Az adatbázis-kapcsolati beállítások a következő változók inicializálása:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Adatbázis-kapcsolat létrehozása
    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Sorok és oszlopok a tábla nyctaxi_trip jelentéshez számát
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

* Sorainak = 173179759  
* Oszlopok teljes száma = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Olvassa el a az SQL Server-adatbázisból egy kis méretű adatok minta
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

A minta tábla 6.492000 másodperc olvasási idő  
A beolvasott sorok és oszlopok száma = (84952, 21)

#### <a name="descriptive-statistics"></a>Leíró statisztika
Most már készen áll a mintavételezett adatok feltárására. Kezdődik meg, a leíró statisztikája megnézzük a **út\_távolság** (vagy bármilyen más) fájlmezőket:

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Vizualizáció: Diagram – példa
Ezután megnézzük a Dobozdiagram a megjelenítéséhez a quantiles trip távolság

    df1.boxplot(column='trip_distance',return_type='dict')

![#1 ábrázolása][1]

#### <a name="visualization-distribution-plot-example"></a>Vizualizáció: Terjesztési diagram példa
    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![#2 ábrázolása][2]

#### <a name="visualization-bar-and-line-plots"></a>Vizualizáció: Sáv- és sor grafikon
Ebben a példában azt az öt bins trip távolság bin és a dobozolási eredményeinek képi megjelenítése.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Azt jeleníti meg a fenti bin terjesztési egy szót vagy. sor, az alábbi diagram

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![#3 ábrázolása][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![#4 ábrázolása][4]

#### <a name="visualization-scatterplot-example"></a>Vizualizáció: Teszteredményekből példa
Bemutatjuk a pontdiagram között **út\_idő\_a\_másodperc** és **út\_távolság** megtekintéséhez, hogy van-e bármilyen korrelációs

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![#6 ábrázolása][6]

Hasonló módon is közötti kapcsolat ellenőrzése **arány\_kód** és **út\_távolság**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![#8 ábrázolása][8]

### <a name="sub-sampling-the-data-in-sql"></a>Mintavétel az adatokat az SQL-ben
Modell létrehozása adatok előkészítésekor [Azure Machine Learning Studio](https://studio.azureml.net), vagy dönthet a a **SQL-lekérdezésben használandó közvetlenül az adatok importálása modullal** vagy megőrizni a visszafejtett és a mintavételezett adatokat az új táblában, amely a használhatja a [adatok importálása] [ import-data] modul egy egyszerű **kiválasztása * FROM < a\_új\_tábla\_neve >** .

Ebben a szakaszban létre fogunk hozni egy új táblát a mintavételezett és visszafejtett adatok tárolásához. A közvetlen SQL-lekérdezés a modell létrehozásához például az a [Adatáttekintés és az SQL Server mérnöki funkció](#dbexplore) szakaszban.

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Hozzon létre egy minta táblát, és adja meg az illesztett táblákat 1 %. Eldobni a tábla első, ha az már létezik.
Ebben a szakaszban azt csatlakozzon a táblák **nyctaxi\_út** és **nyctaxi\_diszkont**, 1 % véletlenszerűen kinyerése és a egy új tábla neve a mintavételezett adatok megőrzése  **nyctaxi\_egy\_%-os**:

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

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>IPython Notebook az SQL-lekérdezések használata az adatok feltárása
Ebben a szakaszban a rendszer megőrzi a fentiekben létrehozott új tábla mintavételezés 1 % adatokat használó adatok disztribúciók tárgyaljuk. Vegye figyelembe, hogy hasonló explorations használatával végezheti el az eredeti táblázatnak, igény szerint használatával **TABLESAMPLE** mintát vagy, ha korlátozza az eredményeket, és a egy adott időszak többön feltárás korlátozni a **begyűjtés\_dátum és idő** particionálja, ahogyan a [Adatáttekintés és az SQL Server mérnöki funkció](#dbexplore) szakasz.

#### <a name="exploration-daily-distribution-of-trips"></a>Feltárás: Napi terjesztési utak
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Feltárás: Kivételre medallion megoszlása
    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>SQL-lekérdezések használata az IPython Notebook szolgáltatás létrehozása
Ez a szakasz az új címkéket fog létrehozni, és a szolgáltatások közvetlenül az SQL-lekérdezések használatával, működő minta 1 % tábla létrehozott az előző szakaszban.

#### <a name="label-generation-generate-class-labels"></a>Címke létrehozása: Osztály címkék létrehozása
A következő példában az általunk létrehozott két készlet a modellezési használandó címkék:

1. Bináris osztály címkék **Formabontó** (ha tipp kapnak előrejelzésére)
2. Multiclass címkék **tipp\_osztály** (előrejelzésére, a bin tipp vagy a tartomány)
   
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

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Funkciófejlesztési: Kategorikus oszlopok száma funkciói
Ebben a példában alakítja át egy kategorikus mezőt egy numerikus mezőben kategóriákhoz cserélje le az adatok az előfordulások száma.

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

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Funkciófejlesztési: A numerikus oszlopok Bin funkciók
Ebben a példában egy folyamatos numerikus mező alakítja előre beállított kategória-tartományok, azaz a átalakító numerikus mezőt egy kategorikus mezőt.

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

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Funkciófejlesztési: Hely jellemzők kinyerése decimális szélességi és hosszúsági koordinátákkal
Ebben a példában felszámolja szélességi és/vagy hosszúság mező decimális ábrázolása több régióban mezőibe eltérő a granularitási, mint például ország, város, város, letiltása, stb. Vegye figyelembe, hogy az új földrajzi mezők nincsenek leképezve a tényleges helyekre. A leképezés geocode helyeken további információkért lásd: [a Bing Maps REST szolgáltatások](https://msdn.microsoft.com/library/ff701710.aspx).

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

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Ellenőrizze a végleges képernyő featurized tábla
    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Mi most már készen áll a folytatásra modell létrehozásának és a modell üzembe helyezése [Azure Machine Learning](https://studio.azureml.net). Az adatokat, nevezetesen korábban azonosított problémák előrejelzési bármelyike:

1. Bináris osztályozás: előre e tipp fizették útnak.
2. Többosztályos osztályozási: előre fizetett, a korábban definiált osztályok megfelelően tipp tartományán.
3. Regresszió. feladat: előre fizetett belépőt a tip mennyisége.  

## <a name="mlmodel"></a>Az Azure Machine Learning modellek létrehozása
A modellezés gyakorlat megkezdéséhez jelentkezzen be az Azure Machine Learning-munkaterületet. Ha még nem hozott machine learning-munkaterület, [hozzon létre egy Azure Machine Learning-munkaterület](../studio/create-workspace.md).

1. Ismerkedés az Azure Machine Learninget, tekintse meg [Mi az Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Jelentkezzen be [az Azure Machine Learning Studio](https://studio.azureml.net).
3. A Studio kezdőlap rengeteg információt, videókat, oktatóanyagokat, valamint hivatkozásokat biztosít a modulok referencia és egyéb vállalati forrásokhoz. Fore Azure Machine Learninggel kapcsolatos további információkért tekintse meg a [Azure Machine Learning dokumentációs központban](https://azure.microsoft.com/documentation/services/machine-learning/).

Egy tipikus betanítási kísérlet a következőkből áll:

1. Hozzon létre egy **+ új** kísérletezhet.
2. Az Azure Machine Learning-adatokat kérhet.
3. Előzetes feldolgozása, átalakíthatja, és szükség szerint kezeli.
4. Funkciók létrehozása, igény szerint.
5. Az adatok felosztása képzési érvényesítési/tesztelési adatkészletek (vagy különálló adatkészletek minden).
6. Válassza ki egy vagy több gépi tanulási algoritmusok függően a tanulás a probléma megoldásához. Például bináris osztályozási többosztályos osztályozási, regressziós.
7. A betanítási adatkészletet használó egy vagy több modelleket taníthat be.
8. Az érvényesítés adatkészletéhez a betanított modellek pontozása.
9. Értékelje ki a tanulási probléma a lényeges metrikák számítási a modellek.
10. Részletes adhatja meg a modellek, és válassza ki a legjobb modellt üzembe helyezéséhez.

Ebben a gyakorlatban tudjuk már megvizsgálta és fejthetők vissza az adatokat az SQL Server, és dönteni, hogy az Azure Machine Learning a mintanagyság. Egy vagy több úgy döntött, hogy előrejelzési modell felépítése:

1. Az adatok Azure Machine Learning használatával a [adatok importálása] [ import-data] modul elérhető a **adatok bemeneti és kimeneti** szakaszban. További információkért lásd: a [adatok importálása] [ import-data] modul referenciájának oldalát.
   
    ![Az Azure Machine Learning-adatok importálása][17]
2. Válassza ki **Azure SQL Database** , a **adatforrás** a a **tulajdonságok** panel.
3. Az adatbázis DNS-nevét adja meg a **adatbázis-kiszolgáló neve** mező. Formátum: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Adja meg a **adatbázisnév** a megfelelő mezőben.
5. Adja meg a **SQL felhasználónév** a a ** kiszolgálói aqccount felhasználónevet és a jelszót a **kiszolgáló felhasználói fiók jelszava**.
7. Az a **adatbázis-lekérdezés** szövegterület szerkesztése, illessze be a lekérdezést, amely a szükséges adatbázis-mezők (beleértve a címkéket például számított mezőket), és le az adatokat a kívánt mintanagyság minták.

Az alábbi ábra egy példát egy bináris osztályozási kísérletet, közvetlenül az SQL Server adatbázisából az adatok olvasása van. Hasonló kísérletek többosztályos osztályozási és regressziós probléma lehet létrehozni.

![Az Azure Machine Learning Train][10]

> [!IMPORTANT]
> A modellezési adatok kinyerése és a lekérdezés példák mintavételi biztosított az előző szakaszokban **három modellezési gyakorlatok összes címkék szerepelnek a lekérdezés**. Az egyes modellezési gyakorlatok (kötelező) fontos lépés, hogy **kizárása** a szükségtelen címkéket, a másik két problémákat, és bármely más **adatszivárgás cél**. Például bináris osztályozási használata esetén használja a címke **Formabontó** és kizárja a mezőket **tipp\_osztály**, **tipp\_összeg**, és **teljes\_összeg**. Az utóbbi cél adatszivárgás mivel, azok magukban foglalják az ötlet fizetős.
> 
> Felesleges oszlopok kizárása és/vagy a cél adatszivárgás, használhatja a [Select Columns in Dataset] [ select-columns] modul vagy a [metaadatainak szerkesztése][edit-metadata]. További információkért lásd: [Select Columns in Dataset] [ select-columns] és [metaadatainak szerkesztése] [ edit-metadata] oldalak hivatkozhat.
> 
> 

## <a name="mldeploy"></a>Az Azure Machine Learning modellek üzembe helyezéséhez
Amikor készen áll a modellt, könnyedén telepítheti közvetlenül a kísérletből webszolgáltatásként. További információ az Azure Machine Learning-webszolgáltatások üzembe helyezéséhez: [egy Azure Machine Learning webszolgáltatás üzembe helyezése](../studio/publish-a-machine-learning-web-service.md).

Új webszolgáltatás üzembe helyezéséhez kell tennie:

1. Hozzon létre egy pontozó kísérletet.
2. A webszolgáltatás üzembe helyezéséhez.

A pontozó fülre egy kísérlet létrehozásához egy **befejezett** betanítási kísérlet, kattintson a **létre pontozási KÍSÉRLETEZHET** az alacsonyabb művelet sávon.

![Azure Scoring][18]

Az Azure Machine Learning megkísérli a tanítási kísérlet összetevői alapján pontozási kísérlet létrehozása. Különösen hajtja végre:

1. Mentse a betanított modell, és távolítsa el a modell képzési modult.
2. Azonosítsa a logikai **bemeneti porthoz** képviselő várt sémát.
3. Azonosítsa a logikai **kimeneti port** képviselő kimeneti sémája várt webes szolgáltatás.

A pontozó kísérlet jön létre, amikor, tekintse át, és igény szerint. Egy tipikus illesztését, hogy cserélje le a bemeneti adatkészlet és/vagy lekérdezési egyet, amely nem tartalmazza a címkemezők, mivel ezek nem lesz elérhető a szolgáltatás meghívásakor. Célszerű is jó megoldás a bemeneti adatkészlet és/vagy lekérdezési méretének csökkentése néhány rekordok éppen elegendő jelzi a bemeneti sémát. A kimeneti portra, a szokás, hogy kizárja az összes beviteli mezőket, és csak a **pontozott címkék** és **pontozott valószínűség** be a kimenet a [Select Columns in Dataset] [ select-columns] modul.

Az alábbi ábra egy minta kísérlet pontozási van. Amikor készen áll a központi telepítése, kattintson a **WEBSZOLGÁLTATÁS közzététele** gombra az alsó művelet sávon.

![Az Azure Machine Learning közzététele][11]

A recap, ez a forgatókönyv az oktatóanyag egy Azure adatelemzési környezetet, egy nagy nyilvános adatkészletet egészen a modellek betanítása és a egy Azure Machine Learning webszolgáltatás üzembe helyezése adatgyűjtés használhatta hozott létre.

### <a name="license-information"></a>Licencinformációk
Ez a minta forgatókönyv és a kísérő parancsfájlok és IPython notebook(s) osztanak meg a Microsoft mellett az MIT-licenccel. Ellenőrizze a LICENSE.txt fájlt további részletekért a Githubon mintakódot a címtárban.

### <a name="references"></a>Referencia
• [Andrés Monroy NYC Taxi lelassítja letöltési oldala](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC Útadatok taxiköltség Chris Whong szerint](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi és Limousine Bizottság kutatási és a statisztika](http://www.nyc.gov/html/tlc/html/technology/aggregated_data.shtml)

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

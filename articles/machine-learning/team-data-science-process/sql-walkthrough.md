---
title: Hozhat létre és helyezhet üzembe egy modellt az SQL Server VM - csoportos adatelemzési folyamat
description: Fejleszthet és telepíthet egy gépi tanulási modellt az SQL Server-beli virtuális gépen egy nyilvánosan elérhető adatkészlet.
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
ms.openlocfilehash: a47f30cf00624faf098c8b605534cf355eacadee
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251582"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>A csoportos adatelemzési folyamat működés közben: az SQL Server használata
Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és helyezhet üzembe gépi tanulási modellt SQL Server és nyilvánosan elérhető adatkészlettel – a [New York-i taxis](https://www.andresmh.com/nyctaxitrips/) adatkészlettel. Az eljárást követi a szokásos adatelemzési munkafolyamathoz: fogadni, és Fedezze fel az adatokat, Funkciók tervezése, tanulás egyszerűbbé tételével majd hozhat létre és helyezhet üzembe modelleket.

## <a name="dataset"></a>NYC taxi TRIPS adatkészlet leírása
A New York-i taxi Trip-adat körülbelül 20 GB tömörített CSV-fájlból áll (~ 48 GB tömörítetlen), amely több mint 173 000 000 egyedi utazást és az egyes utazásokhoz fizetett viteldíjat foglal magában. Minden egyes út rekord tartalmazza a begyűjtést és dropoff hely és idő, anonimizált feltörés (illesztőprogramok) licencszám és medallion (taxi az egyedi azonosító) számát. Az adatok minden lelassítja ismerteti az év 2013-hoz, és minden hónapban megtalálható a következő két adatkészletet:

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

Az utazáshoz\_az adathoz és az utazáshoz\_viteldíj a következő mezőkből áll: emlékérem, Hack\_licenc és pickup\_DateTime.

## <a name="mltasks"></a>Példák az előrejelzési feladatokra
Három előrejelzési problémát fogunk kialakítani a *tipp\_összeg*alapján, nevezetesen:

* Bináris besorolás: megjósolhatja, hogy egy adott utazási tipp kifizetése megtörtént-e, azaz egy *tipp\_* , amely nagyobb, mint $0, pozitív példa, míg a *tip\_mennyiségű* $0 értéke negatív példa.
* Többosztályos osztályozási: előre fizetett az utazás a tip tartományán. A *tipp\_összegét* öt raktárhelyre vagy osztályra osztjuk:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
* Regresszió. feladat: előre fizetett belépőt a tip mennyisége.  

## <a name="setup"></a>Az Azure adatelemzési környezet beállítása a speciális elemzésekhez
Ahogy a [terv a környezeti](plan-your-environment.md) útmutatóban is látható, több lehetőség is használható a New York-i taxis adatkészlettel az Azure-ban:

* Az adatok Azure-blobokban, majd a modellt az Azure Machine Learning használata
* Az adatok betöltése az SQL Server-adatbázist, majd a modellt az Azure Machine Learning

Ebben az oktatóanyagban az adat párhuzamos tömeges importálását mutatjuk be egy SQL Serverba, az adatfeltárásba, a szolgáltatások fejlesztésére, valamint a SQL Server Management Studio és a IPython notebook használatával végzett mintavételezésre. [](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) A IPython- [jegyzetfüzetek](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) a githubon vannak megosztva. Egy mintául szolgáló IPython notebook az Azure-blobokban adatokkal való munka érhető el ugyanazon a helyen.

Az Azure Data Science környezet beállításához:

1. [Tárfiók létrehozása](../../storage/common/storage-account-create.md)
2. [Azure Machine Learning munkaterület létrehozása](../studio/create-workspace.md)
3. [Data Science Virtual Machine kiépítése](../data-science-virtual-machine/setup-sql-server-virtual-machine.md), amely egy SQL Server és egy IPython notebook-kiszolgálót biztosít.
   
   > [!NOTE]
   > A mintaszkriptek és IPython-jegyzetfüzeteket a rendszer letölti a Data Science virtuális gép a telepítés során. A virtuális gép telepítés utáni parancsfájl lefutásakor a minták szerepelni fog a virtuális gép dokumentumtár:  
   > 
   > * Példák a parancsfájlokra: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Minta IPython notebookok: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   ahol a `<user_name>` a virtuális gép Windows-bejelentkezési neve. A minta **mappák példaként** jelennek meg a minta-és **minta-IPython jegyzetfüzetekben**.
   > 
   > 

Az adatkészletek mérete, az adatforrás helye és a kiválasztott Azure-cél környezet alapján ez a forgatókönyv hasonló a [forgatókönyvhöz \#5: nagy adathalmaz egy helyi fájlokban, célként SQL Server az Azure-beli virtuális gépen](plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Adatok beolvasása nyilvános forrásból
Az új virtuális gépre történő másolásához az adatok [áthelyezéséhez az Azure Blob Storage az adatok áthelyezése az Azure-ba és az Azure](move-azure-blob.md) -ba című témakörben leírt módszerek bármelyikét felhasználhatja, hogy a [New York](https://www.andresmh.com/nyctaxitrips/) -i (NYC)

Az AzCopy használatával adatok másolása:

1. Jelentkezzen be a virtuális gép (VM)
2. Hozzon létre egy új könyvtárat a virtuális gép adatlemezén (Megjegyzés: ne használja a virtuális géphez tartozó ideiglenes lemezt adatlemezként).
3. Egy parancssori ablakban futtassa a következő Azcopy parancssort, és cserélje le az adatokat (2) létrehozott mappa < path_to_data_folder >:
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    Ha a AzCopy befejeződik, a rendszer összesen 24 tömörített CSV-fájlt (12 az utazás\_és 12 az utazás\_viteldíjat) az Adatmappában kell megadni.
4. Csomagolja ki a letöltött fájlokat. Vegye figyelembe a mappára, ahol a kibontott fájlok találhatók. Ezt a mappát a rendszer < elérési útra\_\_\_fájlok\>.

## <a name="dbload"></a>SQL Server adatbázisba való tömeges importálás
A nagy mennyiségű adat egy SQL Databaseba való betöltésének és továbbításának, valamint az azt követő lekérdezéseknek a teljesítményét *particionált táblák és nézetek*használatával lehet javítani. Ebben a szakaszban a [párhuzamos tömeges adatimportálás az SQL Partition Tables használatával](parallel-load-sql-partitioned-tables.md) című rész útmutatását követve hozzon létre egy új adatbázist, és párhuzamosan töltse be az információkat particionált táblákba.

1. Ha bejelentkezett a virtuális gépre, indítsa el **SQL Server Management Studio**.
2. Csatlakozás a Windows-hitelesítés használatával.
   
    ![Csatlakozás ssms használatával][12]
3. Ha még nem módosította a SQL Server hitelesítési módot, és létrehozott egy új SQL-bejelentkezési felhasználót, nyissa meg a **change\_auth. SQL** nevű parancsfájlt a **minta parancsfájlok** mappában. Módosítsa az alapértelmezett felhasználónév és jelszó. A parancsfájl futtatásához kattintson a **végrehajtás** gombra az eszköztáron.
   
    ![Szkript végrehajtása][13]
4. Győződjön meg arról, és/vagy mappák módosítása, az SQL Server alapértelmezett adatbázis és naplófájlok annak biztosítása érdekében az újonnan létrehozott adatbázisok adatlemez lesz tárolva. Az adattárolási terhelésre optimalizált SQL Server VM rendszerkép előre konfigurálva van az adatok és a naplók lemezekkel. Ha a virtuális gép nem tartalmazza az adatlemezt, és a virtuális gép telepítési folyamat során hozzáadott új virtuális merevlemezeket, módosítsa az alapértelmezett mappák az alábbiak szerint:
   
   * Kattintson a jobb gombbal a SQL Server nevére a bal oldali panelen, majd kattintson a **Tulajdonságok**elemre.
     
       ![SQL Server tulajdonságai][14]
   * Válassza ki az **adatbázis-beállítások** elemet a bal oldali **lap kiválasztása** listából.
   * Ellenőrizze és/vagy módosítsa az **adatbázis alapértelmezett helyét** az Ön által választott **adatlemez** -helyekre. Ez a hely, ahol az új adatbázisok találhatók, ha az alapértelmezett beállításokkal lettek létrehozva.
     
       ![Az SQL Database alapértelmezett értéke][15]  
5. Új adatbázis létrehozásához és a particionált táblák tárolására szolgáló fájlcsoportok megnyitásához nyissa meg a **\_adatbázis létrehozása\_default. SQL**. A szkript létrehoz egy **TaxiNYC** és 12 fájlcsoportok nevű új adatbázist az alapértelmezett adatelérési helyen. Minden fájlcsoportja egy hónapig tart az utazás\_az adatgyűjtés és az utazás\_a díjszabási adatmennyiség. Ha szükséges, módosítsa az adatbázis nevét. A szkript futtatásához kattintson a **végrehajtás** gombra.
6. Következő lépésként hozzon létre két partíciós táblát, egyet az utazáshoz\_, egy másikat pedig az utazás\_a viteldíjat. Nyissa meg a minta parancsfájl **létrehozás\_particionált\_table. SQL**, amely a következőket teszi:
   
   * Hozzon létre egy partíciós függvény kell felosztani az adatokat a hónap szerint.
   * Hozzon létre egy partícióséma, különböző fájlcsoportba minden hónapban adatok leképezése.
   * Hozzon létre két particionált táblát a particionálási sémához: a **nyctaxi\_Trip** megtartja az utazás\_az adatelérési és a **nyctaxi\_viteldíj** az utazás\_viteldíjat fogja tárolni.
     
     A parancsfájl futtatásához és a particionált táblák létrehozásához kattintson a **végrehajtás** gombra.
7. A **Sample Scripts** mappában két PowerShell-szkript van megadva, amelyek az adatSQL Server táblákba való párhuzamos tömeges importálást mutatják be.
   
   * a **bcp\_parallel\_általános. ps1** egy általános parancsfájl, amely párhuzamosan importálja az adattáblázatba az adatmennyiséget. Módosítsa ezt a szkriptet a bemeneti és a cél változók beállítása a szkriptben a Megjegyzés sorok jelzett.
   * a **bcp\_parallel\_nyctaxi. ps1** az általános parancsfájl előre konfigurált verziója, és mindkét tábla betöltésére használható a New York-i taxis szolgáltatáshoz.  
8. Kattintson a jobb gombbal a **bcp\_parallel\_nyctaxi. ps1** parancsfájl nevére, majd a **Szerkesztés** elemre kattintva nyissa meg a PowerShellben. Tekintse át az előre definiált változókat, és módosítsa a kiválasztott adatbázis neve, a bemeneti adatok mappája, a cél napló mappája és a minta formátumú fájlok elérési útjai alapján a **nyctaxi_trip. XML** és **a** **nyctaxi\_fare. XML** fájlt.
   
    ![Adatok tömeges importálása][16]
   
    Is kiválaszthat a hitelesítési mód, alapértelmezett érték a Windows-hitelesítés. Kattintson a zöld nyílra, kattintson az eszköztár Futtatás. A szkript elindít 24 tömeges importálási műveletek párhuzamosan, 12 egyes particionált táblára vonatkozóan. Az adatok importálása folyamatban van az SQL Server alapértelmezett adat mappában nyissa meg a fenti beállított is figyelheti.
9. A PowerShell-parancsfájlt a kezdési és befejezési idejét jelzi. Ha az összes tömeges importálás befejeződött, a befejezési időpont jelentett. A cél napló mappájában ellenőrizze, hogy a tömeges importálások sikeresek voltak-e, azaz nem történtek-e hibák a cél napló mappában.
10. Az adatbázis most már készen áll a feltárás funkciófejlesztési és egyéb műveletek. Mivel a táblák a **pickup\_datetime** mező szerint vannak particionálva, a **Where** záradékban lévő **felvételi\_datetime** feltételekkel rendelkező lekérdezések kihasználhatják a partíciós sémát.
11. A **SQL Server Management Studioban**tekintse át a megadott minta parancsfájl- **mintát\_lekérdezések. SQL**. A lekérdezési sorok bármelyikének futtatásához jelölje ki a lekérdezések sorát, majd kattintson az eszköztáron a **végrehajtás** lehetőségre.
12. A NYC Taxi lelassítja adatok betöltése két külön táblázatban. Összekapcsolási műveletek javítása érdekében javasoljuk a tábla indexelése. A parancsfájl **létrehozása\_particionált\_index. SQL** létrehoz egy particionált indexeket a kompozit JOIN Key **medalion, a hack\_License és a pickup\_datetime**.

## <a name="dbexplore"></a>Adatfeltárási és-szolgáltatás-mérnöki SQL Server
Ebben a szakaszban az adatok feltárását és a szolgáltatás létrehozását fogjuk elvégezni, ha az SQL-lekérdezéseket közvetlenül a **SQL Server Management Studio** futtatja a korábban létrehozott SQL Server-adatbázis használatával. A mintául szolgáló **\_lekérdezések. SQL** nevű minta parancsfájl a **minta parancsfájlok** mappában található. Módosítsa a parancsfájlt úgy, hogy módosítsa az adatbázis nevét, ha az eltér az alapértelmezetttől: **TaxiNYC**.

Ebben a gyakorlatban a következő történik:

* Kapcsolódjon **SQL Server Management Studio** Windows-hitelesítéssel vagy SQL-hitelesítéssel, valamint az SQL-bejelentkezési név és jelszó használatával.
* Fedezze fel az adatokat a különböző időtartományok néhány mezőt disztribúciók.
* Vizsgálja meg a szélességi és hosszúsági mezők adatok minőségét.
* Bináris és többosztályos besorolási Címkék készítése a **tipp\_összeg**alapján.
* Szolgáltatások készítése és trip távokat számítási/összehasonlítása.
* Csatlakozzon a két táblázatot, és csomagolja ki egy véletlenszerűen vett minta modellek létrehozásához használt.

Amikor elkészült, lépjen az Azure Machine Learning, a következő lehetőségekkel vagy:  

1. Mentse a végső SQL-lekérdezést az adatok kinyeréséhez és mintavételéhez, és másolja be a lekérdezést közvetlenül a Azure Machine Learning [importálási][import-data] adatmodulba, vagy
2. Maradjon meg az új adatbázistábla modell-létrehozásához használni kívánt mintavételes és megtervezett adattípusok, és használja az új táblát a Azure Machine Learning [adatimportálás][import-data] moduljában.

Ebben a szakaszban a végső lekérdezést fogjuk menteni az adatok kinyerésére és mintavételezésére. A második módszer a IPython notebook szakaszának [Adatfeltárási és-szolgáltatás-mérnöki](#ipnb) szakaszában látható.

A sorok és oszlopok korábban segítségével a párhuzamos tömeges importálás, a táblák számát egy gyors ellenőrzés

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Feltárás: Utazás eloszlás medallion szerint
Ebben a példában a medallion (-i taxik számokat) azonosít, több mint 100 lelassítja egy adott időtartamon belül. A lekérdezés kihasználja a particionált tábla elérését, mivel azt a **pickup\_datetime**értékkel rendelkező partíciós sémája feltétele. A teljes adatkészlet lekérdezése is teszi a particionált tábla használja, és/vagy a vizsgálat index.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Feltárás: Utazás terjesztési medallion és hack_license
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

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Feltárás: kiosztott és nem kiosztott utak eloszlása
Ebben a példában találja, hogy voltak Formabontó és időszak (vagy ha a teljes évre vonatkozó teljes adatkészlet) egy adott idő alatt nem Formabontó lelassítja a száma. Ehhez a terjesztéshez tükrözi a bináris címke terjesztése bináris osztályozási modellezési később használható.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Feltárás: Osztály és címtartomány terjesztési tipp
Ebben a példában kiszámítja a terjesztési tipp címtartományok egy adott időtartamon (vagy a teljes adatkészlethez, ha a teljes évre vonatkozó). A címkézési osztályok ezen eloszlását később a többosztályos besorolás modellezéséhez fogjuk használni.

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
Ebben a példában a begyűjtés és dropoff hosszúsági alakítja át, és SQL földrajzi szélesség mutat, kiszámítja az SQL a földrajzi pontokat különbség használatával trip távolság és egy véletlenszerűen vett minta az eredmények az összehasonlításhoz adja vissza. A példában az eredményeket, és csak a a minőségi értékelés lekérdezési korábban tartozó érvényes koordináták korlátozza.

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
A címke és a geography átalakítás feltárás lekérdezéseket is használható a számlálási eltávolításával címkék/szolgáltatások létrehozásához. További mérnöki SQL-példákat az [adatelemzési és funkció-mérnöki IPython notebook](#ipnb) szakaszban talál. Hatékonyabban futtathatja a szolgáltatás-létrehozási lekérdezéseket a teljes adatkészletre vagy annak nagy részhalmazára az SQL-lekérdezések használatával, amelyek közvetlenül a SQL Server adatbázis-példányon futnak. A lekérdezések **SQL Server Management Studio**, IPython jegyzetfüzetben vagy bármely olyan fejlesztési eszközön vagy környezetben hajthatók végre, amely helyileg vagy távolról is hozzáfér az adatbázishoz.

#### <a name="preparing-data-for-model-building"></a>A modell létrehozásához az adatok előkészítése
A következő lekérdezés összekapcsolja a **nyctaxi\_Trip** és a **nyctaxi\_viteldíjak** táblázatát, **létrehoz**egy bináris besorolási címkét, egy többosztályos besorolási címke **\_osztályt**, és Kinyer egy 1%-os véletlenszerű mintát a teljes csatlakoztatott adatkészletből. Ez a lekérdezés átmásolható közvetlenül a [Azure Machine learning Studio](https://studio.azureml.net) [Importálás][import-data] adatmodulba az Azure-beli SQL Server-adatbázis példányának közvetlen adatfeldolgozásához. A lekérdezés nem tartalmazza a helytelen rekordot (0, 0) koordinátáit.

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


## <a name="ipnb"></a>Adatfelderítési és-funkció-mérnöki IPython notebookon
Ebben a szakaszban végezzük el a adatáttekintés és a szolgáltatás létrehozása Python és az SQL-lekérdezéseket futtassanak a korábban létrehozott SQL Server-adatbázis használatával. Egy **Machine-learning-adat-Science-Process-SQL-Story** nevű minta IPython jegyzetfüzet. a Ipynb a **minta IPython jegyzetfüzetek** mappában található. Ez a jegyzetfüzet a [githubon](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks)is elérhető.

Big data használatakor kövesse az alábbi javasolt sorozatot:

* Olvassa el az adatok néhányat példaként egy memórián belüli adatok keretbe.
* Hajtsa végre az egyes Vizualizációk és explorations mintavételezett adatok használatával.
* A mintavételezett adatok funkciófejlesztési kísérletezhet.
* A nagyobb méretű adatfeltárás, adatkezelés és funkciófejlesztési a Python használatával SQL-lekérdezéseket közvetlenül kiadni az SQL Server-adatbázist az Azure-beli virtuális gépen.
* Döntse el, az Azure Machine Learning-modell létrehozásának minta mérete.

Ha elkészült, lépjen tovább az Azure Machine Learning, a következő lehetőségekkel vagy:  

1. Mentse a végső SQL-lekérdezést az adatok kinyeréséhez és mintavételéhez, és másolja be a lekérdezést közvetlenül a Azure Machine Learning [Importálás][import-data] adatmodulba. Ez a módszer a Azure Machine Learning szakaszban bemutatott [építési modellekben](#mlmodel) látható.    
2. Maradjon meg az új adatbázis-táblában a modell-létrehozáshoz használni kívánt mintavételes és megtervezett adatértékek, majd használja az új táblát az [adatimportálás][import-data] modulban.

Az alábbiakban a néhány adatfeltárás, adatvizualizáció és példák mérnöki funkció. További példákért tekintse meg az SQL IPython jegyzetfüzetet a **minta IPython jegyzetfüzetek** mappában.

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

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Sorok és oszlopok a tábla nyctaxi_trip jelentéshez számát
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
Most már készen áll a mintavételezett adatok feltárására. Első lépésként tekintse meg az **utazás\_távolság** (vagy bármely más) mező (ke) t leíró statisztikáit:

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
Megmutatjuk, hogy az **utazás\_idő\_a\_a másodpercben** és az **út\_távolság** között, hogy van-e korreláció

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![#6 ábrázolása][6]

Hasonlóképpen ellenőrizhető a **ráta\_a kód** és az **utazás\_távolság**közötti kapcsolat.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![#8 ábrázolása][8]

### <a name="sub-sampling-the-data-in-sql"></a>Mintavétel az adatokat az SQL-ben
Az [Azure Machine learning Studio](https://studio.azureml.net)-beli modell-létrehozási adatok előkészítésekor dönthet úgy, **hogy az SQL-lekérdezést közvetlenül az Adatimportálási modulban használja** , vagy megtartja a tervezett és a mintavételezésen alapuló adatok egy új táblázatban, amelyet az [adatimportálási][import-data] modulban egy egyszerű **Select < * paranccsal használhat a\_új\_tábla\_név >** .

Ebben a szakaszban egy új táblázatot hozunk létre, amely a mintául szolgáló és a megtervezett adathalmazokat fogja tárolni. A modell építésének közvetlen SQL-lekérdezési példáját a SQL Server szakaszban található [Adatfeltárási és-szolgáltatás-fejlesztés](#dbexplore) tartalmazza.

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Hozzon létre egy minta táblát, és adja meg az illesztett táblákat 1 %. Eldobni a tábla első, ha az már létezik.
Ebben a szakaszban összekapcsoljuk a táblákat a **nyctaxi\_az utazást** és a **nyctaxi\_viteldíjat**, kinyerjük az 1%-os véletlenszerű mintát, és megtartjuk a mintavételezési adatok egy új táblanév **nyctaxi,\_egy\_százalékban**:

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
Ebben a szakaszban a fent létrehozott új táblázatban megőrzött 1%-os mintavételes adateloszlásokat vizsgáljuk. Hasonló feltárások hajthatók végre az eredeti táblák használatával, opcionálisan a **TABLESAMPLE** használatával korlátozhatja a feltárási mintát, vagy az eredményeket egy adott időszakra korlátozhatja a **pickup\_datetime** partíciók használatával, ahogyan az a SQL Server szakaszban szereplő [adatelemzési és szolgáltatás-fejlesztésekben](#dbexplore) is látható.

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

1. Bináris **osztályok feliratai** (előre jelezve, ha a tipp meg lesz adva)
2. Többosztályos címkék **tipp\_osztály** (a tip-raktárhely vagy-tartomány előrejelzése)
   
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
Ez a példa egy folytonos numerikus mezőt alakít át előre definiált kategóriákba, azaz a numerikus mezőt egy kategorikus mezővé alakítja.

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
Ez a példa egy szélességi és/vagy hosszúsági mező decimális megjelenítését bontja fel több, különböző részletességgel rendelkező régióba, például ország/régió, város, város, blokk stb. Az új geo-mezők nincsenek a tényleges helyükre leképezve. További információ a geocode-telephelyek hozzárendeléséről: [Bing Maps Rest Services](https://msdn.microsoft.com/library/ff701710.aspx).

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

Most már készen áll az építés és a modell üzembe helyezésének modellezésére [Azure Machine Learningban](https://studio.azureml.net). Az adatokat, nevezetesen korábban azonosított problémák előrejelzési bármelyike:

1. Bináris osztályozás: előre e tipp fizették útnak.
2. Többosztályos osztályozási: előre fizetett, a korábban definiált osztályok megfelelően tipp tartományán.
3. Regresszió. feladat: előre fizetett belépőt a tip mennyisége.  

## <a name="mlmodel"></a>Modellek kiépítése a Azure Machine Learningban
A modellezés gyakorlat megkezdéséhez jelentkezzen be az Azure Machine Learning-munkaterületet. Ha még nem hozott létre Machine learning-munkaterületet, tekintse meg [a Azure Machine learning munkaterület létrehozása](../studio/create-workspace.md)című témakört.

1. A Azure Machine Learning megkezdéséhez tekintse meg a [Mi az a Azure Machine learning Studio?](../studio/what-is-ml-studio.md)
2. Jelentkezzen be [Azure Machine learning Studioba](https://studio.azureml.net).
3. A Studio kezdőlap rengeteg információt, videókat, oktatóanyagokat, valamint hivatkozásokat biztosít a modulok referencia és egyéb vállalati forrásokhoz. További információ a Azure Machine Learningről: [Azure Machine learning dokumentációs központ](https://azure.microsoft.com/documentation/services/machine-learning/).

Egy tipikus betanítási kísérlet a következő lépésekből áll:

1. Hozzon létre egy **+ új** kísérletet.
2. Az Azure Machine Learning-adatokat kérhet.
3. Szükség szerint előre feldolgozhatja, átalakíthatja és kezelheti az adatfeldolgozást.
4. Funkciók létrehozása, igény szerint.
5. Az adatok felosztása képzési érvényesítési/tesztelési adatkészletek (vagy különálló adatkészletek minden).
6. Válassza ki egy vagy több gépi tanulási algoritmusok függően a tanulás a probléma megoldásához. Például a bináris besorolás, a többosztályos besorolás, a regresszió.
7. A betanítási adatkészletet használó egy vagy több modelleket taníthat be.
8. Az érvényesítés adatkészletéhez a betanított modellek pontozása.
9. Értékelje ki a tanulási probléma a lényeges metrikák számítási a modellek.
10. Hangolja be a modell (eke) t, és válassza ki a telepítendő legjobb modellt.

Ebben a gyakorlatban tudjuk már megvizsgálta és fejthetők vissza az adatokat az SQL Server, és dönteni, hogy az Azure Machine Learning a mintanagyság. Egy vagy több előrejelzési modell kiépítéséhez a következőket döntöttük:

1. Az adatok beolvasása **és a kimenet** szakaszban elérhető adatok [importálása][import-data] modul használatával Azure Machine learning adatokat. További információ: az [adatok importálása][import-data] modul referenciája lap.
   
    ![Az Azure Machine Learning-adatok importálása][17]
2. Válassza a **Azure SQL Database** lehetőséget a **Tulajdonságok** panelen lévő **adatforrásként** .
3. Adja meg az adatbázis DNS-nevét az **adatbázis-kiszolgáló neve** mezőben. Formátum: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Adja meg az **adatbázis nevét** a megfelelő mezőben.
5. Adja meg az **SQL-felhasználónevet** a **kiszolgáló felhasználói fiókjának nevében**, és a **jelszót** a **kiszolgáló felhasználói fiókjának jelszavában**.
7. Az **adatbázis-lekérdezés** szövegmezőben illessze be azt a lekérdezést, amely kibontja a szükséges adatbázis-mezőket (beleértve a kiszámított mezőket, például a címkéket), és a legördülő menüben az adatok kinyerését a kívánt minta méretre.

Az alábbi ábra egy példát egy bináris osztályozási kísérletet, közvetlenül az SQL Server adatbázisából az adatok olvasása van. Hasonló kísérletek többosztályos osztályozási és regressziós probléma lehet létrehozni.

![Az Azure Machine Learning Train][10]

> [!IMPORTANT]
> Az előző szakaszban ismertetett modellezési adatok kinyerésére és mintavételezésére vonatkozó példákban a **három modellezési gyakorlat összes címkéje szerepel a lekérdezésben**. A modellezési gyakorlatok fontos (kötelező) lépése, hogy **kizárják** a másik két probléma felesleges címkéit, valamint az egyéb **megcélzott szivárgásokat**. Ha például bináris besorolást használ, használja a megjelenő **címkét, és** zárja ki a mezők **Tipp\_osztály**, a **Tipp\_mennyiség**és a **teljes\_mennyiséget**. Az utóbbi cél adatszivárgás mivel, azok magukban foglalják az ötlet fizetős.
> 
> A szükségtelen oszlopok és/vagy célzott szivárgások kizárásához használja az [Oszlopok kiválasztása az adatkészlet][select-columns] modulban vagy a [metaadatok szerkesztése][edit-metadata]lehetőséget. További információ: [Oszlopok kiválasztása az adatkészletben][select-columns] és a [metaadatokat][edit-metadata] tartalmazó hivatkozások szerkesztése.
> 
> 

## <a name="mldeploy"></a>Modellek üzembe helyezése Azure Machine Learning
Amikor készen áll a modellt, könnyedén telepítheti közvetlenül a kísérletből webszolgáltatásként. Az Azure Machine Learning webszolgáltatások telepítésével kapcsolatos további információkért lásd: [Azure Machine learning webszolgáltatás üzembe](../studio/deploy-a-machine-learning-web-service.md)helyezése.

Új webszolgáltatás üzembe helyezéséhez kell tennie:

1. Hozzon létre egy pontozó kísérletet.
2. A webszolgáltatás üzembe helyezéséhez.

A **befejezett** betanítási kísérletből származó pontozási kísérlet létrehozásához kattintson a **pontozási kísérlet létrehozása** lehetőségre az alsó műveleti sávon.

![Azure Scoring][18]

Az Azure Machine Learning megkísérli a tanítási kísérlet összetevői alapján pontozási kísérlet létrehozása. Különösen hajtja végre:

1. Mentse a betanított modell, és távolítsa el a modell képzési modult.
2. Azonosítson egy logikai **bemeneti portot** , amely a várt bemeneti adatsémát jelképezi.
3. Azonosítson egy logikai **kimeneti portot** a várt webszolgáltatás kimeneti sémájának megjelenítéséhez.

A pontozó kísérlet jön létre, amikor, tekintse át, és igény szerint. Egy tipikus beállítás, ha a bemeneti adatkészletet és/vagy lekérdezést lecseréli egy olyanra, amely kizárja a címke mezőket, mivel ezek a címkék nem lesznek elérhetők a sémában a szolgáltatás hívásakor. Ajánlott továbbá a bemeneti adatkészlet és/vagy a lekérdezés méretének csökkentése néhány rekordra, amely elegendő a bemeneti séma jelzéséhez. A kimeneti port esetében gyakori, hogy kizárják az összes bemeneti mezőt, és csak a kimenetben szereplő **mutatókat** és a kimutatott **valószínűségeket** tartalmazzák a [DataSet oszlopok kiválasztása az adatkészlet][select-columns] modulban.

Az alábbi ábra egy minta kísérlet pontozási van. Ha készen áll a telepítésre, kattintson a **közzétételi webszolgáltatás** gombra az alsó műveleti sávon.

![Az Azure Machine Learning közzététele][11]

A recap, ez a forgatókönyv az oktatóanyag egy Azure adatelemzési környezetet, egy nagy nyilvános adatkészletet egészen a modellek betanítása és a egy Azure Machine Learning webszolgáltatás üzembe helyezése adatgyűjtés használhatta hozott létre.

### <a name="license-information"></a>Licencinformációk
Ez a minta forgatókönyv és a kísérő parancsfájlok és IPython notebook(s) osztanak meg a Microsoft mellett az MIT-licenccel. További részletekért olvassa el a következőt a GitHubon található mintakód könyvtárában: LICENSE. txt.

### <a name="references"></a>Hivatkozások
• [Andrés MONROY NYC taxi TRIPS letöltési oldal](https://www.andresmh.com/nyctaxitrips/)  
• [A New York-i taxis utazási adatvédelme Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• A [New York-i taxi és a limuzin Bizottság kutatási és statisztikai adatai](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

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

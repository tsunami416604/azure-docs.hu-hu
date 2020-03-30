---
title: Modell létrehozása és üzembe helyezése SQL Server virtuális gépben – Csapatadat-elemzési folyamat
description: Gépi tanulási modell létrehozása és üzembe helyezése az SQL Server használatával egy nyilvánosan elérhető adatkészlettel rendelkező Azure virtuális gépen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251582"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>A csapatadat-elemzési folyamat működés közben: az SQL Server használata
Ebben az oktatóanyagban végigvezeti a folyamat ot egy gépi tanulási modell létrehozásának és üzembe helyezésének az SQL Server és egy nyilvánosan elérhető adatkészlet - a [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) adatkészlet használatával. Az eljárás egy szabványos adatelemzési munkafolyamatot követ: betöltése és feltárása az adatok, mérnök funkciók a tanulás megkönnyítése érdekében, majd hozzon létre és telepítsen egy modellt.

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>NYC Taxi utak adatkészlet leírása
A NYC Taxi Trip adatok mintegy 20 GB tömörített CSV fájlok (~ 48 GB tömörítetlen), amely több mint 173 millió egyéni utak és a viteldíjak fizetett minden út. Minden út rekord tartalmazza a pickup és dropoff helyét és idejét, anonimizált hack (driver's) engedély száma és medallion (taxi egyedi id) számát. Az adatok a 2013-as év összes utazására vonatkoznak, és minden hónapra vonatkozóan a következő két adatkészletben szerepelnek:

1. A "trip_data" CSV tartalmazza az utazás részleteit, például az utasok számát, a felvételi és leadási pontokat, az utazás időtartamát és az utazás hosszát. Íme néhány mintarekord:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. A "trip_fare" CSV tartalmazza az egyes utazásokért fizetett viteldíj részleteit, például a fizetés típusát, a viteldíj összegét, a pótdíjat és az adókat, a tippeket és az útdíjakat, valamint a teljes kifizetett összeget. Íme néhány mintarekord:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Az egyedülálló kulcs,\_hogy\_csatlakozzon utazási adatok és utazási\_viteldíj áll\_a területeken: medál, hack engedély és pickup datetime.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Példák előrejelzési feladatokra
Három előrejelzési problémát fogunk megfogalmazni a *tipp\_összege*alapján, nevezetesen:

* Bináris besorolás: Tippelje meg, hogy egy tippet kifizettek-e egy utazásért, azaz a 0 $-nál nagyobb *\_tippösszeg* pozitív példa- e, míg a 0 $ *tipp\_összege* negatív példa.
* Többosztályos besorolás: Az utazásért fizetett tipp tartományának előrejelzése. A *hegy\_összegét* öt tárolóra vagy osztályra osztjuk:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
* Regressziós feladat: Az utazásért fizetett tipp összegének előrejelzése.  

## <a name="setting-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Az Azure adatelemzési környezetének beállítása a fejlett elemzésekhez
Amint az a [Környezet megtervezése](plan-your-environment.md) útmutatóból is látható, számos lehetőség közül választhat az Azure-ban a NYC Taxi Trips adatkészlettel való együttműködésre:

* Az Azure-blobokban lévő adatok, majd a modell az Azure Machine Learningben
* Töltse be az adatokat egy SQL Server-adatbázisba, majd modellelje az Azure Machine Learningben

Ebben az oktatóanyagban bemutatjuk az adatok párhuzamos tömeges importálását egy SQL Server kiszolgálóra, az adatok feltárását, a szolgáltatástervezést és a mintavételezést az SQL Server Management Studio használatával, valamint az IPython Notebook használatával. [A mintaparancsfájlok](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) és az [IPython-jegyzetfüzetek](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) meg vannak osztva a GitHubon. Az Azure-blobokban lévő adatokkal való együttműködésre vonatkozó minta IPython-jegyzetfüzet is elérhető ugyanazon a helyen.

Az Azure Data Science-környezet beállítása:

1. [Tárfiók létrehozása](../../storage/common/storage-account-create.md)
2. [Azure Machine Learning-munkaterület létrehozása](../studio/create-workspace.md)
3. [Egy adatelemzési virtuális gép kiépítése,](../data-science-virtual-machine/setup-sql-server-virtual-machine.md)amely sql servert és IPython notebook-kiszolgálót biztosít.
   
   > [!NOTE]
   > A mintaparancsfájlok és az IPython-jegyzetfüzetek a telepítési folyamat során letöltődnek az adatelemzési virtuális gépre. Amikor a virtuális gép telepítés utáni parancsfájl befejeződik, a minták a virtuális gép Dokumentumok tárában lesznek:  
   > 
   > * Példa szkriptek:`C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Minta IPython notebookok:`C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   hol `<user_name>` van a virtuális gép Windows bejelentkezési neve. A mintamappákra **mintaszkriptekként** és **IPython-jegyzetfüzetek mintaként hivatkozunk.**
   > 
   > 

Az adatkészlet mérete, az adatforrás helye és a kiválasztott Azure-célkörnyezet alapján ez a forgatókönyv hasonló az [ \#5.](plan-sample-scenarios.md#largelocaltodb)

## <a name="get-the-data-from-public-source"></a><a name="getdata"></a>Az adatok beszerezése nyilvános forrásból
A [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) adatkészlet nyilvános helyről való leéséhez használhatja az Adatok [áthelyezése az Azure Blob Storage-ba és az Azure Blob Storage-ból](move-azure-blob.md) az adatokat az új virtuális gépre leírt módszerek bármelyikét.

Az adatok másolása az AzCopy használatával:

1. Bejelentkezés a virtuális gépre (VM)
2. Hozzon létre egy új könyvtárat a virtuális gép adatlemez (Megjegyzés: Ne használja a virtuális gép hez kapcsolódó ideiglenes lemez adatlemezként).
3. A parancssorablakban futtassa a következő Azcopy parancssort, és cserélje le <path_to_data_folder> a (2) bekezdésben létrehozott adatmappára:
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    Amikor az AzCopy befejeződik, összesen 24 tömörített CSV\_fájlnak kell\_lennie (12 az utazási adatokés 12 az utazási viteldíjhoz) az adatmappában.
4. Csomagolja ki a letöltött fájlokat. Jegyezze fel azt a mappát, amelyben a tömörítetlen fájlok találhatók. Ezt a mappát az\_adatfájlok\_\_<\>elérési útjaként fogják emlegetni.

## <a name="bulk-import-data-into-sql-server-database"></a><a name="dbload"></a>Adatok tömeges importálása az SQL Server adatbázisba
*Particionált táblák és nézetek*használatával nagy mennyiségű adat betöltésének/átvitelének teljesítménye javítható az SQL-adatbázisba és az azt követő lekérdezésekhez. Ebben a szakaszban a párhuzamos [tömeges adatok importálása SQL partíciós táblák használatával](parallel-load-sql-partitioned-tables.md) leírt utasításokat követve hozzon létre egy új adatbázist, és töltse be az adatokat particionált táblákpárhuzamosan.

1. A virtuális gépbe bejelentkezve indítsa el az **SQL Server Management Studio alkalmazást.**
2. Csatlakozás a Windows-hitelesítés használatával.
   
    ![SSMS csatlakozás][12]
3. Ha még nem módosította az SQL Server hitelesítési módját, és új SQL bejelentkezési felhasználót hozott létre, nyissa meg az **\_auth.sql** nevű parancsfájlt a **Mintaparancsfájlok** mappában. Módosítsa az alapértelmezett felhasználónevet és jelszót. A parancsfájl futtatásához kattintson az eszköztár **Végrehajtás** gombjára.
   
    ![Parancsfájl végrehajtása][13]
4. Ellenőrizze és/vagy módosítsa az SQL Server alapértelmezett adatbázis- és naplómappáit, és győződjön meg arról, hogy az újonnan létrehozott adatbázisok adatlemezen tárolódnak. Az SQL Server virtuálisgép-lemezkép, amely az adattárház terhelések előre konfigurálva az adatok és a naplólemezek. Ha a virtuális gép nem tartalmazott adatlemezt, és új virtuális merevlemezeket adott hozzá a virtuális gép beállítási folyamata során, módosítsa az alapértelmezett mappákat az alábbiak szerint:
   
   * Kattintson a jobb gombbal az SQL Server nevére a bal oldali panelen, és válassza a **Tulajdonságok parancsot.**
     
       ![SQL Server tulajdonságai][14]
   * Válassza az **Adatbázis beállításai lehetőséget** a Bal oldali **Lapkiválasztása** listából.
   * Ellenőrizze és/vagy módosítsa az **adatbázis alapértelmezett helyeit** az Ön által választott **adatlemez** helyekre. Ez a hely az a hely, ahol az új adatbázisok az alapértelmezett beállításokkal jönnek létre.
     
       ![SQL-adatbázis alapértelmezései][15]  
5. Új adatbázis és a particionált táblák tárolására létrehozott fájlcsoportok létrehozásához nyissa meg a db **\_\_default.sql sablonparancsfájlt.** A parancsfájl létrehoz egy új adatbázist **TaxiNYC** és 12 fájlcsoportok az alapértelmezett adathelyen. Minden fájlcsoport egy hónapos\_utazási és\_utazási díjadatokat fog tárolni. Szükség esetén módosítsa az adatbázis nevét. A parancsfájl futtatásához kattintson a **Végrehajtás** gombra.
6. Ezután hozzon létre két partíciós táblát, egyet az utazási\_adatokhoz, egyet pedig az utazási\_viteldíjhoz. Nyissa meg a **mintaparancsfájlt, amely\_particionált\_table.sql**, amely:
   
   * Hozzon létre egy partíciófüggvényt az adatok havi felosztásához.
   * Hozzon létre egy partíciósémát, amely leszeretné képezni az egyes havi adatokat egy másik fájlcsoporthoz.
   * Hozzon létre két particionált táblát a partíciósémához: **nyctaxi\_utazás** fogja\_tartani az utazási\_adatokat, és **nyctaxi\_viteldíj** fogja tartani az utazási viteldíj adatokat.
     
     Kattintson a **Végrehajtás gombra** a parancsfájl futtatásához és a particionált táblák létrehozásához.
7. A **Minta parancsfájlok** mappában két powershell-parancsfájlminta található az SQL Server-táblák adatok párhuzamos tömeges importálásának bemutatására.
   
   * **A\_bcp parallel\_generic.ps1** egy általános parancsfájl, amely párhuzamos tömeges importálási adatokat hoz létre egy táblába. Módosítsa ezt a parancsfájlt a bemeneti és célváltozók beállításához a parancsfájl megjegyzéssoraiban jelzettmódon.
   * **Bcp\_\_párhuzamos nyctaxi.ps1** egy előre konfigurált változata az általános script, és lehet használni, hogy töltse be a két tábla a NYC Taxi Trips adatokat.  
8. Kattintson a jobb gombbal a **bcp\_párhuzamos\_nyctaxi.ps1** parancsfájl nevére, és a Szerkesztés **parancsra** kattintva nyissa meg a PowerShellben. Tekintse át az előre beállított változókat, és módosítsa a kiválasztott adatbázisnévnek, a bemeneti adatmappának, a célnapló mappának és a **nyctaxi_trip.xml** és **nyctaxi\_fare.xml** (a **Mintaparancsfájlok** mappában található) mintaformátumú fájlok elérési útjainak megfelelően.
   
    ![Tömeges importálási adatok][16]
   
    Kiválaszthatja a hitelesítési módot is, az alapértelmezett a Windows-hitelesítés. Kattintson a futtatásához kattintson az eszköztár zöld nyilára. A parancsfájl 24 tömeges importálási műveletet indít el párhuzamosan, 12-t minden egyes particionált táblához. Az adatok importálásának előrehaladását figyelheti az SQL Server alapértelmezett adatmappájának fent meghatározott megnyitásával.
9. A PowerShell-parancsfájl jelenti a kezdési és befejezési időpontokat. Ha az összes tömeges importálás befejeződött, a befejezési időpont ot jelenti a jelentés. Ellenőrizze a célnapló mappájában, hogy a tömeges importálás sikeres volt-e, azaz nem jelentettek hibát a célnapló mappájában.
10. Az adatbázis most már készen áll a feltárásra, a szolgáltatástervezésre és más műveletekre. Mivel a táblák particionálása a **felvételi\_datetime** mező szerint van felosztva, a **WHERE** záradékban a **\_felvételi datetime** feltételeket tartalmazó lekérdezések a partícióséma előnyeit élvezik.
11. Az **SQL Server Management Studio**rendszerben fedezze fel a megadott mintaparancsfájl-mintalekérdezéseket.sql **\_**. A mintalekérdezések futtatásához jelölje ki a lekérdezéssorokat, majd kattintson a **Végrehajtás** gombra az eszköztáron.
12. A NYC Taxi Trips adatok két külön táblába töltődnek be. Az illesztési műveletek javítása érdekében erősen ajánlott a táblák indexelése. A mintaszkript **\_\_particionált index.sql** particionált indexeket hoz létre az összetett illesztési kulcs **medálon, a hack\_licencen és a felvételi\_datetime-on.**

## <a name="data-exploration-and-feature-engineering-in-sql-server"></a><a name="dbexplore"></a>Adatfeltárás és szolgáltatástervezés az SQL Server rendszerben
Ebben a szakaszban az SQL-lekérdezések közvetlenül az **SQL Server Management Studio-ban** történő futtatásával hajtjuk végre az adatok feltárását és a szolgáltatásgenerálást a korábban létrehozott SQL Server-adatbázis használatával. A **mintaqueries.sql\_** nevű **mintaparancsfájl a Mintaparancsfájlok** mappában található. Módosítsa a parancsfájlt az adatbázis nevének módosításához, ha az eltér az alapértelmezetttől: **TaxiNYC**.

Ebben a gyakorlatban:

* Csatlakozzon az **SQL Server Management Studio** szolgáltatáshoz a Windows-hitelesítés vagy az SQL-hitelesítés, valamint az SQL bejelentkezési név és jelszó használatával.
* Néhány mező adateloszlásának megismerése különböző időablakokban.
* Vizsgálja meg a hosszúsági és szélességi mezők adatminőségét.
* Bináris és többosztályos besorolási címkék létrehozása a **csúcs\_mennyisége**alapján.
* Funkciók at generálhat, és kiszámítja/összehasonlíthatja az utazási távolságokat.
* Csatlakozzon a két táblázathoz, és bontson ki egy véletlenszerű mintát, amely et modellek létrehozásához fog használni.

Ha készen áll az Azure Machine Learningre való továbbmunkára, a következőket teheti:  

1. A végső SQL-lekérdezés mentése az adatok kinyeréséhez és mintavételéhez, valamint a lekérdezés közvetlen beillesztéséhez az Azure Machine Learning [adatok importálása][import-data] moduljába, vagy
2. Őrize meg a mintavételezett és a tervezett adatokat, amelyeket egy új adatbázistáblában modellépítéshez kíván használni, és az új táblát használja az Azure Machine Learning [adatok importálása][import-data] moduljában.

Ebben a szakaszban a végső lekérdezést az adatok kibontásához és mintavételéhez mentjük. A második módszert az [IPython-jegyzetfüzet adatfeltárása és szolgáltatástervezése](#ipnb) szakasza mutatja be.

A párhuzamos tömeges importálással korábban feltöltött táblák sorai és oszlopai számának gyors ellenőrzéséhez

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Feltárás: Utazás elosztása medál szerint
Ez a példa azonosítja a medált (taxiszámokat) több mint 100 utazással egy adott időszakon belül. A lekérdezés számára előnyös lenne a particionált tábla-hozzáférés, mivel a **\_felvételi datetime**partícióséma határozza meg. A teljes adatkészlet lekérdezése a particionált táblát és/vagy az indexvizsgálatot is használja.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Feltárás: Utazás elosztása medál és hack_license
    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Adatminőség-értékelés: Helytelen hosszúságú és/vagy szélességi területű rekordok ellenőrzése
Ez a példa azt vizsgálja, hogy a hosszúsági és/vagy szélességi mezők bármelyike tartalmaz-e érvénytelen értéket (a radiánfoknak -90 és 90 között kell lennie), vagy (0, 0) koordinátákkal rendelkezik-e.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Feltárás: Tipped vs. Nem tipped utak elosztása
Ez a példa megkeresi azon utazások számát, amelyeket egy adott időszakban (vagy a teljes évre vonatkozó teljes adatkészletben) nem billentettek meg. Ez az eloszlás a bináris osztályozási modellezéshez később használandó bináris címkeeloszlást tükrözi.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Feltárás: Tipp osztály / range eloszlása
Ez a példa kiszámítja a tipptartományok eloszlását egy adott időszakban (vagy a teljes adatkészletben, ha a teljes évre vonatkozik). A címkeosztályok ezen eloszlása később többosztályos osztályozási modellezéshez lesz használva.

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

#### <a name="exploration-compute-and-compare-trip-distance"></a>Feltárás: Számítási és összehasonlítási távolság
Ez a példa a felvételi és a lemorzsolódási hosszúsági és szélességi fokot SQL földrajzi pontokká alakítja, kiszámítja az utazási távolságot az SQL földrajzi pontok különbségének használatával, és az eredmények véletlenszerű mintáját adja vissza az összehasonlításhoz. A példa az eredményeket csak a korábban lefedett adatminőség-értékelési lekérdezés használatával korlátozza érvényes koordinátákra.

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

#### <a name="feature-engineering-in-sql-queries"></a>Szolgáltatástervezés az SQL-lekérdezésekben
A címke generálása és a földrajzi konverzió feltárási lekérdezések is használható címkék/funkciók létrehozásához a számláló rész eltávolításával. További szolgáltatásmérnöki SQL-példák at az [IPython-jegyzetfüzet adatfeltárása és szolgáltatástervezése](#ipnb) szakasza tartalmazza. Hatékonyabb a szolgáltatásgenerálási lekérdezések futtatása a teljes adatkészleten vagy annak egy nagy részhalmazán olyan SQL-lekérdezések használatával, amelyek közvetlenül az SQL Server adatbázispéldányon futnak. A lekérdezések végrehajthatók az **SQL Server Management Studio**, az IPython Notebook vagy bármely olyan fejlesztőeszköz vagy környezetben, amely helyileg vagy távolról is hozzáférhet az adatbázishoz.

#### <a name="preparing-data-for-model-building"></a>Adatok előkészítése a modellépítéshez
A következő lekérdezés csatlakozik a **\_nyctaxi utazás** és **nyctaxi\_viteldíj** táblák, létrehoz egy bináris besorolási címke **billentett**, egy többosztályos besorolási címke **\_tipp osztály,** és kinyeri az 1%-os véletlenszerű mintát a teljes illesztett adatkészletből. Ez a lekérdezés közvetlenül az [Azure Machine Learning Studio](https://studio.azureml.net) Import [Data][import-data] modulba másolható, hogy közvetlen adatbetöltést hajtson elő az Azure-beli SQL Server adatbázispéldányból. A lekérdezés nem tartalmazza a helytelen (0, 0) koordinátákkal rendelkező rekordokat.

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


## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Adatfeltárás és szolgáltatástervezés az IPython notebookban
Ebben a szakaszban az adatok feltárását és a szolgáltatásgenerálást python- és SQL-lekérdezések használatával végezzük a korábban létrehozott SQL Server-adatbázissal. A **Minta IPython-jegyzetfüzetek** mappában található egy **machine-Learning-data-science-process-sql-story.ipynb** nevű mintaIPython-jegyzetfüzet. Ez a jegyzetfüzet a [GitHubon](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks)is elérhető.

Big Data-adatok használata esetén kövesse az ajánlott sorrendet:

* Olvassa be az adatok egy kis mintáját egy memórián belüli adatkeretbe.
* A mintavételezett adatok használatával bizonyos vizualizációkat és feltárásokat hajthat végre.
* Kísérletezzen a szolgáltatástervezéssel a mintavételezett adatok használatával.
* A nagyobb adatfeltárás, az adatok kezelése és a szolgáltatás tervezése, a Python segítségével sql-lekérdezések közvetlenül az SQL Server-adatbázis az Azure vm-ben.
* Döntse el, hogy az Azure Machine Learning modelllétrehozásához mekkora mintát szeretne használni.

Ha készen áll az Azure Machine Learningre való továbbmunkára, a következőket teheti:  

1. Mentse a végső SQL-lekérdezést az adatok kibontásához és mintavételezéséhez, és másolja be a lekérdezést közvetlenül az Azure Machine Learning [adatok importálása][import-data] moduljába. Ezt a módszert az [Azure Machine Learning-csoport modellek létrehozása](#mlmodel) szakaszban mutatja be.    
2. Őrize meg a mintavételezett és a tervezett adatokat, amelyeket egy új adatbázistáblában modellépítéshez kíván használni, majd használja az új táblát az [Adatok importálása][import-data] modulban.

Az alábbiakban néhány adatfeltárás, adatmegjelenítés és a szolgáltatás mérnöki példák. További példákat az SQL IPython-jegyzetfüzetminta című témakörben talál a **Minta IPython-jegyzetfüzetek** mappában.

#### <a name="initialize-database-credentials"></a>Adatbázis-hitelesítő adatok inicializálása
Az adatbázis-kapcsolat beállításainak inicializálása a következő változókban:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Adatbázis-kapcsolat létrehozása
    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Sorok és oszlopok jelentésszáma a táblázatban nyctaxi_trip
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

* Sorok száma összesen = 173179759  
* Oszlopok száma összesen = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Kis adatminta beolvasása az SQL Server adatbázisból
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

A mintatáblázat olvasási ideje 6,492000 másodperc  
Beolvasott sorok és oszlopok száma = (84952, 21)

#### <a name="descriptive-statistics"></a>Leíró statisztika
Most már készen áll, hogy vizsgálja meg a mintavételezett adatokat. Kezdjük nézett leíró statisztikák az **utazási\_távolság** (vagy bármely más) mező (ek):

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Képi megjelenítés: Példa a dobozos nyomtatásra
Ezután megnézzük a doboz telek az utazás távolság a kvantitatív

    df1.boxplot(column='trip_distance',return_type='dict')

![Telek #1][1]

#### <a name="visualization-distribution-plot-example"></a>Vizualizáció: Terjesztési telek példa
    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![#2][2]

#### <a name="visualization-bar-and-line-plots"></a>Képi megjelenítés: Sáv- és vonaltelkek
Ebben a példában az utazási távolságot öt tárolóba tároljuk, és a binning eredményeket vizualizáljuk.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

A fenti bin eloszlást az alábbiak szerint ábrázolhatjuk egy sávban vagy vonalban

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Telek #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Telek #4][4]

#### <a name="visualization-scatterplot-example"></a>Képi megjelenítés: Példa a scatterplot-ra
Megmutatjuk scatter telek között **\_utazási idő\_\_mp** és az utazási **\_távolság,** hogy ha van összefüggés

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Telek #6][6]

Hasonlóképpen ellenőrizhetjük a **\_díjkód** és az **utazási\_távolság**közötti kapcsolatot.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Telek #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>Adatok almintavételezése SQL-ben
Amikor adatokat készít elő az [Azure Machine Learning Studio](https://studio.azureml.net)modellépületéhez, dönthet úgy, hogy az **SQL-lekérdezést közvetlenül az Adatok importálása modulban használja,** vagy megőrizheti a tervezett és mintavételezett adatokat egy új táblában, amelyet az [Adatok importálása][import-data] modulban használhat egy egyszerű **SELECT * FROM <az\_új\_táblanév\_>. **

Ebben a szakaszban létrehozunk egy új táblát a mintavételezett és a mesterséges adatok tárolására. A modellépítés közvetlen SQL-lekérdezésére példa az [SQL Server Adatfeltárásés szolgáltatástervezés című szakasza.](#dbexplore)

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Hozzon létre egy mintatáblázatot, és az illesztett táblák 1%-ával népesítse fel. Először a táblázatot dobja el, ha létezik.
Ebben a részben csatlakozunk a **\_nyctaxi utazás** és **nyctaxi\_viteldíj**táblákhoz, kivonunk egy 1% véletlenszerű mintát, és megkell őriznünk a mintavételezett adatokat egy új táblanévben **nyctaxi\_egy\_százalékkal:**

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

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Adatok feltárása SQL-lekérdezésekkel az IPython-jegyzetfüzetben
Ebben a szakaszban az adateloszlásokat az 1%-os mintában szereplő adatok használatával vizsgáljuk, amelyek a fent létrehozott új táblázatban megmaradnak. Hasonló feltárások végezhetők az eredeti táblák használatával, opcionálisan **tablesample** használatával a feltárási minta korlátozásához, vagy az eredmények adott időszakra való korlátozásával a **felvételi\_datetime** partíciók használatával, amint azt az [SQL Server Adatfeltárás és szolgáltatástechnika szakasza](#dbexplore) is szemlélteti.

#### <a name="exploration-daily-distribution-of-trips"></a>Feltárás: Az utak napi eloszlása
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Feltárás: Utazás elosztása medálonként
    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Szolgáltatásgenerálás SQL-lekérdezések használatával az IPython-jegyzetfüzetben
Ebben a szakaszban új címkéket és funkciókat hozunk létre közvetlenül az SQL-lekérdezések használatával, amelyek az előző szakaszban létrehozott 1%-os mintatáblán működnek.

#### <a name="label-generation-generate-class-labels"></a>Címke készítése: Osztálycímkék létrehozása
A következő példában két címkekészletet hozunk létre a modellezéshez:

1. Binary Class Címkék **billentett** (megjósolni, ha egy tipp lesz adva)
2. Többosztályos címkék **tipposztálya\_** (a hegytároló vagy -tartomány előrejelzése)
   
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

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Funkciótervezés: A kategorikus oszlopok funkcióinak megszámlálása
Ez a példa egy kategorikus mezőt numerikus mezővé alakít át, mivel az egyes kategóriákat az adatokban lévő előfordulások számával helyettesíti.

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

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Funkciótervezés: A numerikus oszlopok raktárhely-szolgáltatásai
Ez a példa egy folytonos numerikus mezőt előre beállított kategóriatartományokká alakít át, azaz a numerikus mezőt kategorikus mezővé alakítja.

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

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Funkciótervezés: A helyjellemzők kinyerése a decimális szélességi/hosszúsági fokból
Ez a példa egy szélességi és/vagy hosszúsági mező decimális ábrázolását több különböző részletességű régiómezőre bontja, például ország/régió, város, város, blokk stb. Az új földrajzi mezők nincsenek leképezve a tényleges helyekre. A geokódhelyek leképezésével kapcsolatos további tudnivalókért a [Bing Maps REST Services című témakörben](https://msdn.microsoft.com/library/ff701710.aspx)talál további információt.

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

#### <a name="verify-the-final-form-of-the-featurized-table"></a>A felvázolt táblázat végleges formájának ellenőrzése
    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Most már készen állunk arra, hogy az [Azure Machine Learningben](https://studio.azureml.net)modellépítést és modelltelepítést is eltegyünk. Az adatok készen állnak a korábban azonosított előrejelzési problémák bármelyikére, nevezetesen:

1. Bináris besorolás: Annak előrejelzése, hogy egy tipp et fizettek-e egy utazásért.
2. Többosztályos besorolás: A kifizetett tipp tartományának előrejelzése a korábban meghatározott osztályok szerint.
3. Regressziós feladat: Az utazásért fizetett tipp összegének előrejelzése.  

## <a name="building-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Modellek létrehozása az Azure Machine Learningben
A modellezési gyakorlat megkezdéséhez jelentkezzen be az Azure Machine Learning-munkaterületre. Ha még nem hozott létre gépi tanulási munkaterületet, olvassa [el az Azure Machine Learning-munkaterület létrehozása című témakört.](../studio/create-workspace.md)

1. Az Azure Machine Learning első lépései: [Mi az Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Jelentkezzen be az [Azure Machine Learning Studio szolgáltatásba.](https://studio.azureml.net)
3. A Studio kezdőlapja rengeteg információt, videót, oktatóanyagot, a modulokhivatkozásra mutató hivatkozásokat és egyéb forrásokat tartalmaz. Az Azure Machine Learningről további információt az [Azure Machine Learning dokumentációs központjában](https://azure.microsoft.com/documentation/services/machine-learning/)talál.

Egy tipikus képzési kísérlet a következő lépésekből áll:

1. Hozzon létre egy **+ÚJ** kísérletet.
2. Az adatok az Azure Machine Learning be.
3. Szükség szerint elődolgozza, átalakítsa és manipulálja az adatokat.
4. Szükség szerint hozzon létre funkciókat.
5. Az adatok felosztása betanítási/érvényesítési/tesztelési adatkészletekre (vagy mindegyikhez külön adatkészletek).
6. Válasszon ki egy vagy több gépi tanulási algoritmust a megoldandó tanulási problémától függően. Például bináris besorolás, többosztályos besorolás, regresszió.
7. Egy vagy több modell betanítása a betanítási adatkészlet használatával.
8. Pontzítsa az érvényesítési adatkészletet a betanított modell(ek) használatával.
9. Értékelje ki a modell(ek) a tanulási probléma megfelelő metrikáinak kiszámításához.
10. Hangolja be a modell(eke)t, és válassza ki az üzembe helyezhető legjobb modellt.

Ebben a gyakorlatban már megvizsgáltuk és megterveztük az adatokat az SQL Serverben, és úgy döntöttünk, hogy a minta mérete az Azure Machine Learning betöltéséhez. Építeni egy vagy több előrejelzési modellek, úgy döntöttünk:

1. Az adatok az Azure Machine Learning az [Adatok importálása][import-data] modul, elérhető az **adatok bemeneti és kimeneti** szakaszban. További információt az [Adatok modul importálása][import-data] modul referencialapján talál.
   
    ![Azure Machine Learning importálási adatai][17]
2. Válassza ki az **Azure SQL Database** **adatforrásként** a **Tulajdonságok** panelen.
3. Írja be az adatbázis DNS-nevét az **Adatbázis-kiszolgáló neve** mezőbe. Formátum:`tcp:<your_virtual_machine_DNS_name>,1433`
4. Írja be az **adatbázis nevét** a megfelelő mezőbe.
5. Írja be az **SQL-felhasználónevet** a **Kiszolgáló felhasználói fiók nevébe**, a **jelszót** pedig a Kiszolgáló **felhasználói fiókjának jelszavába.**
7. Az **Adatbázis-lekérdezés** szerkesztése szövegterületen illessze be a szükséges adatbázismezőket kibontó lekérdezést (beleértve a számított mezőket, például a címkéket is), és vegye le az adatokat a kívánt mintaméretre.

Egy példa egy bináris osztályozási kísérlet olvasása adatokat közvetlenül az SQL Server adatbázis az alábbi ábrán látható. Hasonló kísérletek et lehet kialakítani a többosztályos osztályozási és regressziós problémákhoz.

![Azure Machine Learning Train][10]

> [!IMPORTANT]
> Az előző szakaszokban megadott modellezési adatok kinyerése és mintavételezési lekérdezési példáiban a lekérdezés a **három modellezési gyakorlat összes címkéje szerepel.** Egy fontos (szükséges) lépés az egyes modellezési gyakorlatok, hogy **kizárják** a felesleges címkéket a másik két probléma, és minden más **cél szivárgás**. Például bináris osztályozás esetén használja a **billentett** címkét, és zárja ki a mezők **\_tipposztályát**, **a\_tipp összegét**és a teljes **\_összeget.** Az utóbbiak célpontszivárgások, mivel azt jelentik, hogy a borravalót kifizették.
> 
> A szükségtelen oszlopok és/vagy a célszivárgások kizárásához használhatja az Oszlopok kijelölése az [Adatkészlet modulban][select-columns] vagy a [Metaadatok szerkesztése modult.][edit-metadata] További információt az [Oszlopok kijelölése az adatkészletben][select-columns] és [a Metaadat-referencialapok szerkesztése][edit-metadata] című témakörben talál.
> 
> 

## <a name="deploying-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Modellek üzembe helyezése az Azure Machine Learningben
Ha a modell készen áll, könnyen üzembe helyezheti, mint egy webszolgáltatás közvetlenül a kísérletből. Az Azure Machine Learning webszolgáltatások üzembe helyezéséről az [Azure Machine Learning webszolgáltatás telepítése](../studio/deploy-a-machine-learning-web-service.md)című témakörben talál további információt.

Új webszolgáltatás üzembe helyezéséhez a következőket kell tenni:

1. Hozzon létre egy pontozási kísérletet.
2. Telepítse a webszolgáltatást.

Ha egy **befejezett betanítási** kísérletből pontozási kísérletet szeretne létrehozni, kattintson a **PONTOZÁSI KÍSÉRLET LÉTREHOZÁSA gombra** az alsó műveletsávon.

![Azure pontozás][18]

Az Azure Machine Learning megkísérli a pontozási kísérlet létrehozása a betanítási kísérlet összetevői alapján. Különösen a következőket fogja meg:

1. Mentse a betanított modellt, és távolítsa el a modell betanítási modulokat.
2. A várt bemeneti adatsémát jelölő logikai **bemeneti port** azonosítása.
3. Azonosítsa a várt webszolgáltatás kimeneti sémáját jelző logikai **kimeneti portot.**

A pontozási kísérlet létrehozásakor tekintse át, és szükség szerint módosítsa. Egy tipikus korrekció, hogy cserélje le a bemeneti adatkészlet és/vagy lekérdezés egy, amely kizárja a címke mezőket, mivel ezek a címkék nem lesz elérhető a sémában, amikor a szolgáltatás neve. Az is ajánlott, hogy csökkentse a bemeneti adatkészlet méretét és/vagy a lekérdezés néhány rekordot, elég a bemeneti séma jelzéséhez. A kimeneti port esetében gyakori, hogy kizárja az összes bemeneti mezőt, és csak a **pontozott címkéket** és a **pontozott valószínűségeket** tartalmazza a kimenetben az Oszlopok kijelölése az [adatkészlet modulban][select-columns] használatával.

Az alábbi ábrán egy mintapontozási kísérlet látható. Amikor készen áll az üzembe helyezésre, kattintson a **KÖZZÉTÉTEL WEBSZOLGÁLTATÁS** gombra az alsó műveletsávon.

![Azure Machine Learning közzététele][11]

Összefoglalva, ebben a forgatókönyv-oktatóanyagban létrehozott egy Azure-adatelemzési környezetet, és egy nagy nyilvános adatkészlettel dolgozott az adatgyűjtéstől a modellbetanításig és az Azure Machine Learning webszolgáltatás üzembe helyezéséig.

### <a name="license-information"></a>Licencadatai
Ezt a mintaforgatókönyvet és a hozzá tartozó parancsfájlokat és IPython-jegyzetfüzeteket a Microsoft az MIT-licenc alatt osztja meg. További részletekért tekintse meg a LICENSE.txt fájlt a GitHub mintakódjának könyvtárában.

### <a name="references"></a>Referencia
• [Andrés Monroy NYC Taxi utak Letöltés oldal](https://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC's Taxi Trip adatok Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi és Limuzin Bizottság Kutatási és Statisztikai](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

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

---
title: Modell létrehozása és üzembe helyezése az Azure szinapszis Analytics használatával – csoportos adatelemzési folyamat
description: Egy gépi tanulási modell létrehozása és üzembe helyezése az Azure szinapszis Analytics használatával nyilvánosan elérhető adatkészlettel.
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
ms.openlocfilehash: e64b951a8bb96b25a6ef917b4cebe077d6dd6657
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718446"
---
# <a name="the-team-data-science-process-in-action-using-azure-synapse-analytics"></a>A csoportos adatelemzési folyamat működés közben: az Azure szinapszis Analytics használata
Ebben az oktatóanyagban bemutatjuk, hogyan hozhat létre és helyezhet üzembe gépi tanulási modellt az Azure szinapszis Analytics használatával egy nyilvánosan elérhető adatkészlethez – a [New York-i taxis](https://www.andresmh.com/nyctaxitrips/) adatkészlethez. A bináris besorolási modell alapján megjósolható, hogy egy adott utazási tipp díjköteles-e.  A modellek többosztályos besorolást tartalmaznak (legyen szó vagy sem a tippről) és a regresszióról (a tip-összeg kifizetésének eloszlása).

Az eljárás a [csoportos adatelemzési folyamat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) munkafolyamatát követi. Bemutatjuk, hogyan állíthatja be az adatelemzési környezetet, hogyan tölthető be az Azure szinapszis Analyticsbe az adat, és hogyan használható az Azure szinapszis Analytics vagy egy IPython notebook az adat-és mérnöki funkciók modellezéséhez. Ezután bemutatjuk, hogyan készíthet és helyezhet üzembe az Azure Machine Learning-modell.

## <a name="dataset"></a>A New York-i taxis adatkészlete
A NYC Taxi útadatok körülbelül 20 GB tömörített CSV-fájlok (~ 48 GB tömörítetlen) áll, minden egyes út 173 milliónál egyes utak és a díjakat fizetni. Minden egyes út rekord a begyűjtés és dropoff helyek és időpontok, licencszám anonimizált feltörés (illesztőprogramok) és medallion (taxi az egyedi azonosító) száma tartalmazza. Az adatok minden lelassítja ismerteti az év 2013-hoz, és minden hónapban megtalálható a következő két adatkészletet:

1. A **trip_data. csv** fájl az utazás részleteit tartalmazza, például az utasok számát, a felvételi és a lemorzsolódási pontokat, az utazási időtartamot és a menetidő hosszát. Az alábbiakban néhány példa rekordokat:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. A **trip_fare. csv** fájl az egyes utazások díjait tartalmazza, például a fizetési típust, a viteldíjat, a pótdíjat, az adókat, a tippeket és az autópályadíjat, valamint a teljes fizetett összeget. Az alábbiakban néhány példa rekordokat:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Az utazás\_az adathoz és az\_utazáshoz való csatlakozáshoz használt **egyedi kulcs** a következő három mezőből áll:

* medallion,
* csapkod\_licenc és
* felvételi\_DateTime.

## <a name="mltasks"></a>Az előrejelzési feladatok három típusának kezelése
Három előrejelzési problémát határozunk meg a *tipp\_összeg* alapján, amely háromféle modellezési feladatot szemléltet:

1. **Bináris besorolás**: Ha meg szeretné jósolni, hogy egy adott utazási tipp kifizetése megtörtént-e, akkor a $0-nál nagyobb számú *Tipp\_* pozitív példa, míg a *Tipp\_$0 mennyisége* negatív példa.
2. **Többosztályos besorolás**: az utazáshoz fizetett tipp tartományának előrejelzése. A *tipp\_összegét* öt raktárhelyre vagy osztályra osztjuk:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Regressziós feladat**: az utazáshoz fizetett tipp mennyiségének előrejelzése.

## <a name="setup"></a>Az Azure adatelemzési környezet beállítása a speciális elemzésekhez
Az Azure Data Science környezet beállításához, kövesse az alábbi lépéseket.

**Saját Azure Blob Storage-fiók létrehozása**

* Ha saját Azure Blob Storage-tárolót épít ki, válasszon egy földrajzi helyet az Azure Blob Storage-hoz a vagy a lehető legközelebb az **USA déli középső**régiójában, ahol a NYC-taxi adatai tárolódnak. Az adatok tárolóba a nyilvános blobtárolóban AzCopy használatával a saját tárfiókja másolódnak. Minél közelebb az Azure blob storage-USA déli középső Régiójában, annál gyorsabban (4. lépés) Ez a feladat befejeződik.
* Saját Azure Storage-fiók létrehozásához kövesse az [Azure Storage-fiókokról](../../storage/common/storage-create-storage-account.md)című témakör lépéseit. Mindenképpen adja meg a jegyzetek a következő tárfiók hitelesítő adatainak értékeit, hogy ez az útmutató későbbi részében lesz szükség.

  * **Storage-fiók neve**
  * **Storage-fiók kulcsa**
  * A **tároló neve** (amelyet az Azure Blob Storage-ban tárolni kíván)

**Az Azure szinapszis Analytics-példány kiépítése.**
Az Azure-beli szinapszis Analytics-példány kiépítéséhez kövesse a [Azure SQL Data Warehouse létrehozása és lekérdezése a Azure Portalben](../../sql-data-warehouse/create-data-warehouse-portal.md) című dokumentációt. Győződjön meg arról, hogy a következő Azure szinapszis Analytics-beli hitelesítő adatokkal rendelkezik, amelyeket a későbbi lépésekben fog használni.

* **Kiszolgáló neve**: \<kiszolgáló neve >. database. Windows. net
* **SQLDW (adatbázis) neve**
* **Felhasználónév**
* **Jelszó**

**Telepítse a Visual studiót és a SQL Server Data Tools.** Útmutatásért lásd: [első lépések a Visual Studio 2019 for SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Kapcsolódjon az Azure szinapszis Analyticshez a Visual Studióval.** Útmutatásért lásd: az 1 & 2. lépés a [kapcsolódás Azure SQL Data Warehousehoz](../../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

> [!NOTE]
> Futtassa a következő SQL-lekérdezést az Azure szinapszis Analyticsben létrehozott adatbázison (a kapcsolódási témakör 3. lépésében megadott lekérdezés helyett) a **főkulcs létrehozásához**.
>
>

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Hozzon létre egy Azure Machine Learning munkaterületet az Azure-előfizetésében.** Útmutatásért lásd: [Azure Machine learning munkaterület létrehozása](../studio/create-workspace.md).

## <a name="getdata"></a>Az Azure szinapszis Analytics szolgáltatásba való betöltés
Nyissa meg a Windows PowerShell-parancs konzolt. A következő PowerShell-parancsok futtatásával töltse le a GitHubon megosztott SQL-parancsfájlokat egy helyi könyvtárba, amelyet a *-DestDir*paraméterrel adott meg. A *DestDir* paraméter értékét bármely helyi könyvtárra módosíthatja. Ha a *-DestDir* nem létezik, akkor a PowerShell-szkript hozza létre.

> [!NOTE]
> Előfordulhat, hogy **rendszergazdaként kell futtatnia** a következő PowerShell-parancsfájl futtatásakor, ha a *DestDir* -címtárhoz rendszergazdai jogosultság szükséges a létrehozásához vagy a írásához.
>
>

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

A sikeres végrehajtás után a jelenlegi munkakönyvtára a *-DestDir-* ra változik. Kell látnia képernyő jelenik meg az alábbi módon:

![Aktuális munkakönyvtárban történt változtatásokat][19]

A *-DestDir*futtassa a következő PowerShell-parancsfájlt rendszergazdai módban:

    ./SQLDW_Data_Import.ps1

Amikor a PowerShell-parancsfájl első alkalommal fut, a rendszer arra kéri, hogy adja meg az adatokat az Azure szinapszis Analytics és az Azure Blob Storage-fiókból. Ez a PowerShell-szkript befejezése után az első alkalommal a hitelesítő adatokat futtató, bemeneti fog alkalmazáskonfigurációjának SQLDW.conf konfigurációs fájlt a jelenlegi munkakönyvtárban. A jövőbeli Futtatás a PowerShell parancsfájl van lehetőség, olvassa el az összes szükséges paraméterek a konfigurációs fájlból. Ha módosítania kell néhány paramétert, megadhatja a képernyőn megjelenő paramétereket a konfigurációs fájl törlésével és a paraméterek értékeinek a megadásával, vagy módosíthatja a paramétereket úgy, hogy a SQLDW. conf fájlt szerkeszti a *-DestDir* könyvtárban.

> [!NOTE]
> Annak elkerülése érdekében, hogy a séma neve ne legyen ütközik az Azure Azure szinapszis Analyticsben már meglévő paraméterekkel, ha közvetlenül az SQLDW. conf fájlból olvas be paramétereket, a rendszer egy 3 jegyű véletlenszerű számot ad hozzá a séma neveként a SQLDW. conf fájlból alapértelmezett sémaként. az egyes futtatások neve. A PowerShell-parancsfájl kérheti a séma neve: a név felhasználói belátása szerint lehet megadni.
>
>

Ez a **PowerShell-parancsfájl** a következő feladatokat hajtja végre:

* **Letölti és telepíti a AzCopy**, ha a AzCopy még nincs telepítve

        $AzCopy_path = SearchAzCopy
        if ($AzCopy_path -eq $null){
               Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
            InstallAzCopy
            $AzCopy_path = SearchAzCopy
        }
            $env_path = $env:Path
            for ($i=0; $i -lt $AzCopy_path.count; $i++){
                if ($AzCopy_path.count -eq 1){
                    $AzCopy_path_i = $AzCopy_path
                } else {
                    $AzCopy_path_i = $AzCopy_path[$i]
                }
                if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
                    Write-Host $AzCopy_path_i 'not in system path, add it...'
                    [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
                    $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
                    $env_path = $env:Path
                }
* **Adatok másolása a saját blob Storage-fiókjába** a nyilvános blobból a AzCopy

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* A következő parancsokkal tölti be az adatait a saját blob Storage-fiókjából **(a LoadDataToSQLDW. SQL futtatásával) az Azure szinapszis Analytics** szolgáltatásba.

  * Hozzon létre egy sémát

          EXEC (''CREATE SCHEMA {schemaname};'');
  * Adatbázishoz kötődő hitelesítő adatok létrehozása

          CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
          WITH IDENTITY = ''asbkey'' ,
          Secret = ''{StorageAccountKey}''
  * Külső adatforrás létrehozása Azure Storage-blobhoz

          CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;

          CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;
  * A csv-fájlok a külső fájlformátum létrehozása. Tömörítetlen adatok és a függőleges vonás karakterrel elválasztva egymástól mezők.

          CREATE EXTERNAL FILE FORMAT {csv_file_format}
          WITH
          (
              FORMAT_TYPE = DELIMITEDTEXT,
              FORMAT_OPTIONS
              (
                  FIELD_TERMINATOR ='','',
                  USE_TYPE_DEFAULT = TRUE
              )
          )
          ;
  * Külső diszkont és NYC taxi adatkészlet trip táblák létrehozása az Azure blob storage-ban.

          CREATE EXTERNAL TABLE {external_nyctaxi_fare}
          (
              medallion varchar(50) not null,
              hack_license varchar(50) not null,
              vendor_id char(3),
              pickup_datetime datetime not null,
              payment_type char(3),
              fare_amount float,
              surcharge float,
              mta_tax float,
              tip_amount float,
              tolls_amount float,
              total_amount float
          )
          with (
              LOCATION    = ''/nyctaxifare/'',
              DATA_SOURCE = {nyctaxi_fare_storage},
              FILE_FORMAT = {csv_file_format},
              REJECT_TYPE = VALUE,
              REJECT_VALUE = 12
          )

            CREATE EXTERNAL TABLE {external_nyctaxi_trip}
            (
                   medallion varchar(50) not null,
                   hack_license varchar(50)  not null,
                   vendor_id char(3),
                   rate_code char(3),
                   store_and_fwd_flag char(3),
                   pickup_datetime datetime  not null,
                   dropoff_datetime datetime,
                   passenger_count int,
                   trip_time_in_secs bigint,
                   trip_distance float,
                   pickup_longitude varchar(30),
                   pickup_latitude varchar(30),
                   dropoff_longitude varchar(30),
                   dropoff_latitude varchar(30)
            )
            with (
                LOCATION    = ''/nyctaxitrip/'',
                DATA_SOURCE = {nyctaxi_trip_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12
            )

    - Adatok betöltése az Azure Blob Storage külső tábláiból az Azure szinapszis Analyticsbe

            CREATE TABLE {schemaname}.{nyctaxi_fare}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_fare}
            ;

            CREATE TABLE {schemaname}.{nyctaxi_trip}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_trip}
            ;

    - Hozzon létre egy minta-adattábla (NYCTaxi_Sample), és adatok beszúrása, hogy az SQL-lekérdezéseket a trip és diszkont táblák kiválasztása. (A forgatókönyv néhány lépésének ezt a táblázatot kell használnia.)

            CREATE TABLE {schemaname}.{nyctaxi_sample}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            (
                SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
                tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
                tip_class = CASE
                        WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
                FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
                WHERE datepart("mi",t.pickup_datetime) = 1
                AND t.medallion = f.medallion
                AND   t.hack_license = f.hack_license
                AND   t.pickup_datetime = f.pickup_datetime
                AND   pickup_longitude <> ''0''
                AND   dropoff_longitude <> ''0''
            )
            ;

A tárfiók földrajzi helye hatással van a betöltési időt.

> [!NOTE]
> A saját blob Storage-fiókja földrajzi helyétől függően az adatok nyilvános blobból a privát Storage-fiókba való másolásának folyamata körülbelül 15 percet, vagy akár hosszabb időt is igénybe vehet, valamint az adatoknak a Storage-fiókból az Azure-ba való betöltésének folyamata Az Azure szinapszis Analytics 20 percet vagy hosszabb időt is igénybe vehet.
>
>

Dönthet arról, hogy milyen tegye Ha ismétlődő a forrás és cél fájlokat kell.

> [!NOTE]
> Ha a .csv-fájlokat másolja a nyilvános blob storage a saját blob storage-fiók már szerepel a privát blob storage-fiók, az AzCopy ekkor megkérdezi, hogy szeretné-e felülírja őket. Ha nem kívánja felülírni őket, írja be az **n** bemenetet, amikor a rendszer kéri. Ha felül szeretné írni az **összeset** , írja be **a** következőt:, ha a rendszer kéri. **A.** CSV fájlok egyenként is beírhatók.
>
>

![Az AzCopy kimenete][21]

Használhatja a saját adatait. Ha az adatok a helyszíni gépen, a valós életben alkalmazásban, az AzCopy továbbra is használhatja a helyszíni adatok feltöltése a saját Azure blob Storage. Csak a **forrás** helyét kell módosítania, `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`a PowerShell-parancsfájl AzCopy parancsában az adatait tartalmazó helyi könyvtárba.

> [!TIP]
> Ha az adatok már szerepelnek a saját Azure Blob Storage-ban a valós életben, akkor kihagyhatja a AzCopy lépést a PowerShell-szkriptben, és közvetlenül feltöltheti az adatait az Azure Azure szinapszis Analytics szolgáltatásba. Ezt a beállítást, a szkriptet a formátum az adatok manipulálását további módosításokat.
>
>

Ez a PowerShell-szkript az Azure szinapszis elemzési információit is csatlakoztatja az adatelemzési példában található fájlokhoz SQLDW_Explorations. SQL, SQLDW_Explorations. ipynb és a SQLDW_Explorations_Scripts. a. file-ban, így a három fájl készen áll a kipróbálásra azonnal a PowerShell-parancsfájl befejeződése után.

A sikeres végrehajtást követően látni fogja a képernyő alábbi módon:

![Egy sikeres parancsfájl végrehajtása kimenete][20]

## <a name="dbexplore"></a>Az Azure szinapszis Analytics adatfelderítési és-funkciós mérnöki szolgáltatása
Ebben a szakaszban a **Visual Studio Adateszközeivel**közvetlenül az Azure szinapszis Analytics szolgáltatásban futtatott SQL-lekérdezéseket végzünk az adatok feltárásához és a szolgáltatások létrehozásához. Az ebben a szakaszban használt összes SQL-lekérdezés megtalálható a *SQLDW_Explorations. SQL*nevű minta parancsfájlban. Ez a fájl már letöltötte a helyi könyvtárba a PowerShell parancsfájlhoz. Azt is lekérheti a [githubról](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). A GitHubon található fájl azonban nem rendelkezik az Azure szinapszis Analytics-információkkal, amelyekhez csatlakoztatva van.

Kapcsolódjon az Azure szinapszis Analytics szolgáltatáshoz a Visual Studióval az Azure szinapszis Analytics bejelentkezési nevével és jelszavával, és nyissa meg az **SQL Object Explorer** az adatbázis és a táblák importálásának megerősítéséhez. Kérje le a *SQLDW_Explorations. SQL* fájlt.

> [!NOTE]
> Egy párhuzamos adatraktár (PDW) lekérdezési szerkesztőjének megnyitásához használja az **új lekérdezési** parancsot, amíg a PDW be van jelölve az **SQL Object Explorer**. A standard SQL-Lekérdezésszerkesztő PDW által nem támogatott.
>
>

A jelen szakaszban leírt adatelemzési és szolgáltatás-létrehozási feladatok típusai:

* Fedezze fel az adatokat a különböző időtartományok néhány mezőt disztribúciók.
* Vizsgálja meg a szélességi és hosszúsági mezők adatok minőségét.
* Bináris és többosztályos besorolási Címkék készítése a **tipp\_összeg**alapján.
* Szolgáltatások készítése és trip távokat számítási/összehasonlítása.
* Csatlakozzon a két táblázatot, és csomagolja ki egy véletlenszerűen vett minta modellek létrehozásához használt.

### <a name="data-import-verification"></a>Adatok importálása ellenőrzése
Ezeket a lekérdezéseket, adja meg a sorok számát egy gyors ellenőrzés és oszlopokat a táblában korábban a Polybase a párhuzamos tömeges segítségével importálja,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Kimenet:** 173 179 759 és 14 oszlopot kell kapnia.

### <a name="exploration-trip-distribution-by-medallion"></a>Feltárás: Utazás eloszlás medallion szerint
Ez a példa lekérdezés azonosítja a medallions (-i taxik számokat), amely egy adott időtartamon belül több mint 100 lelassítja befejeződött. A lekérdezés kihasználja a particionált tábla elérését, mivel azt a **pickup\_datetime**értékkel rendelkező partíciós sémája feltétele. A teljes adatkészlet lekérdezése is teszi a particionált tábla használja, és/vagy a vizsgálat index.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Kimenet:** A lekérdezésnek egy táblázatot kell visszaadnia, amely meghatározza a 13 369 medálokat (taxikat) és a 2013-ben befejezett utak számát. Az utolsó oszlopban befejeződött lelassítja darabszámát tartalmazza.

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Feltárás: Utazás terjesztési medallion és hack_license
Ebben a példában a medallions (-i taxik számokat) azonosítja, és hack_license számok (illesztőprogramok), amely befejezése több mint 100 lelassítja a megadott időn belül.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Kimenet:** A lekérdezésnek egy 13 369-sorral rendelkező táblázatot kell visszaadnia, amely meghatározza a 13 369-es autó-és illesztőprogram 2013-azonosítókat, amelyek több, a 100-as utakon történtek Az utolsó oszlopban befejeződött lelassítja darabszámát tartalmazza.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Adatok vizsgálatának: helytelen hosszúsági és/vagy szélességi ellenőrzése
Ebben a példában folytat, ha a hosszúsági és/vagy szélességi mezők vagy érvénytelen értéket tartalmazza (radián fok – 90 és 90 között kell lennie), vagy rendelkezik (0, 0) koordinátáit.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Kimenet:** A lekérdezés 837 467 olyan utakat ad vissza, amelyek érvénytelen hosszúságú és/vagy szélességi mezővel rendelkeznek.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Feltárás: Formabontó nem Formabontó lelassítja terjesztési és összehasonlítása
Ebben a példában megkeresi a utazásokat, és hány volt Formabontó, amelyek nem voltak Formabontó, a megadott időtartam során (vagy a teljes adatkészlethez, ha a teljes évre vonatkozó, ahogy azt az itt beállított) száma. Ehhez a terjesztéshez tükrözi a bináris címke terjesztése bináris osztályozási modellezési később használható.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Kimenet:** A lekérdezésnek a következő tip-frekvenciákat kell visszaadnia az 2013-as évre: 90 447 622-as és 82 264 709 nem.

### <a name="exploration-tip-classrange-distribution"></a>Feltárás: Tipp osztály és címtartomány terjesztési
Ebben a példában kiszámítja a terjesztési tipp címtartományok egy adott időtartamon (vagy a teljes adatkészlethez, ha a teljes évre vonatkozó). A címkézési osztályok ezen eloszlását később a többosztályos besorolás modellezéséhez fogjuk használni.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

**Kimeneti**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Feltárás: Számítás, és hasonlítsa össze a trip távolság
Ebben a példában a begyűjtés és dropoff hosszúsági alakítja át, és SQL földrajzi szélesség mutat, kiszámítja az SQL a földrajzi pontokat különbség használatával trip távolság és egy véletlenszerűen vett minta az eredmények az összehasonlításhoz adja vissza. A példában az eredményeket, és csak a a minőségi értékelés lekérdezési korábban tartozó érvényes koordináták korlátozza.

    /****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

### <a name="feature-engineering-using-sql-functions"></a>Funkciófejlesztési SQL függvények használatával
Néha SQL-ben a műszaki elemzésekhez hatékony lehetőség is lehet. Ebben az útmutatóban meghatározott egy SQL-függvény alapján számítja ki a közvetlen a begyűjtés és dropoff helyek közötti távolság. A **Visual Studio Adateszközeiben**a következő SQL-szkripteket futtathatja.

Itt van az SQL-szkriptet, amely meghatározza a távolságskála függvény.

    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function calculate the direct distance between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

Íme egy példa funkciókat létrehozni az SQL-lekérdezést a függvény hívása:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Kimenet:** Ez a lekérdezés létrehoz egy táblát (2 803 538 sorral) a felvételi és a lemorzsolódási szélességi és hosszúsági körökkel, valamint a megfelelő közvetlen távolságot mérföldben. Az első három sorra vonatkozó eredmények:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>A modell létrehozásához az adatok előkészítése
A következő lekérdezés összekapcsolja a **nyctaxi\_Trip** és a **nyctaxi\_viteldíjak** táblázatát, **létrehoz**egy bináris besorolási címkét, egy többosztályos besorolási címkét **\_osztályt**, és Kinyeri a teljes csatlakoztatott adatkészletből származó mintát. A mintavétel egy részét a begyűjtési időpont alapján lelassítja lekérésével.  Ez a lekérdezés átmásolható közvetlenül a [Azure Machine learning Studio (klasszikus)](https://studio.azureml.net) [Importálás][importálja] modulba az Azure-beli SQL Database példányból származó közvetlen adatfeldolgozáshoz. A lekérdezés nem tartalmazza a helytelen rekordot (0, 0) koordinátáit.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Amikor elkészült, lépjen az Azure Machine Learning, a következő lehetőségekkel vagy:

1. Mentse a végső SQL-lekérdezést az adatok kinyeréséhez és mintavételéhez, majd másolja be a lekérdezést közvetlenül egy importálási adatokba, majd[importálja] az adatok importálása modulba a Azure Machine learning, vagy
2. Maradjon meg az új Azure szinapszis Analytics-táblázatban a modell-létrehozáshoz használni kívánt mintavételes és megtervezett adattípusok, és használja az új táblázatot az[adatimportálási] Adatmodul [importálása]Azure Machine Learningban. A korábbi lépésben a PowerShell-szkript elvégezte ezt a feladatot. Ezt a táblázatot az adatok importálása modullal közvetlenül a olvashat.

## <a name="ipnb"></a>Adatfelderítési és-funkció-mérnöki IPython notebookon
Ebben a szakaszban az adatok feltárását és a szolgáltatás létrehozását fogjuk elvégezni a Python-és SQL-lekérdezésekkel a korábban létrehozott Azure szinapszis Analytics használatával. Egy **SQLDW_Explorations. ipynb** nevű és egy Python-parancsfájlt tartalmazó minta IPython-jegyzetfüzetet **SQLDW_Explorations_Scripts.** a helyi könyvtárba letöltöttük. A [githubon](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW)is elérhetők. Két fájlt a Python-szkriptek megegyeznek. A Python-parancsfájl tárolásához kapja meg abban az esetben, ha nem rendelkezik az IPython Notebook kiszolgálót. Ez a két minta Python-fájl a **python 2,7**alatt lett tervezve.

A IPython jegyzetfüzetben és a helyi gépre letöltött Python-parancsfájlban a szükséges Azure szinapszis-elemzési információkat a korábban a PowerShell-parancsfájl csatlakoztatta. Azok a módosítás nélkül végrehajtható.

Ha már beállított egy Azure Machine Learning munkaterületet, közvetlenül feltöltheti a minta IPython notebookot a AzureML IPython notebook szolgáltatásba, és megkezdheti a futtatását. Az alábbi lépésekkel töltheti fel a AzureML IPython notebook Service-be:

1. Jelentkezzen be a Azure Machine Learning munkaterületre, kattintson a felül található **Studio** elemre, majd a weblap bal oldalán kattintson a **jegyzetfüzetek** elemre.

    ![Kattintson a Studio ezután NOTEBOOKOK][22]
2. Kattintson az **új** elemre a weblap bal alsó sarkában, és válassza a **Python 2**lehetőséget. Ezután adjon meg egy nevet a jegyzetfüzetet, és kattintson a pipa jelre az új üres IPython Notebook létrehozása.

    ![Az új gombra, majd válassza ki a Python 2][23]
3. Kattintson a **Jupyter** szimbólumra az új IPython jegyzetfüzet bal felső sarkában.

    ![Kattintson a Jupyter szimbólum][24]
4. Húzza a minta IPython jegyzetfüzetet a AzureML IPython notebook szolgáltatás **faoldalára** , és kattintson a **feltöltés**elemre. Ezt követően a minta IPython Notebook lesz feltöltve az AzureML IPython Notebook szolgáltatásba.

    ![Kattintson a feltöltés][25]

A minta futtatásához IPython Notebook vagy a Python-szkriptfájlt, a következő Python-csomagok szükségesek. Az AzureML IPython Notebook szolgáltatás használja, ha ezek a csomagok előre telepítve lett.

- pandas
- numpy
- matplotlib
- a pyodbc
- PyTables

A nagy mennyiségű Azure Machine Learning speciális analitikai megoldásainak létrehozásakor itt látható a javasolt folyamat:

* Olvassa el az adatok néhányat példaként egy memórián belüli adatok keretbe.
* Hajtsa végre az egyes Vizualizációk és explorations mintavételezett adatok használatával.
* A mintavételezett adatok funkciófejlesztési kísérletezhet.
* Ha nagyobb adatfeltárásra, adatkezelésre és-fejlesztésre van lehetőség, a Python használatával közvetlenül az Azure szinapszis Analytics szolgáltatásban adhat ki SQL-lekérdezéseket.
* Döntse el, a mintanagyság kell, hogy megfelelő-e az Azure Machine Learning-modell létrehozásához.

A következőket: néhány adatfeltárás, adatvizualizáció és példák mérnöki funkció. További adatok explorations megtalálható a minta IPython Notebook és a példa Python-parancsfájl tárolásához.

### <a name="initialize-database-credentials"></a>Adatbázis-hitelesítő adatok inicializálása
Az adatbázis-kapcsolati beállítások a következő változók inicializálása:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Adatbázis-kapcsolat létrehozása
Íme a kapcsolati karakterláncot, amely létrehozza az adatbázishoz való csatlakozáshoz.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Sorok és oszlopok a tábla < nyctaxi_trip > jelentés száma
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Sorainak = 173179759
* Oszlopok teljes száma = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_fare"></a>Sorok és oszlopok a tábla < nyctaxi_fare > jelentés száma
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Sorainak = 173179759
* Oszlopok teljes száma = 11

### <a name="read-in-a-small-data-sample-from-the-azure-synapse-analytics-database"></a>Kis adatminta beolvasása az Azure szinapszis Analytics-adatbázisából
    t0 = time.time()

    query = '''
        SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
        WHERE datepart("mi",t.pickup_datetime) = 1
        AND   t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Olvassa el a minta tábla 14.096495 másodperc idő.
A beolvasott sorok és oszlopok száma = (1000, 21).

### <a name="descriptive-statistics"></a>Leíró statisztika
Most már készen áll a mintavételezett adatok feltárására. Először tekintse meg az **utazás\_távolságot** (vagy bármely más, a megadható mezőt) leíró statisztikát.

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Vizualizáció: Diagram – példa
Ezután megnézzük a Dobozdiagram megjelenítése a quantiles trip távolság számára.

    df1.boxplot(column='trip_distance',return_type='dict')

![Box-diagram kimenet][1]

### <a name="visualization-distribution-plot-example"></a>Vizualizáció: Terjesztési diagram példa
A terjesztés és a mintavételezett trip távolságot a hisztogram megjelenítő grafikon.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Terjesztési diagram kimenet][2]

### <a name="visualization-bar-and-line-plots"></a>Vizualizáció: Sáv- és grafikon
Ebben a példában azt az öt bins trip távolság bin és a dobozolási eredményeinek képi megjelenítése.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Azt jeleníti meg a fenti bin terjesztési egy sávon, vagy az ábrázolást. sor:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Sáv diagram kimenet][3]

és

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Diagram kimeneti sor][4]

### <a name="visualization-scatterplot-examples"></a>Vizualizáció: Teszteredményekből példák
Megmutatjuk, hogy az **utazás\_idő\_a\_a másodpercben** és az **út\_távolság** között, hogy van-e korreláció

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Idő és a távolság közötti kapcsolat Teszteredményekből kimenete][6]

Hasonlóképpen ellenőrizhető a **ráta\_a kód** és az **utazás\_távolság**közötti kapcsolat.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Kód és a távolság közötti kapcsolat Teszteredményekből kimenete][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>A mintavételezett adatok IPython notebook az SQL-lekérdezések használata az adatok feltárása
Ebben a szakaszban a fent létrehozott új táblázatban megőrzött mintavételes adateloszlásokat vizsgáljuk. Hasonló feltárások is elvégezhetők az eredeti táblák használatával.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Feltárás: Jelentés sorok és oszlopok száma a mintában szereplő táblázatban
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Feltárás: Formabontó/nem azokat kioldják terjesztési
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Feltárás: Tipp osztály terjesztési
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Feltárás: Jeleníti meg a tip terjesztési-osztály
    tip_class_dist['tip_freq'].plot(kind='bar')

![#26 ábrázolása][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Feltárás: Napi terjesztési utak
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Feltárás: Kivételre medallion megoszlása
    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Feltárás: Utazás terjesztési medallion és feltörés licenc
    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Feltárás: Utazás idő terjesztése
    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Feltárás: Utazás távolság terjesztési
    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Feltárás: Fizetési információtípus terjesztése
    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Ellenőrizze a végleges képernyő featurized tábla
    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Modellek készítése Azure Machine Learning
Most már készen áll az építés és a modell üzembe helyezésének modellezésére [Azure Machine Learningban](https://studio.azureml.net). Az adatokat, nevezetesen korábban azonosított problémák előrejelzési bármelyike használható:

1. **Bináris besorolás**: megjósolhatja, hogy fizetett-e borravalót egy útra.
2. **Többosztályos besorolás**: a kifizetett tipp tartományának előrejelzéséhez a korábban definiált osztályok alapján.
3. **Regressziós feladat**: az utazáshoz fizetett tipp mennyiségének előrejelzése.

A modellezési gyakorlat megkezdéséhez jelentkezzen be a **Azure Machine learning (klasszikus)** munkaterületre. Ha még nem hozott létre Machine learning-munkaterületet, tekintse meg a [Azure Machine learning Studio (klasszikus) munkaterület létrehozása](../studio/create-workspace.md)című témakört.

1. A Azure Machine Learning megkezdéséhez tekintse meg a [Mi az a Azure Machine learning Studio (klasszikus)?](../studio/what-is-ml-studio.md)
2. Jelentkezzen be [Azure Machine learning Studioba (klasszikus)](https://studio.azureml.net).
3. A Machine Learning Studio (klasszikus) kezdőlapja rengeteg információt, videót, oktatóanyagokat, hivatkozásokat tartalmaz a modulok hivatkozásához és egyéb erőforrásokhoz. További információ a Azure Machine Learningről: [Azure Machine learning dokumentációs központ](https://azure.microsoft.com/documentation/services/machine-learning/).

Egy tipikus betanítási kísérlet a következő lépésekből áll:

1. Hozzon létre egy **+ új** kísérletet.
2. Az Azure Machine Learning Studio (klasszikus) beolvasása.
3. Szükség szerint előre feldolgozhatja, átalakíthatja és kezelheti az adatfeldolgozást.
4. Funkciók létrehozása, igény szerint.
5. Az adatok felosztása képzési érvényesítési/tesztelési adatkészletek (vagy különálló adatkészletek minden).
6. Válassza ki egy vagy több gépi tanulási algoritmusok függően a tanulás a probléma megoldásához. Például a bináris besorolás, a többosztályos besorolás, a regresszió.
7. A betanítási adatkészletet használó egy vagy több modelleket taníthat be.
8. Az érvényesítés adatkészletéhez a betanított modellek pontozása.
9. Értékelje ki a tanulási probléma a lényeges metrikák számítási a modellek.
10. Hangolja be a modell (eke) t, és válassza ki a telepítendő legjobb modellt.

Ebben a gyakorlatban már megvizsgáltuk és megtervezjük az Azure szinapszis Analytics szolgáltatásban tárolt adatmennyiséget, és a minta méretét a Azure Machine Learning Studio (klasszikus) betöltéséhez is eldöntöttük. Itt látható az eljárást követve hozhat létre, a prediktív modellek közül legalább egyet:

1. Az adatok beolvasása a Azure Machine Learning Studio (klasszikus) elemre az adatok importálása **és kimenete** szakaszban elérhető [importálási adatok][importálja] modul használatával. További információkért lásd az [adatok][importálja] adatmodul-hivatkozás lapot.

    ![Az Azure Machine Learning-importálási adatok][17]
2. Válassza a **Azure SQL Database** lehetőséget a **Tulajdonságok** panelen lévő **adatforrásként** .
3. Adja meg az adatbázis DNS-nevét az **adatbázis-kiszolgáló neve** mezőben. Formátum: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Adja meg az **adatbázis nevét** a megfelelő mezőben.
5. Adja meg az *SQL-felhasználónevet* a **kiszolgáló felhasználói fiókjának nevében**, és a *jelszót* a **kiszolgáló felhasználói fiókjának jelszavában**.
7. Az **adatbázis-lekérdezés** szövegmezőben illessze be azt a lekérdezést, amely kibontja a szükséges adatbázis-mezőket (beleértve a kiszámított mezőket, például a címkéket), és a legördülő menüben az adatok kinyerését a kívánt minta méretre.

Az alábbi ábrán egy példa az adatok közvetlenül az Azure szinapszis Analytics-adatbázisból való beolvasására (ne felejtse el lecserélni a táblázat nevét nyctaxi_trip és nyctaxi_fare a séma neve és a táblázatban használt táblanév alapján. útmutató). Hasonló kísérletek többosztályos osztályozási és regressziós probléma lehet létrehozni.

![Az Azure Machine Learning Train][10]

> [!IMPORTANT]
> Az előző szakaszban ismertetett modellezési adatok kinyerésére és mintavételezésére vonatkozó példákban a **három modellezési gyakorlat összes címkéje szerepel a lekérdezésben**. A modellezési gyakorlatok fontos (kötelező) lépése, hogy **kizárják** a másik két probléma felesleges címkéit, valamint az egyéb **megcélzott szivárgásokat**. Ha például bináris besorolást használ, használja a megjelenő **címkét, és** zárja ki a mezők **Tipp\_osztály**, a **Tipp\_mennyiség**és a **teljes\_mennyiség értékét**. Az utóbbi cél adatszivárgás mivel, azok magukban foglalják az ötlet fizetős.
>
> A szükségtelen oszlopok vagy a célzott szivárgások kizárásához használja az [Oszlopok kiválasztása az adatkészlet][select-columns] modulban vagy a [metaadatok szerkesztése][edit-metadata]lehetőséget. További információ: [Oszlopok kiválasztása az adatkészletben][select-columns] és a [metaadatokat][edit-metadata] tartalmazó hivatkozások szerkesztése.
>
>

## <a name="mldeploy"></a>Modellek üzembe helyezése Azure Machine Learning
Amikor készen áll a modellt, könnyedén telepítheti közvetlenül a kísérletből webszolgáltatásként. Az Azure ML-webszolgáltatások üzembe helyezésével kapcsolatos további információkért lásd: [Azure Machine learning webszolgáltatás üzembe](../studio/deploy-a-machine-learning-web-service.md)helyezése.

Új webszolgáltatás üzembe helyezéséhez kell tennie:

1. Hozzon létre egy pontozó kísérletet.
2. A webszolgáltatás üzembe helyezéséhez.

A **befejezett** betanítási kísérletből származó pontozási kísérlet létrehozásához kattintson a **pontozási kísérlet létrehozása** lehetőségre az alsó műveleti sávon.

![Azure Scoring][18]

Az Azure Machine Learning megkísérli a tanítási kísérlet összetevői alapján pontozási kísérlet létrehozása. Különösen hajtja végre:

1. Mentse a betanított modell, és távolítsa el a modell képzési modult.
2. Azonosítson egy logikai **bemeneti portot** , amely a várt bemeneti adatsémát jelképezi.
3. Azonosítson egy logikai **kimeneti portot** a várt webszolgáltatás kimeneti sémájának megjelenítéséhez.

A pontozási kísérlet létrehozásakor tekintse át az eredményeket, és szükség szerint végezze el a módosítást. Egy tipikus beállítás, hogy lecseréli a bemeneti adatkészletet vagy lekérdezést egy olyanra, amely kizárja a címke mezőket, mert ezek a címke mezők nem lesznek leképezve a sémához a szolgáltatás meghívásakor. Ajánlott továbbá a bemeneti adatkészlet és/vagy a lekérdezés méretének csökkentése néhány rekordra, amely elegendő a bemeneti séma jelzéséhez. A kimeneti port esetében gyakori, hogy kizárják az összes bemeneti mezőt, és csak a kimenetben szereplő **mutatókat** és a kimutatott **valószínűségeket** tartalmazzák a [DataSet oszlopok kiválasztása az adatkészlet][select-columns] modulban.

Az alábbi ábrán egy minta kísérlet pontozási biztosítunk. Ha készen áll a telepítésre, kattintson a **közzétételi webszolgáltatás** gombra az alsó műveleti sávon.

![Az Azure Machine Learning közzététele][11]

## <a name="summary"></a>Összegzés
Recap, amit eddig még ebben az oktatóanyagban a forgatókönyv, hogy létrehozott egy Azure adatelemzési környezetet, használta korábban a nagy nyilvános adatkészlet, a csoportos adatelemzési folyamat, egészen a modell betanítása, majd az adatgyűjtés keresztül tart, a az Azure Machine Learning webszolgáltatás üzembe helyezése.

### <a name="license-information"></a>Licencinformációk
Ez a minta forgatókönyv és a kísérő parancsfájlok és IPython notebook(s) osztanak meg a Microsoft mellett az MIT-licenccel. További részletekért olvassa el a következőt a GitHubon található mintakód könyvtárában: LICENSE. txt.

## <a name="references"></a>Referencia
- [Andrés Monroy NYC taxi TRIPS letöltési oldal](https://www.andresmh.com/nyctaxitrips/)
- [A New York-i taxis utazási adatvédelme Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)
- [A New York-i taxi és a limuzin Bizottság kutatási és statisztikai adatai](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sqldw-walkthrough/azuremltrain.png
[11]: ./media/sqldw-walkthrough/azuremlpublish.png
[12]: ./media/sqldw-walkthrough/ssmsconnect.png
[13]: ./media/sqldw-walkthrough/executescript.png
[14]: ./media/sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/sqldw-walkthrough/bulkimport.png
[17]: ./media/sqldw-walkthrough/amlreader.png
[18]: ./media/sqldw-walkthrough/amlscoring.png
[19]: ./media/sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/sqldw-walkthrough/ps_load_data.png
[21]: ./media/sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[importálja]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

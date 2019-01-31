---
title: Hozhat létre és helyezhet üzembe egy modellt az SQL Data Warehouse - csoportos adatelemzési folyamat
description: Fejleszthet és telepíthet egy gépi tanulási modellt az SQL Data Warehouse egy nyilvánosan elérhető adatkészlet.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/24/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 21eec258b14bb0524170c9307d06fee7b7abc644
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466642"
---
# <a name="the-team-data-science-process-in-action-using-sql-data-warehouse"></a>A csoportos adatelemzési folyamat működés közben: az SQL Data Warehouse használatával
Ez az oktatóanyag azt mutatja be létrehozásának és üzembe helyezésének egy gépi tanulási modellt az SQL Data Warehouse (az SQL DW) egy nyilvánosan elérhető adatkészlethez – a [NYC Taxi lelassítja](http://www.andresmh.com/nyctaxitrips/) adatkészlet. A bináris osztályozási modell összeállított előrejelzi e tipp fizetős útnak és többosztályos osztályozási és regressziós modellek is taglalja, hogy a terjesztési előre fizetett összegek tipp.

Az eljárást követi a [csoportos adatelemzési folyamat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) munkafolyamat. Bemutatjuk, hogyan állíthatja be az adatelemzési környezetet az adatok betöltése az SQL dw-vel, és hogyan használhatja az SQL DW, vagy egy IPython Notebook Fedezze fel az adatokat és a visszafejtés modell funkciói. Ezután bemutatjuk, hogyan készíthet és helyezhet üzembe az Azure Machine Learning-modell.

## <a name="dataset"></a>A NYC Taxi lelassítja adatkészlet
A NYC Taxi útadatok körülbelül 20GB tömörített CSV-fájlok (~ 48GB tömörítetlen) áll, minden egyes út 173 milliónál egyes utak és a díjakat fizetni. Minden egyes út rekord tartalmazza a begyűjtés és dropoff helyek és időpontok, anonimizált feltörés (illesztőprogramok) jogosítvány száma, és a medallion (taxi az egyedi azonosító) számát. Az adatok minden lelassítja ismerteti az év 2013-hoz, és minden hónapban megtalálható a következő két adatkészletet:

1. A **trip_data.csv** fájl trip részleteit, például utasok, begyűjtést és dropoff pontok, út időtartama és út hossza számát tartalmazza. Az alábbiakban néhány példa rekordokat:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. A **trip_fare.csv** fájl tartalmazza az egyes út, például a fizetési típus, diszkont összeg, pótdíj és adók, tippek és útdíjak, fizetett diszkont és a teljes összeg fizetős részleteit. Az alábbiakban néhány példa rekordokat:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A **egyedi kulcs** trip léptetni\_adatokat és trip\_diszkont épül fel a következő három mezőt:

* medallion,
* hack\_licenc és
* begyűjtés\_datetime.

## <a name="mltasks"></a>Előrejelzési feladatok három típusú cím
Azt a három előrejelzési problémák alapján állítson össze a *tipp\_összeg* három típusú feladatok modellezési mutatja be:

1. **Bináris osztályozás**: Előrejelzési e tipp fizették útnak, azaz egy *tipp\_összeg* nagyobb több, mint 0 USD pozitív példa, miközben egy *tipp\_összeg* negatív példa az 0 USD.
2. **Többosztályos osztályozási**: Tipp számos előre fizetett az utazás. Hogy osztani a *tipp\_összeg* öt bins vagy osztályok:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Regresszió feladat**: Előre fizetett belépőt a tip mennyisége.

## <a name="setup"></a>Állítsa be az Azure adatelemzési környezetet a speciális elemzésekhez
Az Azure Data Science környezet beállításához, kövesse az alábbi lépéseket.

**A saját Azure blob storage-fiók létrehozása**

* Amikor üzembe helyezi az Azure blob-tárolóban, válassza ki az Azure blob storage in vagy a lehető legközelebb a földrajzi helymeghatározás **USA déli középső Régiójában**, azaz, hogy a NYC Taxi adatok tárolására. Az adatok tárolóba a nyilvános blobtárolóban AzCopy használatával a saját tárfiókja másolódnak. Minél közelebb az Azure blob storage-USA déli középső Régiójában, annál gyorsabban (4. lépés) Ez a feladat befejeződik.
* A saját Azure storage-fiók létrehozásához kövesse a lépéseket követve [tudnivalók az Azure storage-fiókok](../../storage/common/storage-create-storage-account.md). Mindenképpen adja meg a jegyzetek a következő tárfiók hitelesítő adatainak értékeit, hogy ez az útmutató későbbi részében lesz szükség.

  * **Tárfiók neve**
  * **Tárfiók kulcsa**
  * **Tárolónév** (amely azt szeretné, hogy az adatok tárolhatók az Azure blob storage)

**Üzembe helyezhető az Azure SQL DW-példányt.**
Kövesse a dokumentációban a [hozzon létre egy SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) kiépítéséhez egy SQL Data Warehouse-példányhoz. Győződjön meg arról, hogy az a következő SQL Data Warehouse hitelesítő adatok a későbbi lépésekben fogja használni, hogy jelölések.

* **Kiszolgálónév**: <server Name>. database.windows.net
* **SQLDW (adatbázis) neve**
* **Felhasználónév**
* **Jelszó**

**Telepítse a Visual Studio és az SQL Server Data Tools.** Útmutatásért lásd: [Visual Studio 2015 telepítése és/vagy az SSDT (SQL Server Data Tools) az SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Az Azure SQL dw-ben a Visual Studio használatával csatlakozhat.** Útmutatásért lásd: 1. és 2 lépéseket [csatlakozhat az Azure SQL Data Warehouse a Visual Studióval](../../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

> [!NOTE]
> Futtassa a következő SQL lekérdezést az adatbázison, az SQL Data Warehouse (helyett a connect témakör 3. lépésében megadott lekérdezést) létrehozott a **hozzon létre egy főkulcsot**.
>
>

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Hozzon létre egy Azure Machine Learning-munkaterületet az Azure-előfizetéshez.** Útmutatásért lásd: [létrehozása az Azure Machine Learning-munkaterület](../studio/create-workspace.md).

## <a name="getdata"></a>Az adatok betöltése az SQL Data Warehouse-bA
Nyissa meg a Windows PowerShell-parancs konzolt. Töltse le a példában az SQL-parancsokat futtassa a következő PowerShell parancsfájl osztjuk meg Önnel a Githubon egy helyi könyvtárba, megadhatja a paraméterrel fájlok *- DestDir*. Módosíthatja a paraméter értékének *- DestDir* bármely helyi könyvtárba. Ha *- DestDir* nem létezik, azt fogja létrehozni a PowerShell-parancsfájlt.

> [!NOTE]
> Szüksége lehet **Futtatás rendszergazdaként** a következő PowerShell-parancsprogram végrehajtásakor, ha a *DestDir* könyvtár létrehozása vagy írható-e rendszergazdai jogosultsággal kell.
>
>

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

A sikeres végrehajtása után az aktuális munkakönyvtárba vált *- DestDir*. Kell látnia képernyő jelenik meg az alábbi módon:

![Aktuális munkakönyvtárban történt változtatásokat][19]

Az a *- DestDir*, rendszergazdai módban hajtsa végre a következő PowerShell-parancsfájlt:

    ./SQLDW_Data_Import.ps1

Amikor a PowerShell-parancsprogram az első alkalommal fut, a rendszer kéri, az Azure SQL dw-vel és az Azure blob storage-fiókjában bemeneti. Ez a PowerShell-szkript befejezése után az első alkalommal a hitelesítő adatokat futtató, bemeneti fog alkalmazáskonfigurációjának SQLDW.conf konfigurációs fájlt a jelenlegi munkakönyvtárban. A jövőbeli Futtatás a PowerShell parancsfájl van lehetőség, olvassa el az összes szükséges paraméterek a konfigurációs fájlból. Ha néhány paraméter módosítani szeretné, kiválaszthatja bemeneti paraméterei a kérés esetén a képernyő törlésével ezt a konfigurációs fájlt, és a paraméterek értékét bevitelével, mivel a rendszer kéri, vagy módosíthatja a paraméter értékét a SQLDW.conffájlban *- DestDir* könyvtár.

> [!NOTE]
> Annak érdekében, hogy a séma neve ütközik, amelyek már létezik az Azure SQL dw-vel a paramétereket közvetlenül SQLDW.conf fájlból történő olvasásakor elkerülése érdekében 3 számjegyből véletlenszerűen hozzáadódik a séma neve a SQLDW.conf fájlból az egyes futtatások alapértelmezett séma neveként. A PowerShell-parancsfájl kérheti a séma neve: a név felhasználói belátása szerint lehet megadni.
>
>

Ez **PowerShell-parancsprogram** fájlt a következő feladatokat hajtja végre:

* **Letölti és telepíti az AzCopy**, ha az AzCopy még nem telepítette

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
* **Másolja az adatokat a személyes blob storage-fiók** a nyilvános blobból az Azcopyval

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* **A Polybase (LoadDataToSQLDW.sql végrehajtásával) az Azure SQL dw-be adatokat tölt** a privát blob storage-fiókjában az alábbi parancsokkal.

  * Hozzon létre egy sémát

          EXEC (''CREATE SCHEMA {schemaname};'');
  * Adatbázishoz kötődő hitelesítő adatok létrehozása

          CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
          WITH IDENTITY = ''asbkey'' ,
          Secret = ''{StorageAccountKey}''
  * Az Azure storage blob a külső adatforrás létrehozása

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

    - A külső táblákból az Azure blob storage-adatok betöltése az SQL Data Warehouse

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

    - Hozzon létre egy minta-adattábla (NYCTaxi_Sample), és adatok beszúrása, hogy az SQL-lekérdezéseket a trip és diszkont táblák kiválasztása. (Ez az útmutató néhány lépése kell használnia a mintául szolgáló tábla.)

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
> Saját blob storage-fiókjában, földrajzi helyétől függően folyamata adatokat másol egy nyilvános blob saját tárfiókja nagyjából 15 percig is eltarthat, vagy akár hosszabb ideig, és a folyamat a tárfiókban lévő adatok betöltése az Azure-bA Az SQL DW 20 percet is igénybe vehet, vagy hosszabb.
>
>

Dönthet arról, hogy milyen tegye Ha ismétlődő a forrás és cél fájlokat kell.

> [!NOTE]
> Ha a .csv-fájlokat másolja a nyilvános blob storage a saját blob storage-fiók már szerepel a privát blob storage-fiók, az AzCopy ekkor megkérdezi, hogy szeretné-e felülírja őket. Ha nem szeretné felülírni őket, bemeneti **n** amikor a rendszer kéri. Ha szeretné felülírni **összes** őket, bemeneti **egy** amikor a rendszer kéri. Emellett megadhatja **y** külön-külön felülírja a .csv fájlt.
>
>

![Az AzCopy kimenete][21]

Használhatja a saját adatait. Ha az adatok a helyszíni gépen, a valós életben alkalmazásban, az AzCopy továbbra is használhatja a helyszíni adatok feltöltése a saját Azure blob Storage. Csak módosítani szeretné a **forrás** helyét, `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, az AzCopy-parancsot a PowerShell-parancsfájl a helyi könyvtárba, amely tartalmazza az adatokat a.

> [!TIP]
> Ha az adatok a valós életben alkalmazás már a saját Azure blob storage-ban, a PowerShell-parancsfájlt az AzCopy lépés kihagyhatja, és közvetlenül töltse fel az adatokat az Azure SQL dw-be. Ezt a beállítást, a szkriptet a formátum az adatok manipulálását további módosításokat.
>
>

Ez a Powershell-szkript is rendkívüli az Azure SQL DW-információkat az adatok feltárása példa fájlok SQLDW_Explorations.sql SQLDW_Explorations.ipynb és SQLDW_Explorations_Scripts.py, hogy ezek a fájlok, melynek azonnal után készen áll a PowerShell parancsfájl futása befejeződött.

A sikeres végrehajtást követően látni fogja a képernyő alábbi módon:

![Egy sikeres parancsfájl végrehajtása kimenete][20]

## <a name="dbexplore"></a>Az adatok feltárása és az Azure SQL Data Warehouse funkciófejlesztési feladatok
Ez a szakasz által futtatott SQL-lekérdezéseket közvetlenül az Azure SQL DW végzünk adatok feltárása és a szolgáltatás generálása **Visual Studio Data Tools**. Ebben a szakaszban használt összes SQL-lekérdezéseket a minta parancsfájl nevű található *SQLDW_Explorations.sql*. Ez a fájl már letöltötte a helyi könyvtárba a PowerShell parancsfájlhoz. Is lekérhet a [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). De a fájlt a Githubon nincs csatlakoztatva az Azure SQL DW információk.

Az Azure SQL dw-vel az SQL DW bejelentkezési nevet és jelszót a Visual Studio használatával csatlakozhat, és nyissa meg a **SQL Object Explorer** annak ellenőrzéséhez, hogy az adatbázis és a táblák lettek importálva. Lekérni a *SQLDW_Explorations.sql* fájlt.

> [!NOTE]
> A Parallel Data warehouse-ba (PDW) query-szerkesztő megnyitásához használja a **új lekérdezés** közben a PDW van kijelölve, a parancs a **SQL Object Explorer**. A standard SQL-Lekérdezésszerkesztő PDW által nem támogatott.
>
>

Íme az adatok feltárására és a szolgáltatás generálása feladatok végrehajtása ebben a szakaszban:

* Fedezze fel az adatokat a különböző időtartományok néhány mezőt disztribúciók.
* Vizsgálja meg a szélességi és hosszúsági mezők adatok minőségét.
* Bináris és többosztályos osztályozási címkék alapján készítése a **tipp\_összeg**.
* Szolgáltatások készítése és trip távokat számítási/összehasonlítása.
* Csatlakozzon a két táblázatot, és csomagolja ki egy véletlenszerűen vett minta modellek létrehozásához használt.

### <a name="data-import-verification"></a>Adatok importálása ellenőrzése
Ezeket a lekérdezéseket, adja meg a sorok számát egy gyors ellenőrzés és oszlopokat a táblában korábban a Polybase a párhuzamos tömeges segítségével importálja,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**A kimenetre:** 173,179,759 sorok és oszlopok 14 szerezheti be.

### <a name="exploration-trip-distribution-by-medallion"></a>Feltárás: Utazás eloszlás medallion szerint
Ez a példa lekérdezés azonosítja a medallions (-i taxik számokat), amely egy adott időtartamon belül több mint 100 lelassítja befejeződött. A lekérdezés kiaknázhatják a particionált tábla hozzáférés, mivel megfelel a partíciós sémája **begyűjtés\_datetime**. A teljes adatkészlet lekérdezése is teszi a particionált tábla használja, és/vagy a vizsgálat index.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**A kimenetre:** A lekérdezés egy tábla sorok megadása a 13,369 medallions (taxikra) és a trip tölti ki őket a 2013-ban számát adja vissza. Az utolsó oszlopban befejeződött lelassítja darabszámát tartalmazza.

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Feltárás: Utazás terjesztési medallion és hack_license
Ebben a példában a medallions (-i taxik számokat) azonosítja, és hack_license számok (illesztőprogramok), amely befejezése több mint 100 lelassítja a megadott időn belül.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**A kimenetre:** A lekérdezés 13,369 sorok megadása a 13,369 autó/illesztőprogram azonosítóját, amely 2013-ban, hogy 100 lelassítja további végrehajtotta egy táblában adja vissza. Az utolsó oszlopban befejeződött lelassítja darabszámát tartalmazza.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Minőségi értékelési adatokat: Ellenőrizze a rekordokat és/vagy a helytelen hosszúság szélesség
Ebben a példában folytat, ha a hosszúsági és/vagy szélességi mezők vagy érvénytelen értéket tartalmazza (radián fok – 90 és 90 között kell lennie), vagy rendelkezik (0, 0) koordinátáit.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**A kimenetre:** A lekérdezés érvénytelen hosszúsági és/vagy szélességi mezőkkel rendelkező 837,467 utakat adja vissza.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Feltárás: Nem Formabontó lelassítja terjesztési és Formabontó
Ebben a példában megkeresi a utazásokat, és hány volt Formabontó, amelyek nem voltak Formabontó, a megadott időtartam során (vagy a teljes adatkészlethez, ha a teljes évre vonatkozó, ahogy azt az itt beállított) száma. Ehhez a terjesztéshez tükrözi a bináris címke terjesztése bináris osztályozási modellezési később használható.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**A kimenetre:** A lekérdezés a következő tipp gyakoriságok adja vissza az év 2013-hoz: 90,447,622 Formabontó és 82,264,709 nem-Formabontó.

### <a name="exploration-tip-classrange-distribution"></a>Feltárás: Az osztály és címtartomány terjesztési tipp
Ebben a példában kiszámítja a terjesztési tipp címtartományok egy adott időtartamon (vagy a teljes adatkészlethez, ha a teljes évre vonatkozó). Ez a később fogja használni a modellezési többosztályos osztályozási címke osztályok eloszlása.

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

**A kimenetre:**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Feltárás: Számítás és trip távolság összehasonlítása
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
Néha SQL-ben a műszaki elemzésekhez hatékony lehetőség is lehet. Ebben az útmutatóban meghatározott egy SQL-függvény alapján számítja ki a közvetlen a begyűjtés és dropoff helyek közötti távolság. A következő SQL-szkripteket futtathat **Visual Studio Data Tools**.

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

**A kimenetre:** Ez a lekérdezés (2,803,538 sorok) táblázatot a begyűjtés és dropoff földrajzi szélesség és hosszúság és a megfelelő közvetlen távokat mérföldre lévő állít elő. Az alábbiakban az eredmények első 3 sorra vonatkozóan:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>A modell létrehozásához az adatok előkészítése
Az alábbi lekérdezés csatlakozik a **nyctaxi\_út** és **nyctaxi\_diszkont** táblák, létrehoz egy bináris osztályozási címke **Formabontó**, amely egy többcsoportos besorolási címke **tipp\_osztály**, és a egy minta kigyűjti a teljes csatlakozó adatkészlet. A mintavétel egy részét a begyűjtési időpont alapján lelassítja lekérésével.  Ez a lekérdezés másolja, majd a beillesztett közvetlenül a [Azure Machine Learning Studio](https://studio.azureml.net) [adatok importálása] [ import-data] az SQL database példányát a közvetlen adatbetöltést modulja Azure-t. A lekérdezés nem tartalmazza a helytelen rekordot (0, 0) koordinátáit.

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

1. A végső SQL-lekérdezés kibontása és a mintaadatok és másolás és beillesztés közvetlenül a lekérdezés mentéséhez egy [adatok importálása] [ import-data] modul az Azure Machine Learning, vagy
2. Azt tervezi, hogy egy új SQL DW-tábla létrehozásához használja a mintavételezett és visszafejtett adatok megőrzése és az új táblázat a [adatok importálása] [ import-data] az Azure Machine Learning modul. A PowerShell-parancsfájlt a korábbi lépésben ezt nem teszi meg. Ezt a táblázatot az adatok importálása modullal közvetlenül a olvashat.

## <a name="ipnb"></a>Az adatok feltárása és az IPython notebook funkciófejlesztési feladatok
Ebben a szakaszban adatáttekintés és a szolgáltatás létrehozása a Python használatával végezzük el, és a korábban létrehozott SQL-lekérdezéseket az SQL dw-ben. Egy mintául szolgáló IPython notebook nevű **SQLDW_Explorations.ipynb** és a egy Python-parancsfájl **SQLDW_Explorations_Scripts.py** vannak töltve a helyi könyvtárba. Akkor is elérhetők a [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Két fájlt a Python-szkriptek megegyeznek. A Python-parancsfájl tárolásához kapja meg abban az esetben, ha nem rendelkezik az IPython Notebook kiszolgálót. Ez a két mintát úgy vannak beállítva, a Python **Python 2.7-es**.

A szükséges Azure SQL DW adatokat. a minta a helyi számítógépre letöltött IPython Notebook és a Python-parancsfájl tárolásához rendelkezik lett csatlakoztatva a PowerShell-parancsfájl által korábban. Azok a módosítás nélkül végrehajtható.

Ha már beállított egy AzureML-munkaterületet, közvetlenül a minta IPython Notebook feltöltéséhez az AzureML IPython Notebook szolgáltatás és indítsa el azt. Az AzureML IPython Notebook szolgáltatás feltölteni lépései a következők:

1. Jelentkezzen be az AzureML-munkaterülethez, "Studio" kattintson a felül, és kattintson a lap bal oldalán "NOTEBOOKOK".

    ![Kattintson a Studio ezután NOTEBOOKOK][22]
2. "Új" kattintson a lap bal alsó sarkában, majd válassza "a Python 2". Ezután adjon meg egy nevet a jegyzetfüzetet, és kattintson a pipa jelre az új üres IPython Notebook létrehozása.

    ![Az új gombra, majd válassza ki a Python 2][23]
3. Kattintson a "Jupyter" szimbólumra az új IPython Notebook bal felső sarkában.

    ![Kattintson a Jupyter szimbólum][24]
4. A minta IPython Notebook áthúzása a a **fa** az AzureML IPython Notebook szolgáltatás, majd kattintson a lap **feltöltése**. Ezt követően a minta IPython Notebook lesz feltöltve az AzureML IPython Notebook szolgáltatásba.

    ![Kattintson a feltöltés][25]

A minta futtatásához IPython Notebook vagy a Python-szkriptfájlt, a következő Python-csomagok szükségesek. Az AzureML IPython Notebook szolgáltatás használja, ha ezek a csomagok előre telepítve lett.

    - pandas
    - numpy
    - matplotlib
    - a pyodbc
    - PyTables

A javasolt feladatütemezés létrehozását, speciális analitikai megoldások az AzureML nagy mennyiségű adat-, a következő:

* Olvassa el az adatok néhányat példaként egy memórián belüli adatok keretbe.
* Hajtsa végre az egyes Vizualizációk és explorations mintavételezett adatok használatával.
* A mintavételezett adatok funkciófejlesztési kísérletezhet.
* A nagyobb méretű adatfeltárás, adatkezelés és funkciófejlesztési a Python használatával adja ki az SQL-lekérdezéseket közvetlenül az SQL dw-vel szemben.
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

### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Sorok és oszlopok a tábla < nyctaxi_trip > jelentés száma
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

### <a name="report-number-of-rows-and-columns-in-table-nyctaxifare"></a>Sorok és oszlopok a tábla < nyctaxi_fare > jelentés száma
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

### <a name="read-in-a-small-data-sample-from-the-sql-data-warehouse-database"></a>Olvassa el a kis méretű adatok minta az SQL Data Warehouse adatbázisból
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
Most már készen áll a mintavételezett adatok feltárására. Kezdődik meg, az egyes leíró statisztikája megnézzük a **út\_távolság** (vagy bármely más mezők szeretne megadni).

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
Bemutatjuk a pontdiagram között **út\_idő\_a\_másodperc** és **út\_távolság** megtekintéséhez, hogy van-e bármilyen korrelációs

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Idő és a távolság közötti kapcsolat Teszteredményekből kimenete][6]

Hasonló módon is közötti kapcsolat ellenőrzése **arány\_kód** és **út\_távolság**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Kód és a távolság közötti kapcsolat Teszteredményekből kimenete][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>A mintavételezett adatok IPython notebook az SQL-lekérdezések használata az adatok feltárása
Ebben a szakaszban a mintavételezett adatok, amelyeket a rendszer megőrzi a fentiekben létrehozott új tábla használó adatok disztribúciók tárgyaljuk. Vegye figyelembe, hogy hasonló explorations használatával végezheti el az eredeti táblát.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Feltárás: Sorok és oszlopok a mintában szereplő táblázatban jelentéshez számát
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Feltárás: A hibaválasz Formabontó/nem terjesztési
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Feltárás: Osztály terjesztési tipp
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Feltárás: A tip terjesztési nyomtatandó osztály
    tip_class_dist['tip_freq'].plot(kind='bar')

![#26 ábrázolása][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Feltárás: Az utak napi terjesztési
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Feltárás: Medallion trip megoszlása
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

## <a name="mlmodel"></a>Az Azure Machine Learning modellek készítése
Mi most már készen áll a folytatásra modell létrehozásának és a modell üzembe helyezése [Azure Machine Learning](https://studio.azureml.net). Az adatokat, nevezetesen korábban azonosított problémák előrejelzési bármelyike használható:

1. **Bináris osztályozás**: Előrejelzési e tipp fizették útnak.
2. **Többosztályos osztályozási**: Tipp számos előre fizetett, a korábban meghatározott osztály alapján.
3. **Regresszió feladat**: Előre fizetett belépőt a tip mennyisége.

A modellezés gyakorlat megkezdéséhez jelentkezzen be a **Azure Machine Learning** munkaterületen. Ha még nem hozott machine learning-munkaterület, [hozzon létre egy Azure Machine Learning studio-munkaterület](../studio/create-workspace.md).

1. Ismerkedés az Azure Machine Learninget, tekintse meg [Mi az Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Jelentkezzen be [az Azure Machine Learning Studio](https://studio.azureml.net).
3. A Studio kezdőlap rengeteg információt, videókat, oktatóanyagokat, valamint hivatkozásokat biztosít a modulok referencia és egyéb vállalati forrásokhoz. Azure Machine Learninggel kapcsolatos további információkért tekintse meg a [Azure Machine Learning dokumentációs központban](https://azure.microsoft.com/documentation/services/machine-learning/).

Egy tipikus betanítási kísérlet a következő lépésekből áll:

1. Hozzon létre egy **+ új** kísérletezhet.
2. Az adatok lekérése az Azure Machine Learning studióba.
3. Előzetes feldolgozása, átalakíthatja, és szükség szerint kezeli.
4. Funkciók létrehozása, igény szerint.
5. Az adatok felosztása képzési érvényesítési/tesztelési adatkészletek (vagy különálló adatkészletek minden).
6. Válassza ki egy vagy több gépi tanulási algoritmusok függően a tanulás a probléma megoldásához. Például bináris osztályozási többosztályos osztályozási, regressziós.
7. A betanítási adatkészletet használó egy vagy több modelleket taníthat be.
8. Az érvényesítés adatkészletéhez a betanított modellek pontozása.
9. Értékelje ki a tanulási probléma a lényeges metrikák számítási a modellek.
10. Részletes adhatja meg a modellek, és válassza ki a legjobb modellt üzembe helyezéséhez.

Ebben a gyakorlatban azt már megvizsgálta és fejthetők vissza az adatokat az SQL Data Warehouse, és kiválasztotta a mintanagyság az, hogy az Azure Machine Learning studióban. Itt látható az eljárást követve hozhat létre, a prediktív modellek közül legalább egyet:

1. Az adatok lekérése az Azure Machine Learning studio használatával a [adatok importálása] [ import-data] modul elérhető a **adatok bemeneti és kimeneti** szakaszban. További információkért lásd: a [adatok importálása] [ import-data] modul referenciájának oldalát.

    ![Az Azure Machine Learning-importálási adatok][17]
2. Válassza ki **Azure SQL Database** , a **adatforrás** a a **tulajdonságok** panel.
3. Az adatbázis DNS-nevét adja meg a **adatbázis-kiszolgáló neve** mező. Formátum: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Adja meg a **adatbázisnév** a megfelelő mezőben.
5. Adja meg a *SQL felhasználónév* a a **kiszolgáló felhasználói fiók nevét**, és a *jelszó* a a **kiszolgáló felhasználói fiók jelszava**.
7. Az a **adatbázis-lekérdezés** szövegterület szerkesztése, illessze be a lekérdezést, amely a szükséges adatbázis-mezők (beleértve a címkéket például számított mezőket), és le az adatokat a kívánt mintanagyság minták.

Egy bináris osztályozási kísérletet, közvetlenül az SQL Data Warehouse-adatbázis történő adatkiolvasás például az alábbi ábrán a (ne felejtse el kicserélni a táblázat neve nyctaxi_trip és nyctaxi_fare a séma neve és a táblaneveket követte a forgatókönyv szerint). Hasonló kísérletek többosztályos osztályozási és regressziós probléma lehet létrehozni.

![Az Azure Machine Learning Train][10]

> [!IMPORTANT]
> A modellezési adatok kinyerése és a lekérdezés példák mintavételi biztosított az előző szakaszokban **három modellezési gyakorlatok összes címkék szerepelnek a lekérdezés**. Az egyes modellezési gyakorlatok (kötelező) fontos lépés, hogy **kizárása** a szükségtelen címkéket, a másik két problémákat, és bármely más **adatszivárgás cél**. Például bináris osztályozási használjon a címke **Formabontó** és kizárja a mezőket **tipp\_osztály**, **tipp\_összeg**, és **teljes\_összeg**. Az utóbbi cél adatszivárgás mivel, azok magukban foglalják az ötlet fizetős.
>
> Kizárása szükségtelen oszlopokat, vagy adatszivárgás célként, előfordulhat, hogy használja a [Select Columns in Dataset] [ select-columns] modul vagy a [metaadatainak szerkesztése][edit-metadata]. További információkért lásd: [Select Columns in Dataset] [ select-columns] és [metaadatainak szerkesztése] [ edit-metadata] oldalak hivatkozhat.
>
>

## <a name="mldeploy"></a>Az Azure Machine Learning modellek üzembe helyezése
Amikor készen áll a modellt, könnyedén telepítheti közvetlenül a kísérletből webszolgáltatásként. További információ az Azure ML-webszolgáltatások üzembe helyezéséhez: [egy Azure Machine Learning webszolgáltatás üzembe helyezése](../studio/publish-a-machine-learning-web-service.md).

Új webszolgáltatás üzembe helyezéséhez kell tennie:

1. Hozzon létre egy pontozó kísérletet.
2. A webszolgáltatás üzembe helyezéséhez.

A pontozó fülre egy kísérlet létrehozásához egy **befejezett** betanítási kísérlet, kattintson a **létre pontozási KÍSÉRLETEZHET** az alacsonyabb művelet sávon.

![Azure Scoring][18]

Az Azure Machine Learning megkísérli a tanítási kísérlet összetevői alapján pontozási kísérlet létrehozása. Különösen hajtja végre:

1. Mentse a betanított modell, és távolítsa el a modell képzési modult.
2. Azonosítsa a logikai **bemeneti porthoz** képviselő várt sémát.
3. Azonosítsa a logikai **kimeneti port** képviselő kimeneti sémája várt webes szolgáltatás.

A pontozó kísérlet jön létre, amikor, tekintse át, és ellenőrizze, hogy igény szerint. Egy tipikus illesztését, hogy cserélje le a bemeneti adatkészlet és/vagy lekérdezési egyet, amely nem tartalmazza a címkemezők, mivel ezek nem lesz elérhető a szolgáltatás meghívásakor. Célszerű is jó megoldás a bemeneti adatkészlet és/vagy lekérdezési méretének csökkentése néhány rekordok éppen elegendő jelzi a bemeneti sémát. A kimeneti portra, a szokás, hogy kizárja az összes beviteli mezőket, és csak a **pontozott címkék** és **pontozott valószínűség** be a kimenet a [Select Columns in Dataset] [ select-columns] modul.

Az alábbi ábrán egy minta kísérlet pontozási biztosítunk. Amikor készen áll a központi telepítése, kattintson a **WEBSZOLGÁLTATÁS közzététele** gombra az alsó művelet sávon.

![Az Azure Machine Learning közzététele][11]

## <a name="summary"></a>Összegzés
Recap, amit eddig még ebben az oktatóanyagban a forgatókönyv, hogy létrehozott egy Azure adatelemzési környezetet, használta korábban a nagy nyilvános adatkészlet, a csoportos adatelemzési folyamat, egészen a modell betanítása, majd az adatgyűjtés keresztül tart, a az Azure Machine Learning webszolgáltatás üzembe helyezése.

### <a name="license-information"></a>Licencinformációk
Ez a minta forgatókönyv és a kísérő parancsfájlok és IPython notebook(s) osztanak meg a Microsoft mellett az MIT-licenccel. Ellenőrizze a LICENSE.txt fájlt a könyvtárban mintakódot a Githubon további részletekért.

## <a name="references"></a>Referencia
• [Andrés Monroy NYC Taxi lelassítja letöltési oldalát](http://www.andresmh.com/nyctaxitrips/) • [FOILing NYC Útadatok taxiköltség által Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/) • [NYC Taxi és Limousine Bizottság kutatási és a statisztika](http://www.nyc.gov/html/tlc/html/technology/aggregated_data.shtml)

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
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

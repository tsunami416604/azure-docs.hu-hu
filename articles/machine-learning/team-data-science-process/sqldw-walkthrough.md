---
title: "Az Team tudományos folyamat működés közben: az SQL Data Warehouse |} Microsoft Docs"
description: "Bővített Analitikát folyamat és a technológia, működés közben"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 88ba8e28-0bd7-49fe-8320-5dfa83b65724
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: bradsev;weig
ms.openlocfilehash: 73517a8d58700e987ce80889dadf8791e53170a3
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="the-team-data-science-process-in-action-using-sql-data-warehouse"></a>Az Team tudományos folyamat működés közben: az SQL Data Warehouse
Az oktatóanyag azt ismerteti létrehozása és telepítése a gépi tanulási modellek az SQL Data Warehouse (SQL DW) keresztül egy nyilvánosan elérhető adatkészlet--a [NYC Taxi Utazgatással](http://www.andresmh.com/nyctaxitrips/) adatkészlet. A bináris osztályozási modell összeállított képes-e tipp fizetnek útnak, és multiclass besorolás és regressziós modell, amely a terjesztési megjósolható a fizetős tipp adatmennyiség is ismertetése.

Az eljárást követi a [Team adatok tudományos folyamat (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) munkafolyamat. Megmutatjuk, hogyan állíthatja egy tudományos környezetben, az adatok betöltése az SQL DW, és hogyan használják az SQL DW vagy egy IPython Notebook Fedezze fel az adatokat és a visszafejtés funkciói szorulnak modell. Ezután megmutatjuk, hogyan lehet elkészíteni a modell Azure Machine Learning segítségével telepítheti.

## <a name="dataset"></a>A NYC Taxi Utazgatással adatkészlet
A NYC Taxi út tartalmaz körülbelül 20GB tömörített CSV-fájlok (tömörítetlen ~ 48GB), minden út kifizette több mint 173 millió egyedi való adatváltások számát és a vitel rögzítése. Minden út rekord tartalmazza a felvétel és Gyűjtőtár helyeket és időpontokat, anonimizált rejthetők el (illesztőprogram) licencszám, és a medallion (taxi tartozó egyedi azonosító) számát. Az adatok minden való adatváltások számát ismerteti az év 2013, és minden hónap a következő két adatkészletet találhatók:

1. A **trip_data.csv** fájl tartalmazza út részleteit, például az utasok, a felvételi és dropoff pontok, út időtartama és út hossza száma. Íme néhány példa rekordok:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. A **trip_fare.csv** fájl a jegy ára kifizette minden út, például a fizetési mód, jegy ára összeg, emelt díjas és adókat, tippeket és autópályadíjak, és a teljes összeg fizetős részleteit tartalmazza. Íme néhány példa rekordok:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A **egyedi kulcs** történő csatlakozás út\_adatok és út\_jegy ára a következő három mezőből áll:

* medallion,
* ellophatja\_licenc és
* a felvételi\_datetime.

## <a name="mltasks"></a>Három típusú előrejelzés feladatok cím
Azt a három előrejelzés problémák alapján állítson össze a *tipp\_összeg* három különböző feladatok modellezési mutatja be:

1. **Bináris osztályozási**: előre jelezni-e tipp kifizetett utazás, azaz egy *tipp\_összeg* nagyobb, mint 0 egy pozitív példában látható, miközben egy *tipp\_Összeg* $ 0 egy negatív példában látható.
2. **Multiclass besorolási**: út kifizette tipp számos előre jelezni. Azt a osztani a *tipp\_összeg* öt bins vagy osztályok:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Regressziós feladat**: megjósolható a fizetős útnak tipp mennyisége.  

## <a name="setup"></a>A speciális elemzésekre az Azure data tudományos környezet beállítása
Állítsa be a Azure Adattudomány környezetet, kövesse az alábbi lépéseket.

**A saját Azure blob storage-fiók létrehozása**

* Ha a saját Azure blob-tároló, válassza ki az Azure blob storage a vagy a lehető legközelebb a földrajzi helymeghatározás **déli középső Régiójában**, vagyis a következőt: Taxi adatok tárolására. Az adatok tárolót a nyilvános blob storage tárolóból AzCopy használatával saját tárfiókba kerülnek. Minél közelebb az Azure blob storage déli középső Régiójában, minél gyorsabban (4. lépés) Ez a feladat befejeződik.
* A saját Azure storage-fiók létrehozásához kövesse a következő részben ismertetett lépések [tudnivalók az Azure storage-fiókok](../../storage/common/storage-create-storage-account.md). Ne felejtse el megjegyzések adja meg a következő tárfiók hitelesítő adatainak értékeit, akkor ez az útmutató későbbi részében szükség lesz.
  
  * **A tárfiók neve**
  * **Tárfiók kulcsa**
  * **Tároló neve** (amely az adatokat az Azure blob Storage tárolóban tárolni kívánt)

**Az Azure SQL DW-példányt kiépítéséhez.**
Kövesse a dokumentációban a [SQL Data Warehouse létrehozása](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) egy SQL Data Warehouse-példányokhoz kiépítéséhez. Győződjön meg arról, hogy, hogy a következő SQL-adatraktár hitelesítő adatok a későbbi lépésekben használt jelölések.

* **Kiszolgálónév**: <server Name>. database.windows.net
* **SQLDW (adatbázis) neve**
* **Felhasználónév**
* **Jelszó**

**Telepítse a Visual Studio és az SQL Server Data Tools összetevővel.** Útmutatásért lásd: [Visual Studio 2015 telepítése és/vagy az SSDT (SQL Server Data Tools) az SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Csatlakozás az Azure SQL DW a Visual Studio.** Útmutatásért lásd: 1 és 2 lépéseket [csatlakozás az Azure SQL Data Warehouse a Visual Studio](../../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

> [!NOTE]
> Futtassa a következő SQL lekérdezést az adatbázis az SQL Data Warehouse (és nem a lekérdezés a connect témakör 3. lépésében megadott) létrehozott a **hozzon létre egy főkulcsot**.
> 
> 

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Hozzon létre egy Azure Machine Learning munkaterülettel alatt az Azure-előfizetéshez.** Útmutatásért lásd: [hozzon létre egy Azure Machine Learning munkaterülettel](../studio/create-workspace.md).

## <a name="getdata"></a>Az adatok betöltése az SQL Data Warehouse
Nyisson meg egy Windows PowerShell-parancs konzolt. Futtassa a következő PowerShell-parancsok futtatásával töltse le a példában SQL parancsfájlok, amelyek osztjuk meg Önnel a Githubon az paraméterrel megadott helyi könyvtárba *- DestDir*. Módosíthatja a paraméter értékének *- DestDir* bármely helyi könyvtárba. Ha *- DestDir* nem létezik, a rendszer automatikusan létrehozza a PowerShell parancsfájlhoz.

> [!NOTE]
> Szükség lehet **Futtatás rendszergazdaként** a következő PowerShell-parancsfájl végrehajtásakor, ha a *DestDir* directory létrehozásához vagy íráshoz rendszergazdai jogosultság szükséges.
> 
> 

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Sikeres végrehajtása után az aktuális munkakönyvtárban vált *- DestDir*. Képernyőn láthatja el alatt, például:

![][19]

Az a *- DestDir*, a következő PowerShell-parancsfájl végrehajtása felügyeleti üzemmódban:

    ./SQLDW_Data_Import.ps1

Ha a PowerShell-parancsfájl futtatása először, a rendszer kéri, hogy adjon meg az Azure SQL DW és az Azure blob storage-fiók. A PowerShell parancsfájl befejeződésekor először, a hitelesítő adatok futtató meg bemeneti fog készült SQLDW.conf konfigurációs fájlt a jelenlegi munkakönyvtárát. A jövőbeli Futtatás a PowerShell parancsfájl az összes szükséges paraméterek a konfigurációs fájlból olvasható lehetősége van. Egyes paraméterek módosítani szeretné, ha paraméterei a kérés esetén a képernyő törölni a konfigurációs fájlt, és a bevitel a paraméterek értékét, a rendszer kéri a felhasználótól, vagy választhatja a paraméterértékeket a aSQLDW.conffájlszerkesztésévelmódosíthatja*- DestDir* könyvtár.

> [!NOTE]
> Ahhoz, hogy a séma neve ütközik az alábbiakhoz már létezik az Azure SQL DW, ha Fájlolvasási paraméterek közvetlenül a SQLDW.conf elkerülése érdekében 3 számjegyű véletlenszerűen kerül a séma nevének a SQLDW.conf fájlból minden egyes futtatásához alapértelmezett séma neveként. A PowerShell parancsfájl kérheti a séma nevének: felhasználói belátása neve adható meg.
> 
> 

Ez **PowerShell-parancsfájl** fájl a következő feladatokat hajtja végre:

* **Letölti és telepíti az AzCopy**, ha az AzCopy nincs telepítve
  
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
* **Másolja az adatokat a személyes blob storage-fiók** az AzCopy nyilvános blob
  
        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* **A Polybase (LoadDataToSQLDW.sql végrehajtásával) segítségével az Azure SQL DW adatokat tölt** az az alábbi parancsokkal a titkos blob storage-fiók.
  
  * Hozzon létre egy séma
    
          EXEC (''CREATE SCHEMA {schemaname};'');
  * Egy adatbázishoz kötődő hitelesítő adatok létrehozása
    
          CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
          WITH IDENTITY = ''asbkey'' ,
          Secret = ''{StorageAccountKey}''
  * Az Azure storage-blob egy külső adatforrás létrehozása
    
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
  * Hozzon létre egy csv-fájl egy külső fájlformátum. Adatok tömörítetlen és mezőket a függőleges vonal választják el egymástól.
    
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
  * Külső jegy ára és NYC taxi adatkészlet út táblák létrehozása az Azure blob Storage tárolóban.
    
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

    - Adatok betöltése az Azure blob storage külső táblát az SQL Data Warehouse

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

    - Hozzon létre egy minta-adattábla (NYCTaxi_Sample), és adatok beszúrása azt az SQL-lekérdezések a út és a jegy ára táblák kiválasztása. (Ez az útmutató néhány lépése kell használnia a mintatáblát.)

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

A storage-fiókok földrajzi helye a betöltési idők hatással van.

> [!NOTE]
> Attól függően, hogy a titkos blob storage-fiók földrajzi elhelyezkedését, a folyamat egy nyilvános blob titkos tárfiókja adatok másolása körülbelül 15 percig is eltarthat, vagy akár hosszabb, és a korábbi tárfiókban lévő adatok betöltése az Azure-bA folyamata SQL DW 20 percet vehet igénybe, vagy hosszabb.  
> 
> 

Döntse el, hogy milyen Ha ismétlődő a forrás és cél fájlokat fog.

> [!NOTE]
> Ha a .csv-fájlokat másolni a nyilvános blob storage a saját blob storage-fiók már szerepel a titkos blob storage-fiók, AzCopy megkérdezi, hogy meg szeretné felülír. Ha nem szeretné, hogy felülírja, bemeneti  **n**  megjelenésekor. Ha szeretné felülírni **összes** , bemeneti **egy** megjelenésekor. Is szövegszerkesztőben **y** .csv fájlok egyenként felülírásához.
> 
> 

![#21. megrajzolásához][21]

Használhatja a saját adatait. Ha az adatok a valós életben alkalmazásban a helyszíni gépen, AzCopy továbbra is használhatja a helyszíni adatok feltöltése a saját Azure blob Storage. Csak módosítani szeretné a **forrás** helyét, `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, az AzCopy parancs a helyi könyvtárba, amely tartalmazza az adatokat a PowerShell parancsfájl.

> [!TIP]
> Ha az adatok a valós életben alkalmazás már a saját Azure blob Storage tárolóban, az AzCopy hagyja ki a PowerShell parancsfájl, és közvetlenül az adatokat az Azure SQL DW feltölteni. Ezt a beállítást a parancsfájl személyessé tétele érdekében, az adatok formátumának további műveleteket.
> 
> 

A Powershell-parancsfájlt is csatlakozik az Azure SQL DW-információkat az adatok feltárása például fájlok SQLDW_Explorations.sql SQLDW_Explorations.ipynb és SQLDW_Explorations_Scripts.py, hogy ezek a fájlok kell próbált azonnal után készen áll a PowerShell parancsfájl lefutott.

Egy sikeres végrehajtása után képernyőn megjelenik alá, például:

![][20]

## <a name="dbexplore"></a>Az adatok feltárása, a szolgáltatás fejlesztés az Azure SQL Data Warehouse
Ebben a szakaszban szemben Azure SQL DW közvetlenül az SQL-lekérdezések futtatásával végezzük adatok feltárása és a szolgáltatás generálása **Visual Studio Data Tools**. Ebben a szakaszban használt összes SQL-lekérdezések itt található: a minta parancsfájlt nevű *SQLDW_Explorations.sql*. Ez a fájl már letöltött a helyi könyvtárba a PowerShell parancsfájlhoz. Azt is megtalálja a [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). De a fájl a Githubban nincsenek-e a hálózathoz csatlakoztatva működik a Azure SQL DW-adatok.

Az Azure SQL DW SQL DW-bejelentkezési nevet és jelszót a Visual Studio használatával csatlakozhat, és nyissa meg a **SQL Object Explorer** annak ellenőrzéséhez, hogy az adatbázis és a táblák importálta. Beolvasni a *SQLDW_Explorations.sql* fájlt.

> [!NOTE]
> A Parallel Data Warehouse (PDW) lekérdezés-szerkesztő megnyitásához használja a **új lekérdezés** a PDW kiválasztása a parancsot a **SQL Object Explorer**. A szokásos SQL lekérdezés-szerkesztő PDW által nem támogatott.
> 
> 

Az alábbiakban a típusú adatok feltárása és a szolgáltatás generálása feladatok végre ebben a szakaszban:

* Megismerkedhet a különböző idő windows néhány mezőinek disztribúciók adatok.
* Vizsgálja meg a szélességi és hosszúsági mezők adatok minőségét.
* Multiclass és bináris besorolási címkék alapján készítése a **tipp\_összeg**.
* Szolgáltatások létrehozása és számítási/összehasonlítása út távolság.
* Csatlakozás a két tábla, és bontsa ki a modellek létrehozásához használt véletlenszerű minta.

### <a name="data-import-verification"></a>Az importálás ellenőrzése
Ezeket a lekérdezéseket, adja meg a sorok száma a gyors ellenőrzése és oszlopok a táblázatokban fel korábban segítségével a Polybase által párhuzamos tömeges importálásához

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Kimenet:** 173,179,759 sorok és oszlopok 14 kapja meg.

### <a name="exploration-trip-distribution-by-medallion"></a>Feltárása: Út elosztása medallion szerint
Ez a példa lekérdezés 100-nál több való adatváltások számát egy adott időszakon belül elvégzett medallions (taxi számok) azonosítja. A lekérdezés előnyös a particionált tábla hozzáférés óta, akkor annak partíciós sémája által **a felvételi\_datetime**. A teljes adatkészlet lekérdezése is teszi a particionált tábla használja, és/vagy a vizsgálat index.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Kimenet:** a lekérdezés visszaadja-e egy tábla sorait a 13,369 medallions (taxikra) és egyéb 2013 befejezte út száma. Az utolsó oszlopban befejeződött utak számát tartalmazza.

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Feltárása: Út terjesztési medallion és hack_license
Ebben a példában a medallions (taxi számok) azonosítja, és hack_license számok (-illesztőprogramok) az elvégzett több mint 100 való adatváltások számát egy adott időszakon belül.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Kimenet:** a lekérdezés megadásával több befejeződött, hogy 100 utazás közben 2013 13,369 autó/illesztőprogram azonosítók 13,369 sorokból kell visszaadnia egy tábla. Az utolsó oszlopban befejeződött utak számát tartalmazza.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Adatok vizsgálatának: helytelen hosszúsági és/vagy szélességi rekordjainak ellenőrzése
Ez a példa pedig megvizsgálja, ha a hosszúsági és/vagy szélességi mezőinek vagy érvénytelen értéket tartalmazza (radián fok -90 és 90 között kell lennie), vagy (0, 0) koordinátarendszerében.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Kimenet:** a lekérdezés érvénytelen hosszúsági és/vagy szélességi mezői lehetnek 837,467 való adatváltások számát adja vissza.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Feltárása: Nem Formabontó utazgatással terjesztési és Formabontó
Ez a példa megkeresi való adatváltások számát, amely volt Formabontó és a szám, amely nem volt Formabontó, egy adott időszakra vonatkozó (vagy a teljes adatkészletet, ha a teljes évre vonatkozó szerint van beállítva itt) száma. Ehhez a terjesztéshez a bináris címke terjesztési használandó később bináris osztályozási modellezési tükrözi.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Kimenet:** a lekérdezés visszaadja-e a következő tipp gyakoriságokat 2013: 90,447,622 Formabontó évben és 82,264,709 nem Formabontó.

### <a name="exploration-tip-classrange-distribution"></a>Feltárása: Tipp osztály/tartomány terjesztési
Ebben a példában kiszámítja a terjesztési tipp tartományait egy adott időszakban (vagy a teljes adatkészletet, ha a teljes évre vonatkozó). Ez az a terjesztési később használandó multiclass besorolás modellezési címke osztályok.

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

### <a name="exploration-compute-and-compare-trip-distance"></a>Feltárása: Számítási, és hasonlítsa össze a út távolság
Ebben a példában a felvétel és Gyűjtőtár hosszúság alakítja át, és SQL a földrajzi szélesség mutat, út távolság SQL geográfiai pontok különbség használatával kiszámítja és visszaadja az eredmények összehasonlítása a véletlenszerű minta. A példa az eredményeket a minőségi assessment lekérdezést kezelt korábban csak a érvényes koordináták korlátozza.

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

### <a name="feature-engineering-using-sql-functions"></a>A szolgáltatás műszaki osztály SQL függvények használata
SQL-függvény néha lehet a hatékony beállítás a szolgáltatás mérnöki csapathoz. Ebben a bemutatóban meghatározott egy SQL-függvény a felvétel és dropoff helyek közötti távolság szerint közvetlen kiszámításához. A következő SQL-parancsfájlok futtathat **Visual Studio Data Tools**.

Ez az SQL-parancsfájlt, amely meghatározza a távolságskála függvény.

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

Íme egy példa a szolgáltatások létrehozni az SQL-lekérdezés függvény meghívásához:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Kimenet:** Ez a lekérdezés a felvétel és dropoff földrajzi szélesség (2,803,538 sorok) tábla állít elő, és hosszúságot és a megfelelő közvetlen miles megadott. Az alábbiakban az eredményeket az első 3 sort:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Adatok előkészítése a modell létrehozásának
Az alábbi lekérdezés illesztések a **nyctaxi\_út** és **nyctaxi\_jegy ára** táblák, állít elő, a bináris besorolási címkék **Formabontó**, egy több osztály besorolási címke **tipp\_osztály**, és egy minta kibontja a teljes illesztett adatkészletből. A mintavétel lekérésével egy részét a felvételi ideje alapján való adatváltások számát.  Ez a lekérdezés másolja, majd a beillesztett közvetlenül a [Azure Machine Learning Studio](https://studio.azureml.net) [és adatokat importálhat] [ import-data] modul a közvetlen adatfeldolgozást az SQL adatbázis példányából Azure. A lekérdezés nem tartalmazza a rekordok helytelen (0, 0) koordinátarendszerében.

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

Ha készen áll az Azure Machine Learning folytatja, akkor előfordulhat, hogy vagy:  

1. A végső SQL lekérdezés kibontása és az az adatokat és a másolás-beillesztés közvetlenül a lekérdezés mentéséhez egy [és adatokat importálhat] [ import-data] modul az Azure Machine Learning, vagy
2. A modell egy új SQL DW-tábla létrehozásához használni kívánt mintavételi és visszafejtett adatok megmaradnak, és az új táblázat a [és adatokat importálhat] [ import-data] az Azure Machine Learning modulban. A korábbi lépésben a PowerShell parancsfájl végzett ezt meg. Elolvashatja, közvetlenül az adatok importálása a modulban a táblából.

## <a name="ipnb"></a>Az adatok feltárása, a szolgáltatás fejlesztés IPython jegyzetfüzet
Ebben a szakaszban végezzük el az adatok feltárása és a szolgáltatás generálása mindkét pythonos környezetekben, és az SQL-Adatraktár SQL lekérdezések korábban létrehozott. Egy minta IPython notebook nevű **SQLDW_Explorations.ipynb** és a Python-parancsfájl **SQLDW_Explorations_Scripts.py** vannak töltve a helyi könyvtárba. Akkor is elérhetők a [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Ezeket a fájlokat két Python parancsfájlok azonosak. A Python-parancsfájl megadott meg abban az esetben, ha nem rendelkezik egy IPython Notebook kiszolgálót. Ez a két minta úgy vannak beállítva, a Python **Python 2.7**.

A szükséges Azure SQL DW információk minta IPython jegyzetfüzet és a Python-parancsfájl letöltése a helyi számítógépre van lett csatlakoztatva a PowerShell-parancsfájl által korábban. Azok a módosítás nélkül végrehajtható.

Ha már beállított egy AzureML-munkaterületet, közvetlenül a minta IPython Notebook feltölteni az AzureML IPython Notebook szolgáltatás és elindultak, azt. AzureML IPython Notebook szolgáltatás feltöltésére lépései a következők:

1. Jelentkezzen be az AzureML-munkaterületen, a lap tetején kattintson az "Studio" és "NOTEBOOKOK" lap bal oldalán kattintson.
   
    ![#22 ábrázolása][22]
2. A weblap bal alsó sarokban kattintson az "Új", és jelölje ki "Python 2". Ezt követően adjon meg egy nevet a notebook, és kattintson a pipa jelre az új üres IPython Notebook létrehozása.
   
    ![#23 ábrázolása][23]
3. Kattintson a "Jupyter" szimbólumra az új IPython Notebook bal felső sarkában.
   
    ![#24 ábrázolása][24]
4. A minta IPython Notebook áthúzása számára a **fa** az AzureML IPython Notebook szolgáltatást, majd kattintson a lap **feltöltése**. Ezt követően a minta IPython Notebook lesz feltöltve az AzureML IPython Notebook szolgáltatáshoz.
   
    ![#25 ábrázolása][25]

A minta futtatásához IPython Notebook vagy a Python parancsfájlt, a következő csomagok szükségesek Python. Az AzureML IPython Notebook szolgáltatást használja, ha ezeket a csomagokat előre telepített törölték.

    - pandas
    - numpy
    - matplotlib
    - pyodbc
    - PyTables

Ha speciális elemzési megoldások kialakításának AzureML nagy adatokkal ajánlott sorrendje a következő:

* Olvassa el az adatok kis mintában egy a memóriában levő keretbe.
* Néhány képi megjelenítések és explorations a mintaadatokat használatával hajtható végre.
* Szolgáltatás műszaki osztály használata a mintaadatokat kísérletezhet.
* Nagyobb az adatok feltárása, adatkezelési és a szolgáltatás mérnöki csapathoz, a Python segítségével közvetlenül az SQL-Adatraktár SQL lekérdezések ki.
* Döntse el, az Azure Machine Learning modell létrehozásának alkalmasnak mintájának méretét.

A következőket: néhány adatok feltárása, adatábrázolási és példák mérnöki szolgáltatás. További adatok explorations megtalálhatók a minta IPython jegyzetfüzet és Python fájlra.

### <a name="initialize-database-credentials"></a>Adatbázis-hitelesítő adatok inicializálása
Az adatbázis-kapcsolati beállításokat a következő változók inicializálása:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Adatbázis-kapcsolat létrehozása
Ez a kapcsolati karakterláncot, amely hoz létre a kapcsolat az adatbázissal.

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

* A sorok teljes számát = 173179759  
* Az oszlopok teljes száma = 14

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

* A sorok teljes számát = 173179759  
* Az oszlopok teljes száma = 11

### <a name="read-in-a-small-data-sample-from-the-sql-data-warehouse-database"></a>Olvassa el a kisméretű minta az SQL Data Warehouse adatbázisból
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

Idő olvasási a mintatáblázat 14.096495 másodperc.  
A beolvasott sorok és oszlopok száma = (1000, 21).

### <a name="descriptive-statistics"></a>Leíró statisztika
Most már készen áll a mintaadatokat felfedezése. Először néhány leíró statisztikája megnézi a **út\_távolság** (vagy bármely más mezők úgy dönt, hogy adja meg).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>A képi megjelenítés: Rajzot – példa
Ezután úgy tekintünk, a Dobozdiagram a megjelenítéséhez a quantiles út távolság.

    df1.boxplot(column='trip_distance',return_type='dict')

![Tőzsdei #1][1]

### <a name="visualization-distribution-plot-example"></a>A képi megjelenítés: Terjesztési rajzot – példa
Amely a telepítési és a mintában szereplő út távolság a hisztogram megjelenítése előkészítésére.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Tőzsdei #2][2]

### <a name="visualization-bar-and-line-plots"></a>A képi megjelenítés: Sáv megnyitásához, majd sor ábra
Ebben a példában azt az öt bins út távolság bin és a binning eredményeinek képi megjelenítése.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

A Microsoft megrajzolásához a fenti bin terjesztési egy sávon, vagy a rajzolási. sor:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![#3 ábrázolása][3]

és

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![#4 ábrázolása][4]

### <a name="visualization-scatterplot-examples"></a>A képi megjelenítés: Scatterplot példák
Pontdiagram rajzot közötti megmutatjuk **út\_idő\_a\_másodperc** és **út\_távolság** van-e bármilyen korrelációs

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![#6 ábrázolása][6]

Hasonlóképpen azt is ellenőrizze közötti kapcsolat **arány\_kód** és **út\_távolság**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![#8 ábrázolása][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Az adatok feltárása a mintaadatokat az SQL-lekérdezések IPython notebook használatával
Ebben a részben azt megismerkedhet a mintában szereplő adatait, amely a fenti létrehozott új tábla megőrződjenek használatával adatokat terjesztéseket. Vegye figyelembe, hogy hasonló explorations használatával végezheti el az eredeti táblázatban.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Feltárása: A jelentés sorok és oszlopok száma a mintában szereplő táblázatban
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Feltárása: A Formabontó/nem terjesztési azokat kioldják
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Feltárása: Tipp osztály terjesztési
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Feltárása: A tipp terjesztési megrajzolásához osztály
    tip_class_dist['tip_freq'].plot(kind='bar')

![#26 ábrázolása][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Feltárása: Való adatváltások számát napi eloszlásáról
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Feltárása: Utazás per medallion terjesztési
    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Feltárása: Út terjesztési medallion és rejthetők el licenc által
    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Feltárása: Út idő terjesztése
    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Feltárása: Út távolság terjesztési
    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Feltárása: Fizetési típus terjesztési
    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Ellenőrizze a featurized tábla végleges formájában
    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Az Azure Machine Learning modellek létrehozása
Azt készen áll a modell létrehozásának és a modell telepítési [Azure Machine Learning](https://studio.azureml.net). Az adatok nevezetesen korábban azonosított előrejelzés problémák valamelyikében használatra kész:

1. **Bináris osztályozási**: előre jelezni-e tipp kifizetett egy út.
2. **Multiclass besorolási**: megjósolható a fizetős, a korábban meghatározott osztályba tipp tartományán.
3. **Regressziós feladat**: megjósolható a fizetős útnak tipp mennyisége.  

A modellezési gyakorlására megkezdéséhez jelentkezzen be a **Azure Machine Learning** munkaterületen. Ha még nem hozott létre machine learning-munkaterület, lásd: [hozzon létre egy Azure ML munkaterületet](../studio/create-workspace.md).

1. Ismerkedés az Azure Machine Learning, lásd: [Mi az Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Jelentkezzen be [Azure Machine Learning Studio](https://studio.azureml.net).
3. A Studio kezdőlap modulok hivatkozást, és más erőforrások számos olyan információt, videók, oktatóanyagok, valamint hivatkozásokat biztosít. Azure Machine Learning kapcsolatos további információkért tekintse meg a [Azure Machine Learning dokumentációs központban](https://azure.microsoft.com/documentation/services/machine-learning/).

Egy tipikus tanítási kísérletet a következő lépésekből áll:

1. Hozzon létre egy **+ új** kipróbálásához.
2. Az adatok beolvasása az Azure ml.
3. Előre feldolgozzák, átalakítására, és szükség esetén az adatok kezelését.
4. Szolgáltatások létrehozása, igény szerint.
5. Az adatok felosztása adatkészletek képzési/érvényesítési/tesztelése (külön adatkészleteket, vagy az egyes).
6. Válasszon egy vagy több gépi tanulási algoritmusok attól függően, hogy a tanulási probléma megoldására. Például bináris osztályozási multiclass besorolás, regressziós.
7. A képzési adatkészlet egy vagy több modell betanításához.
8. Az érvényesítési adatkészletet a betanított modellek segítségével pontozása.
9. Értékelje ki a megfelelő metrikákat a tanulási probléma kiszámításához modellek.
10. Konfigurálva finomhangolhatják a modellek, és válassza ki a legjobb központilag telepítendő modell.

Ebben a gyakorlatban azt már megismerkedett és fejthetők vissza az adatokat az SQL Data Warehouse, és a mintájának méretét az Azure ml betöltési lezárását. A folyamat során legalább egy, az előrejelzési modellek létrehozásához a rendszer:

1. Az Azure gépi tanulás használatával az adatok beolvasása a [és adatokat importálhat] [ import-data] modul érhető el a **bemeneti és kimeneti** szakasz. További információkért lásd: a [és adatokat importálhat] [ import-data] modul referencialapja.
   
    ![Azure ML importálási adatok][17]
2. Válassza ki **Azure SQL Database** , a **adatforrás** a a **tulajdonságok** panel.
3. Az adatbázis DNS-nevét adja meg a **adatbázis-kiszolgáló neve** mező. Formátum:`tcp:<your_virtual_machine_DNS_name>,1433`
4. Adja meg a **adatbázisnév** a megfelelő mezőben.
5. Adja meg a *SQL felhasználónév* a a **kiszolgáló felhasználói fiók nevét**, és a *jelszó* a a **kiszolgáló felhasználói fiók jelszavát**.
6. Ellenőrizze a **fogadja el a kiszolgálói tanúsítvány** lehetőséget.
7. Az a **adatbázis-lekérdezés** szöveg terület szerkesztése, illessze be a lekérdezést, amely a szükséges adatbázis-mezők (beleértve a számított mezőket a címkéket például) és régebbi minták a kívánt mintájának méretét az adatokat.

Az alábbi ábra a lehet például egy bináris osztályozási kísérletet, adatok beolvasása közvetlenül az SQL Data Warehouse-adatbázis (ne felejtse el lecserélni a tábla neve nyctaxi_trip és nyctaxi_fare a séma és a tábla nevének használta, a forgatókönyv). Hasonló kísérletek multiclass besorolás és a visszavonási lehet létrehozni.

![Az Azure ML vonat][10]

> [!IMPORTANT]
> A modellezési adatok kinyerése és a mintavételi lekérdezés példák korábbi szakaszokban találhatók **összes címkéket a három modellezési gyakorlatok szerepelnek a lekérdezés**. Minden a modellezési gyakorlatok (kötelező) fontos lépés, hogy **kizárása** a szükségtelen címkék a más két problémákat, és bármely más **céloz kiszivárgásának**. Például bináris osztályozási használatakor használjon a címke **Formabontó** , és amelyeket a mezők **tipp\_osztály**, **tipp\_összeg**, és **teljes\_összeg**. Az utóbbi cél kiszivárgásának óta azt tartalmazzák a tipp fizetett.
> 
> Felesleges oszlopok kizárásához, vagy a cél kiszivárgásának, használhatja a [Select Columns in Dataset] [ select-columns] modul vagy a [szerkesztése metaadatok][edit-metadata]. További információkért lásd: [Select Columns in Dataset] [ select-columns] és [szerkesztése metaadatok] [ edit-metadata] lapok hivatkoznak.
> 
> 

## <a name="mldeploy"></a>Az Azure Machine Learning modellek telepítése
Amikor készen áll a modell, könnyedén telepítheti azt egy webszolgáltatás-ről a kísérlet. Azure ML web services telepítésével kapcsolatos további információkért lásd: [központi telepítése az Azure Machine Learning webszolgáltatás](../studio/publish-a-machine-learning-web-service.md).

Egy új webszolgáltatás-bővítmény telepítéséhez kell:

1. A pontozási kísérlet létrehozása.
2. A webszolgáltatás üzembe helyezése.

Egy pontozási kísérletet a létrehozásához egy **befejezett** betanítása kísérletet, kattintson a **létrehozása pontozási KÍSÉRLETEZHET** alacsonyabb műveletsávon.

![Pontozás az Azure][18]

Az Azure Machine Learning megpróbál létrehozni egy pontozási kísérletet, a tanítási kísérletet összetevői alapján. Különösen a következőket hajtja végre:

1. A betanított modell elmentésével, és távolítsa el a modell képzési modulok.
2. Azonosítsa a logikai **bemeneti port** a szükséges bemeneti adatok séma képviseli.
3. Azonosítsa a logikai **kimeneti port** képviselő a várt webes szolgáltatás kimeneti sémával.

A pontozási kísérlet jön létre, amikor, tekintse át, és ellenőrizze, hogy szükség szerint állítsa be. Egy tipikus módosítása, hogy cserélje le a bemeneti adatkészlet és/vagy lekérdezési egyet, amely kizárja a címke mezők, mivel ezek nem lesz elérhető a szolgáltatás meghívásakor. Azt is néhány rögzíti, hogy a bemeneti adatkészlet és/vagy lekérdezési méretének csökkentése érdekében célszerű éppen elegendő mértékű jelzi a bemeneti sémát. A kimeneti port esetében gyakori, hogy kizárja az összes beviteli mezőt, és csak a **pontozott címkék** és **program pontozza a mennyiségeket valószínűség** a kimeneti használatával a [Select Columns in Dataset] [ select-columns] modul.

Az alábbi ábra a kísérletben pontozási minta valósul meg. Amikor készen áll a központi telepítése, kattintson a **WEBSZOLGÁLTATÁS közzététele** alacsonyabb műveletsávon gombra.

![Az Azure ML közzététele][11]

## <a name="summary"></a>Összefoglalás
Recap mi azt megtette az oktatóanyag forgatókönyv, hogy hozott létre az Azure data tudományos környezethez, a nagy nyilvános dataset dolgozott véve a folyamatot, a csapat adatok tudományos, egészen az adatgyűjtést modell betanítási, majd a az Azure Machine Learning webszolgáltatás üzembe helyezése.

### <a name="license-information"></a>Licencinformációk
Ez a minta forgatókönyv és a mellékelt parancsfájlok és IPython notebook(s) által megosztott Microsoft alatt a MIT licenccel. Ellenőrizze a LICENSE.txt fájlt további részletekért a Githubon mintakódot a címtárban.

## <a name="references"></a>Referencia
• [Andrés Monroy NYC Taxi utak letöltési oldala](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC taxiköltség út adatok Chris Whong által](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi és Limousine Bizottság kutatási és statisztika](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

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

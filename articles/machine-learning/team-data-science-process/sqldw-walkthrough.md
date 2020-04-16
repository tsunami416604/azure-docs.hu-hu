---
title: Modell létrehozása és üzembe helyezése az Azure Synapse Analytics használatával – Csapatadat-elemzési folyamat
description: Gépi tanulási modellt hozhat létre és helyezhet üzembe az Azure Synapse Analytics használatával egy nyilvánosan elérhető adatkészlettel.
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
ms.openlocfilehash: 9c4c1cfdb927cfd2ee607bfe2a951e06c80f9bfb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418541"
---
# <a name="the-team-data-science-process-in-action-using-azure-synapse-analytics"></a>A csapatadat-elemzési folyamat működés közben: az Azure Synapse Analytics használata
Ebben az oktatóanyagban bemutatjuk, hogy az Azure Synapse Analytics használatával egy gépi tanulási modellt hoz létre és telepít egy nyilvánosan elérhető adatkészlet – a [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) adatkészlet – használatával. A bináris osztályozási modell épített előre jelzi, hogy egy tipp fizetik-e az utazás.  A modellek közé tartozik a többosztályos besorolás (függetlenül attól, hogy van-e tipp) és a regresszió (a kifizetett tippösszegek elosztása).

Az eljárás a [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) munkafolyamatot követi. Bemutatjuk, hogyan állíthat be egy adatelemzési környezetet, hogyan töltheti be az adatokat az Azure Synapse Analytics szolgáltatásba, és hogyan használhatja az Azure Synapse Analytics vagy az IPython-jegyzetfüzetet az adatok és a modellező funkciók tervezéséhez. Ezután bemutatjuk, hogyan hozhat létre és helyezhet üzembe egy modellt az Azure Machine Learning használatával.

## <a name="the-nyc-taxi-trips-dataset"></a><a name="dataset"></a>A NYC Taxi Trips adatkészlet
A NYC Taxi Trip adatok áll körülbelül 20 GB tömörített CSV fájlok (~ 48 GB tömörítetlen), felvétel több mint 173 millió egyéni utak és a viteldíjak fizetett minden út. Minden út rekord tartalmazza a felvételi és dropoff helyeken és időpontokban, anonimizált hack (driver's) engedély számát, és a medál (taxi egyedi azonosító) számát. Az adatok a 2013-as év összes utazására vonatkoznak, és minden hónapra vonatkozóan a következő két adatkészletben szerepelnek:

1. A **trip_data.csv** fájl tartalmazza az utazás részleteit, például az utasok számát, a felvételi és leadási pontokat, az utazás időtartamát és az utazás hosszát. Íme néhány mintarekord:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. A **trip_fare.csv** fájl tartalmazza az egyes utazásokért fizetett viteldíj részleteit, például a fizetés típusát, a viteldíj összegét, a pótdíjat és az adókat, a tippeket és az útdíjakat, valamint a teljes kifizetett összeget. Íme néhány mintarekord:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Az **unique key** utazási\_adatok és az\_utazási viteldíj összekapcsolására használt egyedi kulcs a következő három mezőből áll:

* Medál
* csapkod\_engedély és
* a\_felvétel dátumidejét.

## <a name="address-three-types-of-prediction-tasks"></a><a name="mltasks"></a>Háromféle előrejelzési feladat kezelése
Három előrejelzési problémát fogalmazunk meg a *tipp\_összege* alapján, hogy háromféle modellezési feladatot mutassunk be:

1. **Bináris besorolás:** Annak előrejelzéséhez, hogy egy tippet fizettek-e egy utazásért, azaz a 0 $-nál nagyobb *\_tippösszeg* pozitív példa, míg a 0 $ *tipp\_összege* negatív példa.
2. **Többosztályos besorolás**: Az utazásért fizetett tipp tartományának előrejelzése. A *hegy\_összegét* öt tárolóra vagy osztályra osztjuk:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Regressziós feladat:** Az utazásért fizetett tipp összegének előrejelzése.

## <a name="set-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Az Azure-adatelemzési környezet beállítása a fejlett elemzésekhez
Az Azure Data Science-környezet beállításához kövesse az alábbi lépéseket.

**Saját Azure blobstorage-fiók létrehozása**

* Amikor saját Azure blob-tárolót épít ki, válasszon egy földrajzi helyet az Azure blobstorage-hoz az **USA déli középső részén**vagy a lehető legközelebb, ahol a NYC Taxi-adatok tárolásra kerül. Az adatok másolása az AzCopy használatával a nyilvános blob storage-tároló egy tárolóba a saját tárfiókban. Minél közelebb van az Azure blob storage-hoz az USA déli középső régiója, annál gyorsabban befejeződik ez a feladat (4. lépés).
* Saját Azure Storage-fiók létrehozásához kövesse az [Azure Storage-fiókok – ismertet](../../storage/common/storage-create-storage-account.md)című dokumentumban ismertetett lépéseket. Ügyeljen arra, hogy a következő értékektárfiók hitelesítő adatait, mivel szükség lesz rájuk később ebben a forgatókönyvben.

  * **Tárfiók neve**
  * **Tárfiók kulcsa**
  * **Tároló neve** (amely az adatokat az Azure blob storage-ban szeretné tárolni)

**Azure Synapse Analytics-példány kiépítése.**
Kövesse az Azure Synapse Analytics-példány [létrehozásához és lekérdezéséhez az Azure SQL Data Warehouse létrehozása című](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md) dokumentációt. Győződjön meg arról, hogy a következő Azure Synapse Analytics hitelesítő adatokat, amelyek későbbi lépésekben fognak használni, győződjön meg arról, hogy a következő Azure Synapse Analytics hitelesítő adatokat.

* **Kiszolgáló**neve \<: kiszolgáló neve>.database.windows.net
* **SQLDW (adatbázis) neve**
* **Felhasználónév**
* **Jelszó**

**Telepítse a Visual Studio és az SQL Server Data Tools alkalmazást.** További információt a [Visual Studio 2019 for SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-install-visual-studio.md)– első lépései című témakörben talál.

**Csatlakozzon az Azure Synapse Analytics szolgáltatáshoz a Visual Studióval.** További információt az [1](../../synapse-analytics/sql/connect-overview.md)& 2.

> [!NOTE]
> Futtassa a következő SQL-lekérdezést az Azure Synapse Analytics szolgáltatásban létrehozott adatbázison (a csatlakozási témakör 3. lépésében megadott lekérdezés helyett), hogy **hozzon létre egy főkulcsot.**
>
>

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Hozzon létre egy Azure Machine Learning-munkaterületet az Azure-előfizetésében.** További információt az [Azure Machine Learning-munkaterület létrehozása című témakörben talál.](../studio/create-workspace.md)

## <a name="load-the-data-into-azure-synapse-analytics"></a><a name="getdata"></a>Az adatok betöltése az Azure Synapse Analytics szolgáltatásba
Nyisson meg egy Windows PowerShell parancskonzolt. Futtassa a következő PowerShell-parancsokat a GitHubon megosztott példa SQL-parancsfájlok letöltéséhez egy helyi könyvtárba, amelyet a *-DestDir*paraméterrel ad meg. A *-DestDir* paraméter értékét bármely helyi könyvtárra módosíthatja. Ha *a -DestDir* nem létezik, a PowerShell-parancsfájl hozza létre.

> [!NOTE]
> Előfordulhat, hogy a következő PowerShell-parancsfájl végrehajtásakor rendszergazdaként kell **futtatnia,** ha a *DestDir* könyvtárnak rendszergazdai jogosultságra van szüksége a létrehozásához vagy írásához.
>
>

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

A sikeres végrehajtás után az aktuális munkakönyvtár *-DestDir*-re változik. Önnek kellene képesnek lenni által lát képernyő szeret alul:

![A munkakönyvtár jelenlegi módosításai][19]

A *-DestDir*játékban hajtsa végre a következő PowerShell-parancsfájlt rendszergazdai módban:

    ./SQLDW_Data_Import.ps1

Amikor a PowerShell-parancsfájl első alkalommal fut, a rendszer megkéri, hogy adja meg az adatokat az Azure Synapse Analytics és az Azure blob storage-fiók. Amikor ez a PowerShell-parancsfájl első alkalommal fut, a bevitt hitelesítő adatok a jelenlegi munkakönyvtárban lévő SQLDW.conf konfigurációs fájlba lesznek írva. A PowerShell-parancsfájl jövőbeli futtatása lehetővé teszi az összes szükséges paraméter olvasását ebből a konfigurációs fájlból. Ha módosítania kell néhány paramétert, a paramétereket a képernyőn a rendszer kérésre adja meg a konfigurációs fájl törlésével és a paraméterek értékeinek kérésként történő bevitelével, vagy módosíthatja a paraméterértékeket a *-DestDir* könyvtársSQLDW.conf fájl jának szerkesztésével.

> [!NOTE]
> Annak elkerülése érdekében, hogy a sémanév ütközik azokkal, amelyek már léteznek az Azure Azure Synapse Analytics, amikor a paraméterek olvasása közvetlenül a SQLDW.conf fájlt, egy háromjegyű véletlen számot ad hozzá a séma nevét a SQLDW.conf fájlt, mint az alapértelmezett séma nevét minden futtatáskor. A PowerShell-parancsfájl sémanév rekedése lehet: a nevet a felhasználó belátása szerint megadhatja.
>
>

Ez a **PowerShell-parancsfájl** a következő feladatokat hajtja végre:

* **Az AzCopy letöltése és telepítése,** ha az AzCopy még nincs telepítve

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
* **Adatok másolása a privát blobtár-fiókba** a nyilvános blobból az AzCopy segítségével

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* **Adatokat tölt be a Polybase használatával (a LoadDataToSQLDW.sql végrehajtásával) az Azure Synapse Analytics-be** a privát blob storage-fiókjából a következő parancsokkal.

  * Séma létrehozása

          EXEC (''CREATE SCHEMA {schemaname};'');
  * Adatbázis hatókörrel rendelkező hitelesítő adatainak létrehozása

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
  * Hozzon létre külső fájlformátumot egy csv-fájlhoz. Az adatok tömörítetlenek, a mezők pedig a csőkarakterrel vannak elválasztva.

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
  * Hozzon létre külső viteldíj- és utazási táblákat a NYC taxi adatkészlethez az Azure blob storage-ban.

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

    - Adatok betöltése külső táblákból az Azure blobstorage-ban az Azure Synapse Analytics szolgáltatásba

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

    - Hozzon létre egy mintaadattáblát (NYCTaxi_Sample), és szúrjon be adatokat az utazás- és viteldíjtáblák SQL-lekérdezéseinek kiválasztásából. (A forgatókönyv néhány lépésének ezt a mintatáblát kell használnia.)

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

A tárfiókok földrajzi helye befolyásolja a betöltési időket.

> [!NOTE]
> A privát blob-tárfiók földrajzi helyétől függően a nyilvános blobból a privát tárfiókba történő másolás folyamata körülbelül 15 percet vagy még tovább is eltarthat, és az adatok betöltése a tárfiókból az Azure Azure Synapse Analytics-be 20 percet vagy tovább is eltarthat.
>
>

El kell döntenie, hogy mi a teendő, ha ismétlődő forrás- és célfájljai vannak.

> [!NOTE]
> Ha a nyilvános blobstorage-ból a privát blob tárfiókba másolandó .csv fájlok már léteznek a privát blobtár-fiókban, az AzCopy megkérdezi, hogy felül írja-e őket. Ha nem szeretné felülírni őket, írja be **az n-t,** amikor a rendszer kéri. Ha **mindegyiket** felül szeretné írni, akkor a beíráskor adjon meg **egy.** **A** .csv fájlok egyenkénti felülírásához y-t is beírhat.
>
>

![Kimenet az AzCopy-ból][21]

Használhatja a saját adatait. Ha az adatok a helyszíni gép a valós életben alkalmazás, továbbra is használhatja az AzCopy feltöltheti a helyszíni adatokat a privát Azure blob storage.If your data is in your on-premises application in your real life application, you still use on-premises data to your private Azure blob storage. Csak meg kell **Source** változtatni a `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`forrás helyét, a PowerShell parancsfájl azAzCopy parancsában az adatokat tartalmazó helyi könyvtárra.

> [!TIP]
> Ha az adatok már a privát Azure blob storage a valós életben alkalmazás, kihagyhatja az AzCopy lépést a PowerShell-parancsfájlban, és közvetlenül feltöltheti az adatokat az Azure Azure Synapse Analytics. Ehhez a parancsfájl további szerkesztésére van szükség, hogy az adatok formátumához igazodjon.
>
>

Ez a PowerShell-parancsfájl az Azure Synapse Analytics-adatokat is csatlakoztatja az adatfeltárási példafájlokhoz SQLDW_Explorations.sql, SQLDW_Explorations.ipynb és SQLDW_Explorations_Scripts.py, hogy ez a három fájl azonnal kipróbálható legyen a PowerShell-parancsfájl befejezése után.

A sikeres végrehajtás után az alábbihoz hasonló képernyő jelenik meg:

![Sikeres parancsfájl-végrehajtás kimenete][20]

## <a name="data-exploration-and-feature-engineering-in-azure-synapse-analytics"></a><a name="dbexplore"></a>Az Azure Synapse Analytics adatfeltárása és szolgáltatástervezése
Ebben a szakaszban az adatok feltárását és a szolgáltatásgenerálást úgy végezzük, hogy sql-lekérdezéseket futtatunk az Azure Synapse Analytics szolgáltatásban közvetlenül a **Visual Studio Data Tools**használatával. Az ebben a szakaszban használt összes SQL-lekérdezés a *SQLDW_Explorations.sql*nevű mintaparancsfájlban található. Ezt a fájlt a PowerShell-parancsfájl már letöltötte a helyi címtárba. A [GitHubról](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql)is lekérheti. De a githubon lévő fájlhoz nincs csatlakoztatva az Azure Synapse Analytics-információ.

Csatlakozzon az Azure Synapse Analytics-hez a Visual Studió segítségével az Azure Synapse Analytics bejelentkezési nevével és jelszavával, és nyissa meg az **SQL Object Explorert,** hogy ellenőrizze az adatbázis és a táblák importálását. A *SQLDW_Explorations.sql* fájl beolvasása.

> [!NOTE]
> A Párhuzamos adattárház (PDW) lekérdezésszerkesztő megnyitásához használja az **Új lekérdezés** parancsot, amíg a PDW ki van jelölve az SQL **Object Explorer**programban . A PDW nem támogatja a szabványos SQL-lekérdezésszerkesztőt.
>
>

Az ebben a szakaszban végrehajtott adatfeltárási és szolgáltatásgenerálási feladatok típusai a következők:

* Néhány mező adateloszlásának megismerése különböző időablakokban.
* Vizsgálja meg a hosszúsági és szélességi mezők adatminőségét.
* Bináris és többosztályos besorolási címkék létrehozása a **csúcs\_mennyisége**alapján.
* Funkciók at generálhat, és kiszámítja/összehasonlíthatja az utazási távolságokat.
* Csatlakozzon a két táblázathoz, és bontson ki egy véletlenszerű mintát, amely et modellek létrehozásához fog használni.

### <a name="data-import-verification"></a>Adatimportálás ellenőrzése
Ezek a lekérdezések gyorsan ellenőrzik a sorok és oszlopok számát a táblákban, amelyeket korábban töltöttek ki a Polybase párhuzamos tömeges importálásával,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Kimenet:** 173 179 759 sort és 14 oszlopot kell kapnia.

### <a name="exploration-trip-distribution-by-medallion"></a>Feltárás: Utazás elosztása medál szerint
Ez a példalekérdezés azonosítja azokat a medálokat (taxiszámokat), amelyek egy adott időszakon belül több mint 100 utat teljesítettek. A lekérdezés számára előnyös lenne a particionált tábla-hozzáférés, mivel a **\_felvételi datetime**partícióséma határozza meg. A teljes adatkészlet lekérdezése a particionált táblát és/vagy az indexvizsgálatot is használja.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Kimenet:** A lekérdezésnek egy olyan táblát kell visszaadnia, amelyben a 13 369 medál (taxik) és a 2013-ban végrehajtott utazások száma szerepel. Az utolsó oszlop a befejezett utak számát tartalmazza.

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Feltárás: Utazás elosztása medál és hack_license
Ez a példa azonosítja azokat a medálokat (taxiszámokat) és hack_license számokat (illesztőprogramokat), amelyek egy adott időszakon belül több mint 100 utat teljesítettek.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Kimenet:** A lekérdezésnek egy 13 369 sort tartalmazó táblát kell visszaadnia, amely 13 369 olyan autó-/illesztőprogram-azonosítót tartalmaz, amelyek 2013-ban több mint 100 utat teljesítettek. Az utolsó oszlop a befejezett utak számát tartalmazza.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Adatminőség-értékelés: Ellenőrizze a helytelen hosszúságú és/vagy szélességi adatokat
Ez a példa azt vizsgálja, hogy a hosszúsági és/vagy szélességi mezők bármelyike tartalmaz-e érvénytelen értéket (a radiánfoknak -90 és 90 között kell lennie), vagy (0, 0) koordinátákkal rendelkezik-e.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Kimenet:** A lekérdezés 837 467 olyan utat ad vissza, amelyek hosszúsági és/vagy szélességi mezői érvénytelenek.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Feltárás: Tipp vs. nem hegyű utak eloszlása
Ez a példa megkeresi a megadott időszakban (vagy a teljes adatkészletben nem megadott időszakban nem megadott számú utazások számát, ha a teljes évet lefedi, ahogy az itt van beállítva). Ez az eloszlás a bináris osztályozási modellezéshez később használandó bináris címkeeloszlást tükrözi.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Kimenet:** A lekérdezésnek a következő tippfrekvenciákat kell visszaadnia a 2013-as évre: 90 447 622 hegyű és 82 264 709 nem hegyű.

### <a name="exploration-tip-classrange-distribution"></a>Feltárás: Tipposztály/tartomány eloszlása
Ez a példa kiszámítja a tipptartományok eloszlását egy adott időszakban (vagy a teljes adatkészletben, ha a teljes évre vonatkozik). A címkeosztályok ezen eloszlása később többosztályos osztályozási modellezéshez lesz használva.

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

**Kimeneti:**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Feltárás: Számítási és összehasonlítási távolság
Ez a példa a felvételi és a lemorzsolódási hosszúsági és szélességi fokot SQL földrajzi pontokká alakítja, kiszámítja az utazási távolságot az SQL földrajzi pontok különbségének használatával, és az eredmények véletlenszerű mintáját adja vissza az összehasonlításhoz. A példa az eredményeket csak a korábban lefedett adatminőség-értékelési lekérdezés használatával korlátozza érvényes koordinátákra.

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

### <a name="feature-engineering-using-sql-functions"></a>Szolgáltatástervezés SQL-függvényekkel
Néha az SQL-függvények hatékony megoldást választhatnak a szolgáltatástervezéshez. Ebben a forgatókönyvben definiáltunk egy SQL függvényt a felvételi és a leadási helyek közötti közvetlen távolság kiszámításához. A **Visual Studio Data Tools**alkalmazásban a következő SQL-parancsfájlokat futtathatja.

Itt van az SQL script, amely meghatározza a távolság függvényt.

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

Íme egy példa, amely meghívja ezt a függvényt az SQL-lekérdezés szolgáltatásainak létrehozásához:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Kimenet:** Ez a lekérdezés létrehoz egy táblát (2 803 538 sorból) a felvételi és a leadási szélességi és hosszúsági fokokkal, valamint a megfelelő közvetlen távolságokkal mérföldben. Itt vannak az első három sor eredményei:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | Közvetlen távolság |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Adatok előkészítése a modellépítéshez
A következő lekérdezés csatlakozik a **\_nyctaxi utazás** és **nyctaxi\_viteldíj** táblák, létrehoz egy bináris besorolási címke **billentett**, egy többosztályos besorolási címke **\_tipp osztály,** és kinyeri a mintát a teljes illesztett adatkészletből. A mintavételezés az utazások egy részhalmazának lekérésével történik a felvételi idő alapján.  Ez a lekérdezés közvetlenül az [Azure Machine Learning Studio (klasszikus)](https://studio.azureml.net) Import [Data][import-adatmodulba] másolható az Azure-beli SQL Database-példányból történő közvetlen adatbetöltéshez. A lekérdezés nem tartalmazza a helytelen (0, 0) koordinátákkal rendelkező rekordokat.

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

Ha készen áll az Azure Machine Learningre való továbbmunkára, a következőket teheti:

1. A végső SQL-lekérdezés mentése az adatok kinyeréséhez és mintavételéhez, valamint a lekérdezés közvetlen beillesztése közvetlenül az Azure Machine Learning[importálási adatok] moduljába, vagy
2. Őrize meg a mintavételezett és a tervezett adatokat, amelyeket egy új Azure Synapse Analytics-táblában modellépítéshez kíván használni, és használja az új táblát az [Adatok][importálása-adatok] modulban az Azure Machine Learningben. A PowerShell-parancsfájl a korábbi lépésben ezt a feladatot megtette. Az Adatok importálása modulban közvetlenül olvashat ebből a táblából.

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Adatfeltárás és szolgáltatástervezés az IPython notebookban
Ebben a szakaszban az adatok feltárását és a szolgáltatás generálását python- és SQL-lekérdezések használatával végezzük a korábban létrehozott Azure Synapse Analytics ellen. Egy **SQLDW_Explorations.ipynb** nevű minta IPython-jegyzetfüzet és egy **SQLDW_Explorations_Scripts.py** python parancsfájl a helyi könyvtárba lett letöltve. A [GitHubon](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW)is elérhetők. Ez a két fájl megegyezik a Python szkriptek. A Python parancsfájl abban az esetben érhető el, ha nem rendelkezik IPython notebook-kiszolgálóval. Ez a két python-fájl **python 2.7**alatt készült.

A szükséges Azure Synapse Analytics-információkat a minta IPython notebook és a Python script fájlt a helyi számítógépre letöltött már csatlakoztatva van a PowerShell-parancsfájl korábban. Ezek bármilyen módosítás nélkül végrehajthatók.

Ha már beállított egy Azure Machine Learning-munkaterületet, közvetlenül feltöltheti a minta IPython-jegyzetfüzetet az AzureML IPython-jegyzetfüzet-szolgáltatásba, és elkezdheti futtatni. Az AzureML IPython-jegyzetfüzet-szolgáltatásba való feltöltés lépéseit az alábbiakszerint ismertetem:

1. Jelentkezzen be az Azure Machine Learning-munkaterületre, kattintson a **Studio"** elemre a lap tetején, és kattintson a **jegyzetfüzetek** elemre a weblap bal oldalán.

    ![Kattintson a Studio, majd notebook][22]
2. Kattintson az **ÚJ** gombra a weblap bal alsó sarkában, és válassza a **Python 2**lehetőséget. Ezután adjon meg egy nevet a jegyzetfüzetnek, és kattintson a pipára az új üres IPython Notebook létrehozásához.

    ![Kattintson az ÚJ gombra, majd válassza a Python 2 lehetőséget.][23]
3. Kattintson az új IPython-jegyzetfüzet bal felső sarkában található **Jupyter** szimbólumra.

    ![Kattintson a Jupyter szimbólumra][24]
4. Húzza a minta IPython-jegyzetfüzetet az AzureML IPython-jegyzetfüzet-szolgáltatás **falapjára,** és kattintson a **Feltöltés gombra.** Ezután a minta IPython-jegyzetfüzet lesz feltöltve az AzureML IPython-jegyzetfüzet szolgáltatásba.

    ![Kattintson a Feltöltés gombra][25]

A minta IPython Notebook vagy a Python parancsfájl futtatásához a következő Python-csomagok szükségesek. Ha az AzureML IPython Notebook szolgáltatást használja, ezek a csomagok előre telepítve vannak.

- Pandák
- numpy
- matplotlib között
- pyodbc között
- PyTables (PyTables)

Ha speciális analitikai megoldásokat hoz létre az Azure Machine Learningen nagy adatokkal, az alábbiakat javasoljuk:

* Olvassa be az adatok egy kis mintáját egy memórián belüli adatkeretbe.
* A mintavételezett adatok használatával bizonyos vizualizációkat és feltárásokat hajthat végre.
* Kísérletezzen a szolgáltatástervezéssel a mintavételezett adatok használatával.
* A nagyobb adatfeltárás, az adatok kezelése és a szolgáltatás tervezés, a Python segítségével sql-lekérdezések közvetlenül az Azure Synapse Analytics.
* Döntse el, hogy a minta mérete alkalmas az Azure Machine Learning modell létrehozása.

Az alábbiakban néhány adatfeltárás, adatmegjelenítés és a szolgáltatás mérnöki példák. További adatfeltárások megtalálható a minta IPython Notebook és a minta Python parancsfájl.

### <a name="initialize-database-credentials"></a>Adatbázis hitelesítő adatainak inicializálása
Az adatbázis-kapcsolat beállításainak inicializálása a következő változókban:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Adatbázis-kapcsolat létrehozása
Itt van a kapcsolat ihúr, amely létrehozza a kapcsolatot az adatbázissal.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>A táblázat sorainak és oszlopainak száma <nyctaxi_trip>
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

* Sorok száma összesen = 173179759
* Oszlopok száma összesen = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_fare"></a>A táblázat sorainak és oszlopainak száma <nyctaxi_fare>
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

* Sorok száma összesen = 173179759
* Oszlopok száma összesen = 11

### <a name="read-in-a-small-data-sample-from-the-azure-synapse-analytics-database"></a>Kis adatminta beolvasása az Azure Synapse Analytics-adatbázisból
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

A mintatábla olvasási ideje 14,096495 másodperc.
A beolvasott sorok és oszlopok száma = (1000, 21).

### <a name="descriptive-statistics"></a>Leíró statisztikák
Most már készen áll a mintavételezett adatok feltárására. Kezdjük nézett néhány leíró statisztikák az **utazási\_távolság** (vagy bármely más területen úgy dönt, hogy adja meg).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Vizualizáció: Példa a dobozos nyomtatásra
Ezután megnézzük a doboz telek az utazás távolság a képzelni a kvantikát.

    df1.boxplot(column='trip_distance',return_type='dict')

![Mezőtelek kimenete][1]

### <a name="visualization-distribution-plot-example"></a>Vizualizáció: Terjesztési telek példa
Olyan telkek, amelyek a mintavételezett úttávolságok eloszlását és hisztogramját ábrázolják.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Elosztási telek kimenete][2]

### <a name="visualization-bar-and-line-plots"></a>Képi megjelenítés: Sáv- és vonaltelkek
Ebben a példában az utazási távolságot öt tárolóba tároljuk, és a binning eredményeket vizualizáljuk.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

A fenti raktárhely-eloszlást sávban vagy vonalábrázolásban ábrázolhatjuk a következőkkel:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Sávtelek kimenete][3]

és

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Sortelek kimenete][4]

### <a name="visualization-scatterplot-examples"></a>Vizualizáció: Scatterplot példák
Megmutatjuk scatter telek között **\_utazási idő\_\_mp** és az utazási **\_távolság,** hogy ha van összefüggés

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Az idő és a távolság közötti kapcsolat scatterplot kimenete][6]

Hasonlóképpen ellenőrizhetjük a **\_díjkód** és az **utazási\_távolság**közötti kapcsolatot.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![A kód és a távolság közötti kapcsolat scatterplot kimenete][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>A mintában szereplő adatok adatfeltárása IPython-jegyzetfüzetBEN SQL-lekérdezésekkel
Ebben a szakaszban az adateloszlásokat a mintavételezett adatok használatával vizsgáljuk, amelyek a fent létrehozott új táblában megmaradnak. Hasonló feltárások végezhetők az eredeti táblázatok használatával.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Feltárás: A mintában szereplő tábla sorainak és oszlopainak jelentése
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Feltárás: Tippelt/nem tripped Elosztás
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Feltárás: Tipp osztály eloszlása
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Feltárás: A csúcs eloszlásának ábrázolása osztály szerint
    tip_class_dist['tip_freq'].plot(kind='bar')

![Telek #26][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Feltárás: Az utak napi eloszlása
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Feltárás: Utazás elosztása medálonként
    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Feltárása: Trip forgalmazás medál és csapkod engedély
    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Feltárás: Utazásidő eloszlása
    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Feltárás: Távolságeloszlás
    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Feltárás: Kifizetéstípus felosztása
    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>A felvázolt táblázat végleges formájának ellenőrzése
    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="build-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Modellek készítése az Azure Machine Learningben
Most már készen állunk arra, hogy az [Azure Machine Learningben](https://studio.azureml.net)modellépítést és modelltelepítést is eltegyünk. Az adatok készen állnak a korábban azonosított előrejelzési problémák bármelyikében való használatra, nevezetesen:

1. **Bináris besorolás**: Annak előrejelzése, hogy egy tipp et fizettek-e egy utazásért.
2. **Többosztályos besorolás**: A kifizetett tipp tartományának előrejelzése a korábban meghatározott osztályok szerint.
3. **Regressziós feladat:** Az utazásért fizetett tipp összegének előrejelzése.

A modellezési gyakorlat megkezdéséhez jelentkezzen be az **Azure Machine Learning (klasszikus)** munkaterületére. Ha még nem hozott létre gépi tanulási munkaterületet, olvassa [el az Azure Machine Learning Studio (klasszikus) munkaterület létrehozása című témakört.](../studio/create-workspace.md)

1. Az Azure Machine Learning első lépései: [Mi az Azure Machine Learning Studio (klasszikus)?](../studio/what-is-ml-studio.md)
2. Jelentkezzen be az [Azure Machine Learning Studio (klasszikus)](https://studio.azureml.net)rendszerbe.
3. A Machine Learning Studio (klasszikus) kezdőlapja rengeteg információt, videót, oktatóanyagot, a modulokra mutató referenciára mutató hivatkozásokat és egyéb forrásokat tartalmaz. Az Azure Machine Learningről további információt az [Azure Machine Learning dokumentációs központjában](https://azure.microsoft.com/documentation/services/machine-learning/)talál.

Egy tipikus képzési kísérlet a következő lépésekből áll:

1. Hozzon létre egy **+ÚJ** kísérletet.
2. Az adatok beszerezhető az Azure Machine Learning Studio (klasszikus) rendszerbe.
3. Szükség szerint elődolgozza, átalakítsa és manipulálja az adatokat.
4. Szükség szerint hozzon létre funkciókat.
5. Az adatok felosztása betanítási/érvényesítési/tesztelési adatkészletekre (vagy mindegyikhez külön adatkészletek).
6. Válasszon ki egy vagy több gépi tanulási algoritmust a megoldandó tanulási problémától függően. Például bináris besorolás, többosztályos besorolás, regresszió.
7. Egy vagy több modell betanítása a betanítási adatkészlet használatával.
8. Pontzítsa az érvényesítési adatkészletet a betanított modell(ek) használatával.
9. Értékelje ki a modell(ek) a tanulási probléma megfelelő metrikáinak kiszámításához.
10. Hangolja be a modell(eke)t, és válassza ki az üzembe helyezhető legjobb modellt.

Ebben a gyakorlatban már megvizsgáltuk és megterveztük az adatokat az Azure Synapse Analytics szolgáltatásban, és úgy döntöttünk, hogy a minta mérete betöltéshez az Azure Machine Learning Studio (klasszikus). Itt van az eljárás, hogy építsenek egy vagy több előrejelzési modellek:

1. Az adatokat az Azure Machine Learning Studio (klasszikus) az Adatok[importálása-adatok] modul, elérhető az **adatbevitel és kimenet** szakaszban. [Import Data] További információt az [Import Data][Import-Data-Data] module referencialapján talál.

    ![Azure ML importálási adatok][17]
2. Válassza ki az **Azure SQL Database** **adatforrásként** a **Tulajdonságok** panelen.
3. Írja be az adatbázis DNS-nevét az **Adatbázis-kiszolgáló neve** mezőbe. Formátum:`tcp:<your_virtual_machine_DNS_name>,1433`
4. Írja be az **adatbázis nevét** a megfelelő mezőbe.
5. Írja be az *SQL-felhasználónevet* a **Kiszolgáló felhasználói fiók nevébe**, a *jelszót* pedig a Kiszolgáló **felhasználói fiókjának jelszavába.**
7. Az **Adatbázis-lekérdezés** szerkesztése szövegterületen illessze be a szükséges adatbázismezőket kibontó lekérdezést (beleértve a számított mezőket, például a címkéket is), és vegye le az adatokat a kívánt mintaméretre.

Egy példa egy bináris besorolási kísérlet adatok at közvetlenül az Azure Synapse Analytics adatbázis az alábbi ábrán (ne feledje, hogy cserélje ki a táblanevek nyctaxi_trip és nyctaxi_fare a séma nevét és a forgatókönyvben használt táblanevek). Hasonló kísérletek et lehet kialakítani a többosztályos osztályozási és regressziós problémákhoz.

![Azure ML-vonat][10]

> [!IMPORTANT]
> Az előző szakaszokban megadott modellezési adatok kinyerése és mintavételezési lekérdezési példáiban a lekérdezés a **három modellezési gyakorlat összes címkéje szerepel.** Egy fontos (szükséges) lépés az egyes modellezési gyakorlatok, hogy **kizárják** a felesleges címkéket a másik két probléma, és minden más **cél szivárgás**. Bináris osztályozás esetén például a **címke hegyezi,** és zárja ki a mezők **\_tip osztály**, **tipp\_összege**, és **a\_teljes összeg**. Az utóbbiak célpontszivárgások, mivel azt jelentik, hogy a borravalót kifizették.
>
> A szükségtelen oszlopok vagy a célszivárgások kizárásához használhatja az Oszlopok kijelölése az [Adatkészlet modulban][select-columns] vagy a [Metaadatok szerkesztése modult.][edit-metadata] További információt az [Oszlopok kijelölése az adatkészletben][select-columns] és [a Metaadat-referencialapok szerkesztése][edit-metadata] című témakörben talál.
>
>

## <a name="deploy-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Modellek üzembe helyezése az Azure Machine Learningben
Ha a modell készen áll, könnyen üzembe helyezheti, mint egy webszolgáltatás közvetlenül a kísérletből. Az Azure ML webszolgáltatások üzembe helyezéséről az [Azure Machine Learning webszolgáltatás telepítése](../studio/deploy-a-machine-learning-web-service.md)című témakörben talál további információt.

Új webszolgáltatás üzembe helyezéséhez a következőket kell tenni:

1. Hozzon létre egy pontozási kísérletet.
2. Telepítse a webszolgáltatást.

Ha egy **befejezett betanítási** kísérletből pontozási kísérletet szeretne létrehozni, kattintson a **PONTOZÁSI KÍSÉRLET LÉTREHOZÁSA gombra** az alsó műveletsávon.

![Azure pontozás][18]

Az Azure Machine Learning megkísérli a pontozási kísérlet létrehozása a betanítási kísérlet összetevői alapján. Különösen a következőket fogja meg:

1. Mentse a betanított modellt, és távolítsa el a modell betanítási modulokat.
2. A várt bemeneti adatsémát jelölő logikai **bemeneti port** azonosítása.
3. Azonosítsa a várt webszolgáltatás kimeneti sémáját jelző logikai **kimeneti portot.**

A pontozási kísérlet létrehozásakor tekintse át az eredményeket, és szükség szerint módosítsa. Egy tipikus korrekció, hogy cserélje le a bemeneti adatkészlet vagy lekérdezés egy, amely kizárja a címke mezőket, mert ezek a címke mezők nem lesz leképezve a séma, amikor hívja a szolgáltatást. Az is ajánlott, hogy csökkentse a bemeneti adatkészlet méretét és/vagy a lekérdezés néhány rekordot, elég a bemeneti séma jelzéséhez. A kimeneti port esetében gyakori, hogy kizárja az összes bemeneti mezőt, és csak a **pontozott címkéket** és a **pontozott valószínűségeket** tartalmazza a kimenetben az Oszlopok kijelölése az [adatkészlet modulban][select-columns] használatával.

Az alábbi ábrán egy mintapontozási kísérlet található. Amikor készen áll az üzembe helyezésre, kattintson a **KÖZZÉTÉTEL WEBSZOLGÁLTATÁS** gombra az alsó műveletsávon.

![Azure ML közzététel][11]

## <a name="summary"></a>Összefoglalás
Összefoglalva, mit tettünk ebben a forgatókönyv-oktatóanyagban, létrehozott egy Azure-adatelemzési környezetet, egy nagy nyilvános adatkészlettel dolgozott, végighaladva a csapatadat-elemzési folyamaton, az adatgyűjtéstől a modellképzésig, majd egy Azure Machine Learning-webszolgáltatás üzembe helyezéséig.

### <a name="license-information"></a>Licencadatai
Ezt a mintaforgatókönyvet és a hozzá tartozó parancsfájlokat és IPython-jegyzetfüzeteket a Microsoft az MIT-licenc alatt osztja meg. További részletekért tekintse meg a LICENSE.txt fájlt a GitHub mintakódjának könyvtárában.

## <a name="references"></a>Referencia
- [Andrés Monroy NYC Taxi utak Letöltés oldal](https://www.andresmh.com/nyctaxitrips/)
- [FOILing NYC's Taxi Trip adatok Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)
- [NYC Taxi és Limuzin Bizottság Kutatási és Statisztikai](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

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
[adatimportálás]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

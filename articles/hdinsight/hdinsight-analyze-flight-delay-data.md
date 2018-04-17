---
title: A HDInsight - Azure Hadoop repülési késleltetés adatok elemzése |} Microsoft Docs
description: Ismerje meg, egy Windows PowerShell-parancsfájl használata a HDInsight-fürtök létrehozása, egy Hive-feladat futtatása, a Sqoop feladat futtatása és a fürt törlésekor.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00e26aa9-82fb-4dbe-b87d-ffe8e39a5412
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 6e42cfa666ad6b6523043f4412a321789adad9a1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>Repülési késleltetés adatok elemzése a Hive HDInsight használatával
Hive lehetővé teszi egy SQL-szerű nevű programozási nyelv használatával a feladatok Hadoop MapReduce futó  *[HiveQL][hadoop-hiveql]*, amelyek alkalmazhatók felé összefoglalójához, kérdez le, és nagy mennyiségű adatot elemzése.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések egy Windows-alapú HDInsight-fürt szükséges. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement). A Linux-alapú fürt lépéseiért lásd: [repülési késleltetés adatok elemzése a Hive HDInsight (Linux) használatával](hdinsight-analyze-flight-delay-data-linux.md).

Azure HDInsight legfőbb előnye egyike az adatok tárolási és számítási szétválasztását. HDInsight Azure Blob storage-tárolására használ. Egy tipikus munkába beletartozik a három részből áll:

1. **Adatok tárolása az Azure Blob Storage tárolóban.**  Például időjárás adatok, érzékelőadatait, webes naplókat, és ebben az esetben repülési késleltetés adatok menti azokat Azure Blob Storage tárolóban.
2. **Feladatok futtatása.** Amikor az adatok feldolgozására, futtatja egy Windows PowerShell-parancsfájlt (vagy egy ügyfélalkalmazás) HDInsight-fürtök létrehozásához, feladatok futtatása, és törölheti a fürtöt. A feladatok az Azure Blob storage kimeneti adatok mentése. A kimeneti adatok a fürtök törlése után is megmarad. Ezzel a módszerrel kell fizetnie csak néhány Ön által igénybe vett.
3. **A kimeneti beolvasása az Azure Blob storage**, vagy az ebben az oktatóanyagban az Azure SQL-adatbázis az adatok exportálása.

Az alábbi ábrán látható, a forgatókönyv és a jelen oktatóanyag struktúra:

![HDI. FlightDelays.flow][img-hdi-flightdelays-flow]

Vegye figyelembe, hogy az ábrán szereplő számok megfelelnek-e a szakaszok címét. **M** a fő folyamat jelenti. **A** a tartalmat a függelékben jelenti.

A fő részét az oktatóanyag bemutatja, hogyan egy Windows PowerShell-parancsfájl használata a következő feladatok végezhetők el:

* HDInsight-fürtök létrehozása.
* Egy Hive-feladat futtatása repülőtereken átlagos késleltetése kiszámításához a fürtön. Egy Azure Blob storage-fiókot a repülési késleltetés adatokat tárolja.
* Futtasson egy Azure SQL Database adatbázist a Hive-feladat kimeneti exportálása Sqoop feladatot.
* A HDInsight-fürt törlése.

A függelékben található utasításokat repülési késleltetés adatok feltöltése, a Hive lekérdezési karakterlánc létrehozása/feltöltése és az Azure SQL-adatbázis előkészítése a Sqoop feladat.

> [!NOTE]
> A jelen dokumentumban leírt lépések Windows-alapú HDInsight-fürtök vonatkoznak. A Linux-alapú fürt lépéseiért lásd: [adatelemzés repülési késleltetés segítségével Hive HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md)

### <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Munkaállomás Azure PowerShell-lel**.

    > [!IMPORTANT]
    > A HDInsight-erőforrások Azure Service Managerrel történő kezelésének Azure PowerShell-támogatása **elavult**, így 2017. január 1-től megszűnt. A jelen dokumentumban leírt lépések az új HDInsight-parancsmagokat használják, amelyek az Azure Resource Managerrel működnek.
    >
    > Kérjük, kövesse az alábbi cikkben leírt lépéseket az Azure PowerShell legújabb verziójának telepítéséhez: [Install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Az Azure PowerShell letöltése és konfigurálása). Ha vannak olyan parancsprogramjai, amelyeket módosítani kell az új, az Azure Resource Managerrel működő parancsmagok használatához, tekintse meg az alábbi cikket: [Migrating to Azure Resource Manager-based development tools for HDInsight clusters](hdinsight-hadoop-development-using-azure-resource-manager.md) (Az Azure Resource Manager-alapú fejlesztési eszközökre való áttérés HDInsight-fürtök esetén).

**Ebben az oktatóanyagban használt fájlok**

Ez az oktatóanyag használja légitársaság felé továbbított adatok időben teljesítményének [kutatási és innovatív technológia felügyeleti, szállítására statisztika iroda vagy RITA][rita-website].
Egy Azure Blob storage tárolók a nyilvános Blob engedéllyel rendelkező fel lett töltve az adatok másolatát.
A PowerShell parancsfájl részét másolja az adatokat a következő nyilvános blobtárolóban az alapértelmezett blob tároló a fürt. A Blob tárolóhoz is másolja a HiveQL-parancsfájlt.
Ha azt szeretné, további get/feltöltése az adatokat a saját tárfiók, és a HiveQL parancsfájl létrehozása/feltöltés kapcsolatos tudnivalókat lásd: [függelék](#appendix-a) és [B függelék](#appendix-b).

Az alábbi táblázat az ebben az oktatóanyagban használt fájlok:

<table border="1">
<tr><th>Fájlok</th><th>Leírás</th></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>A HiveQL-parancsfájlt a Hive feladat használja. Ez a parancsfájl az Azure Blob storage-fiókkal és a nyilvános hozzáférés fel lett töltve. <a href="#appendix-b">B függelék</a> utasítások előkészítése és a fájl feltöltése a saját Azure Blob storage-fiók.</td></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>A Hive-feladatokban bemeneti adatainál. Az adatok az Azure Blob storage-fiókkal és a nyilvános hozzáférés fel lett töltve. <a href="#appendix-a">A függelék</a> utasításokat rendelkezzen az adatokat, és az adatok feltöltése a saját Azure Blob storage-fiók.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>A Hive-feladat kimeneti elérési útja. Az alapértelmezett tároló a kimeneti adatok tárolására szolgál.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>A Hive feladat állapota mappa az alapértelmezett tároló.</td></tr>
</table>

## <a name="create-cluster-and-run-hivesqoop-jobs"></a>Fürt létrehozása és Hive/Sqoop feladatok futtatása
Hadoop-MapReduce kötegelt feldolgozásával. Egy Hive-feladat futtatásához a legköltséghatékonyabb módon, hogy hozzon létre egy fürtöt, a feladat, és törli a feladatot, a feladat befejezése után. Az alábbi parancsfájl bemutatja a teljes folyamat.
A HDInsight-fürtök létrehozása és Hive-feladatok futtatása további információkért lásd: [Hadoop létrehozása a HDInsight-fürtök] [ hdinsight-provision] és [használata a HDInsight Hive] [hdinsight-use-hive].

**A Hive-lekérdezések futtatásához Azure PowerShell**

1. Hozzon létre egy Azure SQL-adatbázis és a Sqoop feladat kimenetének tábla utasításait [C függelék](#appendix-c).
2. Nyissa meg a Windows PowerShell ISE, és futtassa a következő parancsfájlt:

    ```powershell
    $subscriptionID = "<Azure Subscription ID>"
    $nameToken = "<Enter an Alias>"

    ###########################################
    # You must configure the follwing variables
    # for an existing Azure SQL Database
    ###########################################
    $existingSqlDatabaseServerName = "<Azure SQL Database Server>"
    $existingSqlDatabaseLogin = "<Azure SQL Database Server Login>"
    $existingSqlDatabasePassword = "<Azure SQL Database Server login password>"
    $existingSqlDatabaseName = "<Azure SQL Database name>"

    $localFolder = "E:\Tutorials\Downloads\" # A temp location for copying files.
    $azcopyPath = "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy" # depends on the version, the folder can be different

    ###########################################
    # (Optional) configure the following variables
    ###########################################

    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2"

    $HDInsightClusterName = $namePrefix + "hdi"
    $httpUserName = "admin"
    $httpPassword = "<Enter the Password>"

    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $HDInsightClusterName # use the cluster name

    $existingSqlDatabaseTableName = "AvgDelays"
    $sqlDatabaseConnectionString = "jdbc:sqlserver://$existingSqlDatabaseServerName.database.windows.net;user=$existingSqlDatabaseLogin@$existingSqlDatabaseServerName;password=$existingSqlDatabaseLogin;database=$existingSqlDatabaseName"

    $hqlScriptFile = "/tutorials/flightdelays/flightdelays.hql"

    $jobStatusFolder = "/tutorials/flightdelays/jobstatus"

    ###########################################
    # Login
    ###########################################
    try{
        $acct = Get-AzureRmSubscription
    }
    catch{
        Login-AzureRmAccount
    }
    Select-AzureRmSubscription -SubscriptionID $subscriptionID

    ###########################################
    # Create a new HDInsight cluster
    ###########################################

    # Create ARM group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create the default storage account
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName -Location $location -Type Standard_LRS

    # Create the default Blob container
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultBlobContainerName -Context $defaultStorageAccountContext

    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $HDInsightClusterName `
        -Location $location `
        -ClusterType Hadoop `
        -OSType Windows `
        -ClusterSizeInNodes 2 `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $existingDefaultBlobContainerName

    ###########################################
    # Prepare the HiveQL script and source data
    ###########################################

    # Create the temp location
    New-Item -Path $localFolder -ItemType Directory -Force

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
    $blobs = Get-AzureStorageBlob -Container "flightdelay" -Context $context
    #$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload data to default container

    $azcopycmd = "cmd.exe /C '$azcopyPath\azcopy.exe' /S /Source:'$localFolder' /Dest:'https://$defaultStorageAccountName.blob.core.windows.net/$defaultBlobContainerName/tutorials/flightdelays' /DestKey:$defaultStorageAccountKey"

    Invoke-Expression -Command:$azcopycmd

    ###########################################
    # Submit the Hive job
    ###########################################
    Use-AzureRmHDInsightCluster -ClusterName $HDInsightClusterName -HttpCredential $httpCredential
    $response = Invoke-AzureRmHDInsightHiveJob `
                    -Files $hqlScriptFile `
                    -DefaultContainer $defaultBlobContainerName `
                    -DefaultStorageAccountName $defaultStorageAccountName `
                    -DefaultStorageAccountKey $defaultStorageAccountKey `
                    -StatusFolder $jobStatusFolder

    write-Host $response

    ###########################################
    # Submit the Sqoop job
    ###########################################
    $exportDir = "wasb://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net/tutorials/flightdelays/output"

    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
                    -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ResourceGroupName $resourceGroupName `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose

    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $HDInsightClusterName `
        -HttpCredential $httpCredential `
        -WaitTimeoutInSeconds 3600 `
        -Job $sqoopJob.JobId

    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -DefaultContainer $existingDefaultBlobContainerName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardError

    ###########################################
    # Delete the cluster
    ###########################################
    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName
    ```
3. Csatlakozni az SQL Database adatbázishoz, és tekintse meg a AvgDelays tábla városonként átlagos repülési késések:

    ![HDI. FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]

- - -

## <a id="appendix-a"></a>A függelék – felhőszolgáltató közötti átviteléhez az Azure Blob storage késleltetés adatok feltöltése
Az adatfájl és a HiveQL parancsfájlok feltöltése (lásd: [B függelék](#appendix-b)) is szükség van néhány. A lényege az adatfájlokat és a HiveQL fájl HDInsight fürtök létrehozásával és a Hive-feladat futtatása előtt. Erre két lehetősége van:

* **Az azonos Azure Storage fiókot használjon, amely a HDInsight-fürt által az alapértelmezett fájlrendszer használható.** Mivel a HDInsight-fürt lesz a fiók tárelérési kulcs, nem kell további módosításokat.
* **A HDInsight fürt alapértelmezett fájlrendszer egy másik Azure Storage-fiókot használni.** Ha ez a helyzet, módosítania kell a Windows PowerShell parancsfájl található létrehozásának része [hozzon létre HDInsight-fürtöt és futtatási Hive/Sqoop feladatok](#runjob) egy további tárfiókkal a tárfiók csatolásához. Útmutatásért lásd: [Hadoop létrehozása a HDInsight-fürtök][hdinsight-provision]. A HDInsight-fürt majd tudja a tárfiók elérési kulcsának.

> [!NOTE]
> A Blob storage a adatok fájl elérési útja rögzített bekódolni a a HiveQL-parancsfájlt. Ennek megfelelően kell frissíteni.

**A felé továbbított adatok letöltése**

1. Keresse meg a [kutatási és innovatív technológia felügyeleti, iroda szállítására statisztikák][rita-website].
2. A lapon válassza ki a következő értékeket:

    <table border="1">
    <tr><th>Name (Név)</th><th>Érték</th></tr>
    <tr><td>Szűrő év</td><td>2013 </td></tr>
    <tr><td>Időszak szűrése</td><td>Január</td></tr>
    <tr><td>Mezők</td><td>*Év*, *FlightDate*, *UniqueCarrier*, *szolgáltatónként*, *FlightNum*, *OriginAirportID*, *Származási*, *OriginCityName*, *OriginState*, *DestAirportID*, *cél*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*,  *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*,  *LateAircraftDelay* (a többi mező törlése)</td></tr>
    </table>
3. Kattintson a **letöltése**.
4. Bontsa ki a fájlt a **C:\Tutorials\FlightDelay\2013Data** mappa. Minden fájlt egy CSV-fájl, és körülbelül 60 GB-nál.
5. Nevezze át a fájlt a benne található adatok hónap nevét. Például a január adatokat tartalmazó fájl neve volna *January.csv*.
6. Ismételje meg a 2. és 5 fájlok letöltéséhez az egyes 2013 12 hónapig. Szüksége lesz legalább egy fájlt az oktatóanyag futtatásához.

**A felhőszolgáltató közötti átviteléhez késleltetés adatokat feltölteni az Azure Blob-tároló**

1. Készítse elő a paramétereket:

    <table border="1">
    <tr><th>Változó neve</th><th>Megjegyzések</th></tr>
    <tr><td>$storageAccountName</td><td>Az Azure Storage-fiók hol szeretne feltölteni az adatokat.</td></tr>
    <tr><td>$blobContainerName</td><td>A Blob tároló, ahol szeretne feltölteni az adatokat.</td></tr>
    </table>
2. Nyissa meg az Azure PowerShell ISE.
3. A parancsfájl ablaktáblára illessze be a következő parancsfájlt:

    ```powershell
    [CmdletBinding()]
    Param(

        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
        [String]$storageAccountName,

        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
        [String]$blobContainerName
    )

    #Region - Variables
    $localFolder = "C:\Tutorials\FlightDelay\2013Data"  # The source folder
    $destFolder = "tutorials/flightdelay/2013data"     #The blob name prefix for the files to be uploaded
    #EndRegion

    #Region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #EndRegion

    #Region - Validate user input
    Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
    # Validate the Storage account
    if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
    {
        Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
        exit
    }
    else{
        $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
    }

    # Validate the container
    $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
    {
        Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
        Exit
    }
    #EngRegion

    #Region - Copy the file from local workstation to Azure Blob storage
    if (test-path -Path $localFolder)
    {
        foreach ($item in Get-ChildItem -Path $localFolder){
            $fileName = "$localFolder\$item"
            $blobName = "$destFolder/$item"

            Write-Host "Copying $fileName to $blobName" -ForegroundColor Green

            Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
        }
    }
    else
    {
        Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
    }

    # List the uploaded files on HDInsight
    Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
    #EndRegion
    ```
4. A szkriptek futtatásához nyomja le az **F5** billentyűt.

Ha a fájlok feltöltése más módszert használja, ellenőrizze, hogy a fájl elérési útja oktatóanyagok/flightdelay/adatokat. A fájlokhoz való hozzáférést szintaxisa a következő:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelay/data

Az elérési út oktatóanyagok/flightdelay/adata akkor jön létre, amikor a fájlok feltöltött virtuális mappa. Győződjön meg arról, hogy vannak-e egy minden hónapban a 12 fájlt.

> [!NOTE]
> A Hive-lekérdezések kiolvasni az új helyet kell frissíteni.
>
> A tároló engedéllyel nyilvános vagy kötést létrehozni a tárfiókot a HDInsight-fürt vagy be kell állítania. Ellenkező esetben a Hive lekérdezés-karakterlánc csak akkor érhessék el az adatfájlokat.

- - -

## <a id="appendix-b"></a>B függelék – hozzon létre, és töltse fel a HiveQL-parancsfájlt
Az Azure PowerShell használatával több HiveQL utasítást egy olyan időpontra, vagy egy parancsfájl fájlba a HiveQL utasítás csomag. Ez a szakasz bemutatja, hogyan hozhat létre a HiveQL-parancsfájlt, és töltse fel a parancsfájl az Azure Blob storage Azure PowerShell használatával. Hive igényel a HiveQL parancsfájlok Azure Blob Storage tárolóban tárolni.

A HiveQL-parancsfájlt kell elvégezni a következőket:

1. **Dobja el a delays_raw táblát**, abban az esetben, ha a tábla már létezik.
2. **A delays_raw külső Hive tábla létrehozásához** a felhőszolgáltató közötti átviteléhez késleltetés fájljai a Blob storage helyre mutat. Ez a lekérdezés határozza meg, hogy mezők határolja ",", és hogy sorok "\n" vannak-e állítva. Ez problémát jelent, amikor a mezőértékek tartalmazhat vesszőket, mert struktúra nem különbözteti meg, amely a mezőhatároló vesszővel és a mezőérték része egy (amely a helyzet a mezőjének értékét származási\_VÁROS\_nevét, és a cél\_ VÁROS\_neve). A lekérdezés orvoslása érdekében hoz létre a TEMP oszlopok helytelenül osztott oszlopok adatok tárolásához.
3. **A késést table DROP**, abban az esetben, ha a tábla már létezik.
4. **A késést tábla létrehozása**. Akkor célszerű távolítja el az adatokat további feldolgozás előtt. Ez a lekérdezés táblát hoz létre új *késések*, a delays_raw táblából. Vegye figyelembe, hogy a rendszer nem másolja a átmeneti oszlopokban (ahogy korábban említettük), és hogy a **substring** függvény segítségével távolítsa el az idézőjelek közé az adatokból.
5. **A késleltetés átlagos időjárása és a csoportok az eredmények számítási város név szerint.** Akkor lesz is az eredményeket a Blob storage. Vegye figyelembe, hogy a lekérdezés aposztrófot eltávolítja az adatokat, és amelyeket kihagy forrása értéke **weather_delay** null értékű. Erre akkor szükség, mert a Sqoop, az oktatóanyag későbbi részében használt nem kezeli az ezeket az értékeket szabályosan alapértelmezés szerint.

A HiveQL parancsok teljes listáját lásd: [Hive adatdefiníciós nyelv][hadoop-hiveql]. Minden HiveQL parancs pontosvesszővel kell állítanunk.

**A HiveQL parancsfájl létrehozása**

1. Készítse elő a paramétereket:

    <table border="1">
    <tr><th>Változó neve</th><th>Megjegyzések</th></tr>
    <tr><td>$storageAccountName</td><td>Az Azure Storage-fiók hol szeretne feltölteni a HiveQL-parancsfájlt.</td></tr>
    <tr><td>$blobContainerName</td><td>A Blob tároló, ahol szeretne feltölteni a HiveQL-parancsfájlt.</td></tr>
    </table>
    
2. Nyissa meg az Azure PowerShell ISE.  

3. Másolja és illessze be az alábbi parancsfájl a parancsfájl panelen:  

    ```powershell
    [CmdletBinding()]
    Param(

        # Azure Blob storage variables
        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
        [String]$storageAccountName,

        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
        [String]$blobContainerName
    )

    #region - Define variables
    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    # The HiveQL script file is exported as this file before it's uploaded to Blob storage
    $hqlLocalFileName = "e:\tutorials\flightdelay\flightdelays.hql"

    # The HiveQL script file will be uploaded to Blob storage as this blob name
    $hqlBlobName = "tutorials/flightdelay/flightdelays.hql"

    # These two constants are used by the HiveQL script file
    #$srcDataFolder = "tutorials/flightdelay/data"
    $dstDataFolder = "/tutorials/flightdelay/output"
    #endregion

    #Region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #EndRegion

    #Region - Validate user input
    Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
    # Validate the Storage account
    if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
    {
        Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
        exit
    }
    else{
        $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
    }

    # Validate the container
    $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
    {
        Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
        Exit
    }
    #EngRegion

    #region - Validate the file and file path

    # Check if a file with the same file name already exists on the workstation
    Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
    if (test-path $hqlLocalFileName){

        $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'

        if ($isDelete.ToLower() -ne "y")
        {
            Exit
        }
    }

    # Create the folder if it doesn't exist
    $folder = split-path $hqlLocalFileName
    if (-not (test-path $folder))
    {
        Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green

        new-item $folder -ItemType directory
    }
    #end region

    #region - Write the Hive script into a local file
    Write-Host "`nWriting the Hive script into a file on your workstation ..." `
                -ForegroundColor Green

    $hqlDropDelaysRaw = "DROP TABLE delays_raw;"

    $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
            "YEAR string, " +
            "FL_DATE string, " +
            "UNIQUE_CARRIER string, " +
            "CARRIER string, " +
            "FL_NUM string, " +
            "ORIGIN_AIRPORT_ID string, " +
            "ORIGIN string, " +
            "ORIGIN_CITY_NAME string, " +
            "ORIGIN_CITY_NAME_TEMP string, " +
            "ORIGIN_STATE_ABR string, " +
            "DEST_AIRPORT_ID string, " +
            "DEST string, " +
            "DEST_CITY_NAME string, " +
            "DEST_CITY_NAME_TEMP string, " +
            "DEST_STATE_ABR string, " +
            "DEP_DELAY_NEW float, " +
            "ARR_DELAY_NEW float, " +
            "CARRIER_DELAY float, " +
            "WEATHER_DELAY float, " +
            "NAS_DELAY float, " +
            "SECURITY_DELAY float, " +
            "LATE_AIRCRAFT_DELAY float) " +
        "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
        "LINES TERMINATED BY '\n' " +
        "STORED AS TEXTFILE " +
        "LOCATION 'wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';"

    $hqlDropDelays = "DROP TABLE delays;"

    $hqlCreateDelays = "CREATE TABLE delays AS " +
        "SELECT YEAR AS year, " +
            "FL_DATE AS flight_date, " +
            "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
            "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
            "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
            "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
            "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
            "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
            "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
            "DEST_AIRPORT_ID AS dest_airport_id, " +
            "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
            "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
            "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
            "DEP_DELAY_NEW AS dep_delay_new, " +
            "ARR_DELAY_NEW AS arr_delay_new, " +
            "CARRIER_DELAY AS carrier_delay, " +
            "WEATHER_DELAY AS weather_delay, " +
            "NAS_DELAY AS nas_delay, " +
            "SECURITY_DELAY AS security_delay, " +
            "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
        "FROM delays_raw;"

    $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
        "SELECT regexp_replace(origin_city_name, '''', ''), " +
            "avg(weather_delay) " +
        "FROM delays " +
        "WHERE weather_delay IS NOT NULL " +
        "GROUP BY origin_city_name;"

    $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal

    $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force
    #endregion

    #region - Upload the Hive script to the default Blob container
    Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green

    # Create a storage context object
    $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    # Upload the file from local workstation to Blob storage
    Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext
    #endregion

    Write-host "`nEnd of the PowerShell script" -ForegroundColor Green
    ```

    Az alábbiakban a változókat a parancsfájl szerepel:

   * **$hqlLocalFileName** -a parancsfájl menti a HiveQL-parancsfájlt helyileg blobtárolóba való feltöltés előtt. Ez az, hogy a fájl nevét. Az alapértelmezett érték <u>C:\tutorials\flightdelay\flightdelays.hql</u>.
   * **$hqlBlobName** -HiveQL parancsfájl fájl blob neve szerepel az Azure Blob Storage tárolóban. Az alapértelmezett érték: tutorials/flightdelay/flightdelays.hql. A fájlt közvetlenül Azure Blob Storage tárolót fog szerepelni, mert nincs olyan "/" a blob nevének elején. Ha a fájl elérhető a Blob-tároló, szüksége lesz hozzáadása a "/" a fájl neve elején.
   * **$srcDataFolder** és **$dstDataFolder** -= "oktatóanyagok/flightdelay/adatok" = "oktatóanyagok/flightdelay/kimeneti"

- - -
## <a id="appendix-c"></a>C függelék – az Azure SQL-adatbázis előkészítése a Sqoop feladat kimenetére
**Az SQL-adatbázis előkészítése (egyesítés ezzel a a Sqoop parancsfájl)**

1. Készítse elő a paramétereket:

    <table border="1">
    <tr><th>Változó neve</th><th>Megjegyzések</th></tr>
    <tr><td>$sqlDatabaseServerName</td><td>Az Azure SQL adatbázis-kiszolgáló neve. Adja meg, hozzon létre egy új kiszolgálót semmi sem.</td></tr>
    <tr><td>$sqlDatabaseUsername</td><td>A bejelentkezési név az Azure SQL-kiszolgáló. Ha $sqlDatabaseServerName egy meglévő kiszolgáló, a bejelentkezési és a bejelentkezés jelszó hitelesítésére szolgálnak a kiszolgálóval. Máskülönben hozzon létre egy új kiszolgálót használhatók.</td></tr>
    <tr><td>$sqlDatabasePassword</td><td>Az az Azure SQL adatbázis-kiszolgáló bejelentkezési jelszót.</td></tr>
    <tr><td>$sqlDatabaseLocation</td><td>Ezt az értéket csak akkor, ha hoz létre egy új Azure-adatbázis-kiszolgálót használja.</td></tr>
    <tr><td>$sqlDatabaseName</td><td>Az SQL-adatbázis a AvgDelays tábla a Sqoop feladat létrehozásához használt. Hagyja üresen HDISqoop nevű adatbázist fog létrehozni. A táblanév Sqoop feladat kimenetének AvgDelays. </td></tr>
    </table>
    
2. Nyissa meg az Azure PowerShell ISE.

3. Másolja és illessze be az alábbi parancsfájl a parancsfájl panelen:  

    ```powershell
    [CmdletBinding()]
    Param(

        # Azure Resource group variables
        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure resource group name. It will be created if it doesn't exist.")]
        [String]$resourceGroupName,

        # SQL database server variables
        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure SQL Database Server Name. It will be created if it doesn't exist.")]
        [String]$sqlDatabaseServer,

        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure SQL Database admin user.")]
        [String]$sqlDatabaseLogin,

        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure SQL Database admin user password.")]
        [String]$sqlDatabasePassword,

        [Parameter(Mandatory=$True,
                HelpMessage="Enter the region to create the Database in.")]
        [String]$sqlDatabaseLocation,   #For example, West US.

        # SQL database variables
        [Parameter(Mandatory=$True,
                HelpMessage="Enter the database name. It will be created if it doesn't exist.")]
        [String]$sqlDatabaseName # specify the database name if you have one created. Otherwise use "" to have the script create one for you.
    )

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Constants and variables

    # IP address REST service used for retrieving external IP address and creating firewall rules
    [String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
    [String]$fireWallRuleName = "FlightDelay"

    # SQL database variables
    [String]$sqlDatabaseMaxSizeGB = 10

    #SQL query string for creating AvgDelays table
    [String]$sqlDatabaseTableName = "AvgDelays"
    [String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                [origin_city_name] [nvarchar](50) NOT NULL,
                [weather_delay] float,
            CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
            (
                [origin_city_name] ASC
            )
            )"
    #endregion

    #Region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #EndRegion

    #region - Create and validate Azure resouce group
    try{
        Get-AzureRmResourceGroup -Name $resourceGroupName
    }
    catch{
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $sqlDatabaseLocation
    }

    #EndRegion

    #region - Create and validate Azure SQL database server
    try{
        Get-AzureRmSqlServer -ServerName $sqlDatabaseServer -ResourceGroupName $resourceGroupName}
    catch{
        Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

        $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
        $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

        $sqlDatabaseServer = (New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -SqlAdministratorCredentials $credential -Location $sqlDatabaseLocation).ServerName
        Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan

        Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
        $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
        New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress

        #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. Note that this allows Azure traffic from any Azure subscription to access the server.
        New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-Azureservices" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
    }

    #endregion

    #region - Create and validate Azure SQL database

    try {
        Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
    }
    catch {
        Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
        New-AzureRMSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName -Edition "Standard" -RequestedServiceObjectiveName "S1"
    }

    #endregion

    #region -  Execute an SQL command to create the AvgDelays table

    Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = $sqlCreateAvgDelaysTable
    $cmd.executenonquery()

    $conn.close()

    Write-host "`nEnd of the PowerShell script" -ForegroundColor Green
    ```

   > [!NOTE]
   > A parancsfájl egy representational állapot transfer (REST) szolgáltatást használja, http://bot.whatismyipaddress.com, a külső IP-cím beolvasása. Az IP-címet az SQL database-kiszolgálóhoz egy tűzfalszabályt létrehozására szolgál.

    Az alábbiakban néhány változók a parancsfájl szerepel:

   * **$ipAddressRestService** -az alapértelmezett érték http://bot.whatismyipaddress.com. Egy nyilvános IP-címet a külső IP-cím beolvasása a REST-szolgáltatást is. Más szolgáltatások is használhatja, ha azt szeretné. A külső IP-cím, a szolgáltatás segítségével használandó hozzon létre egy tűzfalszabályt, az Azure SQL adatbázis-kiszolgáló számára, hogy Ön hozzáférhessen az adatbázishoz a munkaállomáson (a Windows PowerShell-parancsfájl használatával).
   * **$fireWallRuleName** – az Azure SQL-kiszolgáló a tűzfalszabály neve. Az alapértelmezett név az <u>FlightDelay</u>. Ha azt szeretné, átnevezheti.
   * **$sqlDatabaseMaxSizeGB** -Ez az érték csak egy új Azure SQL adatbázis-kiszolgáló létrehozásakor használható. Az alapértelmezett érték: 10 GB-os. 10 GB-os is elegendő ehhez az oktatóanyaghoz.
   * **$sqlDatabaseName** -Ez az érték csak egy új Azure SQL-adatbázis létrehozásakor használható. Az alapértelmezett érték: HDISqoop. Ha átnevezi, ennek megfelelően kell frissíteni a Sqoop Windows PowerShell-parancsfájlt.
4. A szkriptek futtatásához nyomja le az **F5** billentyűt.
5. Ellenőrizze a parancsfájl kimenete. Győződjön meg arról, hogy a parancsfájl sikeresen lefutott.

## <a id="nextsteps"></a> Következő lépések
Most már megismerte az Azure Blob storage-fájl feltöltése, az adatokat az Azure Blob storage használatával Hive tábla adatokkal való, hogyan futtathat Hive-lekérdezéseket, és Sqoop segítségével exportál adatokat HDFS az Azure SQL-adatbázis. További tudnivalókért tekintse meg a következő cikkeket:

* [Első lépései a hdinsight eszközzel][hdinsight-get-started]
* [A Hive használata a HDInsightban][hdinsight-use-hive]
* [Oozie használata a hdinsight eszközzel][hdinsight-use-oozie]
* [Use Sqoop with HDInsight][hdinsight-use-sqoop]
* [A Pig használata a HDInsightban][hdinsight-use-pig]
* [Java-MapReduce programok fejlesztése a HDInsight][hdinsight-develop-mapreduce]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png

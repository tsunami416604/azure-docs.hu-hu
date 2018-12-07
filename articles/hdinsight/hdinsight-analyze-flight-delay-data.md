---
title: Az Apache hadooppal a HDInsight - Azure repülőjáratok késési adatainak elemzése
description: Ismerje meg, hogyan hozzon létre egy HDInsight-fürtöt, egy Hive-feladat futtatása, a Sqoop-feladat futtatása és a fürt törlése egy Windows PowerShell-parancsfájl használatával.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 2e24a138220f350e56b30406f65bb869dd523bad
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015874"
---
# <a name="analyze-flight-delay-data-by-using-apache-hive-in-hdinsight"></a>Repülőjáratok késési adatainak elemzése a HDInsight az Apache Hive használatával
[Az Apache Hive](https://hive.apache.org/) futó módszert biztosít az [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) feladatok egy SQL-szerű programozási nyelv révén nevű *[HiveQL] [ hadoop-hiveql]*, felé összefoglalójához, lekérdezéséhez és nagy mennyiségű adat elemzéséhez is alkalmazható.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések egy Windows-alapú HDInsight-fürt szükséges. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement). A Linux-alapú fürtökkel működik lépéseiért lásd: [repülőjáratok késési adatainak elemzése a HDInsight (Linux) az Apache Hive használatával](hdinsight-analyze-flight-delay-data-linux.md).

Az egyik fő vívmánya, az Azure HDInsight az adatok tárolási és számítási szétválasztását. HDInsight adatokat tároló Azure Blob storage használ. Egy tipikus feladatot három részből áll:

1. **Data Store az Azure Blob storage-ban.**  Ha például időjárási adatai, érzékelőadatok, webes naplókat, és ebben az esetben repülőjáratok késési adatainak Azure Blob storage-bA lesznek mentve.
2. **Feladatok futtatása.** Amikor az adatok feldolgozására, futtatja egy Windows PowerShell-parancsprogram (vagy egy ügyfélalkalmazás) egy HDInsight-fürt létrehozásához, feladatok és törölheti a fürtöt. A feladatok az Azure Blob storage-mentse a kimeneti adatokat. A kimeneti adatokat a fürt törlése után is megmarad. Ezzel a módszerrel kell fizetnie csak amit Ön által igénybe vett.
3. **A kimenet lekérése az Azure Blob storage-ból**, vagy ez az oktatóanyag exportálja az adatokat egy Azure SQL Database-adatbázishoz.

A következő ábra szemlélteti a forgatókönyvet, és ez az oktatóanyag a struktúrája:

![HDI. FlightDelays.flow][img-hdi-flightdelays-flow]

Vegye figyelembe, hogy az ábrán szereplő számok megfelelnek-e a szakasz címét. **M** a fő folyamatát jelenti. **A** jelenti a tartalmat a függelékben.

A fő részét az oktatóanyag bemutatja, hogyan egy Windows PowerShell-szkript használatával a következő feladatokat:

* Hozzon létre egy HDInsight-fürtön.
* Egy Hive-feladat repülőtereken átlagos késések kiszámításához a fürtön futnak. Repülőjáratok késési adatainak egy Azure Blob storage-fiók van tárolva.
* Egy Azure SQL Database-adatbázishoz a Hive-feladat kimeneti exportálása a Sqoop feladat futtatása.
* A HDInsight-fürt törlése.

A függelékben találja az utasításokat repülőjáratok késési adatainak feltöltése, a Hive a lekérdezési karakterlánc létrehozása és feltöltése és az Azure SQL database előkészítése a Sqoop feladatokhoz.

> [!NOTE]
> A jelen dokumentumban leírt lépések Windows-alapú HDInsight-fürtökre jellemzőek. A Linux-alapú fürtökkel működik lépéseiért lásd: [Apache Hive a HDInsight (Linux) használatával repülőjáratok késési adatainak elemzése](hdinsight-analyze-flight-delay-data-linux.md)

### <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Munkaállomás Azure PowerShell-lel**.

    > [!IMPORTANT]
    > A HDInsight-erőforrások Azure Service Managerrel történő kezelésének Azure PowerShell-támogatása **elavult**, így 2017. január 1-től megszűnt. A jelen dokumentumban leírt lépések az új HDInsight-parancsmagokat használják, amelyek az Azure Resource Managerrel működnek.
    >
    > Kérjük, kövesse az alábbi cikkben leírt lépéseket az Azure PowerShell legújabb verziójának telepítéséhez: [Install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Az Azure PowerShell letöltése és konfigurálása). Ha vannak olyan parancsprogramjai, amelyeket módosítani kell az új, az Azure Resource Managerrel működő parancsmagok használatához, tekintse meg az alábbi cikket: [Migrating to Azure Resource Manager-based development tools for HDInsight clusters](hdinsight-hadoop-development-using-azure-resource-manager.md) (Az Azure Resource Manager-alapú fejlesztési eszközökre való áttérés HDInsight-fürtök esetén).

**Ebben az oktatóanyagban használt fájlok**

Ebben az oktatóanyagban légitársaság repülési adatokat időben teljesítményének [kutatási és innovatív technológia felügyeleti, szállítási statisztikai hivatal vagy RITA][rita-website].
Az adatok másolatát egy Azure Blob storage-tároló nyilvános Blob hozzáférési engedéllyel rendelkező lett feltöltve.
A PowerShell-példaszkript egy részét az adatokat másolja a nyilvános blobtárolóban az alapértelmezett blob tároló, a fürt. A HiveQL-parancsfájlt is másolja ugyanazt a Blob-tárolóba.
Ha szeretné megtanulni, hogyan get feltölti az adatokat a saját tárfiókját, és a HiveQL-parancsfájlt létrehozni vagy feltölteni, olvassa el [függelék](#appendix-a) és [B függelék](#appendix-b).

Az alábbi táblázat a jelen oktatóanyagban használt fájlokat listázza:

<table border="1">
<tr><th>Fájlok</th><th>Leírás</th></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>A HiveQL-parancsfájlt használja a Hive-feladatokban. Ez a szkript egy Azure Blob storage-fiókot a nyilvános hozzáférést lett feltöltve. <a href="#appendix-b">"B" függelék</a> utasítások előkészítése és a fájl feltöltése a saját Azure Blob storage-fiókba.</td></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Bemeneti adatokat a Hive-feladatokban. Az adatok egy Azure Blob storage-fiókot a nyilvános hozzáférést lett feltöltve. <a href="#appendix-a">A függelék</a> utasításokat az adatok beolvasásakor, és az adatok feltöltése a saját Azure Blob storage-fiók rendelkezik.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>A Hive-feladat kimeneti elérési útja. Az alapértelmezett tároló kimeneti adatok tárolására szolgál.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>A Hive feladat állapota mappa az alapértelmezett tároló.</td></tr>
</table>

## <a name="create-cluster-and-run-hivesqoop-jobs"></a>Fürt létrehozása és a Sqoop és Hive-feladatok futtatása
Hadoop MapReduce kötegelt feldolgozás. A leginkább költséghatékony módon futtathat Hive-feladatokat, hogy hozzon létre egy fürtöt, a feladat, és törli a feladatot, a feladat befejezése után. A következő szkriptet az egész folyamat magában foglalja.
Egy HDInsight-fürtöt hoz létre, és Hive-feladatok futtatása a további információkért lásd: [Apache Hadoop-fürtök létrehozása a HDInsight] [ hdinsight-provision] és [Apache Hive használata a HDInsight] [hdinsight-use-hive].

**A Hive-lekérdezések futtatásához az Azure PowerShell**

1. Egy Azure SQL database és a Sqoop feladat kimenetének a táblázat létrehozása szakaszban foglaltak szerint [C függelék](#appendix-c).
2. Nyissa meg a Windows PowerShell ISE-ben, és futtassa a következő szkriptet:

    ```powershell
    $subscriptionID = "<Azure Subscription ID>"
    $nameToken = "<Enter an Alias>"

    ###########################################
    # You must configure the following variables
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
        Connect-AzureRmAccount
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
3. Az SQL database csatlakozhat, és tekintse meg a AvgDelays tábla város szerint átlagos járatok késésének:

    ![HDI. FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]

- - -

## <a id="appendix-a"></a>A függelék – feltöltés repülőjáratok késési adatainak az Azure Blob storage
Az adatfájl feltöltése és a [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) parancsfájlok (lásd: [B függelék](#appendix-b)) néhány tervezést igényel. A cél pedig az adatfájlok és a HiveQL tárolásához, egy HDInsight-fürtöt hoz létre, és a Hive-feladat futtatása előtt. Erre két lehetősége van:

* **A HDInsight-fürt által használandó meg alapértelmezett fájlrendszerként azonos Azure Storage-fiókot használni.** A HDInsight-fürt lesz a Tárfiók hozzáférési kulcsát, mert nem kell további módosításokat.
* **Használja a HDInsight-fürt alapértelmezett fájlrendszerének egy másik Azure Storage-fiókjában.** Ez a helyzet, ha módosítania kell a Windows PowerShell parancsfájl található létrehozásra vonatkozó részét [létre HDInsight-fürt és az Apache Hive/Sqoop futtathatja feladatait](#runjob) a Storage-fiók kiegészítő tárfiókként. Útmutatásért lásd: [Apache Hadoop-fürtök létrehozása a HDInsight][hdinsight-provision]. A HDInsight-fürt majd tudja, hogy a tárfiók hozzáférési kulcsára.

> [!NOTE]
> A Blob storage az adatok fájl elérési útja rögzített bekódolni a HiveQL-parancsfájlt. Ennek megfelelően kell frissíteni.

**A flight adatok letöltése**

1. Nyissa meg a [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website] (Kutatási és Innovációs Műszaki Felügyelőség, Közlekedési Statisztikai Hivatal) oldalt.
2. Az oldalon válassza ki a következő értékeket:

    <table border="1">
    <tr><th>Name (Név)</th><th>Érték</th></tr>
    <tr><td>Filter Year (Szűrési év)</td><td>2013 </td></tr>
    <tr><td>Filter Period (Szűrési időszak)</td><td>January</td></tr>
    <tr><td>Mezők</td><td>*Év*, *FlightDate*, *UniqueCarrier*, *szolgáltatója*, *FlightNum*, *OriginAirportID*, *Forrás*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*,  *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*,  *LateAircraftDelay* (a többi mező törlése)</td></tr>
    </table>

3. Kattintson a **Letöltés** gombra.
4. Bontsa ki a fájlt a **C:\Tutorials\FlightDelay\2013Data** mappát. Minden fájlt egy CSV-fájl, és körülbelül 60 GB-nál.
5. Nevezze át a fájlt a benne található adatokat a hónap nevét. Ha például a január adatokat tartalmazó fájl neve *January.csv*.
6. Ismételje meg a 2. és 5-fájl letöltésére minden, a 12 hónapos a 2013-ban. Szüksége lesz legalább egy fájlra az oktatóanyag futtatásához.

**Repülőjáratok késési adatainak feltöltése az Azure Blob storage**

1. Készítse elő a paramétereket:

    <table border="1">
    <tr><th>Változó neve</th><th>Megjegyzések</th></tr>
    <tr><td>$storageAccountName</td><td>Ahol szeretné feltölteni az adatokat az Azure Storage-fiókhoz.</td></tr>
    <tr><td>$blobContainerName</td><td>Ahol az adatokat feltölteni kívánt Blob-tárolóban.</td></tr>
    </table>
    
2. Nyissa meg az Azure PowerShell ISE-ben.
3. A parancsfájl panelen illessze be az alábbi parancsfájlt:

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
    catch{Connect-AzureRmAccount}
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

Ha a fájlok feltöltése más módszert használni kívánja, ellenőrizze, hogy a fájl elérési útja oktatóanyagok/flightdelay/data. A fájlok szintaxisa:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelay/data

Az oktatóanyagok/flightdelay/adatok elérési útja az a virtuális mappa hozott létre a fájlok feltöltésekor. Győződjön meg arról, hogy nincsenek-e egy minden hónapban a 12 fájlokat.

> [!NOTE]
> Frissítenie kell a Hive-lekérdezést az új hely olvasni.
>
> Vagy konfigurálnia kell a tároló nyilvános vagy a HDInsight-fürt kötést létrehozni a tárfiók hozzáférési engedélyt. Ellenkező esetben a Hive lekérdezés-karakterlánc nem érhetik el az adatfájlokat.

- - -

## <a id="appendix-b"></a>B függelék – létrehozása és feltöltése a HiveQL-parancsfájlt
Az Azure PowerShell használatával futtathatja a több [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) utasításokat egy időben, vagy csomagot egy parancsfájlt a HiveQL-utasítást. Ez a szakasz bemutatja, hogyan hozhat létre egy HiveQL-parancsfájlt, és töltse fel a parancsfájl az Azure Blob storage Azure PowerShell használatával. Hive igényel a HiveQL parancsfájlok az Azure Blob storage-ban kell tárolni.

A HiveQL-parancsfájlt hajtsa végre a következő lesz:

1. **Dobja el a delays_raw táblát**, abban az esetben, ha a tábla már létezik.
2. **Hozzon létre a külső Hive-tábla delays_raw** a flight késleltetés fájljai a Blob storage helyre mutat. Ez a lekérdezés Megadja, hogy a mezők vannak-e elválasztva ",", és hogy sorok "\n" megszűnik. Ez problémát jelent, ha a mezőértékek tartalmazhat vesszőt, mert a Hive, amely egy mező elválasztó vessző és a egy megjegyzés, amely része egy mező értéke nem lehet megkülönböztetni (amennyiben a forrás mezőértékek\_VÁROS\_nevét, és a cél\_ VÁROS\_neve). Ennek a lekérdezést hoz létre ideiglenes oszlopokat adattípusokat tárolnak, amelyek nem megfelelően van felosztva oszlopok.
3. **Dobja el az késleltetések táblát**, abban az esetben, ha a tábla már létezik.
4. **Hozzon létre az késleltetések tábla**. Hasznos lehet törölni az adatokat további feldolgozás előtt. Ez a lekérdezés egy új táblát hoz létre *késések*, a delays_raw táblából. Vegye figyelembe, hogy nem másolódnak át az ideiglenes oszlopokat (ahogy azt korábban említettük), és hogy a **substring** függvény segítségével távolítsa el az idézőjelek között az adatok közül.
5. **Az átlagos időjárás késleltetés és a csoportok az eredmények számítási a város nevét.** Ez lesz is az eredményeket a Blob storage. Fontos megjegyezni, hogy a lekérdezés aposztrófot eltávolítja az adatokat, és ki fogja zárni a sorok, értéke **weather_delay** null értékű. Erre akkor szükség, mert a Sqoop, később az oktatóanyagban használt nem kezeli az ezeket az értékeket szabályosan alapértelmezés szerint.

A HiveQL parancsok teljes listájáért lásd: [Apache Hive-adatdefiníciós nyelv][hadoop-hiveql]. Minden egyes [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) parancs pontosvesszővel kell végződnie.

**A HiveQL parancsfájl létrehozása**

1. Készítse elő a paramétereket:

    <table border="1">
    <tr><th>Változó neve</th><th>Megjegyzések</th></tr>
    <tr><td>$storageAccountName</td><td>Az Azure Storage-fiók ahol szeretné feltölteni a HiveQL-parancsfájlt.</td></tr>
    <tr><td>$blobContainerName</td><td>Ha a HiveQL-parancsfájlt a feltölteni kívánt Blob-tárolóban.</td></tr>
    </table>
    
2. Nyissa meg az Azure PowerShell ISE-ben.  

3. Másolja és illessze be a parancsfájl panelen a következő parancsfájlt:  

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
    catch{Connect-AzureRmAccount}
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

        $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwrite it? (Y/N)'

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

    Íme a szkript használt változók:

   * **$hqlLocalFileName** – a parancsfájl menti a HiveQL-parancsfájlt helyileg előtt blobtárolóba való feltöltését. Ez a fájl nevét. Az alapértelmezett érték <u>C:\tutorials\flightdelay\flightdelays.hql</u>.
   * **$hqlBlobName** – Ez az a HiveQL parancsfájl blob neve az Azure Blob storage-ban használt. Az alapértelmezett érték: tutorials/flightdelay/flightdelays.hql. A fájl közvetlenül az Azure Blob storage lesz írva, mert nem szerepel a "/", a blob nevének elején. Ha szeretne hozzáférni a fájlhoz a Blob storage-ból, szüksége lesz a adja hozzá a "/", a fájl neve elején.
   * **$srcDataFolder** és **$dstDataFolder** -= "oktatóanyagokban/flightdelay/adatok" = "oktatóanyagokban/flightdelay/output"

- - -
## <a id="appendix-c"></a>C függelék – Azure SQL-adatbázis előkészítése a Sqoop feladat kimenete
**Az SQL-adatbázis előkészítése (egyesítés ezzel a a Sqoop parancsfájl)**

1. Készítse elő a paramétereket:

    <table border="1">
    <tr><th>Változó neve</th><th>Megjegyzések</th></tr>
    <tr><td>$sqlDatabaseServerName</td><td>Az Azure SQL adatbázis-kiszolgáló neve. Adja meg, hozzon létre egy új kiszolgálót semmi sem.</td></tr>
    <tr><td>$sqlDatabaseUsername</td><td>A bejelentkezési név az Azure SQL-kiszolgálóhoz. Ha $sqlDatabaseServerName egy meglévő kiszolgálót, a bejelentkezési nevét és bejelentkezési jelszó segítségével hitelesíti a kiszolgálót. Ellenkező esetben ezek szolgálnak hozzon létre egy új kiszolgálót.</td></tr>
    <tr><td>$sqlDatabasePassword</td><td>Az Azure SQL adatbázis-kiszolgáló bejelentkezési jelszava.</td></tr>
    <tr><td>$sqlDatabaseLocation</td><td>Ezt az értéket csak egy új Azure database-kiszolgáló létrehozásakor használja.</td></tr>
    <tr><td>$sqlDatabaseName</td><td>Az SQL database, a Sqoop feladat AvgDelays-tábla létrehozásához használt. Hagyja üresen HDISqoop nevű adatbázist hoz létre. A tábla neve, a Sqoop feladat kimenetének a AvgDelays. </td></tr>
    </table>
    
2. Nyissa meg az Azure PowerShell ISE-ben.

3. Másolja és illessze be a parancsfájl panelen a következő parancsfájlt:  

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
    catch{Connect-AzureRmAccount}
    #EndRegion

    #region - Create and validate Azure resource group
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
   > A szkript egy representational állapot transfer (REST) szolgáltatás http://bot.whatismyipaddress.com, a külső IP-cím lekéréséhez. Az IP-cím szolgál egy tűzfalszabályt az SQL database-kiszolgálóhoz létrehozásához.

    Íme néhány a szkriptben használt változók:

   * **$ipAddressRestService** – az alapértelmezett érték http://bot.whatismyipaddress.com. Egy nyilvános IP-címet REST-szolgáltatás esetében a külső IP-cím beolvasása. Más szolgáltatások is használhatja, ha azt szeretné. A szolgáltatás segítségével a külső IP-cím használandó hozzon létre egy tűzfalszabályt az Azure SQL database-kiszolgáló úgy, hogy hozzáférhessen az adatbázishoz a munkaállomásáról (a Windows PowerShell-parancsfájl használatával).
   * **$fireWallRuleName** – az Azure SQL-kiszolgáló a tűzfalszabály neve. Alapértelmezés szerint ez <u>FlightDelay</u>. Ha azt szeretné, átnevezheti.
   * **$sqlDatabaseMaxSizeGB** – Ez az érték csak egy új Azure SQL database server létrehozásakor használható. Az alapértelmezett érték: 10 GB-ot. 10 GB-ot is elegendő ehhez az oktatóanyaghoz.
   * **$sqlDatabaseName** – Ez az érték csak egy új Azure SQL-adatbázis létrehozásakor használható. Az alapértelmezett érték: HDISqoop. Ha átnevezi, ennek megfelelően frissítse a Sqoop Windows PowerShell-parancsfájlt.
4. A szkriptek futtatásához nyomja le az **F5** billentyűt.
5. Ellenőrizze a parancsprogram kimenete. Győződjön meg arról, hogy a parancsprogram sikeresen lefutott.

## <a id="nextsteps"></a> Következő lépések
Most már megismerte, hogyan tölthet fel egy fájlt az Azure Blob storage-, hogyan lehet az Apache Hive tábla feltöltése adatokkal az adatok Azure Blob Storage használatával, hogyan futtathat Hive-lekérdezések és adatainak exportálása a Sqoop használata [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) az Azure SQL az adatbázis. További tudnivalókért tekintse meg a következő cikkeket:

* [HDInsight – első lépések][hdinsight-get-started]
* [Az Apache Hive használata a HDInsight][hdinsight-use-hive]
* [Az Apache Oozie használata a HDInsight][hdinsight-use-oozie]
* [A HDInsight Apache Sqoop használata][hdinsight-use-sqoop]
* [Az Apache Pig használata a HDInsight][hdinsight-use-pig]
* [Java MapReduce programok fejlesztése a HDInsight][hdinsight-develop-mapreduce]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

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

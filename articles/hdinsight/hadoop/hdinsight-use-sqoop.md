---
title: Az Apache Sqoop-feladatok futtatása az Azure HDInsight (Apache Hadoop)
description: Megtudhatja, hogyan használhatja az Azure Powershellt egy munkaállomásról futtatása Sqoop-importálás és exportálása egy Hadoop-fürt és a egy Azure SQL database között.
ms.reviewer: jasonh
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: 0a03bfe61da6a1de073401fca9c61f3212b9d0bb
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57440097"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Az Apache Sqoop használata a HDInsight Hadoop-keretrendszerrel
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Útmutató a HDInsight az Apache Sqoop használatával importálása és exportálása a HDInsight-fürt és az Azure SQL database vagy SQL Server-adatbázis között.

Bár az Apache Hadoop feldolgozásának strukturálatlan és félig strukturált adatok, naplók és a fájlokat, például kézenfekvő lehet a is lehet szükség, amely a relációs adatbázisokban tárolt strukturált adatok feldolgozása.

[Az Apache Sqoop] [ sqoop-user-guide-1.4.4] eszközt úgy tervezték, Hadoop-fürtök és a relációs adatbázisok közötti adatátvitelhez. Adatokat importálhat egy relációsadatbázis-kezelő rendszerének (RDBMS), például az SQL Server, MySQL és a Hadoop elosztott fájlrendszer (HDFS), az Oracle az adatok átalakítása a Hadoop MapReduce-vagy Apache Hive, és ezután exportálhatja az adatokat egy RDBMS be újra használhatja . Ebben az oktatóanyagban egy SQL Server-adatbázist használ a relációs adatbázis.

A HDInsight-fürtökön támogatott verziói a Sqoop, lásd: [a HDInsight által biztosított fürtverziók újdonságai?][hdinsight-versions]

## <a name="understand-the-scenario"></a>A forgatókönyv megismerése

HDInsight-fürt mintaadatokat tartalmaz. A következő két mintát használja:

* Az Apache Log4j naplófájlt, amely a következő helyen található */example/data/sample.log*. A fájl kinyert a következő naplók kapcsolódnak:
  
        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...
* Hive-tábla nevű *hivesampletable*, hivatkozik az adatfájl található */hive/warehouse/hivesampletable*. A tábla tartalmazza az egyes mobileszköz-adatok. 
  
  | Mező | Adattípus |
  | --- | --- |
  | ClientID |sztring |
  | querytime |sztring |
  | piaci |sztring |
  | deviceplatform |sztring |
  | devicemake |sztring |
  | devicemodel |sztring |
  | state |sztring |
  | Ország |sztring |
  | querydwelltime |double |
  | munkamenet-azonosító |bigint |
  | sessionpagevieworder |bigint |

Ebben az oktatóanyagban használhatja a két adatkészlet tesztelése Sqoop-importálás és exportálása.

## <a name="create-cluster-and-sql-database"></a>Fürt- és SQL-adatbázis létrehozása
Ez a szakasz bemutatja, hogyan hozhat létre egy fürtöt, egy SQL Database és az SQL database sémák futtatja az oktatóanyagot az Azure portal és Azure Resource Manager-sablon használatával. A sablonban található [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). A Resource Manager-sablon üzembe helyezéséhez a táblasémákat az SQL-adatbázis bacpac csomag meghívja.  A bacpac-csomag egy nyilvános blob-tárolóban található https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Ha szeretne egy személyes tárolót használja a bacpac-fájlt, használja a sablonban a következő értékeket:
   
```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

Ha a fürt és az SQL-adatbázis létrehozásához, tekintse meg az Azure PowerShell használatával szeretne [függelék](#appendix-a---a-powershell-sample).

> [!NOTE]  
> Importálja a sablon használatával, vagy csak támogatja az Azure Portalon a BACPAC-fájl importálása az Azure blob storage-ból.

**A resource management-sablonnal környezet konfigurálása**
1. Kattintson az alábbi képre kattintva megnyithatja a Resource Manager-sablont az Azure Portalon.         
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
2. Adja meg a következő tulajdonságokat:

    - **Előfizetés**: Adja meg az Azure-előfizetésében.
    - **Erőforráscsoport**: Hozzon létre egy új Azure-erőforráscsoportot, vagy válasszon ki egy meglévő erőforráscsoportot.  Egy erőforráscsoport van felügyeleti célból.  Ez az objektumok tárolója.
    - **Hely**: Válasszon régiót.
    - **ClusterName**: Adja meg a Hadoop-fürt nevét.
    - **Fürt bejelentkezési neve és jelszava**: Az alapértelmezett bejelentkezési név az admin.
    - **SSH-felhasználónév és -jelszó**.
    - **Az SQL server bejelentkezési nevét és jelszavát adatbázis**.
    - **_artifacts hely**: Használja az alapértelmezett értéket, hacsak nem szeretné használni a saját bacpac-fájlt egy másik helyen található.
    - **_artifacts hely Sas-jogkivonat**: Hagyja üresen.
    - **Bacpac-fájl neve**: Használja az alapértelmezett értéket, hacsak nem szeretné használni a saját bacpac-fájlba.
     
        A következő értékek közül, szoftveresen kötött a változók szakaszban:
        
        |Name (Név)|Érték|
        |----|-----|
        | Alapértelmezett tárfióknév | &lt;ClusterName > tárolásához |
        | Az Azure SQL database-kiszolgálónév | &lt;ClusterName>dbserver |
        | Az Azure SQL-adatbázis neve | &lt;ClusterName > db |
     
3. Válassza ki **elfogadom a feltételeket és a fenti feltételeket**.
4. Kattintson a **Purchase** (Vásárlás) gombra. Megjelenik egy új csempe nevű elküldés központi telepítési sablon üzembe helyezéshez. A fürt és az SQL-adatbázis létrehozása nagyjából 20 percet vesz igénybe.

Ha úgy dönt, hogy a meglévő Azure SQL database vagy a Microsoft SQL Server használata

* **Az Azure SQL database**: Konfigurálnia kell egy tűzfalszabályt az Azure SQL-kiszolgáló engedélyezze a hozzáférést a munkaállomáson. Egy Azure SQL-adatbázis létrehozása, és a tűzfal konfigurálásával kapcsolatos útmutatásért lásd: [első lépései az Azure SQL database-t használó][sqldatabase-get-started]. 
  
  > [!NOTE]  
  > Alapértelmezés szerint az Azure SQL database lehetővé teszi, hogy Azure-szolgáltatások, például Azure HDInsight érkező kapcsolatokat. Ez a tűzfal beállítás le van tiltva, ha szüksége engedélyezi azt az Azure Portalról. Utasítások az Azure SQL-adatbázis létrehozása és tűzfalszabályainak konfigurálása, lásd: [létrehozása és konfigurálása az SQL Database][sqldatabase-create-configure].

* **SQL Server**: Ha a HDInsight-fürt azonos virtuális hálózaton az Azure-ban az SQL Server, használhatja a lépéseket ebben a cikkben az adatok importálása és exportálása az SQL Server-adatbázis.
  
  > [!NOTE]  
  > HDInsight támogatja csak helye-alapú virtuális hálózatok, és ez jelenleg nem működik az affinitáscsoport-alapú virtuális hálózatokat.

  
  * Hozzon létre, és a egy virtuális hálózat konfigurálása, lásd: [hozzon létre egy virtuális hálózatot az Azure portal használatával](../../virtual-network/quick-create-portal.md).
    
    * Az SQL Server használata esetén a helyi adatközpontban, konfigurálnia kell a virtuális hálózatban, mint *site-to-site* vagy *pont – hely*.
      
      > [!NOTE]  
      > A **pont – hely** virtuális hálózatok, SQL Server futnia kell a VPN-ügyfél konfigurációs alkalmazásról, így érhető el a **irányítópult** az Azure virtuális hálózat konfigurációját.


    * Használata esetén az SQL Server-beli virtuális gépen, virtuális hálózat konfigurálása is használható, ha a virtuális gépet üzemeltető SQL Server ugyanazon a virtuális hálózaton HDInsight tagja.
  * Egy HDInsight-fürt létrehozása virtuális hálózaton: [Apache Hadoop-fürtök létrehozása az egyéni beállításokkal HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
    
    > [!NOTE]  
    > Az SQL Server is engedélyeznie kell a hitelesítést. SQL Server-bejelentkezésen kell használnia a jelen cikkben ismertetett lépések végrehajtásához.


**A konfiguráció érvényesítéséhez**

1. Az Azure Portalon nyissa meg az erőforráscsoportot. A csoport négy erőforrásokat kell megjelennie:

    - a fürt
    - az adatbázis-kiszolgáló
    - az adatbázis
    - az alapértelmezett tárfiók

2. Nyissa meg az adatbázis a Microsoft SQL Server Management Studiót.  Üzembe helyezett két adatbázist kell megjelennie:

    ![Azure HDInsight Sqoop SQL Management Studio](./media/hdinsight-use-sqoop/hdinsight-sqoop-sql-management-studio.png)


## <a name="run-sqoop-jobs"></a>Sqoop-feladatok futtatása
HDInsight számos módszer használatával Sqoop-feladatok futtatásához. A következő táblázat segítségével döntse el, melyik módszer a legmegfelelőbb Önnek, majd kövesse a hivatkozást bemutató.

| **Ezzel** Ha azt szeretné... | ...an **interaktív** rendszerhéj | ...**kötegelt** feldolgozása | .. során ez **fürt operációs rendszerének** | ...from ez **ügyfél operációs rendszer** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux |Linux, Unix, Mac OS X vagy Windows |
| [.NET SDK a Hadoophoz](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |? |Linux vagy Windows |Windows (egyelőre) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Linux vagy Windows |Windows |

## <a name="limitations"></a>Korlátozások
* Tömeges exportálása – a Linux-alapú HDInsight, a Sqoop-összekötő segítségével a Microsoft SQL Server vagy az Azure SQL Database-adatok exportálása jelenleg nem támogatja a tömeges beszúrás.
* Kötegelés – a Linux-alapú HDInsight használatakor a `-batch` Beszúrások végrehajtásakor váltson, Sqoop több beszúrás helyett a beszúrási műveletek kötegelése hajt végre.

## <a name="next-steps"></a>További lépések
Most már megtanulhatta, hogyan használható a sqoop használatával. További tudnivalókért lásd:

* [Az Apache Hive használata a HDInsight](../hdinsight-use-hive.md)
* [Az Apache Pig használata a HDInsight](../hdinsight-use-pig.md)
* [Adatok feltöltése a HDInsight][hdinsight-upload-data]: Keresse meg a HDInsight vagy az Azure Blob storage-ba történő feltöltéséhez más módszerekkel.

## <a name="appendix-a---a-powershell-sample"></a>A függelék – egy PowerShell-minta
A PowerShell-mintát a következő lépéseket hajtja végre:

1. Csatlakozás az Azure-bA.
2. Azure-erőforráscsoport létrehozása További információkért lásd: [az Azure PowerShell az Azure Resource Managerrel](../../azure-resource-manager/manage-resource-groups-powershell.md)
3. Hozzon létre egy Azure SQL Database-kiszolgáló, egy Azure SQL database és a két táblázat. 
   
    Ha Ehelyett használja az SQL Server, a táblák létrehozásához használja az alábbi utasításokat:
   
        CREATE TABLE [dbo].[log4jlogs](
         [t1] [nvarchar](50),
         [t2] [nvarchar](50),
         [t3] [nvarchar](50),
         [t4] [nvarchar](50),
         [t5] [nvarchar](50),
         [t6] [nvarchar](50),
         [t7] [nvarchar](50))
   
        CREATE TABLE [dbo].[mobiledata](
         [clientid] [nvarchar](50),
         [querytime] [nvarchar](50),
         [market] [nvarchar](50),
         [deviceplatform] [nvarchar](50),
         [devicemake] [nvarchar](50),
         [devicemodel] [nvarchar](50),
         [state] [nvarchar](50),
         [country] [nvarchar](50),
         [querydwelltime] [float],
         [sessionid] [bigint],
         [sessionpagevieworder][bigint])
   
    Vizsgálja meg az adatbázis és a táblák a legegyszerűbb módja, hogy a Visual studióval. Az adatbázis-kiszolgáló és az adatbázis megfelel az Azure portal használatával.
4. Hozzon létre egy HDInsight-fürtön.
   
    Vizsgálja meg a fürt, használhatja az Azure portal vagy Azure PowerShell-lel.
5. A forrásfájl adatok előzetes feldolgozása.
   
    Ebben az oktatóanyagban exportálja a log4j naplófájlok (tagolt fájl) és a egy Hive-táblába egy Azure SQL Database-adatbázishoz. A tagolt fájl neve */example/data/sample.log*. Az oktatóanyag korábbi részében látott néhány log4j tartalmazó naplók mintáit. A naplófájlban van néhány üres és néhány sorokat hasonló:
   
        java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
            at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
   
    Ez nem okoz gondot az ezeket az adatokat használó más példák, de hogy előtt el kell távolítania az ilyen kivételek azt importálhatja az Azure SQL database vagy SQL Server. Sqoop exportálás sikertelen lesz, ha van egy üres karakterlánc vagy egy sort a kevesebb elemet a mezők meghatároztak az Azure SQL database adatbázistábla számánál. A log4jlogs tábla hét karakterlánc típusú mezőket tartalmaz.
   
    Ez az eljárás létrehoz egy új fájlt a fürtön: tutorials/usesqoop/data/sample.log. Tekintse meg a módosított adatok fájlt, használhatja az Azure Portalon, egy Azure Storage explorer eszköz vagy Azure PowerShell-lel. [HDInsight – első lépések] [ hdinsight-get-started] rendelkezik egy fájlok letöltéséhez, és a fájl tartalmának megjelenítése az Azure PowerShell használatával – kódminta.
6. Az Azure SQL Database-adatfájl exportálásához.
   
    A forrásfájl tutorials/usesqoop/data/sample.log. A tábla, ahol az adatok exportálva lettek log4jlogs nevezzük.
   
   > [!NOTE]  
   > Kapcsolati sztring adatait, nem a jelen szakaszban ismertetett lépések működnie kell egy Azure SQL database vagy SQL Server. Ezeket a lépéseket tesztelt, a következő konfigurációval:
   > 
   > * **Azure virtuális hálózat pont – hely konfiguráció**: Virtuális hálózat a HDInsight-fürthöz csatlakozik egy SQL Server egy privát adatközpontban. Lásd: [pont – hely VPN konfigurálása a felügyeleti portálon](../../vpn-gateway/vpn-gateway-point-to-site-create.md) további információt.
   > * **Azure HDInsight**: Lásd: [Hadoop-fürtök létrehozása az egyéni beállításokkal HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) információ a fürt létrehozása virtuális hálózaton.
   > * **SQL Server 2014**: Konfigurálva, hogy a hitelesítés és a VPN-ügyfél konfigurációs csomag, biztonságosan csatlakozhat a virtuális hálózaton futó.
   > 
   > 
7. Hive-tábla exportálása az Azure SQL Database-adatbázishoz.
8. A HDInsight-fürt a mobiledata tábla importálása.
   
    Tekintse meg a módosított adatok fájlt, használhatja az Azure Portalon, egy Azure Storage explorer eszköz vagy Azure PowerShell-lel.  [HDInsight – első lépések] [ hdinsight-get-started] egy kódmintát, töltse le a fájlt, és a fájl tartalmának megjelenítése az Azure PowerShell használatával kapcsolatos rendelkezik.

### <a name="the-powershell-sample"></a>A PowerShell-minta

```powershell
# Prepare an Azure SQL database to be used by the Sqoop tutorial

#region - provide the following values

$subscriptionID = "<Enter your Azure Subscription ID>"

$sqlDatabaseLogin = "<Enter a SQL Database Login name>" #SQL Database server login
$sqlDatabasePassword = "<Enter a Password>"

$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = "<Enter a Password>"

$sshUserName = "sshuser" #HDInsight ssh username
$sshPassword = $httpPassword 

# used for creating Azure service names
$nameToken = "<Enter an alias>" 
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
#endregion

#region - variables

# Resource group variables
$resourceGroupName = $namePrefix + "rg"
$location = "East US 2" # used by all Azure services defined in this tutorial

# SQL database varialbes
$sqlDatabaseServerName = $namePrefix + "sqldbserver"
$sqlDatabaseName = $namePrefix + "sqldb"
$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
$sqlDatabaseMaxSizeGB = 10

# Used for retrieving external IP address and creating firewall rules
$ipAddressRestService = "https://bot.whatismyipaddress.com"
$fireWallRuleName = "UseSqoop"

# Used for creating tables and clustered indexes
$cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
    [t1] [nvarchar](50),
    [t2] [nvarchar](50),
    [t3] [nvarchar](50),
    [t4] [nvarchar](50),
    [t5] [nvarchar](50),
    [t6] [nvarchar](50),
    [t7] [nvarchar](50))"

$cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"

$cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
[clientid] [nvarchar](50),
[querytime] [nvarchar](50),
[market] [nvarchar](50),
[deviceplatform] [nvarchar](50),
[devicemake] [nvarchar](50),
[devicemodel] [nvarchar](50),
[state] [nvarchar](50),
[country] [nvarchar](50),
[querydwelltime] [float],
[sessionid] [bigint],
[sessionpagevieworder][bigint])"

$cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"

# HDInsight variables
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName
#endregion

# Treat all errors as terminating
$ErrorActionPreference = "Stop"

#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Connect-AzureRmAccount}
#endregion

#region - Create Azure resource group
Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
try{
    Get-AzureRmResourceGroup -Name $resourceGroupName
}
catch{
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
}
#endregion

#region - Create Azure SQL database server
Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
try{
    Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
catch{
    Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

    $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

    $sqlDatabaseServerName = (New-AzureRmSqlServer `
                                -ResourceGroupName $resourceGroupName `
                                -ServerName $sqlDatabaseServerName `
                                -SqlAdministratorCredentials $credential `
                                -Location $location).ServerName
    Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan

    Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
    $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
    New-AzureRmSqlServerFirewallRule `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -FirewallRuleName "$fireWallRuleName-workstation" `
        -StartIpAddress $workstationIPAddress `
        -EndIpAddress $workstationIPAddress

    #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
    #Note that this allows Azure traffic from any Azure subscription to access the server.
    New-AzureRmSqlServerFirewallRule `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -FirewallRuleName "$fireWallRuleName-Azureservices" `
        -StartIpAddress "0.0.0.0" `
        -EndIpAddress "0.0.0.0"
}

#endregion

#region - Create and validate Azure SQL database
Write-Host "`nCreating an Azure SQL database ..." -ForegroundColor Green

try {
    Get-AzureRmSqlDatabase `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -DatabaseName $sqlDatabaseName
}
catch {
    Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
    New-AzureRMSqlDatabase `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -DatabaseName $sqlDatabaseName `
        -Edition "Standard" `
        -RequestedServiceObjectiveName "S1"
}

#endregion

#region - Create tables
Write-Host "Creating the log4jlogs table and the mobiledata table ..." -ForegroundColor Green

$conn = New-Object System.Data.SqlClient.SqlConnection
$conn.ConnectionString = $sqlDatabaseConnectionString
$conn.Open()

# Create the log4jlogs table and index
$cmd = New-Object System.Data.SqlClient.SqlCommand
$cmd.Connection = $conn
$cmd.CommandText = $cmdCreateLog4jTable
$ret = $cmd.ExecuteNonQuery()
$cmd.CommandText = $cmdCreateLog4jClusteredIndex
$cmd.ExecuteNonQuery()

# Create the mobiledata table and index
$cmd.CommandText = $cmdCreateMobileTable
$cmd.ExecuteNonQuery()
$cmd.CommandText = $cmdCreateMobileDataClusteredIndex
$cmd.ExecuteNonQuery()

$conn.close()

#endregion


#region - Create HDInsight cluster

Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green

# Create the default storage account
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -Type Standard_LRS

# Create the default Blob container
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
$defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey 
New-AzureStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageAccountContext 

# Create the HDInsight cluster
$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

New-AzureRmHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $HDInsightClusterName `
    -Location $location `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version 3.6 `
    -ClusterSizeInNodes 2 `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $defaultBlobContainerName  

# Validate the cluster
Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
#endregion

#region - pre-process the source file

Write-Host "Preprocessing the source file ..." -ForegroundColor Green

# This procedure creates a new file with $destBlobName
$sourceBlobName = "example/data/sample.log"
$destBlobName = "tutorials/usesqoop/data/sample.log"

# Define the connection string
$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"

# Create block blob objects referencing the source and destination blob.
$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
$storageClient = $storageAccount.CreateCloudBlobClient();
$storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

# Define a MemoryStream and a StreamReader for reading from the source file
$stream = New-Object System.IO.MemoryStream
$stream = $sourceBlob.OpenRead()
$sReader = New-Object System.IO.StreamReader($stream)

# Define a MemoryStream and a StreamWriter for writing into the destination file
$memStream = New-Object System.IO.MemoryStream
$writeStream = New-Object System.IO.StreamWriter $memStream

# Pre-process the source blob
$exString = "java.lang.Exception:"
while(-Not $sReader.EndOfStream){
    $line = $sReader.ReadLine()
    $split = $line.Split(" ")

    # remove the "java.lang.Exception" from the first element of the array
    # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
    if ($split[0] -eq $exString){
        #create a new ArrayList to remove $split[0]
        $newArray = [System.Collections.ArrayList] $split
        $newArray.Remove($exString)

        # update $split and $line
        $split = $newArray
        $line = $newArray -join(" ")
    }

    # remove the lines that has less than 7 elements
    if ($split.count -ge 7){
        write-host $line
        $writeStream.WriteLine($line)
    }
}

# Write to the destination blob
$writeStream.Flush()
$memStream.Seek(0, "Begin")
$destBlob.UploadFromStream($memStream)

#endregion

#region - export a log file from the cluster to the SQL database

Write-Host "Preprocessing the source file ..." -ForegroundColor Green

$tableName_log4j = "log4jlogs"

# Connection string for Azure SQL Database.
# Comment if using SQL Server
$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
# Connection string for SQL Server.
# Uncomment if using SQL Server.
#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"

$exportDir_log4j = "/tutorials/usesqoop/data"

# Submit a Sqoop job
$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
$sqoopJob = Start-AzureRmHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose
Wait-AzureRmHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput

#endregion

#region - export a Hive table

$tableName_mobile = "mobiledata"
$exportDir_mobile = "/hive/warehouse/hivesampletable"

$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
$sqoopJob = Start-AzureRmHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose

Wait-AzureRmHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput

#endregion

#region - import a database

$targetDir_mobile = "/tutorials/usesqoop/importeddata/"

$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"

$sqoopJob = Start-AzureRmHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose

Wait-AzureRmHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput

#endregion
```


[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: ../hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../../sql-database/sql-database-get-started.md
[sqldatabase-create-configure]: ../../sql-database/sql-database-get-started.md

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: https://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

---
title: "Apache Sqoop feladatok futtatása az Azure HDInsight (Hadoop) |} Microsoft Docs"
description: "Ismerje meg, hogyan használható az Azure PowerShell munkaállomásról futtatása Sqoop importálása és exportálása egy Hadoop-fürt és az Azure SQL-adatbázis között."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 2fdcc6b7-6ad5-4397-a30b-e7e389b66c7a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: jgao
ms.openlocfilehash: 96d6d707d69f80a866f6ac17addabc0db076b079
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="use-sqoop-with-hadoop-in-hdinsight"></a>Sqoop használata a hadooppal a Hdinsightban
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Ismerje meg a Sqoop használata a Hdinsightban történő importálására és exportálására HDInsight-fürt és Azure SQL database vagy az SQL Server-adatbázis között.

Hadoop természetes téve a feldolgozása a strukturálatlan és félig strukturált adatok, például a naplókat, valamint fájlt, azonban a is lehet a relációs adatbázisok tárolt strukturált adatok feldolgozása érdekében.

[Sqoop] [ sqoop-user-guide-1.4.4] az eszköz a Hadoop-fürtök és a relációs adatbázisok közötti adattovábbításra. Adatok importálása egy relációs adatbázis-kezelő rendszerének (RDBMS), például az SQL Server, MySQL, vagy a Hadoop elosztott fájlrendszer (HDFS), az Oracle átalakíthatja az adatokat a Hadoop MapReduce vagy a Hive, majd az adatok exportálása vissza egy RDBMS használhatja. Ebben az oktatóanyagban egy SQL Server adatbázist használ a relációs adatbázis.

A HDInsight-fürtökön támogatott Sqoop verzióiért lásd: [What's new in HDInsight által biztosított fürt verziók?][hdinsight-versions]

## <a name="understand-the-scenario"></a>A forgatókönyv ismertetése

HDInsight-fürtök néhány adatot tartalmaz. A következő két mintát használ:

* A log4j naplófájlt, amely itt található: */example/data/sample.log*. A következő fájl kibontása a következő naplók kapcsolódnak:
  
        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...
* Nevű Hive tábla *hivesampletable*, amely hivatkozik az adatok fájlba */hive/warehouse/hivesampletable*. A táblázat tartalmaz néhány mobileszköz adat. 
  
  | Mező | Adattípus |
  | --- | --- |
  | ClientID |Karakterlánc |
  | querytime |Karakterlánc |
  | piaci |Karakterlánc |
  | deviceplatform |Karakterlánc |
  | devicemake |Karakterlánc |
  | devicemodel |Karakterlánc |
  | state |Karakterlánc |
  | Ország |Karakterlánc |
  | querydwelltime |Dupla |
  | munkamenet-azonosító |bigint |
  | sessionpagevieworder |bigint |

Az oktatóanyag segítségével ezen két adatkészletek tesztelése Sqoop importálása és exportálása.

## <a name="create-cluster-and-sql-database"></a>Fürt és az SQL-adatbázis létrehozása
Ez a szakasz bemutatja, hogyan fürt SQL-adatbázis, az SQL adatbázis sémák és futtatásához az oktatóanyag az Azure-portál és az Azure Resource Manager-sablon létrehozásához. A sablonban található [Azure gyors üzembe helyezési sablonokat](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). A Resource Manager-sablon meghívja a táblasémákat telepítendő SQL-adatbázis bacpac csomag.  A következő nyilvános blobtárolóban https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac a bacpac csomag található. Ha azt szeretné, személyes tároló használata a bacpac-fájlok, a sablon a következő értékeket használja:
   
```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

Ha inkább az Azure PowerShell használatával a fürt és az SQL-adatbázis létrehozása című [függelék](#appendix-a---a-powershell-sample).

> [!NOTE]
> Importálja a sablon használatával, vagy az Azure-portálon csak támogatja BACPAC-fájl importálását az Azure blob storage.

**A környezet erőforrás felügyeleti sablon használatával konfigurálása**
1. Kattintson az alábbi képre kattintva nyissa meg a Resource Manager-sablon az Azure portálon.         
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
2. Adja meg a következő tulajdonságokkal:

    - **Előfizetés**: Adja meg az Azure-előfizetéshez.
    - **Erőforráscsoport**: hozzon létre egy új Azure-erőforráscsoportot, vagy válasszon ki egy meglévő erőforráscsoportot.  Egy erőforráscsoport egy felügyeleti célból.  Objektumok tárolója.
    - **Hely**: Válasszon ki egy régiót.
    - **ClusterName**: Adja meg a Hadoop-fürt nevét.
    - **A fürt bejelentkezési neve és jelszava**: Az alapértelmezett bejelentkezési név az admin.
    - **SSH-felhasználónév és -jelszó**.
    - **SQL adatbázis-bejelentkezési nevet és jelszót**.
    - **hely _artifacts**: az alapértelmezett értéket használja, kivéve, ha egy másik helyen található saját backpac fájlt használja.
    - **hely Sas tokent _artifacts**: hagyja üresen a mezőt.
    - **Bacpac Fájlnév**: az alapértelmezett értéket használja, kivéve, ha szeretné használni a saját backpac fájlt.
     
        A következő értékek a következők név szoftveresen kötött a sablonváltozók szakaszban:
        
        |Név|Érték|
        |----|-----|
        | Alapértelmezett tárfiók neve | &lt;CluterName > tárolásához |
        | Az Azure SQL adatbázis-kiszolgáló neve | &lt;ClusterName > dbserver |
        | Az Azure SQL-adatbázis neve | &lt;ClusterName > db |
     
3. Válassza ki **elfogadom a feltételeket és a fenti feltételek**.
4. Kattintson a **Purchase** (Vásárlás) gombra. Megjelenik egy új csempe jelenik meg Submitting deployment sablon központi telepítéshez. A fürt és az SQL-adatbázis létrehozása nagyjából 20 percet vesz igénybe.

Ha úgy dönt, hogy a meglévő Azure SQL adatbázis vagy a Microsoft SQL Server használata

* **Az Azure SQL adatbázis**: konfigurálnia kell egy tűzfalszabályt az Azure SQL adatbázis-kiszolgáló számára engedélyezi a hozzáférést a munkaállomáson. Egy Azure SQL-adatbázis létrehozása, és a tűzfalon konfigurálásával kapcsolatos útmutatásért lásd: [Azure SQL-adatbázis használatának első][sqldatabase-get-started]. 
  
  > [!NOTE]
  > Alapértelmezés szerint az Azure SQL adatbázis Azure-szolgáltatások, például az Azure HDInsight kapcsolatokat engedélyez. Ha a tűzfal beállítás le van tiltva, engedélyezze az Azure portálról szeretné. További információk az Azure SQL-adatbázis létrehozása és a tűzfalszabályok konfigurálása, lásd: [létrehozása és konfigurálása az SQL-adatbázis][sqldatabase-create-configue].
  > 
  > 
* **SQL Server**: Ha a HDInsight-fürt ugyanazt a virtuális hálózatot az Azure SQL-kiszolgálóként, segítségével a lépéseket a cikkben adatok importálása és exportálása az SQL Server-adatbázishoz.
  
  > [!NOTE]
  > HDInsight támogatja a csak helyalapú virtuális hálózatokat, és jelenleg nem működik az affinitáscsoport-alapú virtuális hálózatokon.
  > 
  > 
  
  * Hozzon létre, és egy virtuális hálózat konfigurálására, [hozzon létre egy virtuális hálózatot az Azure portál használatával](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md).
    
    * SQL Server használatakor az adatközpontban található konfigurálnia kell a virtuális hálózaton: *pont-pont* vagy *pont-pont*.
      
      > [!NOTE]
      > A **pont-pont** virtuális hálózatok, az SQL Server futnia kell a VPN-ügyfél konfigurációs alkalmazás, amely elérhető a a **irányítópult** az Azure-beli virtuális hálózat konfigurációját.
      > 
      > 
    * Használatakor az SQL Server Azure virtuális géphez, virtuális hálózati konfigurációt is használható, ha a virtuális gépet üzemeltető SQL Server a HDInsight megegyező virtuális hálózatban tagja.
  * HDInsight-fürtök létrehozása a virtuális hálózaton: [létrehozása Hadoop-fürtök a Hdinsightban egyéni beállításokkal](../hdinsight-hadoop-provision-linux-clusters.md)
    
    > [!NOTE]
    > SQL Server hitelesítési is lehetővé kell tenni. Ebben a cikkben lépéseinek végrehajtásához egy SQL Server bejelentkezési fiókot kell használnia.
    > 
    > 

**A konfiguráció érvényesítéséhez**

1. Nyissa meg az erőforráscsoportot az Azure portálon. Ekkor megjelenik a csoport négy erőforrások:

    - a fürt
    - az adatbázis-kiszolgáló
    - az adatbázis
    - az alapértelmezett tárfiók

2. Nyissa meg az adatbázis a Microsoft SQL Server Management Studio.  Ekkor megjelenik a két adatbázis telepítve:

    ![Az Azure HDInsight Sqoop SQL Management Studio](./media/hdinsight-use-sqoop/hdinsight-sqoop-sql-management-studio.png)


## <a name="run-sqoop-jobs"></a>Sqoop feladatok futtatása
HDInsight Sqoop feladatok futtatásához számos módszer használatával. A következő táblázat segítségével döntse el, melyik módszert részesíti az Ön számára legmegfelelőbb, majd kövesse a hivatkozást útmutatást.

| **Ezzel** Ha azt szeretné... | ...an **interaktív** rendszerhéj | ...**kötegelt** feldolgozása | és mivel ez **fürt operációs rendszer** | ....from ez **ügyfél operációs rendszer** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X vagy Windows |
| [.NET SDK a Hadoophoz](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |✔ |Linux- vagy Windows |(Egyelőre) Windows |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |✔ |Linux- vagy Windows |Windows |

## <a name="limitations"></a>Korlátozások
* Tömeges export - a Linux-alapú HDInsight, a Sqoop összekötő használt Microsoft SQL Server vagy az Azure SQL Database adatainak exportálása jelenleg nem támogatja a tömeges beszúrások.
* Kötegelés - és a Linux-alapú HDInsight együttes használata esetén a `-batch` beszúrása végrehajtásakor kapcsoló, a Sqoop több beszúrás helyett a beszúrási műveletek kötegelése hajt végre.

## <a name="next-steps"></a>Következő lépések
Most megtanulhatta, hogyan használható a Sqoop. További tudnivalókért lásd:

* [A Hive használata a HDInsightban](../hdinsight-use-hive.md)
* [A Pig használata a HDInsightban](../hdinsight-use-pig.md)
* [Adatok feltöltése a HDInsight][hdinsight-upload-data]: található adatok feltöltése a HDInsight vagy az Azure Blob storage más módszerrel.

## <a name="appendix-a---a-powershell-sample"></a>A melléklet – PowerShell-példa
A PowerShell-példa a következő lépéseket végzi el:

1. Csatlakozás az Azure-bA.
2. Azure-erőforráscsoport létrehozása További információkért lásd: [az Azure PowerShell használata Azure Resource Managerrel](../../azure-resource-manager/powershell-azure-resource-manager.md)
3. Hozzon létre egy Azure SQL adatbázis-kiszolgáló, az Azure SQL-adatbázis és a két tábla. 
   
    Ha ehelyett SQL Servert használja, a táblák létrehozásához használja az alábbi utasításokat:
   
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
   
    A vizsgálata, az adatbázis és a táblák legkönnyebben Visual Studio használata. Az adatbázis-kiszolgáló és az adatbázis megfelel az Azure portál használatával.
4. HDInsight-fürtök létrehozása.
   
    Vizsgálja meg a fürt, használhatja az Azure portálon vagy az Azure PowerShell.
5. Előre feldolgozzák a forrásadatfájlok.
   
    Ebben az oktatóanyagban exportálhatja egy log4j naplófájl (tagolt fájl) és a Hive tábla Azure SQL-adatbázishoz. A tagolt fájl neve */example/data/sample.log*. Az oktatóanyag korábbi részében látott néhány minták log4j naplók. A naplófájlban van néhány üres sorok és az egyes sorok alábbiakhoz hasonló:
   
        java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
            at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
   
    Ennek megfelelően működik, ezeket az adatokat használó más példák, de azt el kell távolítania az ilyen kivételek ahhoz, hogy importálja az Azure SQL database vagy az SQL Server. Sqoop exportálás sikertelen lesz, ha van egy üres karakterlánc vagy egy kevesebb sort elem definiálva az Azure SQL-adatbázistáblában szereplő mezők számánál. A log4jlogs tábla hét karakterlánc típusú mezőket tartalmaz.
   
    Ez az eljárás létrehoz egy új fájlt a fürt: tutorials/usesqoop/data/sample.log. Tekintse meg a módosított adatok fájlt, használhatja az Azure-portálon, az Azure Storage explorer eszköz vagy az Azure PowerShell. [Ismerkedés a HDInsight] [ hdinsight-get-started] rendelkezik egy példakód az Azure PowerShell használatával töltse le a fájlt, és megjeleníti a fájl tartalma.
6. Az Azure SQL-adatbázis egy adatfájlt exportálja.
   
    A forrásfájl tutorials/usesqoop/data/sample.log. A tábla, ahol az adatok exportálása az log4jlogs nevezik.
   
   > [!NOTE]
   > Eltérő kapcsolati karakterlánc információt a jelen szakaszban szereplő lépéseket az Azure SQL-adatbázis, vagy az SQL Server kell működnie. Ezeket a lépéseket a következő konfigurációval teszteltük:
   > 
   > * **Azure-beli virtuális hálózat pont-hely konfigurációs**: virtuális hálózat a HDInsight-fürthöz csatlakozik egy SQL Server egy privát adatközpontban. Lásd: [pont-pont VPN konfigurálásához a kezelési portál](../../vpn-gateway/vpn-gateway-point-to-site-create.md) további információt.
   > * **Az Azure HDInsight 3.1**: lásd: [létrehozása Hadoop-fürtök a Hdinsightban egyéni beállításokkal](../hdinsight-hadoop-provision-linux-clusters.md) információ a fürt létrehozása a virtuális hálózaton.
   > * **Az SQL Server 2014**: konfigurált ahhoz, hogy hitelesítést és fut a VPN-ügyfél konfigurációs csomag biztonságosan a virtuális hálózathoz csatlakozni.
   > 
   > 
7. Hive tábla exportálása az Azure SQL adatbázishoz.
8. A mobiledata tábla importálása a HDInsight-fürthöz.
   
    Tekintse meg a módosított adatok fájlt, használhatja az Azure-portálon, az Azure Storage explorer eszköz vagy az Azure PowerShell.  [Ismerkedés a HDInsight] [ hdinsight-get-started] rendelkezik egy kódminta Azure PowerShell használatával töltse le a fájlt, és megjeleníti a fájl tartalma.

### <a name="the-powershell-sample"></a>A PowerShell-példa

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
$ipAddressRestService = "http://bot.whatismyipaddress.com"
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
catch{Login-AzureRmAccount}
#endregion

#region - Create Azure resouce group
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
[sqldatabase-create-configue]: ../../sql-database/sql-database-get-started.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

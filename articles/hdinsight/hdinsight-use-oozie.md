---
title: A HDInsight Hadoop az Oozie használata
description: Hadoop az Oozie használata a HDInsight, a big data-szolgáltatás. Megtudhatja, hogyan Oozie munkafolyamatokat, és az Oozie-feladatok elküldéséhez.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/25/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 25694a44b1474cccaf62ed5005566cdb5230b15c
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360860"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-in-hdinsight"></a>Az Apache Hadoop megadásához és a munkafolyamat futtatása a HDInsight Apache Oozie használata
[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Ismerje meg, hogyan lehet Apache Oozie segítségével a munkafolyamatokat és a munkafolyamat futtatása a HDInsight. Az Oozie-koordinátor kapcsolatos további információkért lásd: [időalapú Apache Oozie-koordinátor használata a HDInsight][hdinsight-oozie-coordinator-time]. Azure Data Factory kapcsolatban lásd: [Apache Pig használata és az Apache Hive, a Data Factory][azure-data-factory-pig-hive].

Az Apache Oozie egy munkafolyamat/koordinációs rendszer, amely a Hadoop-feladatokat kezeli. Integrálva van a Hadoop-veremmel, és támogatja a Hadoop-feladatok Apache MapReduce, Apache Pig, Apache Hive- és Apache sqoop használatával. Is használható a rendszer, például Java programok vagy héjparancsfájlok ütemezésére adott feladatok ütemezéséhez.

Ez az oktatóanyag utasításait követve meg, hogy a munkafolyamat két műveletet tartalmaz:

![A munkafolyamat diagramja][img-workflow-diagram]

1. Egy Hive-művelet megszámlálni az egyes log-szintű írjon be egy Apache Log4j file egy HiveQL-parancsfájlt futtatja. Minden egyes log4j fájl, amely megjeleníti a típusát és súlyosságát, például [NAPLÓZÁSI szint] mező tartalmazó mezők üzletági áll:
   
        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...
   
    A Hive-parancsfájl kimenet hasonlít:
   
        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4
   
    Hive-ról további információkért lásd: [Apache Hive használata a HDInsight][hdinsight-use-hive].
2. A Sqoop művelet exportálja a HiveQL kimenet egy Azure SQL database egyik táblájába. Sqoop használatával kapcsolatos további információkért lásd: [használata Apache sqoop használatával HDInsight][hdinsight-use-sqoop].

> [!NOTE]  
> Tekintse meg a HDInsight-fürtökön támogatott Oozie verziók [a HDInsight által biztosított az Apache Hadoop-fürtverziók újdonságai?] [hdinsight-versions].
> 
> 

### <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez az oktatóanyag elkezdéséhez a következő elemet kell rendelkeznie:

* **Munkaállomás Azure PowerShell-lel**. 
  

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
  

## <a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Oozie-munkafolyamatokkal, és a kapcsolódó HiveQL-parancsfájlt megadása
Az Oozie-munkafolyamatok definíciók hPDL (egy XML folyamat Definition Language) nyelven íródtak. Az alapértelmezett munkafolyamat-fájl neve *workflow.xml*. A munkafolyamat fájlja, ebben az oktatóanyagban használja a következő:
```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>

        <action name="RunHiveScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.job.queue.name</name>
                        <value>${queueName}</value>
                    </property>
                </configuration>
                <script>${hiveScript}</script>
                <param>hiveTableName=${hiveTableName}</param>
                <param>hiveDataFolder=${hiveDataFolder}</param>
                <param>hiveOutputFolder=${hiveOutputFolder}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>

        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.compress.map.output</name>
                        <value>true</value>
                    </property>
                </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveOutputFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\001"</arg>
            </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>

        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>

        <end name="end"/>
    </workflow-app>
```
Nincsenek definiálva a munkafolyamat két műveletet. A kezdő művelet *RunHiveScript*. Ha a művelet sikeresen lefutott, a következő beavatkozásra *RunSqoopExport*.

A RunHiveScript rendelkezik értékkel a változókat. Az Oozie feladat elküldésekor a munkaállomásáról Azure PowerShell-lel átadhatja az értékeket.

|Munkafolyamat-változók|Leírás|
|---|---|
|${jobTracker}|Adja meg a Hadoop-feladat tracker URL-CÍMÉT. Használat **jobtrackerhost:9010** a HDInsight 3.0-s és a 2.1-es verzió.|
|${nameNode}|Adja meg a Hadoop neve csomópont URL-CÍMÉT. Például használja az alapértelmezett fájl rendszer cím *wasb: / /&lt;containerName&gt;\@&lt;storageAccountName&gt;. blob.core.windows.net*.|
|${queueName}|Megadja, hogy a feladat elküldve a várólista nevét. Használja a **alapértelmezett**.|

|Hive-művelet|Leírás|
|---|---|
|${hiveDataFolder}|Megadja a forráskönyvtár a Hive-tábla létrehozása a parancshoz.|
|${hiveOutputFolder}|Megadja a kimeneti mappát az FELÜLÍRÁSA INSERT utasítás.|
|${hiveTableName}|Itt adhatja meg, amely hivatkozik a log4j adatfájlok Hive-tábla neve.|

|Műveleti változó sqoop használatával|Leírás|
|---|---|
|${sqlDatabaseConnectionString}|Itt adhatja meg az Azure SQL-adatbázis kapcsolati karakterláncát.|
|${sqlDatabaseTableName}|Adja meg az Azure SQL database tábla, ahol az adatok exportálva lettek.|
|${hiveOutputFolder}|Megadja a kimeneti mappát, a Hive-BESZÚRÁSA FELÜLÍRÁSA utasításra vonatkozóan. Ez a Sqoop export (Exportálás-dir) ugyanabban a mappában.|

Oozie-munkafolyamatokkal és a munkafolyamat-műveletek használatával kapcsolatos további információkért lásd: [Apache Oozie 4.0 dokumentáció] [ apache-oozie-400] (a HDInsight 3.0-s verzió) vagy [ApacheOozie3.3.2-dokumentáció] [ apache-oozie-332] (a HDInsight 2.1-es verzió).

A Hive-művelet a munkafolyamat meghívja a HiveQL-parancsfájlt. A parancsfájl három HiveQL utasításokat tartalmazza:

    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

1. **A DROP TABLE utasítás** törli a log4j Hive-táblába, ha van ilyen.
2. **A CREATE TABLE utasítás** log4j Hive külső tábla létrehozása, amely a log4j naplófájlok helyét. A mezőhatároló van ",". Az alapértelmezett sor határoló "\n". Külső Hive-tábla szolgál az éppen eltávolítják az eredeti helyre, ha szeretné futtatni az Oozie-munkafolyamatokkal többször adatfájl elkerülése érdekében.
3. **A FELÜLÍRÁSA INSERT utasítás** számít minden napló-szintű típusok a log4j Hive-táblából, és menti a kimenet az Azure Storage-blobba.

A szkriptben használt három változók vannak:

* ${hiveTableName}
* ${hiveDataFolder}
* ${hiveOutputFolder}

A munkafolyamat-definíciós fájlja (ebben az oktatóanyagban workflow.xml) továbbítja ezeket az értékeket a HiveQL-parancsfájlt futási időben.

A munkafolyamat és a HiveQL fájlt is a blobtárolóban vannak tárolva.  A PowerShell-parancsfájlt az oktatóanyag későbbi részében használja az alapértelmezett tárfiók mindkét fájlokat másolja. 

## <a name="submit-oozie-jobs-using-powershell"></a>PowerShell-lel az Oozie-feladatok elküldése
Az Azure PowerShell jelenleg nem biztosít semmilyen parancsmagok Oozie feladatok meghatározása. Használhatja a **Invoke-RestMethod** Oozie webszolgáltatások meghívása parancsmagot. Az Oozie webszolgáltatási API-ra egy HTTP REST API-t JSON. Az Oozie-webszolgáltatások API kapcsolatos további információkért lásd: [Apache Oozie 4.0 dokumentáció] [ apache-oozie-400] (a HDInsight 3.0-s verzió) vagy [Apache Oozie 3.3.2 dokumentáció] [ apache-oozie-332] (a HDInsight 2.1-es verzió).

Ez a szakasz a PowerShell-parancsprogram a következő lépéseket hajtja végre:

1. Csatlakozás az Azure-bA.
2. Azure-erőforráscsoport létrehozása További információkért lásd: [az Azure PowerShell használata az Azure Resource Manager](../powershell-azure-resource-manager.md).
3. Hozzon létre egy Azure SQL Database-kiszolgáló, egy Azure SQL database és a két táblázat. Ezek a Sqoop művelet a munkafolyamat által használt.
   
    A tábla neve a *log4jLogCount*.
4. Hozzon létre egy HDInsight-fürtöt, az Oozie-feladatok futtatásához használt.
   
    Vizsgálja meg a fürt, használhatja az Azure portal vagy Azure PowerShell-lel.
5. Másolja az oozie-munkafolyamat fájl- és a HiveQL-parancsfájlt az alapértelmezett fájlrendszer.
   
    Mindkét fájljai egy nyilvános Blob-tárolóba.
   
   * Másolja a HiveQL-parancsfájlt (useoozie.hql) az Azure Storage (wasb:///tutorials/useoozie/useoozie.hql).
   * Copy workflow.xml to wasb:///tutorials/useoozie/workflow.xml.
   * Másolja az adatfájl (/ example/data/sample.log) való wasb:///tutorials/useoozie/data/sample.log.
6. Az Oozie-feladat elküldéséhez.
   
    Vizsgálja meg az OOzie-feladat eredményét, használja a Visual Studio vagy más eszközök az Azure SQL Database-adatbázishoz csatlakozhat.

Íme a szkript.  A szkriptet futtathatja a Windows PowerShell ISE-ben. Csak az első 7 változók konfigurálásához kell.
```powershell
    #region - provide the following values

    $subscriptionID = "<Enter your Azure subscription ID>"

    # SQL Database server login credentials used for creating and connecting
    $sqlDatabaseLogin = "<Enter SQL Database Login Name>"
    $sqlDatabasePassword = "<Enter SQL Database Login Password>"

    # HDInsight cluster HTTP user credential used for creating and connecting
    $httpUserName = "admin"  # The default name is "admin"
    $httpPassword = "<Enter HDInsight Cluster HTTP User Password>"

    # Used for creating Azure service names
    $nameToken = "<Enter an Alias>"
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

    # HDInsight variables
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzContext}
    catch{
        Connect-AzAccount
        Select-AzSubscription -SubscriptionId $subscriptionID
    }
    #endregion

    #region - Create Azure resource group
    Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
    try{
        Get-AzResourceGroup -Name $resourceGroupName
    }
    catch{
        New-AzResourceGroup -Name $resourceGroupName -Location $location
    }
    #endregion

    #region - Create Azure SQL database server
    Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
    try{
        Get-AzSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
    catch{
        Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

        $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
        $sqlLoginCredentials = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

        $sqlDatabaseServerName = (New-AzSqlServer `
                                    -ResourceGroupName $resourceGroupName `
                                    -ServerName $sqlDatabaseServerName `
                                    -SqlAdministratorCredentials $sqlLoginCredentials `
                                    -Location $location).ServerName
        Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan

        Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
        $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
        New-AzSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-workstation" `
            -StartIpAddress $workstationIPAddress `
            -EndIpAddress $workstationIPAddress

        #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
        #Note that this allows Azure traffic from any Azure subscription to access the server.
        New-AzSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-Azureservices" `
            -StartIpAddress "0.0.0.0" `
            -EndIpAddress "0.0.0.0"
    }
    #endregion

    #region - Create and validate Azure SQL database
    Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green

    try {
        Get-AzSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName
    }
    catch {
        New-AzSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName `
            -Edition "Standard" `
            -RequestedServiceObjectiveName "S1"
    }
    #endregion

    #region - Create SQL database tables
    Write-Host "Creating the log4jlogs table  ..." -ForegroundColor Green

    $sqlDatabaseTableName = "log4jLogsCount"
    $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
            [Level] [nvarchar](10) NOT NULL,
            [Total] float,
        CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
        (
        [Level] ASC
        )
        )"

    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = $sqlDatabaseConnectionString
    $conn.Open()

    # Create the log4jlogs table and index
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.Connection = $conn
    $cmd.CommandText = $cmdCreateLog4jCountTable
    $cmd.ExecuteNonQuery()

    $conn.close()
    #endregion

    #region - Create HDInsight cluster

    Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green

    # Create the default storage account
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS

    # Create the default Blob container
    $defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext `
                                        -StorageAccountName $defaultStorageAccountName `
                                        -StorageAccountKey $defaultStorageAccountKey 
    New-AzStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext 

    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $HDInsightClusterName `
        -Location $location `
        -ClusterType Hadoop `
        -OSType Windows `
        -ClusterSizeInNodes 2 `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultBlobContainerName 

    # Validate the cluster
    Get-AzHDInsightCluster -ClusterName $hdinsightClusterName
    #endregion

    #region - copy Oozie workflow and HiveQL files

    Write-Host "Copy workflow definition and HiveQL script file ..." -ForegroundColor Green

    # Both files are stored in a public Blob
    $publicBlobContext = New-AzStorageContext -StorageAccountName "hditutorialdata" -Anonymous

    # WASB folder for storing the Oozie tutorial files.
    $destFolder = "tutorials/useoozie"  # Do NOT use the long path here

    Start-CopyAzureStorageBlob `
        -Context $publicBlobContext `
        -SrcContainer "useoozie" `
        -SrcBlob "useooziewf.hql"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -DestBlob "$destFolder/useooziewf.hql" `
        -Force

    Start-CopyAzureStorageBlob `
        -Context $publicBlobContext `
        -SrcContainer "useoozie" `
        -SrcBlob "workflow.xml"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -DestBlob "$destFolder/workflow.xml" `
        -Force

    #validate the copy
    Get-AzStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/workflow.xml

    Get-AzStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/useooziewf.hql

    #endregion

    #region - copy the sample.log file

    Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green

    Start-CopyAzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -SrcContainer $defaultBlobContainerName `
        -SrcBlob "example/data/sample.log"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -destBlob "$destFolder/data/sample.log" 

    #validate the copy
    Get-AzStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/data/sample.log

    #endregion

    #region - submit Oozie job

    $storageUri="wasb://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net"

    $oozieJobName = $namePrefix + "OozieJob"

    #Oozie WF variables
    $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
    $waitTimeBetweenOozieJobStatusCheck=10

    #Hive action variables
    $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
    $hiveTableName = "log4jlogs"
    $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
    $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

    #Sqoop action variables
    $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

    $OoziePayload =  @"
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

    <property>
        <name>nameNode</name>
        <value>$storageUrI</value>
    </property>

    <property>
        <name>jobTracker</name>
        <value>jobtrackerhost:9010</value>
    </property>

    <property>
        <name>queueName</name>
        <value>default</value>
    </property>

    <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
    </property>

    <property>
        <name>hiveScript</name>
        <value>$hiveScript</value>
    </property>

    <property>
        <name>hiveTableName</name>
        <value>$hiveTableName</value>
    </property>

    <property>
        <name>hiveDataFolder</name>
        <value>$hiveDataFolder</value>
    </property>

    <property>
        <name>hiveOutputFolder</name>
        <value>$hiveOutputFolder</value>
    </property>

    <property>
        <name>sqlDatabaseConnectionString</name>
        <value>&quot;$sqlDatabaseConnectionString&quot;</value>
    </property>

    <property>
        <name>sqlDatabaseTableName</name>
        <value>$SQLDatabaseTableName</value>
    </property>

    <property>
        <name>user.name</name>
        <value>$httpUserName</value>
    </property>

    <property>
        <name>oozie.wf.application.path</name>
        <value>$oozieWFPath</value>
    </property>

    </configuration>
    "@

    Write-Host "Checking Oozie server status..." -ForegroundColor Green
    $clusterUriStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/admin/status"
    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $httpCredential -OutVariable $OozieServerStatus

    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $oozieServerStatus = $jsonResponse[0].("systemMode")
    Write-Host "Oozie server status is $oozieServerStatus."

    # create Oozie job
    Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
    Write-Host "`n--------`n$OoziePayload`n--------"
    $clusterUriCreateJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/jobs"
    $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $httpCredential -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug

    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $oozieJobId = $jsonResponse[0].("id")
    Write-Host "Oozie job id is $oozieJobId..."

    # start Oozie job
    Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
    $clusterUriStartJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=start"
    $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $httpCredential | Format-Table -HideTableHeaders #-debug

    # get job status
    Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
    Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

    Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
    $clusterUriGetJobStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
    $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $JobStatus = $jsonResponse[0].("status")

    while($JobStatus -notmatch "SUCCEEDED|KILLED")
    {
        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $JobStatus = $jsonResponse[0].("status")
        $jobStatus
    }

    Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!" -ForegroundColor Green

    #endregion
```

**Majd futtassa újból az oktatóanyag**

Futtassa újra a munkafolyamatot, törölnie kell a következőkkel:

* A Hive-parancsfájl kimeneti fájl
* A log4jLogsCount táblában lévő adatokat

Itt látható egy minta PowerShell-parancsfájlt, amelyet használhat:
```powershell
    $resourceGroupName = "<AzureResourceGroupName>"

    $defaultStorageAccountName = "<AzureStorageAccountName>"
    $defaultBlobContainerName = "<ContainerName>"

    #SQL database variables
    $sqlDatabaseServerName = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabasePassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
    $destContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    Remove-AzStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $defaultBlobContainerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()
```

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtudhatta, hogyan lehet Apache Oozie-munkafolyamatokkal definiálása és az Oozie-feladatok futtatása a PowerShell használatával. További tudnivalókért tekintse meg a következő cikkeket:

* [Időalapú Apache Oozie-koordinátor használata a HDInsight][hdinsight-oozie-coordinator-time]
* [Az Apache Hadoop-használatának Apache Hive a HDInsight elemzéséhez mobil kézibeszélőt használata][hdinsight-get-started]
* [Az Azure Blob storage használata a HDInsight][hdinsight-storage]
* [Felügyelheti a HDInsight PowerShell használatával][hdinsight-admin-powershell]
* [A HDInsight Apache Hadoop-feladatok adatok feltöltése][hdinsight-upload-data]
* [A HDInsight az Apache Hadoop Apache Sqoop használata][hdinsight-use-sqoop]
* [Apache Hive használata a HDInsight Apache Hadoop-keretrendszerrel][hdinsight-use-hive]
* [Az Apache Pig használata a HDInsight Apache Hadoop-keretrendszerrel][hdinsight-use-pig]
* [Java MapReduce programok fejlesztése a HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: https://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md


[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[sqldatabase-get-started]: ../sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: https://hadoop.apache.org/
[apache-oozie-400]: https://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: https://oozie.apache.org/docs/3.3.2/

[powershell-download]: https://azure.microsoft.com/downloads/
[powershell-about-profiles]: https://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/library/ee176961.aspx

[cindygross-hive-tables]: https://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png  
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: https://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

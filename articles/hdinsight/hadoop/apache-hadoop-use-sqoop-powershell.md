---
title: Apache Sqoop-feladatok futtatása a PowerShell és az Azure HDInsight használatával
description: Megtudhatja, hogyan használhatja a Azure PowerShell egy munkaállomásról az Apache Sqoop importálására és exportálására egy Apache Hadoop-fürt és egy Azure SQL-adatbázis között.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: hrasheed
ms.openlocfilehash: 6cb3e91bed4f16cf1e30c535b5ed667fc690dd53
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499274"
---
# <a name="run-apache-sqoop-jobs-by-using-azure-powershell-for-apache-hadoop-in-hdinsight"></a>Apache Sqoop-feladatok futtatása a HDInsight Apache Hadoop Azure PowerShell használatával
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Megtudhatja, hogyan használhatja a Azure PowerShellt az Apache Sqoop-feladatok Azure HDInsight való futtatásához egy HDInsight-fürt és egy Azure SQL Database-adatbázis vagy egy SQL Server-adatbázis közötti adatok importálásához és exportálásához. Ez a példa a `/tutorials/usesqoop/data/sample.log` származó adatok exportálását az alapértelmezett Storage-fiókból, majd egy SQL Server adatbázis `log4jlogs` nevű táblába importálja. Ez a cikk az [Apache Sqoop és a Hadoop HDInsight-ben való használatának](./hdinsight-use-sqoop.md)folytatása.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A cikk elkezdése előtt a következő elemeket kell megadnia:

* Egy munkaállomás, amelyen Azure PowerShell [az modul](https://docs.microsoft.com/powershell/azure/overview) telepítve van.

* A [tesztkörnyezet üzembe](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) állításának befejezése az [Apache Sqoop és a Hadoop használatával a HDInsight-ben](./hdinsight-use-sqoop.md).


## <a name="run-apache-sqoop-by-using-powershell"></a>Apache Sqoop futtatása a PowerShell használatával
A következő PowerShell-parancsfájl előre feldolgozza a forrásfájlt, majd exportálja egy Azure SQL Database-be az `log4jlogs`táblázatba. Cserélje le a `CLUSTERNAME`, `CLUSTERPASSWORD`és `SQLPASSWORD` értéket az előfeltételként használt értékekre.

```powershell 
<#------ BEGIN USER INPUT ------#>
$hdinsightClusterName = "CLUSTERNAME"
$httpUserName = "admin"  #default is admin, update as needed
$httpPassword = 'CLUSTERPASSWORD'
$sqlDatabasePassword = 'SQLPASSWORD'
<#------- END USER INPUT -------#>

# Other fixed variable that should be used as is
$sqlServerName = $hdinsightClusterName + "dbserver"
$sqlDatabaseName = $hdinsightClusterName + "db"
$tableName_log4j = "log4jlogs"
$exportDir_log4j = "/tutorials/usesqoop/data"
$sourceBlobName = "example/data/sample.log"
$destBlobName = "tutorials/usesqoop/data/sample.log"
$sqljdbcdriver = "/user/oozie/share/lib/sqoop/mssql-jdbc-7.0.0.jre8.jar"

$cluster = Get-AzHDInsightCluster -ClusterName $hdinsightClusterName
$defaultStorageAccountName = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
$defaultStorageContainer = $cluster.DefaultStorageContainer
$resourceGroup = $cluster.ResourceGroup

$sqlServer = Get-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $sqlServerName
$sqlServerLogin = $sqlServer.SqlAdministratorLogin
$sqlServerFQDN = $sqlServer.FullyQualifiedDomainName

#Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzContext}
catch{Connect-AzAccount}

#pre-process the source file
Write-Host "`nPreprocessing the source file ..." -ForegroundColor Green

# This procedure creates a new file with $destBlobName
# Define the connection string
$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroup `
                                -Name $defaultStorageAccountName)[0].Value

# Create block blob objects referencing the source and destination blob.
$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroup `
    -Name $defaultStorageAccountName

$storageContainer = ($storageAccount |Get-AzStorageContainer -Name $defaultStorageContainer).CloudBlobContainer

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

#export the log file from the cluster to the SQL database
Write-Host "Exporting the log file ..." -ForegroundColor Green

$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

# Connection string
$connectionString = "jdbc:sqlserver://$sqlServerFQDN;user=$sqlServerLogin@$sqlServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

# Submit a Sqoop job
$sqoopDef = New-AzHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1" `
    -Files $sqljdbcdriver

$sqoopJob = Start-AzHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef

Wait-AzHDInsightJob `
    -ResourceGroupName $resourceGroup `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroup `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultStorageContainer `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultStorageContainer `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput
```

## <a name="limitations"></a>Korlátozások
A Linux-alapú HDInsight a következő korlátozásokat mutatja be:

* Tömeges exportálás: a Sqoop-összekötő, amely az adatexportálás Microsoft SQL Server vagy Azure SQL Database jelenleg nem támogatja a tömeges beszúrásokat.

* Kötegelt feldolgozás: a beszúrási műveletek végrehajtásakor a `-batch` kapcsoló használatával a Sqoop több beszúrást hajt végre az INSERT művelet végrehajtása helyett. 

## <a name="next-steps"></a>További lépések
Most megtanulta, hogyan használhatja a Sqoop-t. További tudnivalókért lásd:

* Az [Apache Oozie és a HDInsight használata](../hdinsight-use-oozie-linux-mac.md): Sqoop művelet használata Oozie-munkafolyamatokban.
* [Adatok feltöltése a HDInsight-be](../hdinsight-upload-data.md): további módszereket talál az adatok HDInsight vagy Azure Blob Storage-ba való feltöltéséhez.

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

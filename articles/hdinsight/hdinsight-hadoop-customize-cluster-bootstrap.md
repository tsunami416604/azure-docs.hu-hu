---
title: Használatával rendszerindítási – Azure HDInsight-fürtök testre szabása
description: Megtudhatja, hogyan szabhatja testre a HDInsight-fürtök bootstrap használatával.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: bfa36cfeda514be0941481b0e4ed5ab9b3669b54
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238097"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Bootstrap használatával HDInsight-fürtök testre szabása

Egyes esetekben szeretné konfigurálni a konfigurációs fájlokat, többek között:

* clusterIdentity.xml
* Core-site.xml
* Gateway.XML
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* Hive-env.xml
* Hive-site.xml
* mapred-hely
* az oozie-site.xml
* az oozie-env.xml
* a Storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* Server.Properties (kafka-közvetítő konfigurálása)

Használja a rendszerindítási három módszer áll rendelkezésre:

* Azure PowerShell használatával
* A .NET SDK használata
* Az Azure Resource Manager sablonjainak használata

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

További összetevők telepítése HDInsight-fürtön a létrehozás ideje alatt a további információkért lásd:

* [Fürtök testreszabása a HDInsight használatával Script Action (Linux)](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="use-azure-powershell"></a>Azure PowerShell használatával
A következő PowerShell-kódot egy Hive-konfiguráció személyre szabható:

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

$config = New-AzureRmHDInsightClusterConfig `
    | Set-AzureRmHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzureRmHDInsightConfigValues `
        -HiveSite $hiveConfigValues 

New-AzureRmHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config 
```

Egy PowerShell-parancsfájl teljes működő található [függelék](#appendix-powershell-sample).

**Annak ellenőrzése, a módosítás:**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben kattintson a **HDInsight-fürtök**. Ha nem látja, kattintson a **minden szolgáltatás** első.
3. Kattintson a létrehozott fürtöt a PowerShell-parancsfájl használatával.
4. Kattintson a **irányítópult** az Ambari felhasználói felületén nyissa meg a panel tetején.
5. Kattintson a **Hive** a bal oldali menüből.
6. Kattintson a **hiveserver2-n keresztül** a **összefoglalás**.
7. Kattintson a **Configs** fülre.
8. Kattintson a **Hive** a bal oldali menüből.
9. Kattintson a **speciális** fülre.
10. Görgessen le, majd bontsa ki a **hive-hely speciális**.
11. Keressen **hive.metastore.client.socket.timeout** szakaszában.

Néhány további példák a testreszabás, más konfigurációs fájlokat:

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```
További információkért lásd: Determine Azim Uddin blog [testreszabása HDInsight-fürt létrehozása](https://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).

## <a name="use-net-sdk"></a>A .NET SDK használata
Lásd: [a HDInsight .NET SDK használatával Linux-alapú fürtök](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## <a name="use-resource-manager-template"></a>Használja a Resource Manager-sablon
Rendszerindítási használhatja a Resource Manager-sablon:

```json
"configurations": {
    �
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![HDInsight Hadoop fürt bootstrap Azure Resource Manager-sablon testre szabása](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Lásd még
* [A HDInsight Hadoop-fürtök létrehozása] [ hdinsight-provision-cluster] útmutatás egy HDInsight-fürt létrehozása más egyéni beállításai használatával.
* [Parancsfájlművelet-parancsfájlok fejlesztése a HDInsight][hdinsight-write-script]
* [Spark telepítése és használata HDInsight-fürtökön][hdinsight-install-spark]
* [Soir telepítése és használata HDInsight-fürtökön](hdinsight-hadoop-solr-install.md).
* [Giraph telepítése és használata HDInsight-fürtökön](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fürt létrehozása során szakaszai"

## <a name="appendix-powershell-sample"></a>A függelék: PowerShell-minta
A PowerShell-szkript létrehoz egy HDInsight-fürtöt, és személyre szabható egy Hive-beállítást:

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>" 
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US 2"
#endregion

# Treat all errors as terminating
$ErrorActionPreference = "Stop"

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Connect-AzureRmAccount}
#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzureRmResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -Type Standard_GRS

$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
$defaultStorageContext = New-AzureStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey
New-AzureStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

$config = New-AzureRmHDInsightClusterConfig `
    | Set-AzureRmHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzureRmHDInsightConfigValues `
        -HiveSite $hiveConfigValues 

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzureRmHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName

#endregion
```

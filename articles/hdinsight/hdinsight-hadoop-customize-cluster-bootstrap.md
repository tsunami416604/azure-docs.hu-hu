---
title: Azure HDInsight-fürt konfigurációinak testreszabása a bootstrap használatával
description: Ismerje meg, hogyan szabhatja testre a HDInsight-fürt konfigurációját a .net, a PowerShell és a Resource Manager-sablonok használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: e641340ac04415ee4a20cda2bc09bbdbef9802a6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389505"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>HDInsight-fürtök testreszabása a bootstrap használatával

A rendszerindítási parancsfájlok lehetővé teszik, hogy programozott módon telepítse és konfigurálja az összetevőket az Azure HDInsight-ben.

A HDInsight-fürt létrehozásakor három módszer van a konfigurációs fájlok beállításainak beállítására:

* Azure PowerShell használatával
* A .NET SDK használata
* Azure Resource Manager sablon használata

Ilyen programozási módszerek használatával például a következő fájlokban konfigurálhatja a beállításokat:

* clusterIdentity.xml
* Core-site. XML
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred – hely
* oozie-site. XML
* oozie-env.xml
* Storm-site. XML
* tez-site.xml
* webhcat-site.xml
* yarn-site. XML
* Server. Properties (Kafka-Broker konfiguráció)

További információ a HDInsight-fürt további összetevőinek a létrehozás ideje alatt történő telepítéséről: [HDInsight-fürtök testreszabása parancsfájl-művelettel (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="prerequisites"></a>Előfeltételek

* Ha a PowerShellt használja, szüksége lesz az az [modulra](https://docs.microsoft.com/powershell/azure/overview).

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

A következő PowerShell-kód testreszab egy [Apache Hive](https://hive.apache.org/) konfigurációt:

> [!IMPORTANT]  
> Előfordulhat, hogy a `Spark2Defaults` paramétert a [Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue)használatával kell használni. Az alábbi kódrészletben látható üres értékeket adhat át a paraméternek.

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90s" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

New-AzHDInsightCluster `
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

A [függelékben](#appendix-powershell-sample)található egy teljes körűen működő PowerShell-parancsfájl.

**A módosítás ellenőrzése:**

1. Navigáljon `https://CLUSTERNAME.azurehdinsight.net/`, ahol a `CLUSTERNAME` a fürt neve.
1. A bal oldali menüben navigáljon a **struktúra** > **konfigurációk** > **speciális**elemre.
1. Bontsa ki a **speciális kaptár-site**elemet.
1. Keresse meg a **kaptár. metaadattár. Client. socket. timeout** értéket, és erősítse meg, hogy az érték **90-es**.

Néhány példa más konfigurációs fájlok testreszabására:

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

## <a name="use-net-sdk"></a>A .NET SDK használata

Lásd: [Az Azure HDINSIGHT SDK for .net](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).

## <a name="use-resource-manager-template"></a>Resource Manager-sablon használata

A Bootstrap a Resource Manager-sablonban használható:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![A Hadoop testreszabja a fürt rendszerindítási Azure Resource Manager sablonját](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Lásd még

* [Apache Hadoop-fürtök létrehozása a HDInsight-ben](hdinsight-hadoop-provision-linux-clusters.md) útmutatást nyújt a HDInsight-fürtök más egyéni beállítások használatával történő létrehozásához.
* [Parancsfájl-műveleti parancsfájlok fejlesztése a HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Apache Spark telepítése és használata HDInsight-fürtökön](spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [Apache Giraph telepítése és használata HDInsight-fürtökön](hdinsight-hadoop-giraph-install.md).

## <a name="appendix-powershell-sample"></a>Függelék: PowerShell-minta

Ez a PowerShell-szkript létrehoz egy HDInsight-fürtöt, és testreszabja a kaptár beállításait. Ügyeljen arra, hogy `$nameToken`, `$httpPassword`és `$sshPassword`értékeit adja meg.

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>"
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = '<ENTER A PASSWORD>'

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = '<ENTER A PASSWORD>'
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

$location = "East US"
#endregion


####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

# Note: Storage account kind BlobStorage cannot be used as primary storage.

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90s"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
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
Get-AzHDInsightCluster `
    -ClusterName $hdinsightClusterName

#endregion
```

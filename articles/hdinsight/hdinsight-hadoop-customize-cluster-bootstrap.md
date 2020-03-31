---
title: Az Azure HDInsight-fürtkonfigurációk testreszabása a rendszerindítási beállítással
description: Megtudhatja, hogy miként szabhatja testre a HDInsight fürtkonfigurációját programozott módon a .Net, a PowerShell és az Erőforrás-kezelő sablonjaival.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: e641340ac04415ee4a20cda2bc09bbdbef9802a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272525"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>A HDInsight-fürtök testreszabása a Bootstrap használatával

A Bootstrap parancsfájlok lehetővé teszik az összetevők telepítését és konfigurálását az Azure HDInsightban programozott módon.

A HDInsight-fürt létrehozásakor három módszer közül lehet beállítani a konfigurációs fájl beállításait:

* Azure PowerShell használatával
* A .NET SDK használata
* Az Azure Resource Manager sablon használata

Az alábbi programmódszerek segítségével például az alábbi fájlok beállításait állíthatja be:

* clusterIdentity.xml
* core-site.xml
* átjáró.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* struktúra-env.xml
* struktúra-site.xml
* térképpel-telek
* oozie-site.xml
* oozie-env.xml
* vihar-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* server.properties (kafka-broker konfiguráció)

További összetevők hdinsight-fürtre történő telepítéséről a létrehozás idáig a [HDInsight-fürtök testreszabása parancsfájlművelet (Linux) használatával](hdinsight-hadoop-customize-cluster-linux.md)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

* A PowerShell használata esetén szüksége lesz az [Az modulra.](https://docs.microsoft.com/powershell/azure/overview)

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

A következő PowerShell-kód testre szabja az [Apache Hive-konfigurációt:](https://hive.apache.org/)

> [!IMPORTANT]  
> Előfordulhat, `Spark2Defaults` hogy a paramétert az [Add-AzHDInsightConfigValue értékkel](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue)kell használni. Az üres értékeket átadhatja a paraméternek az alábbi kódpéldában látható módon.

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

A teljes működő PowerShell-parancsfájl a [függelékben](#appendix-powershell-sample)található.

**A módosítás ellenőrzése:**

1. Keresse `https://CLUSTERNAME.azurehdinsight.net/` meg, hogy hol `CLUSTERNAME` található a fürt neve.
1. A bal oldali menüben keresse meg a **Hive** > **Configs** > **Advanced menüt.**
1. Bontsa ki **a Speciális struktúrawebhely csomópontot.**
1. Keresse meg **a hive.metastore.client.socket.timeout** fájlt, és ellenőrizze, hogy az érték **90s.**

Néhány további minta más konfigurációs fájlok testreszabásához:

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

Lásd: [Azure HDInsight SDK for .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).

## <a name="use-resource-manager-template"></a>Resource Manager-sablon használata

A rendszerindítási sablon t az Erőforrás-kezelő sablonban használhatja:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![A Hadoop testre szabja a fürtindítási Azure Resource Manager-sablont](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Lásd még

* [Hozzon létre Apache Hadoop-fürtöket a HDInsightban,](hdinsight-hadoop-provision-linux-clusters.md) amely útmutatást nyújt a HDInsight-fürtök más egyéni beállítások kal történő létrehozásához.
* [Parancsfájl-műveletparancsfájlok fejlesztése a HDInsighthoz](hdinsight-hadoop-script-actions-linux.md)
* [Az Apache Spark telepítése és használata HDInsight-fürtökön](spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [Telepítse és használja az Apache Giraph-ot HDInsight-fürtökön.](hdinsight-hadoop-giraph-install.md)

## <a name="appendix-powershell-sample"></a>Függelék: PowerShell-minta

Ez a PowerShell-parancsfájl létrehoz egy HDInsight-fürtöt, és testre szabja a Hive-beállítást. Ügyeljen arra, hogy `$nameToken` `$httpPassword`megadja `$sshPassword`a és a értékét.

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

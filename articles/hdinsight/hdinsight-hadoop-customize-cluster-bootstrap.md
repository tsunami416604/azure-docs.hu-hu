---
title: Testreszabása a bootstrap használatával Azure HDInsight-fürt konfigurációk
description: Ismerje meg, hogyan szabhatja testre a HDInsight-fürt konfigurációját programozott módon a .net, PowerShell és a Resource Manager használatával sablonokat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: b1bc0a68a9cf52e886c0664a474a4dbb75126698
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60735960"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Bootstrap használatával HDInsight-fürtök testre szabása

Rendszer-indításkori parancsprogramok telepítése és konfigurálása az Azure HDInsight szoftveresen összetevők teszi lehetővé. 

Nincsenek három módszer állíthatja be a konfigurációs fájl beállításait, mint a HDInsight-fürt létrehozása:

* Azure PowerShell használatával
* A .NET SDK használata
* Az Azure Resource Manager sablonjainak használata

Például a programozott módszerekkel konfigurálhatja ezeket a fájlokat a:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-hely
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* Server.Properties (kafka-közvetítő konfigurálása)

Információk a HDInsight-fürtön további összetevők telepítése során a létrehozás ideje: [testreszabása HDInsight-fürtök használatával a Script Action (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="prerequisites"></a>Előfeltételek

* Ha a PowerShell használatával, akkor a [Az modul](https://docs.microsoft.com/powershell/azure/overview).

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

A következő PowerShell-kóddal személyre szabható- [Apache Hive](https://hive.apache.org/) konfiguráció:

> [!IMPORTANT]  
> A paraméter `Spark2Defaults` használható kell [Add-AzHDInsightConfigValues](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalues). Üres értékeket adhat át a paramétert, a kódot az alábbi példában látható módon.


```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValues `
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

## <a name="use-net-sdk"></a>A .NET SDK használata
Lásd: [a HDInsight .NET SDK használatával Linux-alapú fürtök](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## <a name="use-resource-manager-template"></a>Használja a Resource Manager-sablon
Rendszerindítási használhatja a Resource Manager-sablon:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![HDInsight Hadoop fürt bootstrap Azure Resource Manager-sablon testre szabása](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Lásd még
* [A HDInsight Apache Hadoop-fürtök létrehozása] [ hdinsight-provision-cluster] útmutatás egy HDInsight-fürt létrehozása más egyéni beállításai használatával.
* [Parancsfájlművelet-parancsfájlok fejlesztése a HDInsight][hdinsight-write-script]
* [Telepítse, és az Apache Spark használata a HDInsight-fürtökön][hdinsight-install-spark]
* [Telepítse, és az Apache Giraph használata a HDInsight-fürtökön](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions-linux.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fürt létrehozása során szakaszai"

## <a name="appendix-powershell-sample"></a>A függelék: PowerShell-minta

A PowerShell-szkript létrehoz egy HDInsight-fürtöt, és személyre szabható egy Hive-beállítást. Ügyeljen arra, hogy adjon meg értéket a `$nameToken`, `$httpPassword`, és `$sshPassword`.

> [!IMPORTANT]  
> A tartozó értékeket `DefaultStorageAccount`, és `DefaultStorageContainer` a rendszer nem adja vissza [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) amikor [biztonságos átvitelre](../storage/common/storage-require-secure-transfer.md) engedélyezve van a tárfiókon.

> [!WARNING]  
> A tárfiók típusának `BlobStorage` HDInsight-fürtök esetén nem használható.


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
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValues `
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

---
title: Azure HDInsight-fürt konfigurációinak testreszabása a bootstrap használatával
description: Ismerje meg, hogyan szabhatja testre a HDInsight-fürt konfigurációját a .net, a PowerShell és a Resource Manager-sablonok használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 15d08b14e38f097e8e9c3e0db893efb1d6efe44d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098668"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>HDInsight-fürtök testreszabása a bootstrap használatával

A rendszerindítási parancsfájlok lehetővé teszik, hogy programozott módon telepítse és konfigurálja az összetevőket az Azure HDInsight-ben.

A HDInsight-fürt létrehozásakor három módszer van a konfigurációs fájlok beállításainak beállítására:

* Azure PowerShell használatával
* A .NET SDK használata
* Az Azure Resource Manager sablonjainak használata

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
> Lehetséges, `Spark2Defaults` hogy a paramétert a [Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue)használatával kell használni. Az alábbi kódrészletben látható üres értékeket adhat át a paraméternek.

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

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

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben kattintson a **HDInsight-fürtök**elemre. Ha nem jelenik meg, kattintson a **minden szolgáltatás** elemre.
3. Kattintson az imént létrehozott fürtre a PowerShell-parancsfájl használatával.
4. Kattintson a panel tetején található **irányítópultra** a Ambari felhasználói felületének megnyitásához.
5. A bal oldali menüben kattintson a **kaptár** elemre.
6. Kattintson a **HiveServer2** elemre az **Összefoglalás**listából.
7. Kattintson a **konfigurációk** fülre.
8. A bal oldali menüben kattintson a **kaptár** elemre.
9. Kattintson a **speciális** fülre.
10. Görgessen le, majd bontsa ki a **speciális kaptár-site**elemet.
11. Keresse meg a **kaptár. metaadattár. Client. socket. timeout** szakaszt a következő szakaszban:.

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
Lásd: [Linux-alapú fürtök létrehozása a HDInsight a .net SDK használatával](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

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

* [Apache Hadoop-fürtök létrehozása a HDInsight-ben][hdinsight-provision-cluster] útmutatást nyújt a HDInsight-fürtök más egyéni beállítások használatával történő létrehozásához.
* [Parancsfájl-műveleti parancsfájlok fejlesztése a HDInsight][hdinsight-write-script]
* [Apache Spark telepítése és használata HDInsight-fürtökön][hdinsight-install-spark]
* [Apache Giraph telepítése és használata HDInsight-fürtökön](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions-linux.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Szakaszok a fürt létrehozása során"

## <a name="appendix-powershell-sample"></a>Függelék PowerShell-minta

Ez a PowerShell-szkript létrehoz egy HDInsight-fürtöt, és testreszabja a kaptár beállításait. Ügyeljen arra, hogy a, `$nameToken`a `$httpPassword`és `$sshPassword`a értékeket adja meg.

> [!WARNING]  
> HDInsight-fürtökhöz nem használható a Storage-fiók típusa `BlobStorage` .

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

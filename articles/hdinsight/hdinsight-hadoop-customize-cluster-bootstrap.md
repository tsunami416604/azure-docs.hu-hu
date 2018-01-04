---
title: "Rendszerindítási - Azure HDInsight-fürtök testreszabása |} Microsoft Docs"
description: "Ismerje meg, hogyan szabhatja testre a rendszerindítási HDInsight-fürtök."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ab2ebf0c-e961-4e95-8151-9724ee22d769
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: ea5453f98c427304fd0b437ba27846a008da2585
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Rendszerindítási HDInsight-fürtök testreszabása

Egyes esetekben konfigurálni szeretné a konfigurációs fájlokat, többek között:

* clusterIdentity.xml
* Core-site.xml
* Gateway.XML
* a hbase-env.xml
* a hbase-site.xml
* hdfs-site.xml
* Hive-env.xml
* Hive-site.xml
* mapred-hely
* oozie-site.xml
* oozie-env.xml
* a Storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* Server.Properties (kafka-broker-konfiguráció)

Rendszerindítási használandó három módszer áll rendelkezésre:

* Azure PowerShell használatával
* A .NET SDK használata
* Az Azure Resource Manager sablonjainak használata

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Információk a HDInsight-fürt további összetevők telepítése során a létrehozásának idejét lásd:

* [Script Action (Linux) HDInsight-fürtök testreszabása](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="use-azure-powershell"></a>Azure PowerShell használatával
A következő PowerShell-kódjába testreszabja a Hive-konfiguráció:

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

PowerShell parancsfájl teljes működő található [függelék](#appendix-powershell-sample).

**A módosítás ellenőrzése:**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a bal oldali menü **a HDInsight-fürtök**. Ha nem látja, kattintson a **további szolgáltatások** első.
3. Kattintson arra a fürtre, újonnan létrehozott a PowerShell-parancsfájl használatával.
4. Kattintson a **irányítópult** az Ambari felhasználói felületének megnyitásához a panel tetején.
5. Kattintson a **Hive** a bal oldali menüből.
6. Kattintson a **hiveserver2-n** a **összegzés**.
7. Kattintson a **Configs** fülre.
8. Kattintson a **Hive** a bal oldali menüből.
9. Kattintson a **speciális** fülre.
10. Görgessen le, majd bontsa ki a **hive-hely speciális**.
11. Keressen **hive.metastore.client.socket.timeout** szakaszában.

Néhány más konfigurációs fájlokat testreszabásáról további minták:

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
További információkért lásd: című Azim Uddin blog [testreszabása a HDInsight-fürt létrehozása](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).

## <a name="use-net-sdk"></a>A .NET SDK használata
Lásd: [fürtök létrehozása Linux-alapú hdinsight .NET SDK használatával](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## <a name="use-resource-manager-template"></a>Használja a Resource Manager-sablon
A Resource Manager-sablon rendszerindítási is használhatja:

```json
"configurations": {
    …
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![HDInsight Hadoop fürthöz bootstrap Azure Resource Manager sablon testreszabása](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Lásd még
* [Hdinsight Hadoop-fürtök létrehozása] [ hdinsight-provision-cluster] HDInsight-fürtök létrehozása más egyéni beállítások használatával kapcsolatos utasításokat tartalmazza.
* [A HDInsight parancsfájlművelet-parancsfájlok fejlesztése][hdinsight-write-script]
* [Telepítse, és válassza a Spark on HDInsight-fürtök][hdinsight-install-spark]
* [Telepítheti és használhatja a HDInsight-fürtök R][hdinsight-install-r]
* [Telepítheti és használhatja a HDInsight-fürtök Solr](hdinsight-hadoop-solr-install.md).
* [Telepítheti és használhatja a HDInsight-fürtök Giraph](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fürt létrehozása során szakaszból"

## <a name="appendix-powershell-sample"></a>A függelék: PowerShell-példa
A PowerShell parancsfájl HDInsight-fürtöt hoz létre, és egy Hive-beállítás testreszabása:

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
catch{Login-AzureRmAccount}
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

---
title: Apache Hadoop-fürtök kezelése a PowerShell segítségével – Azure HDInsight
description: Ismerje meg, hogyan hajthat végre felügyeleti feladatokat az Apache Hadoop-fürtökhöz a HDInsightban az Azure PowerShell használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/13/2020
ms.openlocfilehash: 104975e6424ed96d43434a588997957033c31d93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560354"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Apache Hadoop-fürtök kezelése a HDInsightban az Azure PowerShell használatával

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Az Azure PowerShell segítségével szabályozhatja és automatizálhatja a számítási feladatok üzembe helyezését és felügyeletét az Azure-ban. Ebben a cikkben megtudhatja, hogyan kezelheti az [Apache Hadoop-fürtöket](https://hadoop.apache.org/) az Azure HDInsightban az Azure PowerShell Az modul használatával. A HDInsight PowerShell-parancsmagok listáját az [Az.HDInsight hivatkozási száma](https://docs.microsoft.com/powershell/module/az.hdinsight)tartalmazza.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A PowerShell [Az modul](https://docs.microsoft.com/powershell/azure/overview) telepítve van.

## <a name="create-clusters"></a>Fürtök létrehozása

Lásd: [Linux-alapú fürtök létrehozása a HDInsightban az Azure PowerShell használatával](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Fürtök listázása

Az alábbi paranccsal listázza az aktuális előfizetés összes fürtjeit:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Fürt megjelenítése

A következő paranccsal megjelenítheti egy adott fürt adatait az aktuális előfizetésben:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Fürtök törlése

Fürt törlése a következő paranccsal:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

A fürt is törölhető a fürtöt tartalmazó erőforráscsoport eltávolításával. Az erőforráscsoport törlése törli a csoport összes erőforrását, beleértve az alapértelmezett tárfiókot is.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Fürtök méretezése

A fürtméretezési szolgáltatás lehetővé teszi, hogy módosítsa az Azure HDInsightban futó fürt által használt munkavégző csomópontok számát anélkül, hogy újra létre kellene hoznia a fürtöt. Ha módosítani szeretné a Hadoop-fürt méretét az Azure PowerShell használatával, futtassa a következő parancsot egy ügyfélgépről:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 A fürtök méretezéséről a [HDInsight-fürtök méretezése](./hdinsight-scaling-best-practices.md)című témakörben talál további információt.

## <a name="update-http-user-credentials"></a>HTTP-felhasználói hitelesítő adatok frissítése

[A Set-AzHDInsightGatewayCredential](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightgatewaycredential) beállítja egy Azure HDInsight-fürt átjáró HTTP-hitelesítő adatait.

```powershell
$clusterName = "CLUSTERNAME"
$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Set-AzHDInsightGatewayCredential -ClusterName $clusterName -HttpCredential $credential
```

## <a name="find-the-default-storage-account"></a>Az alapértelmezett tárfiók megkeresése

A következő PowerShell-parancsfájl bemutatja, hogyan szerezheti be az alapértelmezett tárfiók nevét és a kapcsolódó információkat:

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```

## <a name="find-the-resource-group"></a>Az erőforráscsoport megkeresése

Erőforrás-kezelő módban minden HDInsight-fürt egy Azure-erőforráscsoporthoz tartozik.  Az erőforráscsoport megkeresése:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## <a name="submit-jobs"></a>Feladatok beküldése

**MapReduce feladatok elküldése**

Lásd: [A MapReduce futtatása a HDInsightban található példák futtatása.](hadoop/apache-hadoop-run-samples-linux.md)

**Apache Hive-feladatok beküldése**

Lásd: [Apache Hive-lekérdezések futtatása a PowerShell használatával.](hadoop/apache-hadoop-use-hive-powershell.md)

**Apache Sqoop-feladatok beküldése**

Lásd: [Az Apache Sqoop használata a HDInsight segítségével.](hadoop/hdinsight-use-sqoop.md)

**Apache Oozie-feladatok beküldése**

A HDInsight ban az [Apache Oozie használata az Apache Hadoop segítségével definiálhatja és futtathatja a munkafolyamatot.](hdinsight-use-oozie-linux-mac.md)

## <a name="upload-data-to-azure-blob-storage"></a>Adatok feltöltése az Azure Blob storage-ba

Lásd: [Adatok feltöltése a HDInsightba](hdinsight-upload-data.md).

## <a name="see-also"></a>Lásd még:

* [Az.HDInsight-parancsmagok](https://docs.microsoft.com/powershell/module/az.hdinsight/?view=azps-3.1.0#hdinsight)
* [Apache Hadoop-fürtök kezelése a HDInsightban az Azure Portal használatával](hdinsight-administer-use-portal-linux.md)
* [A HDInsight felügyelete parancssori felületen keresztül](hdinsight-administer-use-command-line.md)
* [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Hadoop-feladatok beküldése programozott módon](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Ismerkedés az Azure HDInsight-mal](hadoop/apache-hadoop-linux-tutorial-get-started.md)

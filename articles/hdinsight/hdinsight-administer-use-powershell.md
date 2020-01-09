---
title: Apache Hadoop-fürtök kezelése a PowerShell-lel – Azure HDInsight
description: Megtudhatja, hogyan hajthat végre felügyeleti feladatokat a HDInsight Apache Hadoop-fürtökön a Azure PowerShell használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: e37571b0078b4966aab9f505ddf88c2edb353197
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435627"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Apache Hadoop-fürtök kezelése a HDInsight-ben Azure PowerShell használatával

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

A Azure PowerShell az Azure-beli számítási feladatok üzembe helyezésének és kezelésének vezérlésére és automatizálására használható. Ebből a cikkből megtudhatja, hogyan kezelheti [Apache Hadoop](https://hadoop.apache.org/) -fürtöket az Azure HDInsight az Azure PowerShell az modul használatával. A HDInsight PowerShell-parancsmagok listájáért lásd az az [. HDInsight referenciát](https://docs.microsoft.com/powershell/module/az.hdinsight).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A PowerShell az [modul](https://docs.microsoft.com/powershell/azure/overview) telepítve van.

## <a name="create-clusters"></a>Fürtök létrehozása

Lásd: [Linux-alapú fürtök létrehozása a HDInsight-ben a Azure PowerShell használatával](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Fürtök listázása

Az alábbi paranccsal listázhatja az aktuális előfizetés összes fürtjét:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Fürt megjelenítése

A következő parancs használatával jelenítheti meg egy adott fürt részleteit az aktuális előfizetésben:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Fürtök törlése

Fürt törléséhez használja a következő parancsot:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

A fürtöt a fürtöt tartalmazó erőforráscsoport eltávolításával is törölheti. Egy erőforráscsoport törlése törli a csoport összes erőforrását, beleértve az alapértelmezett Storage-fiókot is.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Fürtök méretezése

A fürt skálázási funkciója lehetővé teszi az Azure HDInsight-ben futó fürt által használt munkavégző csomópontok számának módosítását anélkül, hogy újra létre kellene hozni a fürtöt. A Hadoop-fürt méretének Azure PowerShell használatával történő módosításához futtassa a következő parancsot egy ügyfélszámítógépről:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 A fürtök méretezésével kapcsolatos további információkért lásd: [HDInsight-fürtök](./hdinsight-scaling-best-practices.md)méretezése.

## <a name="grantrevoke-access"></a>Hozzáférés engedélyezése/visszavonása

A HDInsight-fürtök a következő HTTP-webszolgáltatásokkal rendelkeznek (az összes szolgáltatás REST-végpontokkal rendelkezik):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Alapértelmezés szerint ezek a szolgáltatások hozzáférést kapnak. Visszavonhatja vagy megadhatja a hozzáférést. Visszavonás:

```powershell
Revoke-AzHDInsightHttpServicesAccess -ClusterName <Cluster Name>
```

A következő megadása:

```powershell
$clusterName = "<HDInsight Cluster Name>"

# Credential option 1
$hadoopUserName = "admin"
$hadoopUserPassword = '<Enter the Password>'
$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

# Credential option 2
#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Grant-AzHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]  
> A hozzáférés megadásával/visszavonásával alaphelyzetbe állítja a fürt felhasználónevét és jelszavát.

A hozzáférés megadása és visszavonása a portálon keresztül is megtehető. Lásd: [Apache Hadoop-fürtök kezelése a HDInsight-ben a Azure Portal használatával](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>HTTP-felhasználói hitelesítő adatok frissítése

Ez ugyanaz az eljárás, mint a HTTP-hozzáférés engedélyezése/visszavonása. Ha a fürtön engedélyezve van a HTTP-hozzáférés, először vissza kell vonnia azt.  Ezután adja meg a hozzáférést az új HTTP-felhasználói hitelesítő adatokkal.

## <a name="find-the-default-storage-account"></a>Az alapértelmezett Storage-fiók keresése

A következő PowerShell-szkript bemutatja, hogyan kérheti le az alapértelmezett Storage-fiók nevét és a kapcsolódó információkat:

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

A Resource Manager módban minden HDInsight-fürt egy Azure-erőforráscsoporthoz tartozik.  Az erőforráscsoport megkeresése:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## <a name="submit-jobs"></a>Feladatok elküldése

**MapReduce-feladatok elküldése**

Lásd: [a HDInsight-ben található MapReduce-példák futtatása](hadoop/apache-hadoop-run-samples-linux.md).

**Apache Hive feladatok elküldése**

Lásd: [Apache Hive lekérdezések futtatása a PowerShell használatával](hadoop/apache-hadoop-use-hive-powershell.md).

**Apache Sqoop-feladatok elküldése**

Lásd: [az Apache Sqoop használata a HDInsight](hadoop/hdinsight-use-sqoop.md).

**Apache Oozie-feladatok elküldése**

Lásd: az [Apache Oozie és a Apache Hadoop használata munkafolyamatok definiálásához és futtatásához a HDInsight-ben](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Adatok feltöltése az Azure Blob Storage-ba

Lásd: [adatok feltöltése a HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>Lásd még:

* [Az az. HDInsight parancsmagok](https://docs.microsoft.com/powershell/module/az.hdinsight/?view=azps-3.1.0#hdinsight)
* [Apache Hadoop-fürtök kezelése a HDInsight-ben a Azure Portal használatával](hdinsight-administer-use-portal-linux.md)
* [HDInsight felügyelete parancssori felületen keresztül](hdinsight-administer-use-command-line.md)
* [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Hadoop feladatok programozott beküldése](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Ismerkedés az Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)

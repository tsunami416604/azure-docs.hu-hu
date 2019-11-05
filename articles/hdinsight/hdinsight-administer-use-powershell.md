---
title: Apache Hadoop-fürtök kezelése a PowerShell-lel – Azure HDInsight
description: Megtudhatja, hogyan hajthat végre felügyeleti feladatokat a HDInsight Apache Hadoop-fürtökön a Azure PowerShell használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2019
ms.openlocfilehash: b3cdc673d5d99229e3e6934d85cae55f79590830
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494401"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Apache Hadoop-fürtök kezelése a HDInsight-ben Azure PowerShell használatával
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

A Azure PowerShell az Azure-beli számítási feladatok üzembe helyezésének és kezelésének vezérlésére és automatizálására használható. Ebből a cikkből megtudhatja, hogyan kezelheti [Apache Hadoop](https://hadoop.apache.org/) -fürtöket az Azure HDInsight az Azure PowerShell az modul használatával. A HDInsight PowerShell-parancsmagok listájáért lásd az az [. HDInsight referenciát](https://docs.microsoft.com/powershell/module/az.hdinsight).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* A PowerShell az [modul](https://docs.microsoft.com/powershell/azure/overview) telepítve van.

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
A fürt skálázási funkciója lehetővé teszi az Azure HDInsight-ben futó fürt által használt munkavégző csomópontok számának módosítását anélkül, hogy újra létre kellene hozni a fürtöt.

Az adatcsomópontok számának a HDInsight által támogatott különböző típusú fürtökön való módosításának következményei:

* Apache Hadoop

    A Hadoop-fürtben futó munkavégző csomópontok száma zökkenőmentesen megnövelhető a függőben lévő vagy futó feladatok hatása nélkül. A művelet végrehajtása közben új feladatokat is el lehet küldeni. A skálázási művelet során fellépő hibák szabályosan kezelhetők, így a fürt mindig működőképes állapotban marad.

    Ha a Hadoop-fürtöket az adatcsomópontok számának csökkentésével csökkentették, a fürt egyes szolgáltatásai újraindulnak. A szolgáltatások újraindításakor a rendszer az összes futó és függőben lévő feladatot a skálázási művelet befejezésekor okozhatja. A művelet befejezését követően azonban újra elküldheti a feladatokat.
* Apache HBase

    A futása közben zökkenőmentesen hozzáadhat vagy eltávolíthat csomópontokat a HBase-fürthöz. A regionális kiszolgálók a skálázási művelet befejezését követően néhány percen belül automatikusan egyensúlyban vannak. A regionális kiszolgálókat azonban manuálisan is kiegyensúlyozhatja, ha bejelentkezik a átjárócsomóponthoz, majd a következő parancsokat futtatja egy parancssori ablakból:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

* Apache Storm

    A futása közben zökkenőmentesen hozzáadhat vagy eltávolíthat adatcsomópontokat a Storm-fürthöz. A skálázási művelet sikeres befejezése után azonban újra kell osztania a topológiát.

    Az újraelosztás kétféleképpen végezhető el:

  * Storm webes felhasználói felület
  * Parancssori felület (CLI) eszköz

    További részletekért tekintse meg az [Apache Storm dokumentációját](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) .

    A Storm webes felhasználói felülete elérhető a HDInsight-fürtön:

    ![HDInsight Storm skálázási egyensúly](./media/hdinsight-administer-use-powershell/portal-scale-cluster.png)

    Az alábbi példa bemutatja, hogyan használhatja a CLI-parancsot a Storm-topológia újraelosztásához:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

A Hadoop-fürt méretének Azure PowerShell használatával történő módosításához futtassa a következő parancsot egy ügyfélszámítógépről:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


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
$hadoopUserPassword = "<Enter the Password>"
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

* [A HDInsight parancsmag dokumentációja](https://msdn.microsoft.com/library/azure/dn479228.aspx)
* [Apache Hadoop-fürtök kezelése a HDInsight-ben a Azure Portal használatával](hdinsight-administer-use-portal-linux.md)
* [HDInsight felügyelete parancssori felületen keresztül](hdinsight-administer-use-command-line.md)
* [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Hadoop feladatok programozott beküldése](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Ismerkedés az Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)

---
title: Apache Hadoop-fürtök létrehozása az Azure CLI használatával – Azure HDInsight
description: Ismerje meg, hogyan hozhat létre Azure HDInsight-fürtöket a platformfüggetlen Azure CLI használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/03/2020
ms.openlocfilehash: b9d935e72c67b78484337e39e0897d4962340636
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77199041"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>HDInsight-fürtök létrehozása az Azure CLI használatával

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

A jelen dokumentum ban leírt lépések egy HDInsight 3.6-fürt létrehozása az Azure CLI használatával.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Azure CLI. Ha még nem telepítette az Azure CLI-t, a lépések [telepítéséről az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) című témakörben található.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Fürt létrehozása

1. Jelentkezzen be Azure-előfizetésbe. Ha az Azure Cloud Shell használatát tervezi, válassza a **Próbálja ki** a kódblokk jobb felső sarkában. Máshol, belép a követel alul:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Környezeti változók beállítása. A változók ebben a cikkben a Bash alapul. Más környezetekben kisebb eltérésekre lesz szükség. Lásd: [az az-hdinsight-create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) a fürt létrehozásának lehetséges paramétereinek teljes listáját.

    |Paraméter | Leírás |
    |---|---|
    |`--workernode-count`| A fürt munkavégző csomópontjainak száma. Ez a cikk `clusterSizeInNodes` a változót `--workernode-count`használja a számára átadott értékként. |
    |`--version`| A HDInsight fürtverziója. Ez a cikk `clusterVersion` a változót `--version`használja a számára átadott értékként. Lásd még: [Támogatott HDInsight-verziók](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| HDInsight-fürt típusa, például: hadoop, interactivehive, hbase, kafka, storm, spark, rserver, mlservices.  Ez a cikk `clusterType` a változót `--type`használja a számára átadott értékként. Lásd még: [Fürttípusok és konfiguráció](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type).|
    |`--component-version`|A különböző Hadoop-összetevők verziói, térbe választott verziókban, "component=version" formátumban. Ez a cikk `componentVersion` a változót `--component-version`használja a számára átadott értékként. Lásd még: [Hadoop alkatrészek](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).|

    Cserélje `RESOURCEGROUPNAME` `LOCATION`ki `CLUSTERNAME` `STORAGEACCOUNTNAME`a `PASSWORD` , , , , és a kívánt értékeket. Szükség szerint módosítsa a többi változó értékeit. Ezután adja meg a CLI parancsokat.

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'

    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. [Hozza létre az erőforráscsoportot](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) az alábbi parancs megadásával:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Az érvényes helyek listájához `az account list-locations` használja a parancsot, majd használja `name` az érték egyik helyét.

4. [Hozzon létre egy Azure Storage-fiókot](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) az alábbi parancs megadásával:

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. [Bontsa ki az elsődleges kulcsot az Azure Storage-fiókból,](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) és tárolja egy változóban az alábbi parancs megadásával:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Hozzon létre egy Azure Storage-tárolót](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) az alábbi parancs megadásával:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Hozza létre a HDInsight-fürtöt](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) a következő parancs megadásával:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --workernode-count $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > A HDInsight-fürtök különböző típusúak, amelyek megfelelnek a fürt höz hangolt munkaterhelésnek vagy technológiának. Nincs támogatott módszer olyan fürt létrehozására, amely több típust egyesít, például a Stormot és a HBase-t egy fürtön.

    A fürt létrehozási folyamata több percig is eltarthat. Általában 15 körül.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A cikk befejezése után érdemes törölni a fürtöt. A HDInsight segítségével az adatok az Azure Storage-ban tárolódnak, így biztonságosan törölheti a fürtöt, ha nincs használatban. A HDInsight-fürtért is díjat számítunk fel, még akkor is, ha nincs használatban. Mivel a fürt díjai sokszor több, mint a tárolási díjak, célszerű törölni a fürtöket, ha nincsenek használatban.

Az erőforrások eltávolításához adja meg az alábbi parancsok mindegyikét vagy egy részét:

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>További lépések

Most, hogy sikeresen létrehozott egy HDInsight-fürtöt az Azure CLI használatával, az alábbiakban megtudhatja, hogyan dolgozhat a fürttel:

### <a name="apache-hadoop-clusters"></a>Apache Hadoop-fürtök

* [Az Apache Hive használata a HDInsight segítségével](hadoop/hdinsight-use-hive.md)
* [A MapReduce használata a HDInsightsegítségével](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-fürtök

* [Ismerkedés az Apache HBase-lel a HDInsight-on](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Java alkalmazások fejlesztése Apache HBase alkalmazáshoz a HDInsight-on](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm-fürtök

* [Java topológiák fejlesztése apache stormhoz a HDInsighton](storm/apache-storm-develop-java-topology.md)
* [Python-összetevők használata az Apache Storm ban a HDInsighton](storm/apache-storm-develop-python-topology.md)
* [Topológiák üzembe helyezése és figyelése az Apache Storm segítségével a HDInsighton](storm/apache-storm-deploy-monitor-topology-linux.md)

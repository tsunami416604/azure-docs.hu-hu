---
title: Apache Hadoop-fürtök létrehozása az Azure CLI-vel – Azure HDInsight
description: Ismerje meg, hogyan hozhat létre Azure HDInsight-fürtöket a platformfüggetlen Azure CLI használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/03/2020
ms.openlocfilehash: b9d935e72c67b78484337e39e0897d4962340636
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77199041"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>HDInsight-fürtök létrehozása az Azure CLI használatával

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

A jelen dokumentumban ismertetett lépések – HDInsight 3,6-fürt létrehozása az Azure CLI használatával.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Azure CLI-vel. Ha még nem telepítette az Azure CLI-t, olvassa el a következő témakört: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Fürt létrehozása

1. Jelentkezzen be az Azure-előfizetésbe. Ha azt tervezi, hogy Azure Cloud Shell használ, válassza a **kipróbálás** lehetőséget a kód blokk jobb felső sarkában. Máskülönben adja meg az alábbi parancsot:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Környezeti változók beállítása A cikkben szereplő változók használata bash-alapú. Más környezetek esetében kisebb eltérésekre lesz szükség. A fürt létrehozásához szükséges paraméterek teljes listájáért tekintse meg az [az-hdinsight-Create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) lehetőséget.

    |Paraméter | Leírás |
    |---|---|
    |`--workernode-count`| A fürtben lévő munkavégző csomópontok száma. Ez a cikk a változót `clusterSizeInNodes` használja az átadott `--workernode-count`értékként. |
    |`--version`| A HDInsight-fürt verziója. Ez a cikk a változót `clusterVersion` használja az átadott `--version`értékként. Lásd még: [támogatott HDInsight-verziók](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| A HDInsight-fürt típusa, például: Hadoop, interactivehive, hbase, Kafka, Storm, Spark, Rserver, mlservices.  Ez a cikk a változót `clusterType` használja az átadott `--type`értékként. Lásd még: [fürtök típusai és konfigurálása](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type).|
    |`--component-version`|A különböző Hadoop-összetevők verziói az "összetevő = verzió" formátumban, szóközzel tagolt verziókban. Ez a cikk a változót `componentVersion` használja az átadott `--component-version`értékként. Lásd még: [Hadoop-összetevők](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).|

    `LOCATION`Cserélje `RESOURCEGROUPNAME`le a `CLUSTERNAME`, `STORAGEACCOUNTNAME`,, `PASSWORD` , és értéket a kívánt értékekre. Szükség szerint módosítsa a többi változó értékét. Ezután írja be a CLI-parancsokat.

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

3. [Hozza létre az erőforráscsoportot](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) az alábbi parancs beírásával:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Az érvényes helyszínek listájához használja a `az account list-locations` parancsot, majd használja az `name` érték egyik helyét.

4. [Hozzon létre egy Azure Storage-fiókot](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) az alábbi parancs beírásával:

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

5. [Bontsa ki az elsődleges kulcsot az Azure Storage-fiókból](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) , és tárolja egy változóban az alábbi parancs beírásával:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Hozzon létre egy Azure Storage-tárolót](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) az alábbi parancs beírásával:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Hozza létre a HDInsight-fürtöt](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) a következő parancs beírásával:

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
    > A HDInsight-fürtök különböző típusokból állnak, amelyek megfelelnek a fürthöz hangolt munkaterhelésnek vagy technológiának. Nincs támogatott módszer a több típust egyesítő fürt létrehozására, például a Storm és a HBase egy fürtön.

    A fürt létrehozási folyamata több percet is igénybe vehet. Általában körülbelül 15.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A cikk befejezése után érdemes törölni a fürtöt. A HDInsight az adatait az Azure Storage tárolja, így biztonságosan törölheti a fürtöt, ha az nincs használatban. A HDInsight-fürtökért is fizetnie kell, még akkor is, ha nincs használatban. Mivel a fürt díjai több időt vesznek igénybe, mint a tárterületre vonatkozó díjak, a gazdasági érzékek törlik a fürtöket, ha nincsenek használatban.

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

Most, hogy sikeresen létrehozott egy HDInsight-fürtöt az Azure CLI használatával, a következő paranccsal megismerheti a fürttel való munkát:

### <a name="apache-hadoop-clusters"></a>Fürtök Apache Hadoop

* [Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [A MapReduce használata a HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-fürtök

* [Ismerkedés az Apache HBase a HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Java-alkalmazások fejlesztése az Apache HBase a HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Fürtök Apache Storm

* [Java-topológiák fejlesztése a HDInsight Apache Storméhez](storm/apache-storm-develop-java-topology.md)
* [Python-összetevők használata a HDInsight-ben Apache Storm](storm/apache-storm-develop-python-topology.md)
* [Topológiák üzembe helyezése és figyelése Apache Storm a HDInsight-on](storm/apache-storm-deploy-monitor-topology-linux.md)

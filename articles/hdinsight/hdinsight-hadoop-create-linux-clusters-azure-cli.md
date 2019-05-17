---
title: Az Azure CLI-vel – Azure HDInsight használata az Apache Hadoop-fürtök létrehozása
description: Ismerje meg, hogyan hozhat létre HDInsight-fürtök a platformfüggetlen Azure CLI használatával.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: hrasheed
ms.openlocfilehash: 0a278cd98b0dd6c6d8f0fe9bfee81e5bafd4f543
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597671"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Az Azure CLI használatával HDInsight-fürtök létrehozása

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

A HDInsight 3.6-os fürtöt az Azure CLI használatával hoz létre a dokumentum útmutatóban ismertetett lépések.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Azure CLI. Ha még nem telepítette az Azure CLI-vel, tekintse meg [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) lépéseit.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Fürt létrehozása

1. Jelentkezzen be az Azure-előfizetéshez. Ha azt tervezi, hogy az Azure Cloud Shell használatához, majd egyszerűen válassza **kipróbálás** a kódblokk jobb felső sarkában. Más esetben adja meg az alábbi parancsot:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Állítsa be a környezeti változókat. Ez a cikk a változó használatát Bash alapul. Más környezetek enyhe változata lesz szükség. Lásd: [az hdinsight-create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) a fürtlétrehozáshoz lehetséges paraméterek teljes listája.

    |Paraméter | Leírás |
    |---|---|
    |`--size`| A fürtben található munkavégző csomópontok száma. Ez a cikk használja a változó `clusterSizeInNodes` átadott értéket `--size`. |
    |`--version`| A HDInsight-fürt verziója. Ez a cikk használja a változó `clusterVersion` átadott értéket `--version`. Lásd még: [HDInsight-verziók támogatott](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Írja be a HDInsight-fürt, például: interactivehive, hadoop, hbase, a kafka, storm, spark, az rserver, mlservices.  Ez a cikk használja a változó `clusterType` átadott értéket `--type`. Lásd még: [Fürt típusa és konfigurációs](./hdinsight-hadoop-provision-linux-clusters.md#cluster-types).|
    |`--component-version`|A különböző Hadoop-összetevők, szóközzel elválasztott verziókban a verziói "összetevő verzió =" formátumban. Ez a cikk használja a változó `componentVersion` átadott értéket `--component-version`. Lásd még: [Hadoop-összetevők](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).|

    Cserélje le `RESOURCEGROUPNAME`, `LOCATION`, `CLUSTERNAME`, `STORAGEACCOUNTNAME`, és `PASSWORD` a kívánt értékekkel. Igény szerint a változók értékeit módosíthatja. Ezután írja be a CLI-parancsok.

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

3. [Az erőforráscsoport létrehozásához](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) az alábbi parancs beírásával:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Érvényes helyek listájának megtekintéséhez használja a `az account list-locations` parancsot, és használja a helyek a `name` értéket.

4. [Az Azure storage-fiók létrehozása](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) az alábbi parancs beírásával:

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

5. [Bontsa ki az elsődleges kulcsot az Azure storage-fiókból](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) , és tárolja egy változóban az alábbi parancs beírásával:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Hozzon létre egy Azure storage-tároló](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) az alábbi parancs beírásával:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [A HDInsight-fürt létrehozása](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) a következő parancs beírásával:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --size $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-default-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > HDInsight fürtöket a különféle, amelyek megfelelnek a számítási feladatok vagy technológia, amely a fürt van hangolva származnak. Nincs támogatott módszer, amely ötvözi az több típus, például a Storm és a egy fürtön a HBase-fürt létrehozásához.

    A Fürtlétrehozási folyamat befejezéséhez több percig is eltarthat. Általában körülbelül 15.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A cikk befejezése után érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Adja meg az összes vagy néhány erőforrások eltávolítására a következő parancsokat:

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

## <a name="troubleshoot"></a>Az eszköz nem tudta a várt módon befejezni a szinkronizálást. A probléma megoldásának módjáról erre az üzenetre kattintva tájékozódhat.

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>További lépések

Most, hogy sikeresen létrehozott egy HDInsight-fürtöt az Azure CLI-vel, megtudhatja, hogyan használható a fürt a következő használatával:

### <a name="apache-hadoop-clusters"></a>Az Apache Hadoop-fürtök

* [Az Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [Az Apache Pig használata a HDInsight](hadoop/hdinsight-use-pig.md)
* [A MapReduce használata a HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Az Apache HBase-fürtök

* [A HDInsight Apache HBase használatának első lépései](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Az Apache HBase on HDInsight Java-alkalmazások fejlesztése](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm-fürtök

* [Java-topológiák fejlesztése a HDInsight az Apache stormmal](storm/apache-storm-develop-java-topology.md)
* [Az Apache Storm on HDInsight használata a Python-összetevők](storm/apache-storm-develop-python-topology.md)
* [Telepítheti és figyelheti a HDInsight Apache Storm-topológiák](storm/apache-storm-deploy-monitor-topology-linux.md)

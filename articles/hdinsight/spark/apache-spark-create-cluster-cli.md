---
title: 'Rövid útmutató: Apache Spark-fürtök az Azure CLI-vel – Azure HDInsight'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre Apache Spark-fürtöt az Azure HDInsight az Azure CLI használatával.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 71b5e9f0ece79633673b183ca7288852f42ca3c0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494710"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>Rövid útmutató: Apache Spark-fürt létrehozása az Azure HDInsight az Azure CLI használatával

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Apache Spark-fürtöt az Azure HDInsight az Azure CLI használatával. Az Apache Spark a memóriában végzett feldolgozás segítségével teszi lehetővé a gyors adatelemzést és fürtszámítást. Az [Azure parancssori felülete (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) a Microsoft többplatformos parancssori felülete az Azure-erőforrások kezeléséhez.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Azure CLI-vel. Ha még nem telepítette az Azure CLI-t, olvassa el a következő témakört: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-an-apache-spark-cluster"></a>Apache Spark-fürt létrehozása

1. Jelentkezzen be az Azure-előfizetésbe. Ha azt tervezi, hogy Azure Cloud Shell használni, egyszerűen válassza ki a **kipróbálom** lehetőséget a kód blokk jobb felső sarkában. Máskülönben adja meg az alábbi parancsot:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Környezeti változók beállítása Az ebben a rövid útmutatóban szereplő változók használata bash-alapú. Más környezetek esetében kisebb eltérésekre lesz szükség. Cserélje le a RESOURCEGROUPNAME, a LOCATION, a CLUSTERNAME, a STORAGEACCOUNTNAME és a PASSWORD értéket az alábbi kódrészletbe a kívánt értékekkel. Ezután írja be a CLI-parancsokat a környezeti változók beállításához.

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
    export clusterType=spark
    export componentVersion=Spark=2.3
    ```

3. Hozza létre az erőforráscsoportot az alábbi parancs beírásával:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

4. Hozzon létre egy Azure Storage-fiókot az alábbi parancs beírásával:

    ```azurecli-interactive
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. Bontsa ki az elsődleges kulcsot az Azure Storage-fiókból, és tárolja egy változóban az alábbi parancs beírásával:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. Hozzon létre egy Azure Storage-tárolót az alábbi parancs beírásával:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. Hozza létre a Apache Spark-fürtöt a következő parancs beírásával:

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

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyors üzembe helyezés befejezése után érdemes lehet törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

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

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Apache Spark-fürtöt az Azure HDInsight az Azure CLI használatával.  Folytassa a következő oktatóanyaggal, amelyben megtudhatja, hogyan használhatja a HDInsight Spark-fürtöt interaktív lekérdezések mintaadatokon való futtatására.

> [!div class="nextstepaction"]
> [Interaktív lekérdezések futtatása Apache Sparkon](./apache-spark-load-data-run-query.md)

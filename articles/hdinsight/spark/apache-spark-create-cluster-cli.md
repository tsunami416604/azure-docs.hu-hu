---
title: 'Gyors útmutató: Apache Spark-fürt létrehozása az Azure HDInsight az Azure CLI-vel'
description: Ez a rövid útmutató bemutatja, hogyan Apache Spark-fürt létrehozása az Azure HDInsight az Azure CLI használatával.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 72bdab9d7fb5c3019d97ffc4c92257c49ec2b8e5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066251"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>Gyors útmutató: Az Apache Spark-fürt létrehozása az Azure HDInsight, Azure CLI használatával

Ebben a rövid útmutatóban megismerheti, Apache Spark-fürt létrehozása az Azure HDInsight, Azure CLI használatával. Az Apache Spark a memóriában végzett feldolgozás segítségével teszi lehetővé a gyors adatelemzést és fürtszámítást. A [az Azure parancssori felület (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) van a Microsoft többplatformos parancssori felülete Azure-erőforrások kezeléséhez.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Azure CLI. Ha még nem telepítette az Azure CLI-vel, tekintse meg [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) lépéseit.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-an-apache-spark-cluster"></a>Az Apache Spark-fürt létrehozása

1. Jelentkezzen be az Azure-előfizetéshez. Ha azt tervezi, hogy az Azure Cloud Shell használatához, majd egyszerűen válassza **kipróbálás** a kódblokk jobb felső sarkában. Más esetben adja meg az alábbi parancsot:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Állítsa be a környezeti változókat. Ebben a rövid útmutatóban változó használatát Bash alapul. Más környezetek enyhe változata lesz szükség. Cserélje le RESOURCEGROUPNAME, a hely, a CLUSTERNAME, a STORAGEACCOUNTNAME és a JELSZÓT az alábbi kódrészlet a kívánt értékeket. Ezután írja be a környezeti változók beállítása CLI-parancsokat.

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

3. Az erőforráscsoport létrehozásához írja be az alábbi parancsot:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

4. Hozzon létre egy Azure storage-fiókot az alábbi parancs beírásával:

    ```azurecli-interactive
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. Bontsa ki az elsődleges kulcsot az Azure storage-fiókból, és tárolja egy változóban az alábbi parancs beírásával:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. Az alábbi parancs megadásával hozzon létre egy Azure storage-tároló:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. Az Apache Spark-fürt létrehozása a következő parancs beírásával:

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

Miután végzett a gyors üzembe helyezéssel, érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

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

## <a name="next-steps"></a>További lépések

Ebből a gyors útmutatóból megtudhatta, hogyan az Apache Spark-fürt létrehozása az Azure HDInsight, Azure CLI használatával.  Folytassa a következő oktatóanyaggal, amelyben megtudhatja, hogyan használhatja a HDInsight Spark-fürtöt interaktív lekérdezések mintaadatokon való futtatására.

> [!div class="nextstepaction"]
> [Az Apache Spark interaktív lekérdezések futtatása](./apache-spark-load-data-run-query.md)

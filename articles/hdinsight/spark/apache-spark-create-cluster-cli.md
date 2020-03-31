---
title: 'Rövid útmutató: Apache Spark-fürtök az Azure CLI-vel – Azure HDInsight'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre az Azure CLI-t egy Apache Spark-fürt az Azure HDInsightban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 02/03/2020
ms.openlocfilehash: e4679d5a04be7b8c0145fd93818e4187170b4194
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77049681"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>Rövid útmutató: Apache Spark-fürt létrehozása az Azure HDInsightban az Azure CLI használatával

Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre Apache Spark-fürtöt az Azure HDInsightban az Azure parancssori felületével (CLI). Az Azure HDInsight egy felügyelt, teljes körű, nyílt forráskódú elemzési szolgáltatás vállalatok részére. Az Apache Spark HDInsight-keretrendszere gyors adatelemzést és fürtös számítástechnikát tesz lehetővé a memórián belüli feldolgozással. Az Azure CLI a Microsoft platformfüggetlen parancssori felülete, amely Azure-erőforrások felügyeletére szolgál.

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Az Azure CLI,](https://docs.microsoft.com/cli/azure/install-azure-cli)ha nem szeretné használni az Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-an-apache-spark-cluster"></a>Apache Spark-fürt létrehozása

1. Jelentkezzen be Azure-előfizetésbe. Ha az Azure Cloud Shell használatát tervezi, válassza a **Kipróbálás** a következő kódblokk jobb felső sarkában. Máskülönben írja be a következő parancsot:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Környezeti változók beállítása. A változók használata ebben a rövid útmutatóban a Bash-en alapul. Más környezetekben kisebb eltérésekre lesz szükség. Cserélje le a RESOURCEGROUPNAME, LOCATION, CLUSTERNAME, STORAGEACCOUNTNAME és PASSWORD szavakat az alábbi kódrészletben a kívánt értékekkel. Ezután adja meg a CLI parancsokat a környezeti változók beállításához.

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

3. Hozza létre az erőforráscsoportot az alábbi parancs megadásával:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

4. Hozzon létre egy Azure storage-fiókot az alábbi parancs megadásával:

    ```azurecli-interactive
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. Bontsa ki az elsődleges kulcsot az Azure storage-fiókból, és tárolja egy változóban az alábbi parancs megadásával:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. Hozzon létre egy Azure-tárolót az alábbi parancs megadásával:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. Hozza létre az Apache Spark-fürtöt a következő parancs megadásával:

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

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutató befejezése után érdemes törölni a fürtöt. A HDInsight segítségével az adatok az Azure Storage-ban tárolódnak, így biztonságosan törölheti a fürtöt, ha nincs használatban. A HDInsight-fürtért is díjat kell fizetnie, még akkor is, ha nincs használatban. Mivel a fürt díjai sokszor több, mint a tárolási díjak, célszerű törölni a fürtöket, ha nincsenek használatban.

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

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Apache Spark-fürtöt az Azure HDInsightban az Azure CLI használatával.  A következő oktatóanyagra lépve megtudhatja, hogyan használhatja a HDInsight-fürtöt a mintaadatokon futó interaktív lekérdezések futtatásához.

> [!div class="nextstepaction"]
> [Interaktív lekérdezések futtatása az Apache Sparkon](./apache-spark-load-data-run-query.md)

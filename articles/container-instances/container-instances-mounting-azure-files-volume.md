---
title: "Azure-tároló példányát az Azure fájlok kötet csatlakoztatása"
description: "Útmutató: Azure tároló osztályt állapot megőrizni az Azure fájlok kötet csatlakoztatása"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 12/05/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: b2e8e27cecb4d1225e378690063b42f5d5242868
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="mount-an-azure-file-share-with-azure-container-instances"></a>Azure-tároló osztályt az Azure fájlmegosztások csatlakoztatása

Alapértelmezés szerint Azure tároló példányok állapot nélküli alkalmazások. Ha a tároló összeomlik, vagy leállítja, annak teljes állapota elvész. Állapot élettartama meghaladja a tároló megőrizni, a kötet csatlakoztatnia kell külső áruházban. Ez a cikk bemutatja, hogyan használható Azure tároló osztályt egy Azure fájlmegosztás csatlakoztatásához.

## <a name="create-an-azure-file-share"></a>Az Azure-fájlmegosztás létrehozása

Az Azure fájlmegosztások használatához Azure tároló osztályt, akkor létre kell hoznia. A következő parancsprogrammal hozzon létre egy tárfiókot, a fájlmegosztás és a megosztás saját magát. A tárfiók nevének globálisan egyedi, kell lennie, a parancsfájl egy véletlenszerű értékét hozzáadja a kezdőpontját meghatározó karakterlánc.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -l $ACI_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable. The following 'az storage share create' command
# references this environment variable when creating the Azure file share.
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="acquire-storage-account-access-details"></a>Szerezzen be tárfiókadatok hozzáférés

Az Azure fájlmegosztások csatlakoztatása kötetként az Azure-tároló példányok, három értékek kell: a tárfiók nevét, a megosztás neve és a hozzáférési kulcsot.

Ha követte a fenti parancsfájl, a tárfiók nevének végén véletlenszerű értéket hozták létre. A végső karakterláncban (beleértve a véletlenszerű része) lekérdezéséhez használja a következő parancsokat:

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCOUNT
```

A megosztásnév már ismert (definiálva *acishare* a parancsfájlban), hogy most már a tárfiók hívóbetűjét, amely a következő paranccsal található összes:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" -o tsv)
echo $STORAGE_KEY
```

## <a name="deploy-the-container-and-mount-the-volume"></a>A tároló üzembe, és a kötet csatlakoztatása

Az Azure fájlmegosztások csatlakoztatása a tárolóban lévő kötetként, adja meg a megosztás és a kötet csatlakoztatási pontjának a tárolóhoz létrehozásakor [az tároló létrehozása](/cli/azure/container#az_container_create). Ha követte a fenti lépéseket, akkor is csatlakoztathatja a megosztáshoz a következő paranccsal hozhat létre tárolót, korábban létrehozott:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image seanmckenna/aci-hellofiles \
    --ip-address Public \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

## <a name="manage-files-in-mounted-volume"></a>Csatlakoztatott kötet lévő fájlok kezeléséhez

A tároló elindul, ha a egyszerű webalkalmazást telepített keresztül is használhatja a [seanmckenna/aci-hellofiles](https://hub.docker.com/r/seanmckenna/aci-hellofiles/) kép kezelése az Azure-fájlmegosztáshoz megadott csatlakoztatási elérési úton található fájlokat. A web app és az IP-cím beszerzése a [az tároló megjelenítése](/cli/azure/container#az_container_show) parancs:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles -o table
```

Használhatja a [Azure-portálon](https://portal.azure.com) vagy egy eszköz, például a [Microsoft Azure Tártallózó](https://storageexplorer.com) kérhető le, és vizsgálja meg a fájlmegosztás írni a fájlt.

## <a name="next-steps"></a>Következő lépések

További tudnivalók a következő kapcsolatot: [Azure tároló példányok és tároló orchestrators](container-instances-orchestrator-relationship.md).

---
title: Az Azure Container Instances szolgáltatásban az Azure Files kötet csatlakoztatási
description: Ismerje meg, hogyan csatlakoztathatja az Azure Files-köteten is tartalmaz az Azure Container Instances állapota
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 02/20/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 83c86d8310aff80f148e878261ba33b01846006b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441323"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Csatlakoztassa az Azure-fájlmegosztások az Azure Container Instances szolgáltatásban

Alapértelmezés szerint az Azure Container Instances nélküliek. Ha a tároló összeomlik, vagy leáll, az összes állapotában elvész. A tároló állapotának túli élettartamát is tartalmaz, a kötet csatlakoztatnia kell egy külső áruházból. Ez a cikk bemutatja, hogyan csatlakoztathat Azure-fájlmegosztások az Azure Container Instances szolgáltatásban való használatra.

> [!NOTE]
> Az Azure Files-megosztás csatlakoztatásával korlátozódik jelenleg Linux-tárolókat. Arra törekszünk, hogy idővel az összes funkció elérhető legyen a Windows-tárolókon is. Az egyes platformok közötti aktuális eltérésekről a [Azure Container Instances-kvóták és -régiók rendelkezésre állása](container-instances-quotas.md) részben tájékozódhat.

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása

Mielőtt Azure-fájlmegosztások az Azure Container Instances, akkor létre kell hoznia. A következő parancsprogrammal hozzon létre egy tárfiókot a fájlmegosztás és a megosztás magát. A tárfiók nevének globálisan egyedinek, kell lennie, a parancsfájl hozzáad egy véletlenszerű értéket alap karakterláncot.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Export the connection string as an environment variable. The following 'az storage share create' command
# references this environment variable when creating the Azure file share.
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $ACI_PERS_RESOURCE_GROUP --name $ACI_PERS_STORAGE_ACCOUNT_NAME --output tsv`

# Create the file share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="get-storage-credentials"></a>Tároló hitelesítő adatainak lekérése

Azure-fájlmegosztás csatlakoztatása az Azure Container Instancesben kötetként, akkor három értékre szüksége lesz: a tárfiók nevét, a megosztás nevét és a tárelérési kulcs.

Ha követte a fenti a parancsfájlt, a tárfiók nevének végén egy véletlenszerű értéket jött létre. A végső karakterláncban (beleértve a véletlenszerű rész) lekérdezés, használja a következő parancsokat:

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" --output tsv)
echo $STORAGE_ACCOUNT
```

A megosztásnév már ismert (részlete *acishare* a szkriptben), hogy most már a tárfiók hívóbetűjét, a következő paranccsal található összes:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume"></a>Tároló és a csatlakoztatási kötet üzembe helyezése

Azure-fájlmegosztás csatlakoztatása egy tárolóban kötetként, adja meg a megosztás és a kötet csatlakoztatási pont az a tároló létrehozásakor [az tároló létrehozása][az-container-create]. Ha követte a fenti lépéseket, csatlakoztathatja a korábban létrehozott tároló létrehozásához az alábbi parancs használatával:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image microsoft/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

A `--dns-name-label` értéknek egyedinek kell lennie abban az Azure-régióban, ahol a tárolópéldányt létrehozza. Frissítse az értéket az előző parancs, ha egy **DNS-névcímke** hibaüzenet jelenik meg, hogy a parancs futtatásakor.

## <a name="manage-files-in-mounted-volume"></a>Csatlakoztatott kötet található fájlok kezeléséhez

A tároló megkezdéséről keresztül üzembe helyezett egyszerű webalkalmazás használhatja a [microsoft/aci-hellofiles] [ aci-hellofiles] rendszerképet az Azure-fájlmegosztás a megadott csatlakoztatási elérési úton található fájlok kezeléséhez. Szerezze be a webalkalmazás teljesen minősített tartománynevét (FQDN) az a [az container show] [ az-container-show] parancsot:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn
```

Használhatja a [az Azure portal] [ portal] vagy egy eszköz, például a [Microsoft Azure Storage Explorer] [ storage-explorer] beolvasni és írni a fájl vizsgálata a fájlmegosztás.

## <a name="mount-multiple-volumes"></a>Több kötet csatlakoztatási

A tárolópéldány több kötet csatlakoztatása, telepítenie kell használatával egy [Azure Resource Manager-sablon](/azure/templates/microsoft.containerinstance/containergroups).

Először adja meg a megosztás részleteit, és adja meg a kötetek fürtjogosultsági a `volumes` Pole a `properties` szakaszában a sablont. Ha például létrehozta a két Azure-fájlmegosztások nevű *megosztas1* és *share2* tárfiókban *myStorageAccount*, a `volumes` tömb jelenik meg a következőhöz hasonló:

```json
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Ezután az egyes tárolók, amelyben szeretné csatlakoztatni a kötetek tárolócsoportban, töltse fel a `volumeMounts` Pole a `properties` a tároló-definíció szakasza. Például ez csatlakoztatja a két kötet *myvolume1* és *myvolume2*, korábban meghatározott:

```json
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

Tárolópéldány üzembe helyezésének Azure Resource Manager-sablonnal egy példát, olvassa el [többtárolós csoportok az Azure Container Instancesben üzembe helyezése](container-instances-multi-container-group.md).

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan csatlakoztathat egyéb kötettípusok az Azure Container Instances szolgáltatásban:

* [Az Azure-tároló példányát emptyDir kötet csatlakoztatása](container-instances-volume-emptydir.md)
* [Azure-tároló példányát gitRepo kötet csatlakoztatása](container-instances-volume-gitrepo.md)
* [Az Azure Container Instances szolgáltatásban titkos kötet csatlakoztatása](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/r/microsoft/aci-hellofiles/
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
---
title: Az Azure Container Instances szolgáltatásban az Azure Files kötet csatlakoztatási
description: Ismerje meg, hogyan csatlakoztathatja az Azure Files-köteten is tartalmaz az Azure Container Instances állapota
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/05/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 365264d40554f45533e2ddf0aeb9d85f3e8f8d2d
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370618"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Csatlakoztassa az Azure-fájlmegosztások az Azure Container Instances szolgáltatásban

Alapértelmezés szerint az Azure Container Instances nélküliek. Ha a tároló összeomlik, vagy leáll, az összes állapotában elvész. A tároló állapotának túli élettartamát is tartalmaz, a kötet csatlakoztatnia kell egy külső áruházból. Ez a cikk bemutatja, hogyan csatlakoztathat Azure-fájlmegosztások használatával létrehozott [Azure Files](../storage/files/storage-files-introduction.md) Azure Container Instances szolgáltatásban való használatra. Az Azure Files teljes körűen felügyelt felhőbeli fájlmegosztást nyújt, amely az iparági szabványként használt Server Message Block (SMB) protokollon keresztül érhető el. Hasonló az Azure-beli virtuális gépek Azure-fájlmegosztás a fájlmegosztás funkciók használata az Azure-fájlmegosztások az Azure Container Instances nyújt.

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

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Tároló hitelesítő adatainak lekérése

Azure-fájlmegosztás csatlakoztatása az Azure Container Instancesben kötetként, akkor három értékre szüksége lesz: a tárfiók nevét, a megosztás nevét és a tárelérési kulcs.

Ha követte a fenti a parancsfájlt, a tárfiók nevét a $ACI_PERS_STORAGE_ACCOUNT_NAME változóban van tárolva. Tekintse meg a fiók nevét, írja be:

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

A megosztásnév már ismert (részlete *acishare* a szkriptben), hogy most már a tárfiók hívóbetűjét, a következő paranccsal található összes:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume"></a>Tároló és a csatlakoztatási kötet üzembe helyezése

Azure-fájlmegosztás csatlakoztatása egy tárolóban kötetként, adja meg a megosztás és a kötet csatlakoztatási pont az a tároló létrehozásakor [az tároló létrehozása][az-container-create]. Ha követte a fenti lépéseket, csatlakoztathatja a korábban létrehozott tároló létrehozásához az alábbi parancs használatával:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

A `--dns-name-label` értéknek egyedinek kell lennie abban az Azure-régióban, ahol a tárolópéldányt létrehozza. Frissítse az értéket az előző parancs, ha egy **DNS-névcímke** hibaüzenet jelenik meg, hogy a parancs futtatásakor.

## <a name="manage-files-in-mounted-volume"></a>Csatlakoztatott kötet található fájlok kezeléséhez

A tároló megkezdéséről is használhatja a Microsoft-n keresztül üzembe helyezett egyszerű webalkalmazás [aci-hellofiles] [ aci-hellofiles] kis méretű szöveges fájlok létrehozása az Azure-fájlmegosztás a megadott csatlakoztatási elérési úton a lemezképet. Szerezze be a webalkalmazás teljesen minősített tartománynevét (FQDN) az a [az container show] [ az-container-show] parancsot:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn
```

Használhatja a [az Azure portal] [ portal] vagy egy eszköz, például a [Microsoft Azure Storage Explorer] [ storage-explorer] beolvasni és írni a fájl vizsgálata a fájlmegosztás.

## <a name="mount-multiple-volumes"></a>Több kötet csatlakoztatási

A tárolópéldány több kötet csatlakoztatása, telepítenie kell használatával egy [Azure Resource Manager-sablon](/azure/templates/microsoft.containerinstance/containergroups) vagy egy YAML-fájlt.

Sablon használata, adja meg a megosztás részleteit, és adja meg a kötetek fürtjogosultsági a `volumes` Pole a `properties` szakaszában a sablont. Ha például létrehozta a két Azure-fájlmegosztások nevű *megosztas1* és *share2* tárfiókban *myStorageAccount*, a `volumes` tömb jelenik meg a következőhöz hasonló:

```JSON
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

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

Tárolópéldány üzembe helyezésének Azure Resource Manager-sablonnal egy példát, olvassa el [üzembe helyezése egy tárolócsoport](container-instances-multi-container-group.md). Egy példa egy YAML-fájllal: [YAML-többtárolós csoport üzembe helyezése](container-instances-multi-container-yaml.md)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan csatlakoztathat egyéb kötettípusok az Azure Container Instances szolgáltatásban:

* [Az Azure-tároló példányát emptyDir kötet csatlakoztatása](container-instances-volume-emptydir.md)
* [Azure-tároló példányát gitRepo kötet csatlakoztatása](container-instances-volume-gitrepo.md)
* [Az Azure Container Instances szolgáltatásban titkos kötet csatlakoztatása](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
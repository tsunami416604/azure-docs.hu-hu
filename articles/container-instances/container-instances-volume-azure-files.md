---
title: Az Azure Container Instances szolgáltatásban az Azure Files kötet csatlakoztatási
description: Ismerje meg, hogyan csatlakoztathatja az Azure Files-köteten is tartalmaz az Azure Container Instances állapota
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/08/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: bc09aa500743d608c0a3a7a379fe9584c9c55e9b
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657619"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Csatlakoztassa az Azure-fájlmegosztások az Azure Container Instances szolgáltatásban

Alapértelmezés szerint az Azure Container Instances nélküliek. Ha a tároló összeomlik, vagy leáll, az összes állapotában elvész. A tároló állapotának túli élettartamát is tartalmaz, a kötet csatlakoztatnia kell egy külső áruházból. Ez a cikk bemutatja, hogyan csatlakoztathat Azure-fájlmegosztások használatával létrehozott [Azure Files](../storage/files/storage-files-introduction.md) Azure Container Instances szolgáltatásban való használatra. Az Azure Files teljes körűen felügyelt felhőbeli fájlmegosztást nyújt, amely az iparági szabványként használt Server Message Block (SMB) protokollon keresztül érhető el. Hasonló az Azure-beli virtuális gépek Azure-fájlmegosztás a fájlmegosztás funkciók használata az Azure-fájlmegosztások az Azure Container Instances nyújt.

> [!NOTE]
> Az Azure Files-megosztás csatlakoztatásával korlátozódik jelenleg Linux-tárolókat. Amíg folyamatban van a Windows-tárolók idővel az összes funkció, annak aktuális platform különbségek a [áttekintése](container-instances-overview.md#linux-and-windows-containers).

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

## <a name="deploy-container-and-mount-volume---cli"></a>Tároló üzembe helyezése és a csatlakoztatási kötet – CLI

Azure-fájlmegosztás csatlakoztatása egy tárolóban kötetként az Azure CLI-vel, adja meg a megosztás és a kötet csatlakoztatási pont az a tároló létrehozásakor [az tároló létrehozása][az-container-create]. Ha követte a fenti lépéseket, csatlakoztathatja a korábban létrehozott tároló létrehozásához az alábbi parancs használatával:

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

A `--dns-name-label` érték az Azure-régió, ahol a tárolópéldányt létrehozza egyedinek kell lennie. Frissítse az értéket az előző parancs, ha egy **DNS-névcímke** hibaüzenet jelenik meg, hogy a parancs futtatásakor.

## <a name="manage-files-in-mounted-volume"></a>Csatlakoztatott kötet található fájlok kezeléséhez

A tároló megkezdéséről is használhatja a Microsoft-n keresztül üzembe helyezett egyszerű webalkalmazás [aci-hellofiles][aci-hellofiles] image to create small text files in the Azure file share at the mount path you specified. Obtain the web app's fully qualified domain name (FQDN) with the [az container show][az-container-show] parancsot:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn --output tsv
```

Szöveg használja az alkalmazást a mentés után is használhatja a [az Azure portal][portal] or a tool like the [Microsoft Azure Storage Explorer][storage-explorer] beolvasni és írni a fájlmegosztási a fájl vizsgálata.

## <a name="deploy-container-and-mount-volume---yaml"></a>Tároló üzembe helyezése és a kötet - YAML csatlakoztatásához

Is telepítheti egy tárolócsoport és egy tárolót az Azure CLI-vel a kötet csatlakoztatása és a egy [YAML sablon](container-instances-multi-container-yaml.md). YAML-sablon üzembe helyezése a előnyben részesített módszer, több tárolóból álló tárolócsoportok üzembe helyezésekor.

A következő YAML-sablon segítségével létrehozott egy tárolót az határozza meg egy tárolócsoport a `aci-hellofiles` kép. A tároló az Azure-fájlmegosztást csatlakoztathatnak *acishare* korábban létrehozott kötetként. Felsoroltak közül, adja meg a nevet és a tárfiók kulcsát, amelyen a fájlmegosztást a storage-fiókban. 

A CLI példához hasonlóan a `dnsNameLabel` érték az Azure-régió, ahol a tárolópéldányt létrehozza egyedinek kell lennie. Szükség esetén frissítse az értéket a YAML-fájlt.

```yaml
apiVersion: '2018-10-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

A YAML-sablon üzembe helyezéséhez mentse az előző YAML-nevű `deploy-aci.yaml`, majd hajtsa végre a [az tároló létrehozása][az-container-create] parancsot a `--file` paramétert:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Tároló és a csatlakoztatási kötet – erőforrás-kezelő telepítése

CLI-t és a YAML üzembe helyezés mellett egy tárolócsoport telepítheti és az egy Azure-tárolóban lévő kötet csatlakoztatása [Resource Manager-sablon](/azure/templates/microsoft.containerinstance/containergroups).

Első lépésként töltse fel a `volumes` tömb tárolócsoportban `properties` szakaszában a sablont. 

Ezt követően az egyes tárolók, amelyben szeretné a kötet csatlakoztatásához, töltse fel a `volumeMounts` Pole a `properties` a tároló-definíció szakasza.

A következő Resource Manager-sablon segítségével létrehozott egy tárolót az határozza meg egy tárolócsoport a `aci-hellofiles` kép. A tároló az Azure-fájlmegosztást csatlakoztathatnak *acishare* korábban létrehozott kötetként. Felsoroltak közül, adja meg a nevet és a tárfiók kulcsát, amelyen a fájlmegosztást a storage-fiókban. 

Az előző példához hasonlóan a `dnsNameLabel` érték az Azure-régió, ahol a tárolópéldányt létrehozza egyedinek kell lennie. Szükség esetén frissítse az értéket a sablonban.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

A Resource Manager-sablon üzembe helyezéséhez mentse a fenti JSON-nevű `deploy-aci.json`, majd hajtsa végre a [az csoport központi telepítésének létrehozása][az-group-deployment-create] parancsot a `--template-file` paramétert:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Több kötet csatlakoztatási

A tárolópéldány több kötet csatlakoztatása, telepítenie kell használatával egy [Azure Resource Manager-sablon](/azure/templates/microsoft.containerinstance/containergroups) vagy egy YAML-fájlt. Egy sablon vagy a YAML-fájlt szeretne használni, adja meg a megosztás részleteit, és a kötetek definiálása fürtjogosultsági a `volumes` Pole a `properties` szakaszában a sablont. 

Ha például létrehozta a két Azure-fájlmegosztások nevű *megosztas1* és *share2* storage-fiókban *myStorageAccount*, a `volumes` tömb egy Resource Manager a sablon a következőképpen fog a következőhöz hasonló:

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
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
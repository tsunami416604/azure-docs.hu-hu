---
title: Az Azure Files kötet csatlakoztatása tárolócsoportba
description: Megtudhatja, hogyan csatlakoztathat azure-fájlok kötetet az Azure Container Instances állapotának megőrzéséhez
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: f66890c503de8de9160f11fb28795012ae57daeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561337"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Azure-fájlmegosztás csatlakoztatása az Azure Container Instancesben

Az Azure Container Instances alapértelmezés szerint állapot nélküli. Ha a tároló összeomlik vagy leáll, az összes állapota elvész. Ha azt szeretné, hogy az állapot a tároló élettartamán túl is megmaradjon, külső tárból kell csatlakoztatnia egy kötetet. Ahogy ebben a cikkben látható, az Azure Container Instances az [Azure Files](../storage/files/storage-files-introduction.md)használatával létrehozott Azure-fájlmegosztáscsatlakoztatása. Az Azure Files teljes körűen felügyelt fájlmegosztásokat kínál az Azure Storage-ban, amelyek az iparági szabványnak megfelelő SMB protokollon keresztül érhetők el. Az Azure-fájlmegosztás azure container instances használatával fájlmegosztási funkciók hasonló azure-fájlmegosztás azure-beli virtuális gépek használatával.

> [!NOTE]
> Az Azure Files-megosztás csatlakoztatása jelenleg linuxos tárolókra korlátozódik. Az aktuális platformkülönbségek keresése az [áttekintésben.](container-instances-overview.md#linux-and-windows-containers)
>
> Az Azure Files-megosztás egy tárolópéldányhoz való csatlakoztatása hasonló a [Docker-kötéscsatlakoztatáshoz.](https://docs.docker.com/storage/bind-mounts/) Ne feledje, hogy ha egy megosztást olyan tárolókönyvtárba csatlakoztat, amelyben fájlok vagy könyvtárak találhatók, ezeket a fájlokat vagy könyvtárakat a csatlakoztatás eltakarja, és a tároló futása közben nem érhetők el.
>

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása

Az Azure-fájlmegosztást először létre kell hoznia, hogy aztán megoszthassa az Azure Container Instances-példánnyal. Futtassa a következő parancsfájlt a fájlmegosztás és maga a megosztás üzemeltetéséhez létrehozott tárfiók létrehozásához. A tárfiók nevének globálisan egyedinek kell lennie, ezért a szkript hozzáad egy véletlenszerű értéket az alapsztringhez.

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
az storage share create \
  --name $ACI_PERS_SHARE_NAME \
  --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Tároló hitelesítő adatainak lekérése

Ahhoz, hogy az Azure-fájlmegosztást kötetként csatlakoztassa az Azure Container Instancesben, három értékre lesz szüksége: a tárfiók nevére, a megosztás nevére és tárelérési kulcsra.

* **Tárfiók neve** – Ha az előző parancsfájlt használta, a `$ACI_PERS_STORAGE_ACCOUNT_NAME` tárfiók neve a változóban volt tárolva. A fiók nevének megtekintéséhez írja be a következőt:

  ```console
  echo $ACI_PERS_STORAGE_ACCOUNT_NAME
  ```

* **Megosztási név** – Ez az érték `acishare` már ismert (az előző parancsfájlban definiálva)

* **Tárfiók kulcsa** – Ez az érték a következő paranccsal található:

  ```azurecli-interactive
  STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
  echo $STORAGE_KEY
  ```

## <a name="deploy-container-and-mount-volume---cli"></a>Konténer- és csatlakoztatási térfogat üzembe helyezése - CLI

Ha az Azure CLI használatával kötetként szeretne azure-fájlmegosztást csatlakoztatni egy tárolóban, adja meg a megosztási és kötetcsatlakoztatási pontot, amikor létrehozza a tárolót az [az container create][az-container-create]használatával. Ha az előző lépéseket követte, a korábban létrehozott megosztást a következő paranccsal csatlakoztathatja egy tároló létrehozásához:

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

Az `--dns-name-label` értéknek egyedinek kell lennie az Azure-régióban, ahol létrehozza a tárolópéldányt. Frissítse az előző parancs értékét, ha a parancs végrehajtásakor **DNS-névfelirat-címke** hibaüzenet jelenik meg.

## <a name="manage-files-in-mounted-volume"></a>Fájlok kezelése csatlakoztatott köteten

Miután a tároló elindul, használhatja a Microsoft [aci-hellofiles][aci-hellofiles] lemezképen keresztül telepített egyszerű webalkalmazás segítségével kis szöveges fájlokat hozhat létre az Azure-fájlmegosztásban a megadott csatlakoztatási útvonalon. Szerezze be a webalkalmazás teljesen minősített tartománynevét (FQDN) az [az container show][az-container-show] paranccsal:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP \
  --name hellofiles --query ipAddress.fqdn --output tsv
```

Miután szöveget mentett az alkalmazással, használhatja az [Azure Portalon][portal] vagy egy eszköz, mint például a [Microsoft Azure Storage Explorer][storage-explorer] beolvasni és megvizsgálni a fájlmegosztásra írt fájlt vagy fájlokat.

## <a name="deploy-container-and-mount-volume---yaml"></a>Tároló és csatlakoztatási kötet telepítése – YAML

Egy tárolócsoportot is üzembe helyezhet, és az Azure CLI-vel és egy [YAML-sablonnal](container-instances-multi-container-yaml.md)egy tárolóban is csatlakoztathat kötetet. A YAML-sablon által iüzembe helyezés egy előnyben részesített módszer több tárolóból álló tárolócsoportok üzembe helyezésekor.

A következő YAML-sablon egy tárolócsoportot határoz `aci-hellofiles` meg egy, a lemezképpel létrehozott tárolóval. A tároló csatlakoztatja az Azure-fájlmegosztási *acishare* korábban kötetként létrehozott. Ha jel, adja meg a fájlmegosztást tároló tárfiók nevét és tárolási kulcsát. 

A CLI-példához, `dnsNameLabel` az értéknek egyedinek kell lennie az Azure-régióban, ahol létrehozza a tárolópéldányt. Szükség esetén frissítse a YAML-fájl értékét.

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

A YAML-sablonnal történő telepítéshez mentse az `deploy-aci.yaml`előző YAML-kódot egy `--file` fájlba, majd hajtsa végre az az container [create][az-container-create] parancsot a következő paraméterrel:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Tároló és csatlakoztatási kötet telepítése – Erőforrás-kezelő

A CLI és a YAML központi telepítése mellett üzembe helyezhet egy tárolócsoportot, és egy azure [Resource Manager-sablon](/azure/templates/microsoft.containerinstance/containergroups)használatával csatlakoztathat egy kötetet egy tárolóban.

Először a `volumes` sablon tárolócsoport-szakaszában `properties` népesítse be a tömböt. 

Ezután minden egyes tároló, amelyben szeretné csatlakoztatni a kötetet, feltölti a `volumeMounts` tömb a `properties` szakaszban a tároló definíciója.

A következő Erőforrás-kezelő sablon egy tárolócsoportot határoz `aci-hellofiles` meg egy, a lemezképpel létrehozott tárolóval. A tároló csatlakoztatja az Azure-fájlmegosztási *acishare* korábban kötetként létrehozott. Ha jel, adja meg a fájlmegosztást tároló tárfiók nevét és tárolási kulcsát. 

Mint az előző példákban, az `dnsNameLabel` érték egyedinek kell lennie az Azure-régióban, ahol létrehozza a tárolópéldányt. Szükség esetén frissítse a sablon értékét.

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

Az Erőforrás-kezelő sablonnal történő telepítéshez mentse az `deploy-aci.json`előző JSON-t egy `--template-file` fájlba, majd hajtsa végre az az csoport központi telepítési [létrehozási][az-group-deployment-create] parancsát a következő paraméterrel:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Több kötet csatlakoztatása

Több kötet csatlakoztatása egy tárolópéldányban, telepítenie kell egy [Azure Resource Manager sablon](/azure/templates/microsoft.containerinstance/containergroups), egy YAML-fájl vagy más programozott módszer használatával. Sablon vagy YAML-fájl használatához adja meg a megosztás részleteit, és `volumes` határozza `properties` meg a köteteket a fájl szakaszában lévő tömb feltöltésével. 

Ha például két *Share1* nevű Azure Files-megosztást és *share2-t* hozott létre a *myStorageAccount*tárfiókban, az `volumes` Erőforrás-kezelő sablonban lévő tömb a következőhöz hasonlónak tűnik:

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

Ezután a tárolócsoport minden olyan tárolójának, amelyben a köteteket `volumeMounts` csatlakoztatni szeretné, a tárolódefiníció szakaszában feltölti a `properties` tömböt. Ez például a korábban definiált két kötetet, a *myvolume1* és a *myvolume2*kötetet csatlakoztatja:

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

Megtudhatja, hogyan csatlakoztathat más kötettípusokat az Azure Container-példányokban:

* [EmptyDir kötet csatlakoztatása az Azure Container-példányokban](container-instances-volume-emptydir.md)
* [GitRepo-kötet csatlakoztatása az Azure Container-példányokban](container-instances-volume-gitrepo.md)
* [Titkos kötet csatlakoztatása az Azure Container-példányokban](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
---
title: Azure Files kötet csatlakoztatása Azure Container Instances
description: Megtudhatja, hogyan csatlakoztathat Azure Files kötetet az állapot megőrzéséhez Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 07/08/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 25cac6a66baeb1587e4b5ba3f0923ca9c4394706
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325490"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Azure-fájlmegosztás csatlakoztatása Azure Container Instances

Alapértelmezés szerint a Azure Container Instances állapot nélküliek. Ha a tároló összeomlik vagy leáll, az összes állapota elvész. Ha a tároló élettartama után is meg szeretné őrizni az állapotot, a kötetet külső tárolóból kell csatlakoztatnia. Ez a cikk bemutatja, hogyan csatlakoztathat [Azure Files](../storage/files/storage-files-introduction.md) használatával létrehozott Azure-fájlmegosztást a Azure Container Instanceshoz való használatra. Az Azure Files teljes körűen felügyelt felhőbeli fájlmegosztást nyújt, amely az iparági szabványként használt Server Message Block (SMB) protokollon keresztül érhető el. Az Azure-fájlmegosztás Azure Container Instances használatával olyan fájlmegosztási funkciókat biztosít, mint az Azure-fájlmegosztás Azure-beli virtuális gépekkel való használata.

> [!NOTE]
> Egy Azure Files-megosztás csatlakoztatása jelenleg csak Linux-tárolók számára engedélyezett. Miközben dolgozunk a Windows-tárolók összes funkciójának bekapcsolásán, az [áttekintésben](container-instances-overview.md#linux-and-windows-containers)megtalálhatja az aktuális platformmal kapcsolatos különbségeket.

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása

Mielőtt Azure-fájlmegosztást használ a Azure Container Instanceskal, létre kell hoznia azt. Futtassa a következő szkriptet a fájlmegosztás tárolására szolgáló Storage-fiók létrehozásához, és magát a megosztást. A Storage-fiók nevének globálisan egyedinek kell lennie, így a parancsfájl egy véletlenszerű értéket vesz fel az alapsztringbe.

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

## <a name="get-storage-credentials"></a>Tárolási hitelesítő adatok beolvasása

Az Azure-fájlmegosztás Azure Container Instances kötetként való csatlakoztatásához három érték szükséges: a Storage-fiók neve, a megosztás neve és a tárolási hozzáférési kulcs.

Ha a fenti parancsfájlt használta, a Storage-fiók neve a $ACI _PERS_STORAGE_ACCOUNT_NAME változóban volt tárolva. A fiók nevének megtekintéséhez írja be a következőt:

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

A megosztás neve már ismert (a fenti szkriptben *acishare* van definiálva), így az összes marad a Storage-fiók kulcsa, amely a következő paranccsal érhető el:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume---cli"></a>Tároló üzembe helyezése és kötet csatlakoztatása – parancssori felület

Ha egy Azure-fájlmegosztást az Azure CLI használatával szeretne kötetként csatlakoztatni egy tárolóban, adja meg a megosztást és a kötet csatlakoztatási pontját, amikor létrehozza a tárolót az [az Container Create][az-container-create]paranccsal. Ha követte az előző lépéseket, csatlakoztathatja a korábban létrehozott megosztást egy tároló létrehozásához a következő parancs használatával:

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

Az `--dns-name-label` értéknek egyedinek kell lennie azon az Azure-régión belül, ahol létrehozza a Container-példányt. Frissítse az előző parancs értékét, ha a parancs végrehajtásakor a **DNS-név címkéje** hibaüzenetet kap.

## <a name="manage-files-in-mounted-volume"></a>Fájlok kezelése a csatlakoztatott köteten

A tároló elindítása után használhatja a Microsoft [ACI-hellofiles][aci-hellofiles] image to create small text files in the Azure file share at the mount path you specified. Obtain the web app's fully qualified domain name (FQDN) with the [az container show][az-container-show] paranccsal üzembe helyezett egyszerű webalkalmazást:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn --output tsv
```

Miután az alkalmazás használatával mentette a szöveget, a [Azure Portal][portal] or a tool like the [Microsoft Azure Storage Explorer][storage-explorer] segítségével lekérheti és ellenőrizheti a fájlmegosztást.

## <a name="deploy-container-and-mount-volume---yaml"></a>Tároló üzembe helyezése és kötet csatlakoztatása – YAML

Emellett üzembe helyezhet egy tároló csoportot is, és csatlakoztathat egy kötetet egy tárolóban az Azure CLI-vel és egy [YAML-sablonnal](container-instances-multi-container-yaml.md). A YAML sablon általi üzembe helyezés az előnyben részesített módszer, ha több tárolóból álló tároló-csoportokat helyez üzembe.

A következő YAML-sablon definiál egy tároló csoportot, amely egy, a `aci-hellofiles` képpel létrehozott tárolóval rendelkezik. A tároló csatlakoztatja a korábban kötetként létrehozott Azure-fájlmegosztás *acishare* . Ha meg van jelölve, adja meg a fájlmegosztást tároló Storage-fiók nevét és tárolási kulcsát. 

Ahogy a CLI-példában is, `dnsNameLabel` az értéknek egyedinek kell lennie abban az Azure-régióban, ahol létrehozza a Container-példányt. Ha szükséges, frissítse a YAML fájlban található értéket.

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

A YAML sablonnal való üzembe helyezéshez mentse az előző YAML egy nevű `deploy-aci.yaml`fájlba, majd hajtsa végre az az [Container Create][az-container-create] parancsot `--file` a következő paraméterrel:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Tároló üzembe helyezése és a kötet csatlakoztatása – erőforrás-kezelő

A CLI és a YAML üzembe helyezése mellett üzembe helyezhet egy tároló csoportot, és egy Azure [Resource Manager-sablonnal](/azure/templates/microsoft.containerinstance/containergroups)csatlakoztathat egy kötetet egy tárolóhoz.

Először töltse `volumes` fel a tömböt a sablon tároló csoport `properties` szakaszában. 

Ezután minden olyan tárolóhoz, amelyhez csatlakoztatni szeretné a kötetet, töltse `volumeMounts` fel a tömböt `properties` a tároló definíciójának szakaszában.

A következő Resource Manager-sablon definiál egy tároló csoportot, amely egy, a `aci-hellofiles` képpel létrehozott tárolóval rendelkezik. A tároló csatlakoztatja a korábban kötetként létrehozott Azure-fájlmegosztás *acishare* . Ha meg van jelölve, adja meg a fájlmegosztást tároló Storage-fiók nevét és tárolási kulcsát. 

Ahogy az előző példákban is látható `dnsNameLabel` , az értéknek egyedinek kell lennie abban az Azure-régióban, ahol létrehozza a Container-példányt. Ha szükséges, frissítse a sablon értékét.

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

A Resource Manager-sablonnal történő üzembe helyezéshez mentse az előző JSON-fájlt egy `deploy-aci.json`nevű fájlba, majd hajtsa végre az az [Group Deployment Create][az-group-deployment-create] parancsot `--template-file` a következő paraméterrel:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Több kötet csatlakoztatása

Ha több kötetet szeretne csatlakoztatni egy tároló-példányban, [Azure Resource Manager-sablonnal](/azure/templates/microsoft.containerinstance/containergroups) vagy YAML-fájllal kell telepítenie. Ha sablont vagy YAML-fájlt szeretne használni, adja meg a megosztás részleteit, és adja meg a `volumes` köteteket a `properties` sablon szakaszában található tömb kitöltésével. 

Ha például két Azure Files *share1* és *share2* nevű megosztást hozott létre a Storage-fiók *myStorageAccount*, akkor `volumes` egy Resource Manager-sablonban lévő tömb a következőhöz hasonlóan fog megjelenni:

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

Ezután a tároló csoport minden olyan tárolójában, amelyben a köteteket csatlakoztatni szeretné, töltse ki a `volumeMounts` tömböt `properties` a tároló definíciójának szakaszában. Így például a két kötetet csatlakoztatja, a *myvolume1* és a *myvolume2*, korábban definiálva:

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

További mennyiségi típusok csatlakoztatása a Azure Container Instancesban:

* [Az Azure-tároló példányát emptyDir kötet csatlakoztatása](container-instances-volume-emptydir.md)
* [Azure-tároló példányát gitRepo kötet csatlakoztatása](container-instances-volume-gitrepo.md)
* [Titkos kötet csatlakoztatása Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
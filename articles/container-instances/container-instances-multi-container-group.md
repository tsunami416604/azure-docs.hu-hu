---
title: Oktatóanyag – az Azure Container Instances - többtárolós csoport üzembe helyezése sablonnal
description: Ebben az oktatóanyagban elsajátíthatja, hogyan helyezhet üzembe egy tárolócsoportot több tárolót az Azure Container Instances szolgáltatásban az Azure CLI-vel az Azure Resource Manager-sablon használatával.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f769beda1654dc9f58ecff733741fb1ab9118031
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006915"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Oktatóanyag: A Resource Manager-sablon használatával többtárolós csoport központi telepítése

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)

Az Azure Container Instances helyezheti egyetlen gazdagépre használatával több tároló telepítését támogatja egy [tárolócsoport](container-instances-container-groups.md). Egy tárolócsoport akkor hasznos, ha egy alkalmazás oldalkocsi, naplózás, figyelés vagy bármely egyéb konfigurációs létrehozásához, egy szolgáltatást kell-e egy második csatolt folyamat.

Ebben az oktatóanyagban szereplő lépések egy egyszerű két-tároló oldalkocsi konfiguráció futtatása az Azure CLI használatával egy Azure Resource Manager-sablon üzembe helyezésével. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Többtárolós csoport sablon konfigurálása
> * A tároló-csoport központi telepítése
> * Tekintse át a naplókat a tárolók

A Resource Manager-sablon azonnal alkalmazható forgatókönyvekhez amikor szüksége van a tárolócsoport további Azure-szolgáltatási erőforrások (például egy Azure-fájlmegosztással vagy egy virtuális hálózat) üzembe helyezhető. 

> [!NOTE]
> Többtárolós csoportok olyan Linux-tárolókhoz való hozzáférés jelenleg korlátozott. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-template"></a>Egy sablon konfigurálása

Indítsa el a következő JSON másolásával egy új fájlba nevű `azuredeploy.json`. Az Azure Cloud Shellben használhatja a Visual Studio Code létrehozni a fájlt a munkakönyvtárban:

```
code azuredeploy.json
```

A Resource Manager-sablon meghatározza a két tárolót egy tárolócsoportot, a nyilvános IP-cím és a két elérhetővé tett port. A csoportban az első tároló egy internetre irányuló webes alkalmazást futtat. A második tárolót, az oldalkocsi egy HTTP-kérelem egyszerűvé teszi a fő webalkalmazás a csoporthoz tartozó helyi hálózaton keresztül.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
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
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
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
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

A JSON-dokumentum, a következő formátumú objektum hozzáadása egy privát tárolójegyzékben rendszerkép használatához. Ez a konfiguráció példa megvalósítását, lásd: a [ACI Resource Manager sablonreferenciája] [ template-reference] dokumentációját.

```JSON
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Helyezze üzembe a sablont a [az csoport központi telepítésének létrehozása] [ az-group-deployment-create] parancsot.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
```

Néhány másodpercen belül meg kell kapnia az Azure kezdeti válaszát.

## <a name="view-deployment-state"></a>Központi telepítés állapotának megtekintése

Az üzembe helyezés állapotának megtekintéséhez használja a következő [az container show] [ az-container-show] parancsot:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Ha szeretné a futó alkalmazás megtekintéséhez navigáljon az IP-címét a böngészőben. Ha például az IP-címe `52.168.26.124` a következő példakimenetben:

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Tárolónaplók megtekintése

Egy tároló a napló kimenetének megtekintéséhez a [az tárolónaplók] [ az-container-logs] parancsot. A `--container-name` argumentum, adja meg a tároló, amelyből a naplók lekérése. Ebben a példában a `aci-tutorial-app` tároló van megadva.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Kimenet:

```bash
listening on port 80
::1 - - [21/Mar/2019:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Az oldalkocsi tároló naplóit megtekintéséhez futtassa a hasonló parancs megadásával a `aci-tutorial-sidecar` tároló.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Kimenet:

```bash
Every 3s: curl -I http://localhost                          2019-03-21 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 21 Mar 2019 20:36:41 GMT
Connection: keep-alive
```

Amint látható, az oldalkocsi egy HTTP-kérelem, hogy így rendszeres időközönként annak érdekében, hogy fut-e a csoport helyi hálózaton keresztül a fő webes alkalmazásba való. Ebben a példában az oldalkocsi lehetett kibontani a figyelmeztetést jelenít meg, ha nem kapott egy HTTP-válaszkód `200 OK`.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag, amellyel az Azure Resource Manager-sablon üzembe helyezése az Azure Container Instancesben többtárolós csoport. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Többtárolós csoport sablon konfigurálása
> * A tároló-csoport központi telepítése
> * Tekintse át a naplókat a tárolók

További sablonminták, lásd: [Azure Resource Manager-sablonok az Azure Container Instances](container-instances-samples-rm.md).

Azt is megadhatja, hogy egy többtárolós csoport az egy [YAML-fájl](container-instances-multi-container-yaml.md). Miatt a YAML formátumú tömörebb jellegű üzembe helyezés egy YAML-fájlt a megfelelő választás az akkor, ha a telepítés magában foglalja az csak a container Instances szolgáltatásban.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

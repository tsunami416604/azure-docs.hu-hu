---
title: Az Azure Container Instancesben többtárolós csoportok üzembe helyezése
description: Ismerje meg, hogyan helyezhet üzembe egy tárolócsoportot több tárolót az Azure Container Instances szolgáltatásban az Azure Resource Manager-sablon használatával.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 93f73e133e99025b479d0b38512e26088a8eaefa
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369113"
---
# <a name="deploy-a-multi-container-group-with-a-resource-manager-template"></a>Többtárolós csoport Resource Manager-sablonnal üzembe helyezése

Az Azure Container Instances helyezheti egyetlen gazdagépre használatával több tároló telepítését támogatja egy [tárolócsoport](container-instances-container-groups.md). Ez akkor hasznos egy alkalmazás oldalkocsi, naplózás, figyelés vagy bármely egyéb konfigurációs létrehozását, ahol a szolgáltatás egy második csatolt folyamat kell.

Az Azure CLI használatával többtárolós csoportok üzembe helyezésének két módja van:

* Resource Manager-sablon üzembe helyezése (Ez a cikk)
* [YAML-fájl üzembe helyezés](container-instances-multi-container-yaml.md)

Üzembe helyezés Resource Manager-sablonnal a tárolópéldány üzembe helyezésének idején amikor üzembe kell helyeznie a további Azure-szolgáltatási erőforrások (például egy Azure-fájlmegosztással) ajánlott. Miatt a YAML formátumú tömörebb jellegű, üzembe helyezés egy YAML-fájlt a ajánlott, amikor a telepítés magában foglalja az *csak* container Instances szolgáltatásban.

> [!NOTE]
> Többtárolós csoportok olyan Linux-tárolókhoz való hozzáférés jelenleg korlátozott. Arra törekszünk, hogy idővel az összes funkció elérhető legyen a Windows-tárolókon is. Az egyes platformok közötti aktuális eltérésekről a [Azure Container Instances-kvóták és -régiók rendelkezésre állása](container-instances-quotas.md) részben tájékozódhat.

További sablonminták, lásd: [Azure Resource Manager-sablonok az Azure Container Instances](container-instances-samples-rm.md). 

## <a name="configure-the-template"></a>A sablon konfigurálása

Ez a cikk szakaszai végigvezetik egy egyszerű többtárolós oldalkocsi konfiguráció futtatása az Azure Resource Manager-sablon üzembe helyezésével.

Először hozzon létre egy fájlt `azuredeploy.json`, majd másolja bele a következő JSON-fájllal.

A Resource Manager-sablon meghatározza a két tárolót egy tárolócsoportot, a nyilvános IP-cím és a két elérhetővé tett port. A Microsoft nyilvános lemezképeket a tárolók üzembe helyezése. A csoportban az első tároló egy internetkapcsolattal rendelkező alkalmazást futtat. A második tárolót, az oldalkocsi egy HTTP-kérelem egyszerűvé teszi a fő webalkalmazás a csoporthoz tartozó helyi hálózaton keresztül.

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

## <a name="view-logs"></a>Naplók megtekintése

Egy tároló a napló kimenetének megtekintéséhez a [az tárolónaplók] [ az-container-logs] parancsot. A `--container-name` argumentum, adja meg a tároló, amelyből a naplók lekérése. Ebben a példában az első tároló meg van adva.

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

A naplók a kiszolgálóoldali autós tároló megtekintéséhez futtassa ugyanezt a parancsot a második tároló nevének megadása.

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

Amint látható, az oldalkocsi egy HTTP-kérelem, hogy így rendszeres időközönként annak érdekében, hogy fut-e a csoport helyi hálózaton keresztül a fő webes alkalmazásba való. Ebben a példában az oldalkocsi figyelmeztetést jelenít meg, ha egy HTTP-válaszkód eltérő 200-as rendben kapott lehetett kibontani.

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatta, egy többtárolós az Azure container instance-példány üzembe helyezéséhez szükséges lépéseket. És a teljes körű Azure Container Instances szolgáltatással tekintse meg az Azure Container Instances oktatóanyagával.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyaga][aci-tutorial]

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

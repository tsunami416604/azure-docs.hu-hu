---
title: "Több tároló csoportokat az Azure-tároló példányok telepítése"
description: "Útmutató az Azure-tároló esetekben több tároló egy tároló-csoport központi telepítését."
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 12/19/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2ffebf06e2e013f909410fa4861420a5ae3d4dcf
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="deploy-a-container-group"></a>A tároló csoport telepítése

Azure-tároló példányokon alakzatot használatával egyetlen állomásra több tároló telepítését támogatja a [a tárolócsoport](container-instances-container-groups.md). Ez akkor hasznos, ha egy alkalmazás oldalkocsi naplózási, figyelési vagy bármely egyéb konfigurációs felépítése amikor egy szolgáltatás kell egy második csatolt folyamat.

Ez a dokumentum végigvezeti egy egyszerű több tároló oldalkocsi konfigurációs fut az Azure Resource Manager-sablon üzembe helyezésével.

> [!NOTE]
> Több tárolócsoportok jelenleg csak Linux tárolók. Dolgozunk ennek érdekében minden funkció Windows tárolók, amíg a jelenlegi platform különbségek található [kvótái és az Azure-tároló példányok régiónkénti elérhetőség](container-instances-quotas.md).

## <a name="configure-the-template"></a>A sablon konfigurálása

Hozzon létre egy fájlt `azuredeploy.json` és a következő JSON másolása.

Ebben a mintában a tároló két tárolók és a nyilvános IP-cím van definiálva. A csoportban lévő első tároló internetre alkalmazást futtat. A második tároló, a oldalkocsi egy HTTP kérést küld az elsődleges webes alkalmazás a csoporthoz tartozó helyi hálózaton keresztül.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "microsoft/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "microsoft/aci-tutorial-sidecar"
  },
    "resources": [
      {
        "name": "myContainerGroup",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2017-08-01-preview",
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
              }
            ]
          }
        }
      }
    ],
    "outputs": {
      "containerIPv4Address": {
        "type": "string",
        "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', 'myContainerGroup')).ipAddress.ip]"
      }
    }
  }
```

Egy tároló titkos kép beállításjegyzék használatát, az objektum hozzáadása a JSON-dokumentum, az alábbi formátumban.

```json
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

A sablon üzembe helyezése a [az csoport központi telepítésének létrehozása] [ az-group-deployment-create] parancsot.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --name myContainerGroup --template-file azuredeploy.json
```

Néhány másodpercen belül egy kezdeti választ kell kapnia az Azure-ból.

## <a name="view-deployment-state"></a>Központi telepítés állapotának megtekintése

A központi telepítési állapotának megtekintéséhez használja a [az tároló megjelenítése] [ az-container-show] parancsot. Ez visszaad, amellyel az alkalmazás elérhető kiosztott nyilvános IP-cím.

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Kimenet:

```bash
Name              ResourceGroup    ProvisioningState    Image                                                             IP:ports           CPU/Memory    OsType    Location
----------------  ---------------  -------------------  ----------------------------------------------------------------  -----------------  ------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-tutorial-sidecar,microsoft/aci-tutorial-app:v1      40.118.253.154:80  1.0 core/1.5 gb   Linux     westus
```

## <a name="view-logs"></a>Naplók megtekintése

A kimenet egy tároló használatával megtekintheti a [az tároló naplók] [ az-container-logs] parancsot. A `--container-name` argumentum meghatározza a tároló, amelyből való lekérésére naplókat. Ebben a példában az első tároló van megadva.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Kimenet:

```bash
listening on port 80
::1 - - [18/Dec/2017:21:31:08 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [18/Dec/2017:21:31:11 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [18/Dec/2017:21:31:15 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

A kiszolgálóoldali-car tároló a naplók megtekintéséhez futtassa a ugyanazzal a paranccsal a második tároló nevének megadását.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Kimenet:

```bash
Every 3s: curl -I http://localhost                          2017-12-18 23:19:34

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
Date: Mon, 18 Dec 2017 23:19:34 GMT
Connection: keep-alive
```

Ahogy látja, a oldalkocsi HTTP-kérelem, hogy így rendszeres időközönként a fő webalkalmazásnak a csoporthoz tartozó helyi hálózaton keresztül annak érdekében, hogy fut-e. Ebben a példában oldalkocsi figyelmeztetést jelenít meg, ha egy HTTP-válaszkód eltérő 200 OK kapott lehetett kibontani.

## <a name="next-steps"></a>További lépések

Ez a cikk a több tároló Azure tároló példánya telepítéséhez szükséges lépéseket mutatja be. A végpont Azure tároló példányok élményt tekintse meg az Azure-tároló példányok.

> [!div class="nextstepaction"]
> [Azure tároló példányok útmutató][aci-tutorial]

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-group-deployment-create]: /cli/azure/group/deployment#az_group_deployment_create

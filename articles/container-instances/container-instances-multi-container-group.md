---
title: Több tároló csoportokat az Azure-tároló példányok telepítése
description: Útmutató az Azure-tároló esetekben több tároló egy tároló-csoport központi telepítését.
services: container-instances
author: iainfoulds
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6d337c9ed23ac9af884f4113b046a8e9756fd441
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097104"
---
# <a name="deploy-a-container-group"></a>A tároló csoport telepítése

Azure-tároló példányokon alakzatot használatával egyetlen állomásra több tároló telepítését támogatja a [a tárolócsoport](container-instances-container-groups.md). Ez akkor hasznos, ha egy alkalmazás oldalkocsi naplózási, figyelési vagy bármely egyéb konfigurációs felépítése amikor egy szolgáltatás kell egy második csatolt folyamat.

Az Azure parancssori felület használatával több tárolócsoportok telepítéséhez két módszer áll rendelkezésre:

* Erőforrás-kezelő sablon-üzembehelyezés (Ez a cikk)
* [YAM fájltelepítés](container-instances-multi-container-yaml.md)

A Resource Manager-sablon központi telepítése esetén ajánlott kell telepítenie további Azure-szolgáltatások erőforrások (például egy Azure fájlok megosztás) tároló példány központi telepítés alkalmával. Miatt a YAM formátum tömörebb természetétől YAM fájl központi telepítése tartalmazza a központi telepítés esetén ajánlott *csak* tároló példányok.

> [!NOTE]
> Több tárolócsoportok jelenleg csak Linux tárolók. Arra törekszünk, hogy idővel az összes funkció elérhető legyen a Windows-tárolókon is. Az egyes platformok közötti aktuális eltérésekről a [Azure Container Instances-kvóták és -régiók rendelkezésre állása](container-instances-quotas.md) részben tájékozódhat.

## <a name="configure-the-template"></a>A sablon konfigurálása

Ez a cikk a részeket végigvezetik Önt egy egyszerű több tároló oldalkocsi konfigurációs fut az Azure Resource Manager-sablon üzembe helyezésével.

Először hozzon létre egy fájlt `azuredeploy.json`, majd másolja a következő JSON azt.

A Resource Manager sablon határozza meg, a tárolócsoport két tárolókhoz, egy nyilvános IP-cím és két kitett portok. A csoportban lévő első tároló internetre alkalmazást futtat. A második tároló, a oldalkocsi egy HTTP kérést küld az elsődleges webes alkalmazás a csoporthoz tartozó helyi hálózaton keresztül.

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
    "container1image": "microsoft/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "microsoft/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-04-01",
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

Egy tároló titkos kép beállításjegyzék használatát, az objektum hozzáadása a JSON-dokumentum, az alábbi formátumban. Egy megvalósítási példát szemléltet konfiguráció, tekintse meg a [ACI Resource Manager-sablonra való hivatkozást] [ template-reference] dokumentációját.

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

A sablon üzembe helyezése a [az csoport központi telepítésének létrehozása] [ az-group-deployment-create] parancsot.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
```

Néhány másodpercen belül meg kell kapnia az Azure kezdeti válaszát.

## <a name="view-deployment-state"></a>Központi telepítés állapotának megtekintése

A központi telepítési állapotának megtekintéséhez használja a következő [az tároló megjelenítése] [ az-container-show] parancs:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Ha azt szeretné, a futó alkalmazást, keresse meg az IP-címét a böngésző. Például az IP-cím `52.168.26.124` a Ez egy példa a kimenetre:

```bash
Name              ResourceGroup    ProvisioningState    Image                                                           IP:ports               CPU/Memory       OsType    Location
----------------  ---------------  -------------------  --------------------------------------------------------------  ---------------------  ---------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     westus
```

## <a name="view-logs"></a>Naplók megtekintése

A kimenet egy tároló használatával megtekintheti a [az tároló naplók] [ az-container-logs] parancsot. A `--container-name` argumentum meghatározza a tároló, amelyből való lekérésére naplókat. Ebben a példában az első tároló van megadva.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Kimenet:

```bash
listening on port 80
::1 - - [09/Jan/2018:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

A kiszolgálóoldali-car tároló a naplók megtekintéséhez futtassa a ugyanazzal a paranccsal a második tároló nevének megadását.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Kimenet:

```bash
Every 3s: curl -I http://localhost                          2018-01-09 23:25:11

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
Date: Tue, 09 Jan 2018 23:25:11 GMT
Connection: keep-alive
```

Ahogy látja, a oldalkocsi HTTP-kérelem, hogy így rendszeres időközönként a fő webalkalmazásnak a csoporthoz tartozó helyi hálózaton keresztül annak érdekében, hogy fut-e. Ebben a példában oldalkocsi figyelmeztetést jelenít meg, ha egy HTTP-válaszkód eltérő 200 OK kapott lehetett kibontani.

## <a name="next-steps"></a>További lépések

Ez a cikk a több tároló Azure tároló példánya telepítéséhez szükséges lépéseket mutatja be. A végpont Azure tároló példányok élményt tekintse meg az Azure-tároló példányok.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyaga][aci-tutorial]

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-group-deployment-create]: /cli/azure/group/deployment#az_group_deployment_create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

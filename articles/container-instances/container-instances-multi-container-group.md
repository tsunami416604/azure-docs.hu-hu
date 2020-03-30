---
title: Oktatóanyag – Többtárolós csoport telepítése – sablon
description: Ebben az oktatóanyagban megtudhatja, hogyan helyezhet üzembe egy tárolócsoportot több tárolóval az Azure Container Instances egy Azure Resource Manager-sablon használatával az Azure CLI használatával.
ms.topic: article
ms.date: 04/03/2019
ms.custom: mvc
ms.openlocfilehash: d2b4e20520cad28c5d62118f6c9d10fcc43ac89e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533623"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Oktatóanyag: Többtárolós csoport telepítése Erőforrás-kezelő sablon használatával

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)

Az Azure Container Instances támogatja a több tárolók üzembe helyezését egyetlen állomásra egy [tárolócsoport](container-instances-container-groups.md)használatával. A tárolócsoport akkor hasznos, ha egy alkalmazás oldalkocsi naplózás, figyelés, vagy bármely más konfiguráció, ahol a szolgáltatás szüksége van egy második csatlakoztatott folyamat.

Ebben az oktatóanyagban kövesse a lépéseket egy egyszerű kéttárolós oldalkocsi-konfiguráció futtatásához egy Azure Resource Manager-sablon üzembe helyezésével az Azure CLI használatával. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Többtárolós csoportsablon konfigurálása
> * A tárolócsoport telepítése
> * A tárolók naplóinak megtekintése

A Resource Manager-sablon könnyen adaptálható forgatókönyvekhez, amikor további Azure-szolgáltatás-erőforrásokat (például egy Azure-fájlok megosztását vagy egy virtuális hálózatot) kell üzembe helyeznie a tárolócsoporttal. 

> [!NOTE]
> A többtárolós csoportok jelenleg linuxos tárolókra korlátozódnak. 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-template"></a>Sablon konfigurálása

Először másolja a következő JSON-t `azuredeploy.json`egy új fájlba, melynek neve . Az Azure Cloud Shellben a Visual Studio-kód segítségével hozhatja létre a fájlt a munkakönyvtárában:

```
code azuredeploy.json
```

Ez az Erőforrás-kezelő sablon két tárolóval, egy nyilvános IP-címmel és két elérhető porttal rendelkező tárolócsoportot határoz meg. A csoport első tárolója egy internetre néző webalkalmazást futtat. A második tároló, az oldalkocsi http-kérelmet küld a fő webalkalmazásnak a csoport helyi hálózatán keresztül.

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

Privát tárolórendszerkép-beállításjegyzék használatához adjon hozzá egy objektumot a JSON-dokumentumhoz a következő formátummal. A konfiguráció például az [ACI Resource Manager sablon referenciadokumentációjában][template-reference] olvashat.

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

Telepítse a sablont az [az csoport telepítési létrehozási][az-group-deployment-create] parancsával.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
```

Néhány másodpercen belül meg kell kapnia az Azure kezdeti válaszát.

## <a name="view-deployment-state"></a>Telepítési állapot megtekintése

A központi telepítés állapotának megtekintéséhez használja a következő [az container show][az-container-show] parancsot:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Ha meg szeretné tekinteni a futó alkalmazást, keresse meg annak IP-címét a böngészőben. Például az IP `52.168.26.124` ebben a példában kimeneti:

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Tárolónaplók megtekintése

Tekintse meg egy tároló naplókimenetét az [az container logs][az-container-logs] paranccsal. Az `--container-name` argumentum azt a tárolót adja meg, amelyből a naplókat le kell húzni. Ebben a példában a `aci-tutorial-app` tároló van megadva.

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

Az oldalkocsis tároló naplóinak megtekintéséhez futtasson `aci-tutorial-sidecar` egy hasonló parancsot, amely megadja a tárolót.

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

Mint látható, az oldalkocsi rendszeresen http-kérelmet küld a fő webes alkalmazásnak a csoport helyi hálózatán keresztül annak érdekében, hogy az futjon. Ez az oldalkocsis példa kibontható egy riasztás aktiválásához, ha a tól eltérő `200 OK`HTTP-válaszkódot kapott.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure Resource Manager-sablont használt egy többtárolós csoport üzembe helyezéséhez az Azure Container Instances-ben. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Többtárolós csoportsablon konfigurálása
> * A tárolócsoport telepítése
> * A tárolók naplóinak megtekintése

További sablonmintákat az [Azure Resource Manager-sablonok az Azure Container Instances-hez.](container-instances-samples-rm.md)

Többtárolós csoportot [yaml-fájlhasználatával](container-instances-multi-container-yaml.md)is megadhat. A YAML-formátum tömörebb jellege miatt a YAML-fájllal való telepítés jó választás, ha a központi telepítés csak tárolópéldányokat tartalmaz.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

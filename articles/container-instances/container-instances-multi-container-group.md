---
title: "Azure-tároló példányokon - többszörös a tárolócsoport |} Az Azure Docs"
description: "Azure-tároló példányokon - többszörös a tárolócsoport"
services: container-instances
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 140f58582645ea32f77e901eb13364ed145bbecf
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="deploy-a-container-group"></a>A tároló csoport telepítése

Azure-tároló példányokon alakzatot használatával egyetlen állomásra több tároló telepítését támogatja a *a tárolócsoport*. Ez akkor hasznos, ha egy alkalmazás oldalkocsi naplózási, figyelési vagy bármely egyéb konfigurációs felépítése amikor egy szolgáltatás kell egy második csatolt folyamat. 

Ez a dokumentum végigvezeti egy egyszerű több tároló oldalkocsi konfigurációs Azure Resource Manager-sablonnal futtatása.

## <a name="configure-the-template"></a>A sablon konfigurálása

Hozzon létre egy fájlt `azuredeploy.json` és a következő json másolása. 

Ebben a mintában a tároló két tárolók és a nyilvános IP-cím van definiálva. A csoport első tároló internet felé néző alkalmazás fut. A második tároló, a oldalkocsi egy HTTP kérést küld az elsődleges webes alkalmazás a csoporthoz tartozó helyi hálózaton keresztül. 

Ebben a példában oldalkocsi figyelmeztetést jelenít meg, ha egy HTTP-válaszkód eltérő 200 OK kapott lehetett kibontani. 

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

Egy tároló titkos kép beállításjegyzék használatát, az objektum hozzáadása a json-dokumentum, az alábbi formátumban.

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

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

A sablon üzembe helyezése a [az csoport központi telepítésének létrehozása](/cli/azure/group/deployment#create) parancsot.

```azurecli-interactive
az group deployment create --name myContainerGroup --resource-group myResourceGroup --template-file azuredeploy.json
```

Néhány másodpercen belül egy kezdeti választ kap az Azure-ból. 

## <a name="view-deployment-state"></a>Központi telepítés állapotának megtekintése

A központi telepítési állapotának megtekintéséhez használja a `az container show` parancsot. Ez visszaad, amelyben az alkalmazás elérhető kiosztott nyilvános IP-cím.

```azurecli-interactive
az container show --name myContainerGroup --resource-group myResourceGroup -o table
```

Kimenet:

```azurecli
Name              ResourceGroup    ProvisioningState    Image                                                             IP:ports           CPU/Memory    OsType    Location
----------------  ---------------  -------------------  ----------------------------------------------------------------  -----------------  ------------  --------  ----------
myContainerGroup  myResourceGrou2  Succeeded            microsoft/aci-tutorial-sidecar,microsoft/aci-tutorial-app:v1      40.118.253.154:80  1.0 core/1.5 gb   Linux     westus
```

## <a name="view-logs"></a>Naplók megtekintése   

A kimenet egy tároló használatával megtekintheti a `az container logs` parancsot. A `--container-name` argumentum meghatározza a tároló, amelyből való lekérésére naplókat. Ebben a példában az első tároló van megadva. 

```azurecli-interactive
az container logs --name myContainerGroup --container-name aci-tutorial-app --resource-group myResourceGroup
```

Kimenet:

```bash
istening on port 80
::1 - - [27/Jul/2017:17:35:29 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:32 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:35 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:38 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

A kiszolgálóoldali-car tároló a naplók megtekintéséhez futtassa a ugyanazzal a paranccsal a második tároló nevének megadását.

```azurecli-interactive
az container logs --name myContainerGroup --container-name aci-tutorial-sidecar --resource-group myResourceGroup
```

Kimenet:

```bash
Every 3.0s: curl -I http://localhost                                                                                                                       Mon Jul 17 11:27:36 2017

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
Accept-Ranges: bytes
Content-Length: 1663
Content-Type: text/html; charset=utf-8
Last-Modified: Sun, 16 Jul 2017 02:08:22 GMT
Date: Mon, 17 Jul 2017 18:27:36 GMT
```

Ahogy látja, a oldalkocsi HTTP-kérelem, hogy így rendszeres időközönként a fő webalkalmazásnak a csoporthoz tartozó helyi hálózaton keresztül annak érdekében, hogy fut-e.

## <a name="next-steps"></a>Következő lépések

Ez a dokumentum egy Azure-tárolót több tároló példány telepítéséhez szükséges lépéseket mutatja be. Egy teljes körű Azure tároló példányok élmény érdekében tekintse meg az Azure-tároló példányok.

> [!div class="nextstepaction"]
> [Azure tároló példányok oktatóanyag]:./container-instances-tutorial-prepare-app.md

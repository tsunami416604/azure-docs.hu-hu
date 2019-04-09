---
title: Oktatóanyag – az Azure Container Instances - YAML többtárolós csoport üzembe helyezése
description: Ebben az oktatóanyagban elsajátíthatja, hogyan helyezhet üzembe több tárolót az Azure Container Instances szolgáltatásban egy tárolócsoportot egy YAML-fájlt az Azure CLI használatával.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.openlocfilehash: a0a91ece4f219cf822673cd457c064c326b89478
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006185"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Oktatóanyag: Egy YAML-fájllal többtárolós csoport központi telepítése

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

Az Azure Container Instances helyezheti egyetlen gazdagépre használatával több tároló telepítését támogatja egy [tárolócsoport](container-instances-container-groups.md). Egy tárolócsoport akkor hasznos, ha egy alkalmazás oldalkocsi, naplózás, figyelés vagy bármely egyéb konfigurációs létrehozásához, egy szolgáltatást kell-e egy második csatolt folyamat.

Ebben az oktatóanyagban, kövesse a lépéseket egy egyszerű két-tároló oldalkocsi konfiguráció futtatása egy YAML-fájlt az Azure CLI használatával üzembe helyezésével. Egy YAML-fájlt biztosít tömör formátumot, a példány beállításainak megadása. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Egy YAML-fájl konfigurálása
> * A tároló-csoport központi telepítése
> * Tekintse át a naplókat a tárolók

> [!NOTE]
> Többtárolós csoportok olyan Linux-tárolókhoz való hozzáférés jelenleg korlátozott.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-yaml-file"></a>Egy YAML-fájl konfigurálása

Többtárolós csoport üzembe helyezéséhez a [az tároló létrehozása] [ az-container-create] az Azure CLI-parancsot a tároló konfigurációjának meg kell adnia egy YAML-fájlt. Akkor továbbítja a YAML-fájl paramétert a parancshoz.

Első lépésként másolja ki a következő yaml-kódot egy új fájlba nevű **üzembe helyezése aci.yaml**. Az Azure Cloud Shellben használhatja a Visual Studio Code létrehozni a fájlt a munkakönyvtárban:

```
code deploy-aci.yaml
```

A YAML-fájl határozza meg egy tárolócsoport neve "myContainerGroup" két tárolókkal, a nyilvános IP-cím és a két elérhetővé tett port. A Microsoft nyilvános lemezképeket a tárolók üzembe helyezése. A csoportban az első tároló egy internetre irányuló webes alkalmazást futtat. A második tárolót, az oldalkocsi rendszeres időközönként HTTP-kéréseket hajt végre a webalkalmazás első a tárolóban fut, a tárolócsoport helyi hálózaton keresztül.

```YAML
apiVersion: 2018-10-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: mcr.microsoft.com/azuredocs/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
    - protocol: tcp
      port: '8080'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Egy privát tárolójegyzékben rendszerképet használ, adja hozzá a `imageRegistryCredentials` tulajdonság az a tárolócsoportot, a környezet módosított értékeket:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>A tároló-csoport központi telepítése

Hozzon létre egy erőforráscsoportot a [az csoport létrehozása] [ az-group-create] parancsot:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Üzembe helyezése az a tárolócsoportot a [az tároló létrehozása] [ az-container-create] parancs, a YAML-fájl átadása argumentumként:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
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

```console
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

```console
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

Ebben az oktatóanyagban egy YAML-fájlt egy többtárolós csoport az Azure Container Instancesben üzembe helyezéséhez használt. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Többtárolós csoport egy YAML-fájl konfigurálása
> * A tároló-csoport központi telepítése
> * Tekintse át a naplókat a tárolók

Azt is megadhatja, hogy egy többtárolós csoport az egy [Resource Manager-sablon](container-instances-multi-container-group.md). Resource Manager-sablon azonnal alkalmazható forgatókönyvekhez amikor szüksége van, további Azure-szolgáltatási erőforrások a tárolócsoport üzembe helyezése.

<!-- LINKS - External -->


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

---
title: Oktatóanyag – Többtárolós csoport telepítése – YAML
description: Ebben az oktatóanyagban megtudhatja, hogyan helyezhet üzembe egy tárolócsoportot több tárolóval az Azure Container Instances használatával egy YAML-fájl az Azure CLI használatával.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: cce98ec56ee1d84c087150ba486b9482515b46f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533589"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Oktatóanyag: Többtárolós csoport telepítése YAML-fájl használatával

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

Az Azure Container Instances támogatja a több tárolók üzembe helyezését egyetlen állomásra egy [tárolócsoport](container-instances-container-groups.md)használatával. A tárolócsoport akkor hasznos, ha egy alkalmazás oldalkocsi naplózás, figyelés, vagy bármely más konfiguráció, ahol a szolgáltatás szüksége van egy második csatlakoztatott folyamat.

Ebben az oktatóanyagban kövesse a lépéseket egy egyszerű kéttárolós oldalkocsi-konfiguráció futtatásához egy [YAML-fájl](container-instances-reference-yaml.md) üzembe helyezésével az Azure CLI használatával. A YAML-fájl tömör formátumot biztosít a példánybeállítások megadásához. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * YAML-fájl konfigurálása
> * A tárolócsoport telepítése
> * A tárolók naplóinak megtekintése

> [!NOTE]
> A többtárolós csoportok jelenleg linuxos tárolókra korlátozódnak.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-yaml-file"></a>YAML-fájl konfigurálása

Ha egy többtárolós csoportot az [az container create][az-container-create] paranccsal az Azure CLI-ben üzembe szeretne helyezni, meg kell adnia a tárolócsoport konfigurációját egy YAML-fájlban. Ezután adja át a YAML-fájlt paraméterként a parancsnak.

Először másolja a következő YAML-fájlt egy új fájlba, amelynek neve **deploy-aci.yaml**. Az Azure Cloud Shellben a Visual Studio-kód segítségével hozhatja létre a fájlt a munkakönyvtárában:

```
code deploy-aci.yaml
```

Ez a YAML-fájl egy "myContainerGroup" nevű tárolócsoportot határoz meg két tárolóval, egy nyilvános IP-címmel és két elérhető porttal. A tárolók nyilvános Microsoft-lemezképekről vannak telepítve. A csoport első tárolója egy internetre néző webalkalmazást futtat. A második tároló, az oldalkocsi, rendszeres időközönként http-kérelmeket küld a webalkalmazás fut az első tárolóban a tárolócsoport helyi hálózaton keresztül.

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

Privát tárolórendszerkép-jegyzék használatához `imageRegistryCredentials` adja hozzá a tulajdonságot a tárolócsoporthoz a környezetre módosított értékekkel:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>A tárolócsoport telepítése

Erőforráscsoport létrehozása az [az csoport létrehozása][az-group-create] paranccsal:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Telepítse a tárolócsoportot az [az container create][az-container-create] paranccsal, és adja át a YAML-fájlt argumentumként:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
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

```console
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

Mint látható, az oldalkocsi rendszeresen http-kérelmet küld a fő webes alkalmazásnak a csoport helyi hálózatán keresztül annak érdekében, hogy az futjon. Ez az oldalkocsis példa kibontható egy riasztás aktiválásához, ha a tól eltérő `200 OK`HTTP-válaszkódot kapott.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy YAML-fájlt használt egy többtárolós csoport üzembe helyezéséhez az Azure Container Instances-ben. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Többtárolós csoport YAML-fájljának konfigurálása
> * A tárolócsoport telepítése
> * A tárolók naplóinak megtekintése

Többtárolós csoportot is megadhat [egy Erőforrás-kezelő sablon](container-instances-multi-container-group.md)használatával. A Resource Manager-sablon könnyen adaptálható forgatókönyvekhez, amikor további Azure-szolgáltatás-erőforrásokat kell üzembe helyeznie a tárolócsoporttal.

<!-- LINKS - External -->


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

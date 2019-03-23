---
title: Az Azure Container Instances szolgáltatásban az Azure CLI és a YAML többtárolós csoportok üzembe helyezése
description: Ismerje meg, hogyan helyezhet üzembe egy tárolócsoportot több tárolót az Azure Container Instances szolgáltatásban az Azure CLI és a egy YAML-fájl használatával.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/21/2019
ms.author: danlep
ms.openlocfilehash: 10f2340bd85da3dabcd50d51a4dd56d58d31675b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372437"
---
# <a name="deploy-a-multi-container-container-group-with-yaml"></a>Egy többtárolós tárolócsoportot YAML üzembe helyezése

Az Azure Container Instances használatával helyezheti egyetlen gazdagépre több tároló telepítését támogatja egy [tárolócsoport](container-instances-container-groups.md). Többtárolós tárolócsoportok akkor hasznos, ha egy alkalmazás oldalkocsi, naplózás, figyelés vagy bármely egyéb konfigurációs létrehozásához, egy szolgáltatást kell-e egy második csatolt folyamat.

Az Azure CLI használatával többtárolós csoportok üzembe helyezésének két módja van:

* YAML-fájl üzembe helyezés (Ez a cikk)
* [Resource Manager-sablon üzembe helyezése](container-instances-multi-container-group.md)

Miatt a YAML formátumú tömörebb jellegű, üzembe helyezés egy YAML-fájlt a ajánlott, amikor a telepítés magában foglalja az *csak* container Instances szolgáltatásban. Ha további Azure-szolgáltatási erőforrások (például egy Azure-fájlmegosztással) telepítése a tárolópéldány üzembe helyezésének idején van szüksége, ajánlott Resource Manager-sablon üzembe helyezése.

> [!NOTE]
> Többtárolós csoportok olyan Linux-tárolókhoz való hozzáférés jelenleg korlátozott. Idővel az összes funkció a Windows-tárolók dolgozunk, miközben Ön aktuális eltérésekről platform a [kvóták és régiók rendelkezésre állása az Azure Container Instances](container-instances-quotas.md).

## <a name="configure-the-yaml-file"></a>A YAML-fájl konfigurálása

Egy többtárolós tárolócsoportot üzembe helyezéséhez a [az tároló létrehozása] [ az-container-create] kell adja meg a tároló konfigurációjának egy YAML-fájlt, majd a YAML-fájlt, mint az Azure CLI-parancs egy paramétert a parancshoz.

Első lépésként másolja ki a következő yaml-kódot egy új fájlba nevű **üzembe helyezése aci.yaml**.

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

## <a name="view-logs"></a>Naplók megtekintése

Egy tároló a napló kimenetének megtekintéséhez a [az tárolónaplók] [ az-container-logs] parancsot. A `--container-name` argumentum, adja meg a tároló, amelyből a naplók lekérése. Ebben a példában az első tároló meg van adva.

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

A naplók a kiszolgálóoldali autós tároló megtekintéséhez futtassa ugyanezt a parancsot a második tároló nevének megadása.

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

Amint látható, az oldalkocsi egy HTTP-kérelem, hogy így rendszeres időközönként annak érdekében, hogy fut-e a csoport helyi hálózaton keresztül a fő webes alkalmazásba való. Ebben a példában az oldalkocsi figyelmeztetést jelenít meg, ha egy HTTP-válaszkód eltérő 200-as rendben kapott lehetett kibontani.

## <a name="deploy-from-private-registry"></a>A privát regisztrációs adatbázis üzembe helyezése

Egy privát tárolójegyzékben rendszerkép használatához a következők a következő yaml-kódot a környezet módosított értékekkel:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

Például a következő yaml-kódot egy tárolócsoportot amelynek rendszerképet egy privát Azure Container Registry "myregistry" nevű lehívja egyetlen tároló helyezi üzembe:

```YAML
apiVersion: 2018-10-01
location: eastus
name: myContainerGroup2
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: myregistry.azurecr.io/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
  imageRegistryCredentials:
  - server: myregistry.azurecr.io
    username: myregistry
    password: REGISTRY_PASSWORD
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

## <a name="export-container-group-to-yaml"></a>Tárolócsoport YAML exportálása

Egy YAML-fájlt tároló meglévő csoport konfigurációját a az Azure CLI-paranccsal exportálhatja [az tároló exportálási][az-container-export].

Hasznos meg kell őrizni egy tárolócsoport konfigurálása, exportálás lehetővé teszi a tároló csoport konfigurációk tárolása verziókövetés "konfiguráció kódként." Másik lehetőségként használhatja kiindulási pontként az exportált fájlt, egy új konfigurációt a YAML fejlesztése során.

A tárolócsoport, a következő kiállításával korábban létrehozott konfigurációjának exportálása [az tároló exportálási] [ az-container-export] parancsot:

```azurecli-interactive
az container export --resource-group myResourceGroup --name myContainerGroup --file deployed-aci.yaml
```

Nincs kimenet nem jelenik meg a parancs végrehajtása sikeres, de az eredmény megjelenítéséhez a fájl tartalmát is megtekintheti. Ha például az első néhány sorának a `head`:

```console
$ head deployed-aci.yaml
additional_properties: {}
apiVersion: '2018-06-01'
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
```

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatta, egy többtárolós az Azure container instance-példány üzembe helyezéséhez szükséges lépéseket. És a teljes körű Azure Container Instances szolgáltatással, beleértve egy privát Azure container registry használatával tekintse meg az Azure Container Instances oktatóanyagával.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyaga][aci-tutorial]

<!-- LINKS - External -->
[cli-issue-6525]: https://github.com/Azure/azure-cli/issues/6525

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-export]: /cli/azure/container#az-container-export
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

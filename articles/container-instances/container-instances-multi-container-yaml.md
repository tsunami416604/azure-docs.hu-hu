---
title: Több tárolócsoportok Azure tároló példányát az Azure CLI és YAM telepítése
description: Útmutató egy tárolócsoport az Azure-tároló esetekben több tároló telepítését az Azure CLI és YAM-fájl.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: marsma
ms.openlocfilehash: 5dfee15e978d2dba0f50d1dc4b78953698389950
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34851274"
---
# <a name="deploy-a-multi-container-container-group-with-yaml"></a>A több tároló tároló csoport a YAM telepítése

Azure tároló-példányokon támogatja egyetlen állomás alakzatot több tároló telepítését egy [a tárolócsoport](container-instances-container-groups.md). Több tároló tárolócsoportok hasznosak, ha egy alkalmazás oldalkocsi naplózási, figyelési vagy bármely egyéb konfigurációs felépítése amikor egy szolgáltatás kell egy második csatolt folyamat.

Az Azure parancssori felület használatával több tárolócsoportok telepítéséhez két módszer áll rendelkezésre:

* YAM fájl telepítése (Ez a cikk)
* [Erőforrás-kezelő sablon-üzembehelyezés](container-instances-multi-container-group.md)

Miatt a YAM formátum tömörebb természetétől YAM fájl központi telepítése tartalmazza a központi telepítés esetén ajánlott *csak* tároló példányok. Ha további Azure-szolgáltatások erőforrások (például egy Azure fájlok megosztás) tároló példány központi telepítés alkalmával telepítendő kell, a Resource Manager sablon-üzembehelyezés ajánlott.

> [!NOTE]
> Több tárolócsoportok jelenleg csak Linux tárolók. Dolgozunk, hogy minden funkció Windows tárolók, amíg a jelenlegi platform különbségek található [kvótái és az Azure-tároló példányok régiónkénti elérhetőség](container-instances-quotas.md).

## <a name="configure-the-yaml-file"></a>A YAM-fájljának konfigurálása

Több tároló tároló csoport központi telepítése a [az tároló létrehozása] [ az-container-create] kell adja meg a tároló konfigurációjának YAM fájlba, majd továbbítja a YAM fájlt az Azure parancssori felület, a parancsot egy paramétert a parancshoz.

Indítsa el a következő YAM másolásával egy új fájlba nevű **telepítése aci.yaml**.

A YAM fájl határozza meg, a tárolócsoport két tárolókhoz, egy nyilvános IP-cím és két kitett portok. A csoportban lévő első tároló fut az internet felé néző webes alkalmazást. A második tároló, a oldalkocsi rendszeresen hajt végre a tárolócsoport helyi hálózaton keresztül az első tárolóban futó webalkalmazás HTTP-kérelmekre.

```YAML
apiVersion: 2018-06-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: microsoft/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: microsoft/aci-tutorial-sidecar
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

## <a name="deploy-the-container-group"></a>A tároló csoport központi telepítése

Hozzon létre egy erőforráscsoportot a a [az csoport létrehozása] [ az-group-create] parancs:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

A tároló-csoport központi telepítését a [az tároló létrehozása] [ az-container-create] parancs, átadja a YAM fájlt, amelynek argumentuma:

```azurecli-interactive
az container create --resource-group myResourceGroup --name myContainerGroup -f deploy-aci.yaml
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
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-logs"></a>Naplók megtekintése

A kimenet egy tároló használatával megtekintheti a [az tároló naplók] [ az-container-logs] parancsot. A `--container-name` argumentum meghatározza a tároló, amelyből való lekérésére naplókat. Ebben a példában az első tároló van megadva.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Kimenet:

```console
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

```console
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

## <a name="deploy-from-private-registry"></a>A privát beállításjegyzékből telepítése

Egy tároló titkos kép beállításjegyzék használatához tartalmaz a következő YAM értékek környezetnek módosította:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

A következő YAM például egy a tárolócsoport, amelynek kép "myregistry" nevű titkos Azure tároló beállításjegyzékbeli van lekért egyetlen tárolóval telepíti:

```YAML
apiVersion: 2018-06-01
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

## <a name="export-container-group-to-yaml"></a>A tárolócsoport YAM exportálása

Exportálhatja egy meglévő tároló csoport konfigurációs YAM fájlba az Azure CLI paranccsal [az tároló exportálás][az-container-export].

Hasznos, ha meg kell őrizni egy tárolócsoport konfigurációs, exportálás lehetővé teszi a tároló csoportot használó konfigurációk tárolhatnak verziókezelést "kódú konfiguráció". Másik lehetőségként az exportált fájl kiindulási pontként YAM az új konfigurációt ajánlunk.

A tároló csoport a következő kiállításával korábban létrehozott konfigurációjának exportálása [az tároló exportálás] [ az-container-export] parancs:

```azurecli-interactive
az container export --resource-group rg604 --name myContainerGroup --file deployed-aci.yaml
```

Nincs kimenet jelenik meg, ha a parancs végrehajtása sikeres, de megtekintheti az eredményt a fájl tartalmát. Például az első néhány sor a `head`:

```console
$ head deployed-aci.yaml
apiVersion: 2018-02-01-preview
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: microsoft/aci-helloworld:latest
      ports:
```

> [!NOTE]
> Az Azure parancssori felület verzió 2.0.34 van egy [ismert probléma] [ cli-issue-6525] amely exportált tárolócsoportok adja meg a régebbi API verziója **2018-02-01. dátumú előnézeti** (látható az előző JSON kimeneti példát). Ha szeretné telepíteni az exportált YAM fájllal, biztonságosan frissítheti a `apiVersion` értékét az exportált YAM fájlban **2018-06-01**.

## <a name="next-steps"></a>További lépések

Ez a cikk a több tároló Azure tároló példánya telepítéséhez szükséges lépéseket mutatja be. A végpont Azure tároló példányok élményt, beleértve a saját Azure-tárolót beállításjegyzékkel tekintse meg az Azure-tároló példányok.

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

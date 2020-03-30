---
title: Kép készítése a Felhőalapú buildeléssel
description: Az az acr pack build parancs használatával hozzon létre egy tárolórendszerképet egy alkalmazásból, és leküldéses az Azure Container Registry, dockerfile használata nélkül.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: c42bde6bbab5973094302a2d41f004d7600bdf9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087072"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Kép létrehozása és leküldése egy alkalmazásból egy natív felhőalapú buildcsomag használatával

Az Azure CLI `az acr pack build` [`pack`](https://github.com/buildpack/pack) parancs a CLI-eszközt használja a [Buildpacks](https://buildpacks.io/)alkalmazásból, és leküldéses a rendszerképet egy Azure-tároló beállításjegyzékbe. Ez a szolgáltatás lehetővé teszi, hogy gyorsan hozzon létre egy tárolórendszerképet az alkalmazás forráskódjából node.js, Java és más nyelveken, anélkül, hogy docker-fájlt kellene definiálnia.

Használhatja az Azure Cloud Shell vagy az Azure CLI helyi telepítését a cikkben szereplő példák futtatásához. Ha helyileg szeretné használni, a 2.0.70-es vagy újabb verzió szükséges. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="use-the-build-command"></a>A build parancs használata

A felhőbeli natív buildpackek használatával egy tárolórendszerkép létrehozásához és leküldéses futtatásához futtassa az [az acr pack build][az-acr-pack-build] parancsot. Mivel az [az acr build][az-acr-build] parancs létrehoz egy lemezképet, és leküldéses `az acr pack build` egy lemezképet egy Dockerfile forrás- és kapcsolódó kódot, közvetlenül adja meg az alkalmazás forrásfa.

Legalább a következőket adja meg `az acr pack build`a futtatáskor:

* Egy Azure-tároló beállításjegyzék, ahol futtatja a parancsot
* Az eredményül kapott kép neve és címkéje
* Az ACR-feladatok egyik [támogatott környezethelye,](container-registry-tasks-overview.md#context-locations) például egy helyi címtár, egy GitHub-tár vagy egy távoli tarball
* Az alkalmazásnak megfelelő Buildpack builder lemezkép neve. Az Azure Container Registry gyorsítótárazza az építői lemezképeket, például `cloudfoundry/cnb:0.0.34-cflinuxfs3` a gyorsabb buildek érdekében.  

`az acr pack build`támogatja az ACR-feladatok parancsainak egyéb funkcióit, beleértve [a futtatási változókat](container-registry-tasks-reference-yaml.md#run-variables) és [a feladatfuttatást naplónaplókat,](container-registry-tasks-logs.md) amelyeket a program streamel, és későbbi lekérésre is ment.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Példa: Node.js lemezkép készítése a Cloud Foundry Builder segítségével

A következő példa egy tárolórendszerképet hoz létre egy Node.js alkalmazásból az [Azure-Samples/nodejs-docs-hello-world](https://github.com/Azure-Samples/nodejs-docs-hello-world) tárházban, a `cloudfoundry/cnb:0.0.34-cflinuxfs3` szerkesztő használatával. Ezt a szerkesztőt az Azure Container `--pull` Registry gyorsítótárazza, így nincs szükség paraméterre:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

Ez a példa `node-app` létrehozza `1.0` a lemezképet a címkével, és lenyomja azt a *myregistry* tároló rendszerleíró adatbázisába. Ebben a példában a célrendszerleíró adatbázis neve explicit módon elővan készítve a lemezkép nevére. Ha nincs megadva, a rendszerleíró adatbázis bejelentkezési kiszolgálójának neve automatikusan előáll a lemezkép nevére.

A parancs kimenete a kép létrehozásának és lenyomásának előrehaladását mutatja. 

Miután a rendszerkép sikeresen létrejött, futtathatja a Docker, ha telepítve van. Első bejelentkezés a rendszerleíró adatbázisba:

```azurecli
az acr login --name myregistry
```

A kép futtatása:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Tallózással keresse meg `localhost:1337` kedvenc böngészőjében a mintawebalkalmazást. Nyomja `[Ctrl]+[C]` meg a tartály leállításához.

## <a name="example-build-java-image-with-heroku-builder"></a>Példa: Java-lemezkép létrehozása a Heroku builder segítségével

A következő példa egy tárolólemezképet hoz létre a Java alkalmazásból a [buildpack/sample-java-app](https://github.com/buildpack/sample-java-app) repo-ban, a `heroku/buildpacks:18` szerkesztő használatával. A `--pull` paraméter azt adja meg, hogy a parancsnak le kell húznia a legújabb szerkesztőlemezképet. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

Ez a példa `java-app` a parancs futtatási azonosítójával ellátott lemezképet építi fel, és lenyomja azt a *myregistry* tároló rendszerleíró adatbázisába.

A parancs kimenete a kép létrehozásának és lenyomásának előrehaladását mutatja. 

Miután a rendszerkép sikeresen létrejött, futtathatja a Docker, ha telepítve van. Első bejelentkezés a rendszerleíró adatbázisba:

```azurecli
az acr login --name myregistry
```

Futtassa a képet, helyettesítve a képcímkét a runid:Runthe the image, substituting your image tag for *runid:*

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Tallózással keresse meg `localhost:8080` kedvenc böngészőjében a mintawebalkalmazást. Nyomja `[Ctrl]+[C]` meg a tartály leállításához.


## <a name="next-steps"></a>További lépések

Miután létrehoz egy tárolórendszerképet a segítségével, `az acr pack build`telepítheti, mint bármely lemezképet a kívánt cél. Az Azure üzembe helyezési lehetőségei közé tartozik többek között az [App Service](../app-service/containers/tutorial-custom-docker-image.md) vagy az [Azure Kubernetes-szolgáltatás](../aks/tutorial-kubernetes-deploy-cluster.md)futtatását.

Az ACR-feladatok funkcióiról a [Tárolórendszerkép-összeállítás okainak és karbantartásának automatizálása az ACR-feladatokkal című témakörben talál.](container-registry-tasks-overview.md)


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build

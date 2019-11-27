---
title: Rendszerkép létrehozása Felhőbeli natív Buildpack
description: Az az ACR Pack Build paranccsal hozzon létre egy tároló-rendszerképet egy alkalmazásból, és leküldheti Azure Container Registryra anélkül, hogy Docker használ.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 9cd1ae464213027cba3012c93c0ca3894c804750
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456116"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Rendszerkép létrehozása és elküldése egy Felhőbeli natív Buildpack használatával

Az Azure CLI-parancs `az acr pack build` a [`pack`](https://github.com/buildpack/pack) CLI eszközt használja a [Buildpacks](https://buildpacks.io/)-ből, és egy alkalmazást hoz létre, és leküldi a rendszerképet egy Azure Container registrybe. Ez a funkció lehetővé teszi, hogy gyorsan hozzon létre egy tároló-rendszerképet a Node. js-, Java-és más nyelveken anélkül, hogy meg kellene adni egy Docker.

Az Azure CLI Azure Cloud Shell vagy helyi telepítését használhatja a cikkben szereplő példák futtatásához. Ha helyileg szeretné használni, a 2.0.70 vagy újabb verziót kötelező megadni. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="use-the-build-command"></a>A Build parancs használata

Ha Felhőbeli natív Buildpacks szeretné felépíteni és leküldeni egy tároló-rendszerképet, futtassa az az [ACR Pack Build][az-acr-pack-build] parancsot. Míg az az [ACR Build][az-acr-build] parancs egy Docker forrásból és kapcsolódó kódból származó képet hoz létre, és leküldi a `az acr pack build` az alkalmazás forrás-fájának közvetlen megadását.

A `az acr pack build`futtatásakor legalább a következőket kell megadnia:

* Egy Azure Container Registry, amelyen futtatja a parancsot
* Az eredményül kapott képhez tartozó rendszerkép neve és címkéje
* Az ACR-feladatok egyik [támogatott környezeti helye](container-registry-tasks-overview.md#context-locations) , például egy helyi könyvtár, egy GitHub-tárház vagy egy távoli Fez
* Az alkalmazáshoz megfelelő Buildpack Builder-rendszerkép neve. Azure Container Registry gyorsítótárazza a Builder-lemezképeket, például a `cloudfoundry/cnb:0.0.34-cflinuxfs3`t a gyorsabb buildekhez.  

`az acr pack build` támogatja az ACR-feladatok egyéb funkcióit, beleértve a [futtatási változókat](container-registry-tasks-reference-yaml.md#run-variables) és a [tevékenységek futtatására szolgáló naplókat](container-registry-tasks-overview.md#view-task-logs) , amelyeket a rendszer a későbbi lekéréshez is ment.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Példa: Node. js-rendszerkép összeállítása Cloud Foundry Builder-sel

Az alábbi példa egy Node. js-alkalmazásból származó tároló-rendszerképet hoz létre az [Azure-Samples/NodeJS-docs-Hello-World](https://github.com/Azure-Samples/nodejs-docs-hello-world) tárházban a `cloudfoundry/cnb:0.0.34-cflinuxfs3` Builder használatával. A szerkesztőt Azure Container Registry gyorsítótárazza, így nincs szükség `--pull` paraméterre:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

Ez a példa a `node-app` rendszerképet a `1.0` címkével hozza létre, és leküldi a *myregistry* tároló-beállításjegyzékbe. Ebben a példában a célként megadott beállításjegyzék neve explicit módon előtagértéke a rendszerkép nevéhez. Ha nincs megadva, a rendszer automatikusan előtagértéke a beállításjegyzék bejelentkezési kiszolgálójának nevét.

A parancs kimenete a rendszerkép felépítési és leküldéses állapotát mutatja. 

A rendszerkép sikeres felépítése után a Docker használatával futtathatja, ha telepítve van. Először jelentkezzen be a beállításjegyzékbe:

```azurecli
az acr login --name myregistry
```

A rendszerkép futtatása:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Tallózással keresse meg a `localhost:1337` a kedvenc böngészőjében, és tekintse meg a minta webalkalmazást. A tároló leállításához nyomja meg az `[Ctrl]+[C]` gombot.

## <a name="example-build-java-image-with-heroku-builder"></a>Példa: Java-rendszerkép létrehozása a Heroku Builder-vel

Az alábbi példa létrehoz egy tároló rendszerképet a Java-alkalmazásból a [buildpack/Sample-Java-app](https://github.com/buildpack/sample-java-app) tárházban a `heroku/buildpacks:18` Builder használatával. A `--pull` paraméter azt adja meg, hogy a parancsnak le kell kérnie a legújabb Builder-lemezképet. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

Ez a példa a parancs futtatási azonosítójával felcímkézett `java-app` rendszerképet hozza létre, és leküldi a *myregistry* tároló beállításjegyzékbe.

A parancs kimenete a rendszerkép felépítési és leküldéses állapotát mutatja. 

A rendszerkép sikeres felépítése után a Docker használatával futtathatja, ha telepítve van. Először jelentkezzen be a beállításjegyzékbe:

```azurecli
az acr login --name myregistry
```

Futtassa a rendszerképet, és cserélje le a képcímkét a *runid*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Tallózással keresse meg a `localhost:8080` a kedvenc böngészőjében, és tekintse meg a minta webalkalmazást. A tároló leállításához nyomja meg az `[Ctrl]+[C]` gombot.


## <a name="next-steps"></a>Következő lépések

Miután felépíti és leküldte a tároló lemezképét `az acr pack build`segítségével, bármilyen képhez telepítheti a kívánt célra. Az Azure-beli üzembe helyezési lehetőségek közé tartozik a [app Service](../app-service/containers/tutorial-custom-docker-image.md) vagy az [Azure Kubernetes Service](../aks/tutorial-kubernetes-deploy-cluster.md)-ben való futtatás, egyebek között.

Az ACR-feladatok funkcióival kapcsolatos további információkért lásd: [a tárolók rendszerképének automatizálása és karbantartása ACR-feladatokkal](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build

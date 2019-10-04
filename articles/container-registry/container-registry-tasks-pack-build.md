---
title: Azure Container Registry rendszerkép létrehozása alkalmazásból
description: Az az ACR Pack Build paranccsal hozzon létre egy tároló-rendszerképet egy alkalmazásból, és leküldheti Azure Container Registryra anélkül, hogy Docker használ.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/06/2019
ms.author: danlep
ms.openlocfilehash: 4e41bcaff8faef2c4eaec9ae852955d4b7ce354b
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839910"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Rendszerkép létrehozása és elküldése egy Felhőbeli natív Buildpack használatával

Az Azure CLI- `az acr pack build` parancs a [`pack`](https://github.com/buildpack/pack) CLI eszközt használja a [Buildpacks](https://buildpacks.io/)-ből, egy alkalmazás létrehozásához és a rendszerkép Azure Container registrybe való leküldéséhez. Ez a funkció lehetővé teszi, hogy gyorsan hozzon létre egy tároló-rendszerképet a Node. js-, Java-és más nyelveken anélkül, hogy meg kellene adni egy Docker.

Az Azure CLI Azure Cloud Shell vagy helyi telepítését használhatja a cikkben szereplő példák futtatásához. Ha helyileg szeretné használni, a 2.0.70 vagy újabb verziót kötelező megadni. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="use-the-build-command"></a>A Build parancs használata

Ha Felhőbeli natív Buildpacks szeretné felépíteni és leküldeni egy tároló-rendszerképet, futtassa az az [ACR Pack Build][az-acr-pack-build] parancsot. Míg az az [ACR Build][az-acr-build] parancs egy Docker forrásból és kapcsolódó kódból származó képet hoz létre és küld le, `az acr pack build` és közvetlenül az alkalmazás forrásának fáját is megadja.

Legalább a következő futtatásakor `az acr pack build`adja meg a következőket:

* Egy Azure Container Registry, amelyen futtatja a parancsot
* Az eredményül kapott képhez tartozó rendszerkép neve és címkéje
* Az ACR-feladatok egyik [támogatott környezeti helye](container-registry-tasks-overview.md#quick-task) , például egy helyi könyvtár, egy GitHub-tárház vagy egy távoli Fez
* Egy Buildpack-készítő rendszerkép neve, például `cloudfoundry/cnb:bionic`:.  

`az acr pack build`az ACR-feladatok egyéb funkcióit támogatja [](container-registry-tasks-reference-yaml.md#run-variables) , beleértve a futtatási változókat és a [tevékenységek futtatására](container-registry-tasks-overview.md#view-task-logs) szolgáló naplókat, amelyeket a rendszer a későbbi lekéréshez is ment.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Példa: Node. js-rendszerkép összeállítása Cloud Foundry Builder-sel

Az alábbi példa egy tároló rendszerképet hoz létre a Node. js-alkalmazásból az [Azure-Samples/NodeJS-docs-Hello-World](https://github.com/Azure-Samples/nodejs-docs-hello-world) tárházban a `cloudfoundry/cnb:bionic` Builder használatával:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --pull --builder cloudfoundry/cnb:bionic \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

Ez a példa létrehozza `node-app` a képet a `1.0` címkével, és leküldi a *myregistry* tároló beállításjegyzékbe. Itt a célként megadott beállításjegyzék neve explicit módon előtagértéke a rendszerkép nevéhez. Ha nincs megadva, a rendszer automatikusan előtagértéke a beállításjegyzék URL-címét.

A `--pull` paraméter azt adja meg, hogy a parancs lekéri a legújabb Builder-lemezképet.

A parancs kimenete a rendszerkép felépítési és leküldéses állapotát mutatja. 

A rendszerkép sikeres felépítése után a Docker használatával futtathatja, ha telepítve van. Először jelentkezzen be a beállításjegyzékbe:

```azurecli
az acr login --name myregistry
```

A rendszerkép futtatása:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

`localhost:1337` Tallózással keresse meg kedvenc böngészőjét, és tekintse meg a minta webalkalmazást. Nyomja `[Ctrl]+[C]` le a gombot a tároló leállításához.

## <a name="example-build-java-image-with-heroku-builder"></a>Példa: Java-rendszerkép létrehozása a Heroku Builder-vel

Az alábbi példa létrehoz egy tároló rendszerképet a Java-alkalmazásból a [buildpack/Sample-Java-app](https://github.com/buildpack/sample-java-app) tárházban a `heroku/buildpacks:18` Builder használatával:

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

Ez a példa létrehoz `java-app` egy címkét a parancs futtatási azonosítójával, és leküldi azt a *myregistry* tároló-beállításjegyzékbe.

A `--pull` paraméter azt adja meg, hogy a parancs lekéri a legújabb Builder-lemezképet.

A parancs kimenete a rendszerkép felépítési és leküldéses állapotát mutatja. 

A rendszerkép sikeres felépítése után a Docker használatával futtathatja, ha telepítve van. Először jelentkezzen be a beállításjegyzékbe:

```azurecli
az acr login --name myregistry
```

Futtassa a rendszerképet, és cserélje le a képcímkét a *runid*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

`localhost:8080` Tallózással keresse meg kedvenc böngészőjét, és tekintse meg a minta webalkalmazást. Nyomja `[Ctrl]+[C]` le a gombot a tároló leállításához.


## <a name="next-steps"></a>További lépések

Miután létrehozta és leküldte a tároló `az acr pack build`lemezképét a használatával, bármilyen képhez telepítheti azt egy tetszőleges célra. Az Azure-beli üzembe helyezési lehetőségek közé tartozik a [app Service](../app-service/containers/tutorial-custom-docker-image.md) vagy az [Azure Kubernetes Service](../aks/tutorial-kubernetes-deploy-cluster.md)-ben való futtatás, egyebek között.

Az ACR-feladatok funkcióival kapcsolatos további információkért lásd: [a tárolók rendszerképének automatizálása és karbantartása ACR-](container-registry-tasks-overview.md)feladatokkal.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build

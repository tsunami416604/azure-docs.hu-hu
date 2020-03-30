---
title: Többlépéses feladat létrehozása, tesztelése & patch kép
description: Bevezetés a többlépéses feladatok, az ACR-feladatok szolgáltatás az Azure Container Registry, amely feladatalapú munkafolyamatok létrehozásához, teszteléséhez és javításához tárolóképek a felhőben.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 0dcd38559d3f50715f982de4c9c80bfe9c6c8433
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399698"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Többlépéses buildelési, tesztelési és javítási feladatok futtatása ACR-feladatokban

A többlépéses feladatok többlépéses, többtárolós munkafolyamatokkal bővítik az ACR-feladatok egyetlen képépítési és leküldéses képességét. Többlépéses feladatokkal több képet hozhat létre és nyomtasson le sorozatban vagy párhuzamosan. Ezután futtassa ezeket a képeket parancsként egyetlen feladatfuttatáson belül. Minden lépés definiálja a tárolórendszerkép-összeállítási vagy leküldéses műveletet, és egy tároló végrehajtását is meghatározhatja. A többlépéses feladatok minden lépése egy tárolót használ végrehajtási környezetként.

> [!IMPORTANT]
> Ha korábban már létrehozott feladatokat az előzetes verzióban az `az acr build-task` paranccsal, azokat a feladatokat újra létre kell hoznia az [az acr task][az-acr-task] paranccsal.

Futtathat például egy feladatot olyan lépésekkel, amelyek automatizálják a következő logikát:

1. Webalkalmazás-lemezkép létrehozása
1. A webalkalmazás-tároló futtatása
1. Webalkalmazás-tesztkép létrehozása
1. Futtassa a webalkalmazás-teszttárolót, amely teszteket hajt végre a futó alkalmazástárolón
1. Ha a tesztek megfelelnek, hozzon létre egy Helm chart archív csomagot
1. Az `helm upgrade` új Helm-diagram archívumcsomag használatával történő művelet

Minden lépés az Azure-on belül történik, a munka azure-beli számítási erőforrásokra való kiszervezésével és az infrastruktúra-kezelés alóli felszabadításával. Az Azure container-beállításjegyzék mellett csak a használt erőforrásokért kell fizetnie. A díjszabásról az Azure Container [Registry díjszabásának][pricing] **Tárolóösszeállítás** szakaszában talál.


## <a name="common-task-scenarios"></a>Gyakori feladatforgatókönyvek

A többlépéses feladatok a következő logikához hasonló forgatókönyveket tesznek lehetővé:

* Egy vagy több tárolórendszerképet hozhat létre, címkézhet meg és nyomtasson le sorozatban vagy párhuzamosan.
* Az egységteszt és a kódlefedettség eredményeinek futtatása és rögzítése.
* Futtasson és rögzítsen funkcionális teszteket. Az ACR-feladatok több tároló futtatását is támogatják, és egy sor kérést hajtanak végre közöttük.
* Feladatalapú végrehajtás végrehajtása, beleértve a tárolórendszerkép-összeállítás előtti/utáni lépéseit is.
* Telepítsen egy vagy több tárolót a kedvenc üzembe helyezési motorjával a célkörnyezetbe.

## <a name="multi-step-task-definition"></a>Többlépéses feladatdefiníció

Az ACR-feladatok többlépéses feladatait a YAML-fájllépései sorozataként definiálja. Minden lépés megadhat függőségeket egy vagy több korábbi lépés sikeres elvégzésétől. A következő feladatlépés-típusok érhetők el:

* [`build`](container-registry-tasks-reference-yaml.md#build): Hozzon létre egy `docker build` vagy több tárolólemezképet ismerős szintaxissal, sorozatban vagy párhuzamosan.
* [`push`](container-registry-tasks-reference-yaml.md#push): A beépített lemezképek leküldése egy tároló beállításjegyzékébe. A privát beállításjegyzékek, például az Azure Container Registry támogatottak, csakúgy, mint a nyilvános Docker Hub.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Futtasson egy tárolót úgy, hogy az a futó feladat környezetében függvényként működjön. Paramétereket adhat át a tároló `[ENTRYPOINT]`, és adja meg a tulajdonságokat, mint env, leválasztás, és más ismerős `docker run` paramétereket. A `cmd` lépéstípusa lehetővé teszi az egység- és funkcionális tesztelést, egyidejű tárolóvégrehajtással.

A következő kódrészletek bemutatják, hogyan kombinálható ezek a tevékenységlépéstípusok. A többlépéses feladatok olyan egyszerűek lehetnek, mint egyetlen lemezkép létrehozása egy Docker-fájlból, és a rendszerleíró adatbázisba való lenyomása a következőhöz hasonló YAML-fájllal:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
  - push: ["$Registry/hello-world:$ID"]
```

Vagy bonyolultabb, mint például ez a fiktív többlépéses definíció, amely tartalmazza a lépéseket a build, teszt, helm csomag, és helm üzembe helyezése (tároló rendszerleíró adatbázis és Helm tárház konfiguráció nem jelenik meg):

```yml
version: v1.1.0
steps:
  - id: build-web
    build: -t $Registry/hello-world:$ID .
    when: ["-"]
  - id: build-tests
    build -t $Registry/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["$Registry/helloworld:$ID"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: $Registry/helloworld:$ID
  - id: funcTests
    cmd: $Registry/helloworld:$ID
    env: ["host=helloworld:80"]
  - cmd: $Registry/functions/helm package --app-version $ID -d ./helm ./helm/helloworld/
  - cmd: $Registry/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image=$Registry/helloworld:$ID
```

Tekintse meg a [feladat példák](container-registry-tasks-samples.md) at többlépéses feladat YAML-fájlok és Dockerfiles több forgatókönyv esetén.

## <a name="run-a-sample-task"></a>Mintafeladat futtatása

A feladatok támogatják mind a manuális végrehajtást, az úgynevezett "gyors futtatást", mind az automatikus végrehajtást a Git véglegesítése vagy az alaprendszerkép frissítése esetén.

Egy feladat futtatásához először definiálja a feladat lépéseit egy YAML-fájlban, majd végrehajtja az Azure CLI parancsot [az acr run][az-acr-run].

Íme egy példa az Azure CLI parancs, amely egy feladatot futtat egy mintafeladat YAML-fájl használatával. A lépéseket építeni, majd nyomja meg a képet. Frissítse `\<acrName\>` a saját Azure-tároló beállításjegyzék ének nevével a parancs futtatása előtt.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

A feladat futtatásakor a kimenetnek meg kell jelenlennie a YAML-fájlban definiált egyes lépések előrehaladásáról. A következő kimenetben a `acb_step_0` lépések `acb_step_1`a és a .

```azurecli
az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

```output
Sending context to registry: myregistry...
Queued a run with ID: yd14
Waiting for an agent...
2018/09/12 20:08:44 Using acb_vol_0467fe58-f6ab-4dbd-a022-1bb487366941 as the home volume
2018/09/12 20:08:44 Creating Docker network: acb_default_network
2018/09/12 20:08:44 Successfully set up Docker network: acb_default_network
2018/09/12 20:08:44 Setting up Docker configuration...
2018/09/12 20:08:45 Successfully set up Docker configuration
2018/09/12 20:08:45 Logging in to registry: myregistry.azurecr-test.io
2018/09/12 20:08:46 Successfully logged in
2018/09/12 20:08:46 Executing step: acb_step_0
2018/09/12 20:08:46 Obtaining source code and scanning for dependencies...
2018/09/12 20:08:47 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  109.6kB
Step 1/1 : FROM hello-world
 ---> 4ab4c602aa5e
Successfully built 4ab4c602aa5e
Successfully tagged myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:48 Executing step: acb_step_1
2018/09/12 20:08:48 Pushing image: myregistry.azurecr-test.io/hello-world:yd14, attempt 1
The push refers to repository [myregistry.azurecr-test.io/hello-world]
428c97da766c: Preparing
428c97da766c: Layer already exists
yd14: digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812 size: 524
2018/09/12 20:08:55 Successfully pushed image: myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:55 Step id: acb_step_0 marked as successful (elapsed time in seconds: 2.035049)
2018/09/12 20:08:55 Populating digests for step id: acb_step_0...
2018/09/12 20:08:57 Successfully populated digests for step id: acb_step_0
2018/09/12 20:08:57 Step id: acb_step_1 marked as successful (elapsed time in seconds: 6.832391)
The following dependencies were found:
- image:
    registry: myregistry.azurecr-test.io
    repository: hello-world
    tag: yd14
    digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
  git: {}


Run ID: yd14 was successful after 19s
```

A Git véglegesítése vagy az alaprendszerkép-frissítés automatikus buildjeiről további információt a [Lemezkép-buildek automatizálása](container-registry-tutorial-build-task.md) és az [Alapképfrissítés oktatóanyag-készítési](container-registry-tutorial-base-image-update.md) cikkeinek automatizálása című témakörben talál.

## <a name="next-steps"></a>További lépések

A többlépéses feladathivatkozást és a példákat itt találja:

* [Feladathivatkozás](container-registry-tasks-reference-yaml.md) – Tevékenységlépés-típusok, tulajdonságaik és használatuk.
* [Feladatpéldák](container-registry-tasks-samples.md) `task.yaml` – Példa és Docker-fájlok több forgatókönyvhöz, egyszerűen összetett.
* [Cmd repo](https://github.com/AzureCR/cmd) – tárolók gyűjteménye az ACR-feladatok parancsaiként.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task]: /cli/azure/acr/task
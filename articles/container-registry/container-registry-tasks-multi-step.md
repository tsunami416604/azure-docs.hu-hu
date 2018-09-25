---
title: Kép buildelési, tesztelési és javítás az Azure Container Registry több lépésből álló feladatokat automatizálása
description: Egy bevezető több lépésből álló feladatokat, egy ACR-feladatokat az Azure Container Registry létrehozásához, teszteléséhez és javítás tárolórendszerképeket a felhőben a feladat-alapú munkafolyamatok biztosító funkcióját.
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: marsma
ms.openlocfilehash: b88fbf927cff51d78d95ed9501921d528b22b455
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047528"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Többlépéses buildelési, tesztelési és patch feladatok futtatása az ACR-feladatokban

Több lépésből álló feladatokat kiterjesztheti a egyetlen build és a leküldéses képesség ACR feladatokat több lépésből álló, több container alapú munkafolyamatokkal. Többlépéses feladatok segítségével és leküldéses számos olyan rendszerkép található, sorozat- vagy párhuzamosan, és ezeket a képeket futtató parancsok belül egyetlen feladat futtatása. Minden lépés meghatároz egy tárolórendszerképet hozhat létre, vagy küldje le a műveletet, és a is megadhatja, hogy a tároló végrehajtásának. A többlépéses feladat minden lépése tárolót használja a végrehajtási környezetet.

> [!IMPORTANT]
> Ha az előzetes során korábban létrehozott feladatok a `az acr build-task` parancsot, ezeket a feladatokat kell újra létrehozni a [az acr feladat] [ az-acr-task] parancsot.

Például futtathatja egy feladatot, amely automatizálja az alábbi lépésekkel:

1. A webes alkalmazás rendszerkép összeállítása
1. A webalkalmazás tárolójának futtatása
1. Egy alkalmazás webes teszt kép létrehozása
1. Futtassa a webes teszt alkalmazástárolók, szemben a futó teszteket hajt végre, amely alkalmazástárolók
1. Ha a vizsgálatok sikeresek, egy Helm-diagram archív csomag létrehozása
1. Hajtsa végre egy `helm upgrade` Helm-diagram archív új csomag telepítése

Minden lépést az Azure-feladatkiszervezést a munkát az Azure számítási erőforrásokat, és így Ön az infrastruktúra belül menjenek végbe. Amellett, hogy az Azure container registry kell fizetnie csak az erőforrások után. Díjszabási információkért tekintse meg a **tárolót hozhat létre** szakasz [Azure Container Registry díjszabás][pricing].

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="common-task-scenarios"></a>Általános tevékenység-forgatókönyvek

Többlépéses feladatok lehetővé teszik a következőkhöz hasonló forgatókönyveket:

* Címkét, és leküldéses egy vagy több tároló-lemezképet, sorozat- vagy párhuzamosan.
* Futtassa, és rögzítheti egység teszt- és kód lefedettség eredményeket.
* Futtassa, és funkcionális tesztek rögzítése. ACR-feladatok több tárolót futtató, a közöttük kérelmek sorozatának végrehajtása támogatja.
* Feladatalapú végrehajtása, beleértve egy tároló-rendszerkép összeállítását az előkészítő/utólagos lépések végrehajtása.
* A kedvenc telepítési motor a célkörnyezetben, egy vagy több tárolók üzembe helyezése.

## <a name="multi-step-task-definition"></a>A többlépéses feladat meghatározása

A többlépéses feladat az ACR-feladatok több lépést jelentenek egy YAML-fájlt van definiálva. Az egyes lépések függőségek egy vagy több korábbi lépés sikeres befejezése is meghatározhat. A következő lépésben tevékenységtípust érhetők el:

* [`build`](container-registry-tasks-reference-yaml.md#build): Egy vagy több tárolórendszerképek használata ismerős létrehozása `docker build` szintaxist, sorozat- vagy párhuzamosan.
* [`push`](container-registry-tasks-reference-yaml.md#push): Leküldéses készített rendszerképek továbbíthat egy tárolóregisztrációs adatbázisba. Privát beállításjegyzékek, például az Azure Container Registry támogatottak, mert a nyilvános Docker Hub.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Egy tárolóban, futtassa, hogy tudjon működni a futó feladat kontextusában függvényében. Paramétereket adhat át a tároló `[ENTRYPOINT]`, és adja meg a tulajdonságokat, mint az env, válassza le, és más ismerős `docker run` paramétereket. A `cmd` lehetővé teszi a lépés típusát egység és funkcionális tesztelés, a tároló egyidejű végrehajtása.

Lehet, hogy több lépésből álló feladatokat egyszerűen létrehozását, és hogyan lehet továbbítani rá egy lemezképet:

```yaml
version: 1.0-preview-1
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Vagy összetettebb, például ezt a feladatot, amely build lépéseit tartalmazza, tesztelés, a helm-csomagot és a helm telepítése:

```yaml
version: 1.0-preview-1
steps:
  - id: build-web
    build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
    when: ["-"]
  - id: build-tests
    build -t {{.Run.Registry}}/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["{{.Run.Registry}}/helloworld:{{.Run.ID}}"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
  - id: funcTests
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
    env: ["host=helloworld:80"]
  - cmd: {{.Run.Registry}}/functions/helm package --app-version {{.Run.ID}} -d ./helm ./helm/helloworld/
  - cmd: {{.Run.Registry}}/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image={{.Run.Registry}}/helloworld:{{.Run.ID}}
```

## <a name="run-a-sample-task"></a>Egy mintául szolgáló feladat futtatása

Feladat támogatja a "gyors futtatja," nevű, mindkét manuális végrehajtását, és a Git commit vagy alap rendszerképet automatikus végrehajtás frissítése.

Olyan feladatot futtat, akkor először definiálhatja a feladat lépéseket egy YAML-fájlt, majd hajtsa végre az Azure CLI-parancsot [futtatása az acr][az-acr-run].

Íme egy példa futtat egy feladatot egy minta feladat YAML-fájlt használó Azure CLI-parancsot. A lépések hozhat létre, és ezután a rendszerkép leküldése. Frissítés `\<acrName\>` a parancs futtatása előtt a saját Azure tárolóregisztrációs adatbázis nevére.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

A feladat futtatásakor a kimeneti meg kell jelennie a YAML fájlban meghatározott egyes lépéseinek állapotát. A következő kimenet, a lépések látszanak `acb_step_0` és `acb_step_1`.

```console
$ az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
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

A Git commit vagy alap rendszerképet update automatizált buildekig kapcsolatos további információkért lásd: a [rendszerképek létrehozásának automatizálása](container-registry-tutorial-build-task.md) és [rendszerképek frissítési létrehozásának kiinduló](container-registry-tutorial-base-image-update.md) oktatóanyag cikkeire.

## <a name="preview-feedback"></a>Előzetes verzióval kapcsolatos visszajelzések

Amíg az ACR-feladatokat a többlépéses feladat funkció előzetes verzióban érhető el, Felkérjük, hogy visszajelzést. Számos visszajelzés csatornák érhetők el:

* [Problémák](https://aka.ms/acr/issues) – megtekintheti a meglévő hibák és problémák, és újakat naplózása
* [UserVoice](https://aka.ms/acr/uservoice) -szavazhat a meglévő kérések funkciót, vagy hozzon létre új kérések
* [Beszélgetés](https://aka.ms/acr/feedback) -léphet kapcsolatba az Azure Container Registry vitafórum a Stack Overflow Közösséggel

## <a name="next-steps"></a>További lépések

Többlépéses feladatok referenciái és a példákat itt találja:

* [Tevékenység-hivatkozás](container-registry-tasks-reference-yaml.md) -lépés típusok, azok tulajdonságai és használatának.
* [Tevékenység-példák] [ task-examples] – példa `task.yaml` egyszerűektől egészen az összetettekig számos forgatókönyv esetében a fájlokat.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr/run#az-acr-run
[az-acr-task]: /cli/azure/acr#az-acr-task
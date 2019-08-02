---
title: Azure Container Registry feladatok hivatkozása – YAML
description: Az ACR-feladatok YAML kapcsolatos feladatok definiálásának referenciája, beleértve a feladatok tulajdonságait, a lépések típusát, a lépés tulajdonságait és a beépített változókat.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/12/2019
ms.author: danlep
ms.openlocfilehash: 27c38f51104dfb170c59860c96a8e3a86973bb1e
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638913"
---
# <a name="acr-tasks-reference-yaml"></a>ACR-feladatok hivatkozása: YAML

Az ACR-feladatok többlépéses feladatának definíciója egy tároló-központú számítási primitívet biztosít a tárolók létrehozásához, teszteléséhez és javításához. Ez a cikk a több lépésből álló feladatokat definiáló YAML-fájlok parancsait, paramétereit, tulajdonságait és szintaxisát ismerteti.

Ez a cikk a többlépéses tevékenységek YAML-fájljainak ACR-feladatokhoz való létrehozását ismerteti. Ha szeretné bevezetni az ACR-feladatokat, tekintse meg az [ACR-feladatok áttekintését](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>ACR-Task. YAML fájlformátum

Az ACR-feladatok támogatják a többlépéses feladatokat a szabványos YAML szintaxisban. Egy YAML-fájlban definiálhatja a feladatok lépéseit. Ezután manuálisan futtathatja a feladatot a fájl az az [ACR Run][az-acr-run] paranccsal való átadásával. Vagy a fájl használatával hozzon létre egy feladatot az [az ACR Task Create][az-acr-task-create] paranccsal, amelyet a git-véglegesítő vagy az alaprendszerkép frissítése automatikusan indít el. Bár ez a cikk a `acr-task.yaml` lépéseket tartalmazó fájlra hivatkozik, az ACR-feladatok a [támogatott kiterjesztésű](#supported-task-filename-extensions)érvényes fájlneveket támogatják.

A legfelső szintű `acr-task.yaml` primitívek a **feladatok tulajdonságai**, a **Step types**és a **Step tulajdonságok**:

* A [Feladat tulajdonságai](#task-properties) a feladat-végrehajtás során minden lépésre érvényesek. A globális feladatok számos tulajdonsága létezik, beleértve a következőket:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* A [feladat lépésének típusai](#task-step-types) a feladatban végrehajtható műveletek típusait jelölik. Három lépésből álló típus létezik:
  * `build`
  * `push`
  * `cmd`
* A [feladat lépésének tulajdonságai](#task-step-properties) olyan paraméterek, amelyek egy adott lépésre vonatkoznak. Több lépésből álló tulajdonság is létezik, beleértve a következőket:
  * `startDelay`
  * `timeout`
  * `when`
  * ... és még sok más.

A `acr-task.yaml` fájlok alapformátuma, beleértve néhány gyakori lépés tulajdonságait is, az alábbiak szerint történik. Noha az összes rendelkezésre álló lépés tulajdonságai vagy a lépés típusának használata nem teljes körű, az egyszerű fájlformátum gyors áttekintést nyújt.

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
    startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>Támogatott feladatok fájlnév-bővítményei

Az ACR-feladatok több fájlnevet foglalnak `.yaml`magukban, például azt, hogy a rendszer feldolgozza a fájlt. Az alábbi listán *nem* szereplő bővítmények az ACR-feladatok Docker:. YAML,. YML,. toml,. JSON,. sh,. bash,. zsh,. ps1,. ps,. cmd,. bat,. TS,. js,. php,. Másolás,. RB,. lua

A YAML az ACR-feladatok által jelenleg támogatott fájlformátum. A többi filename-bővítmény a lehetséges jövőbeli támogatáshoz van fenntartva.

## <a name="run-the-sample-tasks"></a>A minta feladatok futtatása

A jelen cikk következő részeiben több minta feladatsor található. A minta feladatok nyilvános GitHub-tárházban, [Azure-Samples/ACR-][acr-tasks]feladatokban találhatók. Ezeket az Azure CLI parancs az [ACR Run][az-acr-run]paranccsal futtathatja. A minta parancsai a következőhöz hasonlóak:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

A minta parancsok formázása feltételezi, hogy konfigurálta az alapértelmezett beállításjegyzéket az Azure CLI-ben, így kihagyják a `--registry` paramétert. Az alapértelmezett beállításjegyzék konfigurálásához használja az az [configure][az-configure] parancsot `--defaults` az paraméterrel, amely egy `acr=REGISTRY_NAME` értéket fogad el.

Ha például az Azure CLI-t egy "myregistry" nevű alapértelmezett beállításjegyzékgel szeretné konfigurálni:

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Feladat tulajdonságai

A feladat tulajdonságai általában egy `acr-task.yaml` fájl tetején jelennek meg, és globális tulajdonságok, amelyek a feladat lépéseinek teljes végrehajtása során érvényesek. A globális tulajdonságok némelyike felülbírálható egy adott lépésen belül.

| Tulajdonság | Type | Választható | Leírás | Felülbírálás támogatott | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | Karakterlánc | Igen | Az ACR Tasks `acr-task.yaml` szolgáltatás által elemzett fájl verziója. Míg az ACR-feladatok a visszamenőleges kompatibilitás fenntartására törekednek, ez az érték lehetővé teszi az ACR-feladatok számára a kompatibilitás fenntartását egy meghatározott verzión belül. Ha nincs megadva, az alapértelmezett érték a legújabb verzió. | Nem | Nincsenek |
| `stepTimeout` | int (másodperc) | Igen | A lépés által futtatható másodpercek maximális száma. Ha a tulajdonság meg van adva egy feladathoz, az az `timeout` összes lépés alapértelmezett tulajdonságát állítja be. Ha a `timeout` tulajdonságot egy lépésben adja meg, a felülbírálja a feladat által megadott tulajdonságot. | Igen | 600 (10 perc) |
| `workingDirectory` | Karakterlánc | Igen | A tároló munkakönyvtára a futtatókörnyezetben. Ha a tulajdonság meg van adva egy feladathoz, az az `workingDirectory` összes lépés alapértelmezett tulajdonságát állítja be. Ha egy lépésben meg van adva, a felülbírálja a feladat által megadott tulajdonságot. | Igen | `$HOME` |
| `env` | [karakterlánc, karakterlánc,...] | Igen |  A feladathoz `key=value` tartozó környezeti változókat meghatározó karakterláncok tömbje. Ha a tulajdonság meg van adva egy feladathoz, az az `env` összes lépés alapértelmezett tulajdonságát állítja be. Ha egy lépésben meg van adva, akkor felülbírálja a feladatból örökölt környezeti változókat. | Nincsenek |
| `secrets` | [titok, titkos kód,...] | Igen | [Titkos](#secret) objektumok tömbje. | Nincsenek |
| `networks` | [hálózat, hálózat,...] | Igen | [Hálózati](#network) objektumok tömbje. | None |

### <a name="secret"></a>secret

A titkos objektum a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Type | Választható | Leírás | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | Karakterlánc | Nem | A titok azonosítója. | None |
| `keyvault` | Karakterlánc | Igen | A Azure Key Vault titkos URL-cím. | Nincsenek |
| `clientID` | Karakterlánc | Igen | Az Azure-erőforrások [felhasználó által hozzárendelt felügyelt identitásának ügyfél-](container-registry-tasks-authentication-managed-identity.md) azonosítója. | None |

### <a name="network"></a>network

A hálózati objektum a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Type | Választható | Leírás | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | Karakterlánc | Nem | A hálózat neve. | Nincsenek |
| `driver` | Karakterlánc | Igen | A hálózat kezelésére szolgáló illesztőprogram. | Nincsenek |
| `ipv6` | bool | Igen | Azt jelzi, hogy engedélyezve van-e az IPv6-hálózat. | `false` |
| `skipCreation` | bool | Igen | Megadhatja, hogy kihagyja-e a hálózat létrehozását. | `false` |
| `isDefault` | bool | Igen | Azt határozza meg, hogy a hálózat az Azure Container Registry által biztosított alapértelmezett hálózat-e | `false` |

## <a name="task-step-types"></a>Feladat lépésének típusai

Az ACR-feladatok három lépésből álló típusokat támogatnak. Minden lépés típusa több tulajdonságot is támogat, részletesen az egyes lépésekhez tartozó szakaszban.

| Lépés típusa | Leírás |
| --------- | ----------- |
| [`build`](#build) | Egy jól ismert `docker build` szintaxissal hozza létre a tároló képét. |
| [`push`](#push) | Végrehajtja `docker push` az újonnan létrehozott vagy újracímkézett rendszerképeket egy tároló-beállításjegyzékbe. A Azure Container Registry, a többi privát beállításjegyzék és a nyilvános Docker hub is támogatott. |
| [`cmd`](#cmd) | Futtat egy tárolót parancsként, a tárolónak `[ENTRYPOINT]`átadott paraméterekkel. A `cmd` lépés típusa támogatja a `detach`( `env`z), és más `docker run` , ismerős parancssori kapcsolókat, amelyek lehetővé teszik az egység és a funkcionális tesztelést egyidejű tároló-végrehajtással. |

## <a name="build"></a>építeni

Hozzon létre egy tároló-rendszerképet. A `build` lépés típusa egy több-bérlős, biztonságos működést `docker build` jelent a felhőben, amely első osztályú primitív.

### <a name="syntax-build"></a>Szintaxis: build

```yml
version: v1.0.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

A `build` lépés típusa a következő táblázatban szereplő paramétereket támogatja. A `build` lépés típusa a [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) `--build-arg` parancs összes felépítési lehetőségét is támogatja, például a létrehozási idő változóinak beállítására.

| Paraméter | Leírás | Választható |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Meghatározza a beépített rendszerkép `image:tag` teljes körű minősítését.<br /><br />Mivel a lemezképek belső feladatokhoz használhatók, például a funkcionális tesztek, nem minden rendszerképhez `push` szükséges egy beállításjegyzék. Ahhoz azonban, hogy egy adott rendszerkép egy adott feladat végrehajtásán belül legyen, a képnek hivatkoznia kell erre a névre.<br /><br />Az `az acr build`ACR-feladatok futtatása ellentétben nem biztosítja az alapértelmezett leküldéses viselkedést. Az ACR-feladatok esetében az alapértelmezett forgatókönyv feltételezi, hogy képes felépíteni, érvényesíteni, majd leküldeni egy rendszerképet. Lásd [](#push) : leküldés, hogyan lehet leküldeni a létrehozott rendszerképeket. | Igen |
| `-f` &#124; `--file` | Megadja az átadott `docker build`Docker. Ha nincs megadva, a rendszer a környezet gyökerében lévő alapértelmezett Docker feltételezi. Docker megadásához adja át a fájlnevet a környezet gyökeréhez képest. | Igen |
| `context` | Az átadott `docker build`gyökérkönyvtár. Az egyes feladatok gyökérkönyvtára egy megosztott [workingDirectory](#task-step-properties)van beállítva, és tartalmazza a társított git klónozott könyvtár gyökerét. | Nem |

### <a name="properties-build"></a>Tulajdonságok: létrehozás

A `build` lépés típusa a következő tulajdonságokat támogatja. A tulajdonságok részleteit a cikk [feladat lépés tulajdonságai](#task-step-properties) szakaszában találja.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Választható |
| `disableWorkingDirectoryOverride` | bool | Választható |
| `entryPoint` | Karakterlánc | Választható |
| `env` | [karakterlánc, karakterlánc,...] | Választható |
| `expose` | [karakterlánc, karakterlánc,...] | Választható |
| `id` | Karakterlánc | Választható |
| `ignoreErrors` | bool | Választható |
| `isolation` | Karakterlánc | Választható |
| `keep` | bool | Választható |
| `network` | object | Választható |
| `ports` | [karakterlánc, karakterlánc,...] | Választható |
| `pull` | bool | Választható |
| `repeat` | int | Választható |
| `retries` | int | Választható |
| `retryDelay` | int (másodperc) | Választható |
| `secret` | object | Választható |
| `startDelay` | int (másodperc) | Választható |
| `timeout` | int (másodperc) | Választható |
| `when` | [karakterlánc, karakterlánc,...] | Választható |
| `workingDirectory` | Karakterlánc | Választható |

### <a name="examples-build"></a>Példák: build

#### <a name="build-image---context-in-root"></a>Rendszerkép-környezet létrehozása a gyökérben

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Rendszerkép létrehozása – környezet az alkönyvtárban

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>nyomja

Egy vagy több létrehozott vagy újracímkézett rendszerkép leküldése egy tároló-beállításjegyzékbe. Támogatja a privát beállításjegyzékek, például a Azure Container Registry vagy a nyilvános Docker hub továbbítását.

### <a name="syntax-push"></a>Szintaxis: leküldés

A `push` lépés típusa támogatja a képek gyűjteményét. A YAML-gyűjtemény szintaxisa beágyazott és beágyazott formátumokat is támogat. Egy adott rendszerkép kitolása jellemzően beágyazott szintaxis használatával történik:

```yml
version: v1.0.0
steps:
  # Inline YAML collection syntax
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

A jobb olvashatóság érdekében beágyazott szintaxist használjon több rendszerkép leküldésekor:

```yml
version: v1.0.0
steps:
  # Nested YAML collection syntax
  - push:
    - {{.Run.Registry}}/hello-world:{{.Run.ID}}
    - {{.Run.Registry}}/hello-world:latest
```

### <a name="properties-push"></a>Tulajdonságok: leküldés

A `push` lépés típusa a következő tulajdonságokat támogatja. A tulajdonságok részleteit a cikk [feladat lépés tulajdonságai](#task-step-properties) szakaszában találja.

| | | |
| -------- | ---- | -------- |
| `env` | [karakterlánc, karakterlánc,...] | Választható |
| `id` | Karakterlánc | Választható |
| `ignoreErrors` | bool | Választható |
| `startDelay` | int (másodperc) | Választható |
| `timeout` | int (másodperc) | Választható |
| `when` | [karakterlánc, karakterlánc,...] | Választható |

### <a name="examples-push"></a>Példák: leküldés

#### <a name="push-multiple-images"></a>Több rendszerkép leküldése

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Létrehozás, leküldés és Futtatás

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

A `cmd` lépés típusa egy tárolót futtat.

### <a name="syntax-cmd"></a>Szintaxis: cmd

```yml
version: v1.0.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Tulajdonságok: cmd

A `cmd` lépés típusa a következő tulajdonságokat támogatja:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Választható |
| `disableWorkingDirectoryOverride` | bool | Választható |
| `entryPoint` | Karakterlánc | Választható |
| `env` | [karakterlánc, karakterlánc,...] | Választható |
| `expose` | [karakterlánc, karakterlánc,...] | Választható |
| `id` | sztring | Választható |
| `ignoreErrors` | bool | Választható |
| `isolation` | Karakterlánc | Választható |
| `keep` | bool | Választható |
| `network` | object | Választható |
| `ports` | [karakterlánc, karakterlánc,...] | Választható |
| `pull` | bool | Választható |
| `repeat` | int | Választható |
| `retries` | int | Választható |
| `retryDelay` | int (másodperc) | Választható |
| `secret` | object | Választható |
| `startDelay` | int (másodperc) | Választható |
| `timeout` | int (másodperc) | Választható |
| `when` | [karakterlánc, karakterlánc,...] | Választható |
| `workingDirectory` | Karakterlánc | Választható |

Ezekről a tulajdonságokról a jelen cikk [feladat lépés tulajdonságai](#task-step-properties) szakaszában olvashat bővebben.

### <a name="examples-cmd"></a>Példák: cmd

#### <a name="run-hello-world-image"></a>A Hello-World rendszerkép futtatása

Ez a parancs végrehajtja `hello-world.yaml` a válaszfájlt, amely a Docker hub-on lévő [Hello-World](https://hub.docker.com/_/hello-world/) rendszerképre hivatkozik.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Bash-rendszerkép és echo "Helló világ" futtatása

Ez a parancs végrehajtja `bash-echo.yaml` a fájlt, amely a Docker hub [bash](https://hub.docker.com/_/bash/) -képére hivatkozik.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Adott bash-Képcímke futtatása

Egy adott rendszerkép-verzió futtatásához adja meg a címkét `cmd`a következőben:.

Ez a parancs végrehajtja `bash-echo-3.yaml` a válaszfájlt, amely a Docker hub [bash: 3.0](https://hub.docker.com/_/bash/) képére hivatkozik.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Egyéni lemezképek futtatása

A `cmd` lépés típusa a szabványos `docker run` formátum használatával hivatkozik a képekre. A beállításjegyzékbe nem tartozó rendszerképeket a rendszer feltételezi, hogy a docker.io származik. Az előző példában a következőképpen lehet megjeleníteni:

```yml
version: v1.0.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

A standard szintű `docker run` képhivatkozási `cmd` konvenció használatával bármilyen titkos beállításjegyzékből vagy a nyilvános Docker hub-ból futtathat képeket. Ha ugyanabban a beállításjegyzékben hivatkozik a lemezképekre, amelyben az ACR-feladat végrehajtása történik, nincs szükség beállításjegyzékbeli hitelesítő adatok megadására.

* Azure Container registryből származó rendszerkép futtatása

    Cserélje `[myregistry]` le a nevet a beállításjegyzék nevére:

    ```yml
    version: v1.0.0
    steps:
        - cmd: [myregistry].azurecr.io/bash:3.0 echo hello world
    ```

* A beállításjegyzék-hivatkozás általánosítása futtatási változóval

    Ahelyett, hogy a beállításjegyzékbeli nevet `acr-task.yaml` a fájlban rögzítette, a futtatási [változó](#run-variables)használatával még hatékonyabbá teheti. A `Run.Registry` változó futásidőben lesz kibontva annak a beállításjegyzéknek a nevére, amelyben a feladatot végrehajtja.

    Az előző feladat általánosítása érdekében, hogy az bármilyen Azure Container registryben működjön, hivatkozzon a [Run. Registry](#runregistry) változóra a rendszerkép nevében:

    ```yml
    version: v1.0.0
    steps:
      - cmd: {{.Run.Registry}}/bash:3.0 echo hello world
    ```

## <a name="task-step-properties"></a>Feladat lépésének tulajdonságai

Az egyes lépésekhez tartozó típusok több, a típusához megfelelő tulajdonságot is támogatnak. A következő táblázat az összes elérhető lépés tulajdonságait meghatározza. Nem minden lépés típus támogatja az összes tulajdonságot. Ha szeretné megtekinteni, hogy mely tulajdonságok érhetők el az egyes lépésekhez, tekintse meg a [cmd](#cmd), a [Build](#build)és a leküldéses lépés típusa hivatkozás szakaszokat. [](#push)

| Tulajdonság | Type | Választható | Leírás | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Igen | Azt jelzi, hogy a tárolót le kell-e választani a futtatáskor. | `false` |
| `disableWorkingDirectoryOverride` | bool | Igen | Meghatározza, hogy `workingDirectory` le kell-e tiltani a felülbírálási funkciót. Ezzel együtt `workingDirectory` a paranccsal teljes mértékben szabályozhatja a tároló munkakönyvtárát. | `false` |
| `entryPoint` | Karakterlánc | Igen | Felülbírálja `[ENTRYPOINT]` egy lépés tárolóját. | Nincsenek |
| `env` | [karakterlánc, karakterlánc,...] | Igen | A lépés környezeti változóit meghatározó karakterláncok `key=value` tömbje. | Nincsenek |
| `expose` | [karakterlánc, karakterlánc,...] | Igen | A tárolóból kitett portok tömbje. |  None |
| [`id`](#example-id) | Karakterlánc | Igen | Egyedi módon azonosítja a lépést a tevékenységen belül. A feladat egyéb lépései hivatkozhatnak egy adott lépésre `id`, például a függőségi `when`ellenőrzésre a használatával.<br /><br />A `id` a futó tároló neve is. A feladat más tárolókban futó folyamatai a DNS-állomásnévként `id` , illetve a Docker-naplók [id] használatával való elérésére is hivatkozhatnak, például:. | `acb_step_%d`, ahol `%d` a a YAML-fájlban legfelüli lépés 0 alapú indexe |
| `ignoreErrors` | bool | Igen | Azt jelzi, hogy a lépés sikeres-e, függetlenül attól, hogy hiba történt-e a tároló végrehajtása során. | `false` |
| `isolation` | Karakterlánc | Igen | A tároló elkülönítési szintje. | `default` |
| `keep` | bool | Igen | Azt határozza meg, hogy a lépés tárolóját a végrehajtás után kell-e megőrizni. | `false` |
| `network` | object | Igen | Azonosítja azt a hálózatot, amelyben a tároló fut. | Nincsenek |
| `ports` | [karakterlánc, karakterlánc,...] | Igen | A tárolóból a gazdagépre közzétett portok tömbje. |  None |
| `pull` | bool | Igen | Azt határozza meg, hogy a tároló lekérését kényszerítse-e a gyorsítótár működésének megakadályozása érdekében. | `false` |
| `privileged` | bool | Igen | Azt határozza meg, hogy a tárolót emelt szintű módban kívánja-e futtatni. | `false` |
| `repeat` | int | Igen | A tárolók végrehajtásának megismétléséhez szükséges újrapróbálkozások száma. | 0 |
| `retries` | int | Igen | Az újrapróbálkozások száma, ha egy tároló nem tudja végrehajtani a végrehajtását. Egy újrapróbálkozás csak akkor próbálkozik, ha egy tároló kilépési kódja nem nulla. | 0 |
| `retryDelay` | int (másodperc) | Igen | A tároló végrehajtásának újrapróbálkozásai közötti késleltetés másodpercben. | 0 |
| `secret` | object | Igen | Azonosít egy Azure Key Vault titkos vagy [felügyelt identitást az Azure](container-registry-tasks-authentication-managed-identity.md)-erőforrásokhoz. | Nincsenek |
| `startDelay` | int (másodperc) | Igen | A tároló végrehajtásának késleltetéséhez szükséges másodpercek száma. | 0 |
| `timeout` | int (másodperc) | Igen | A lépés megszakítása előtt legfeljebb ennyi másodpercig futhat. | 600 |
| [`when`](#example-when) | [karakterlánc, karakterlánc,...] | Igen | A feladat egy vagy több lépésének függőségét konfigurálja. | Nincsenek |
| `user` | Karakterlánc | Igen | Egy tároló felhasználóneve vagy UID azonosítója | None |
| `workingDirectory` | sztring | Igen | Egy lépés munkakönyvtárának beállítása. Alapértelmezés szerint az ACR-feladatok létrehoznak egy gyökérkönyvtárat munkakönyvtárként. Ha azonban a Build több lépésből áll, a korábbi lépések megoszthatják az összetevőket a későbbi lépésekkel, ha ugyanazt a munkakönyvtárat adja meg. | `$HOME` |

### <a name="examples-task-step-properties"></a>Példák: Feladat lépésének tulajdonságai

#### <a name="example-id"></a>Példa: azonosító

Hozzon létre két rendszerképet, egypéldányos egy funkcionális tesztet. Minden egyes lépést egy egyedi `id` azonosító határoz meg, amely a tevékenység hivatkozásának `when` más lépéseit a tulajdonságában.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Példa: Ha

A `when` tulajdonság határozza meg a lépés függőségét a feladat egyéb lépésein belül. A két paraméter értékét támogatja:

* `when: ["-"]`– Más lépésektől eltérő függőséget jelez. Egy lépés megadásával `when: ["-"]` azonnal megkezdődik a végrehajtás, és lehetővé teszi az egyidejű lépések végrehajtását.
* `when: ["id1", "id2"]`-Azt jelzi, hogy a lépés a " `id` ID1" és `id` a "ID2" lépésektől függ. Ez a lépés nem hajtható végre, amíg a "ID1" és a "ID2" lépések nem állnak készen.

Ha `when` nincs megadva egy lépésben, ez a lépés a `acr-task.yaml` fájl előző lépésének befejeződésétől függ.

Szekvenciális lépés végrehajtása a `when`következő nélkül:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Szekvenciális lépés végrehajtása a `when`következővel:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

A Parallel images Build:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Párhuzamos rendszerképek létrehozása és függő tesztelés:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Változók futtatása

Az ACR-feladatok olyan alapértelmezett változókat tartalmaznak, amelyek a végrehajtás során elvégzendő feladatok végrehajtásához használhatók. Ezek a változók a formátum `{{.Run.VariableName}}`használatával érhetők el, ahol `VariableName` az a következők egyike:

* `Run.ID`
* `Run.Registry`
* `Run.Date`
* `Run.Commit`
* `Run.Branch`

### <a name="runid"></a>Run.ID

Az egyedi azonosítóval `az acr run` `az acr task create` létrehozott feladatok minden futtatása, átkapcsolása vagy trigger-alapú végrehajtása. Az azonosító a jelenleg futtatott futtatást jelöli.

Általában egy rendszerkép egyedi címkézéséhez használatos:

```yml
version: v1.0.0
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="runregistry"></a>Run.Registry

A beállításjegyzék teljesen minősített kiszolgálójának neve. Általában arra használják, hogy általános referenciául szolgáljon arra a beállításjegyzékre, amelyben a feladat fut.

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="rundate"></a>Run.Date

A Futtatás jelenlegi UTC-ideje.

### <a name="runcommit"></a>Run.Commit

Egy GitHub-adattárba való véglegesítés által aktivált feladat esetén a véglegesítő azonosító.

### <a name="runbranch"></a>Futtatás. ág

Egy GitHub-adattárba való véglegesítés által aktivált feladatok esetében az ág neve.

## <a name="next-steps"></a>További lépések

A többlépéses feladatok áttekintését a [több lépésből álló Build-, tesztelési és javítási feladatok futtatása az ACR-](container-registry-tasks-multi-step.md)feladatokban című témakörben tekintheti meg.

Az egylépéses buildek esetében tekintse meg az [ACR-feladatok áttekintését](container-registry-tasks-overview.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure

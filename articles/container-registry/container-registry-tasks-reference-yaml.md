---
title: Az Azure Container Registry feladatok leírása – YAML
description: Útmutató a tevékenységek meghatározása a YAML ACR feladatokhoz, beleértve a feladat tulajdonságai, a lépés típusok, a lépés tulajdonságai és a beépített változók.
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: marsma
ms.openlocfilehash: a40c05b2775e32bd0932122245914951d42cf712
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047635"
---
# <a name="acr-tasks-reference-yaml"></a>ACR-feladatok hivatkozás: YAML

Többlépéses feladat meghatározása az ACR-feladatokban biztosít egy tároló-központú számítási primitívet létrehozásához, teszteléséhez és a javítással tárolók összpontosítanak. Ez a cikk ismerteti a parancsok, paraméterek, tulajdonságok és szintaxisa a YAML-fájlokat, amelyek meghatározzák a több lépésből álló feladatokat.

Ez a cikk az ACR feladatok YAML-fájlok létrehozása a többlépéses feladat hivatkozást tartalmaz. Ha szeretné, egy ACR-feladatok bemutatása, tekintse meg a [ACR feladatok áttekintése](container-registry-tasks-overview.md).

> [!IMPORTANT]
> Az ACR feladatokat több lépésből álló feladatokat funkciója jelenleg előzetes verzióban érhető el. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="acr-taskyaml-file-format"></a>ACR-task.yaml fájlformátum

ACR-feladatok többlépéses feladat deklarace támogatja a szabványos YAML-szintaxisban. Egy YAML-fájlt, majd manuálisan is futtatható, de a Git commit vagy alap rendszerképet update automatikusan elindította egy feladatütemezés lépéseit határozhatja meg. Bár ez a cikk a webalkalmazásokra `acr-task.yaml` lépéseket tartalmazó fájlként ACR feladatok bármilyen érvényes fájlnevet az támogatja a [bővítmény támogatott](#supported-task-filename-extensions).

A legfelső szintű `acr-task.yaml` primitívekhez van **feladatok Tulajdonságok párbeszédpanelén**, **típusok lépés**, és **. lépés: Tulajdonságok**:

* [Feladatok Tulajdonságok párbeszédpanelén](#task-properties) vonatkozik az összes feladat a végrehajtás lépéseit. Nincsenek három globális feladat tulajdonságai:
  * verzió:
  * stepTimeout
  * totalTimeout
* [Tevékenység-lépés típusok](#task-step-types) képviseli, amely egy feladat elvégezhető műveletek a típusú. Három lépés típusa van:
  * Build
  * Leküldéses
  * cmd
* [Lépés tulajdonságai feladat](#task-step-properties) paraméterek, amelyek egy adott lépés vonatkoznak. Van néhány lépés tulajdonságokat, beleértve:
  * startDelay
  * timeout
  * mikor
  * .. és sok más.

Alapszintű formátumát egy `acr-task.yaml` fájlt, beleértve néhány gyakori lépés tulajdonságai követi. Nem egy teljes körű ábrázolása az összes elérhető lépés tulajdonságai vagy lépés adaptertípusok használata terén, míg az alapszintű fájlformátum gyors áttekintést biztosít.

```yaml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
totalTimeout: # Total seconds allowed for all steps to complete.
steps: # A collection of image or container actions.
    build: # Equivalent to "docker build," but in a multi-tenant environment
    push: # Push a newly built or retagged image to a registry.
      when: # Step property that defines either parallel or dependent step execution.
    cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
      startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>Támogatott feladat fájlnév-kiterjesztések

ACR feladat lefoglalta már néhány fájlnév-bővítményeket, beleértve `.yaml`, hogy feladat-fájlként fogja feldolgozni. Minden bővítmény *nem* a következő lista az ACR-tevékenységre kell tekinteni egy docker-fájlban: .yaml, yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua

YAML az ACR-feladatok által jelenleg támogatott egyetlen fájlformátum. A fájlnév bővítmények lehetséges jövőbeli támogatási számára vannak fenntartva.

## <a name="run-the-sample-tasks"></a>A minta-feladatok futtatása

Ez a cikk a következő szakaszban hivatkozott több minta feladat fájlok vannak. A minta feladatokat egy nyilvános GitHub-adattárában vannak [Azure-Samples/acr-feladatok][acr-tasks]. Az Azure CLI-paranccsal futtathatja őket [futtatása az acr][az-acr-run]. A minta parancsok hasonlók:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

A minta parancsok formázását feltételezi, hogy konfigurálta egy alapértelmezett beállításjegyzéket az Azure CLI-ben, akkor hagyja ki a `--registry` paraméter. A beállításjegyzék egy alapértelmezett konfigurálásához használja a [az konfigurálása] [ az-configure] parancsot a `--defaults` paramétert, amely elfogadja egy `acr=REGISTRY_NAME` érték.

Például egy alapértelmezett beállításjegyzék konfigurálása az Azure CLI "myregistry" nevű:

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Feladat tulajdonságai

Feladat tulajdonságai általában tetején jelennek meg egy `acr-task.yaml` fájlt, és a globális tulajdonságok, amelyek érvényesek a teljes a feladat végrehajtása során. Ezek a globális tulajdonságok némelyike belül egy adott lépés felülbírálható.

| Tulajdonság | Típus | Optional | Leírás | Támogatott felülbírálása | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | sztring | Nem | A következő verziója a `acr-task.yaml` az ACR-feladatok szolgáltatás által elemzett fájlok. ACR-feladatok nagy hangsúlyt fektet a előző verziókkal való kompatibilitás megőrzése érdekében, miközben ez az érték engedélyezi ACR feladatok fenntartja a kompatibilitást belül egy meghatározott verzióra. | Nem | None |
| `stepTimeout` | Int (másodperc) | Igen | Egy lépés futtathatja másodperc maximális számát. Ez a tulajdonság beállításával a lépés egy lépésben felülbírálható [időtúllépési](#timeout) tulajdonság. | Igen | 600 (10 perc) |
| `totalTimeout` | Int (másodperc) | Igen | Másodpercben, hogy egy tevékenység futtatásának maximális számát. A "Futtatás" tartalmazza a végrehajtási és az összes lépés befejezése után a feladat sikeres vagy sikertelen-e. Is tartalmazza a nyomtatási feladat kimeneti például észlelt kép függőségeket és a feladat végrehajtásának állapota. | Nem | 3600 (1 óra) |

## <a name="task-step-types"></a>Lépés tevékenységtípust

ACR-feladatok három lépés típusát támogatja. Minden lépés számos tulajdonságát, az egyes lépés a szakaszban részletes támogatja.

| Lépés típusa | Leírás |
| --------- | ----------- |
| [`build`](#build) | Létrehozza egy tároló rendszerképet ismerős `docker build` szintaxist. |
| [`push`](#push) | Végrehajt egy `docker push` újonnan létrehozott vagy retagged lemezképek továbbíthat egy tárolóregisztrációs adatbázisba. A nyilvános Docker Hub, az Azure Container Registry és az egyéb privát tárolójegyzékek támogatottak.
| [`cmd`](#cmd) | A tároló átadott tárolóban, a parancs paraméterekkel fut `[ENTRYPOINT]`. A `cmd` lépés támogatja env paraméterek, le, és más ismerős `docker run` parancs kapcsolóival, egység és funkcionális tesztelés egyidejű tároló végrehajtási engedélyezése. |

## <a name="build"></a>Build

Állítson össze egy tárolórendszerképet. A `build` lépés típusát jelöli futó több-bérlős, biztonságos módszert `docker build` egy első osztályú primitívet, a felhőben.

### <a name="syntax-build"></a>Szintaxis: létrehozása

```yaml
version: 1.0-preview-1
steps:
    - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
      [property]: [value]
```

A `build` lépés típusát a következő paramétereket támogatja:

| Paraméter | Leírás | Optional |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Meghatározza a teljesen minősített `image:tag` , az összeállított rendszerképet.<br /><br />A feladat belső ellenőrzések funkcióteszteket, például képek használhatók, nem az összes rendszerkép szükséges `push` egy regisztrációs adatbázisba. Azonban egy feladat a végrehajtás belüli lemezkép példányt, a lemezkép kell hivatkozni egy nevet.<br /><br />Ellentétben `az acr build`, ACR-feladatok futtatása nem biztosít alapértelmezett leküldéses működését. ACR-feladatokat, az alapértelmezett forgatókönyv feltételezi, hogy hozhat létre, ellenőrizze, majd a rendszerkép leküldése lehetővé teszi. Lásd: [leküldéses](#push) igény szerint létrehozott leküldéses lemezképek kapcsolatban. | Igen |
| `-f` &#124; `--file` | Adja meg a docker-fájlban átadott `docker build`. Ha nincs megadva, az alapértelmezett a környezet gyökérkönyvtárában található docker-fájlban feltételezzük. Adja meg a docker-fájl alternatív, át kell adnia a fájlnevet a környezet gyökeréhez viszonyítva. | Igen |
| `context` | A gyökérkönyvtár átadott `docker build`. Minden tevékenység gyökérkönyvtárában van beállítva, a megosztott [workingDirectory](#task-step-properties), és a kapcsolódó klónozott Git-könyvtár gyökerében tartalmazza. | Nem |

### <a name="properties-build"></a>Tulajdonságok: létrehozása

A `build` lépés típusát támogatja ezeket a tulajdonságokat a részletei a következő tulajdonságokat a [feladat lépés tulajdonságai](#task-step-properties) című szakaszát.

| | | |
| -------- | ---- | -------- |
| `detach` | Logikai | Optional |
| `entryPoint` | sztring | Optional |
| `env` | [string, string,...] | Optional |
| `id` | sztring | Optional |
| `ignoreErrors` | Logikai | Optional |
| `keep` | Logikai | Optional |
| `startDelay` | Int (másodperc) | Optional |
| `timeout` | Int (másodperc) | Optional |
| `when` | [string, string,...] | Optional |
| `workingDirectory` | sztring | Optional |

### <a name="examples-build"></a>Példák: létrehozása

#### <a name="build-image---context-in-root"></a>Lemezkép - környezet gyökérszintű létrehozása

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Lemezkép - alkönyvtárában található cikkre hivatkozik környezet létrehozása

```yaml
version: 1.0-preview-1
steps:
- build: -t {{.Run.Registry}}/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>Leküldéses

Leküldéses egyet, vagy több beépített vagy retagged lemezképek továbbíthat egy tárolóregisztrációs adatbázisba. Hogyan lehet továbbítani rá, például az Azure Container Registry privát beállításjegyzékek, vagy a nyilvános Docker Hub támogatja.

### <a name="syntax-push"></a>Szintaxis: leküldéses

A `push` lépés típusát támogatja a képek gyűjteménye. YAML-gyűjtemény szintaxis soron belüli és a beágyazott formátumokat támogatja. Hogyan lehet továbbítani rá egy lemezkép általában jelölt belső szintaxis használatával:

```yaml
version: 1.0-preview-1
steps:
  # Inline YAML collection syntax
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

A jobb olvashatóság érdekében szintaxissal beágyazott több lemezképet ügyelni:

```yaml
version: 1.0-preview-1
steps:
  # Nested YAML collection syntax
  - push:
    - {{.Run.Registry}}/hello-world:{{.Run.ID}}
    - {{.Run.Registry}}/hello-world:latest
```

### <a name="properties-push"></a>Tulajdonságok: leküldéses

A `push` lépés az alábbi tulajdonságokat támogatja. Ezeket a tulajdonságokat az információk a [feladat lépés tulajdonságai](#task-step-properties) című szakaszát.

| | | |
| -------- | ---- | -------- |
| `env` | [string, string,...] | Optional |
| `id` | sztring | Optional |
| `ignoreErrors` | Logikai | Optional |
| `startDelay` | Int (másodperc) | Optional |
| `timeout` | Int (másodperc) | Optional |
| `when` | [string, string,...] | Optional |

### <a name="examples-push"></a>Példák: leküldéses

#### <a name="push-multiple-images"></a>Több rendszerképeket

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Hozhat létre, push és futtatása

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

A `cmd` lépés típus fut egy tárolót.

### <a name="syntax-cmd"></a>Szintaxis: cmd

```yaml
version: 1.0-preview-1
steps:
    - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Tulajdonságok: cmd

A `cmd` lépés típusát támogatja a következő tulajdonságokkal:

| | | |
| -------- | ---- | -------- |
| `detach` | Logikai | Optional |
| `entryPoint` | sztring | Optional |
| `env` | [string, string,...] | Optional |
| `id` | sztring | Optional |
| `ignoreErrors` | Logikai | Optional |
| `keep` | Logikai | Optional |
| `startDelay` | Int (másodperc) | Optional |
| `timeout` | Int (másodperc) | Optional |
| `when` | [string, string,...] | Optional |
| `workingDirectory` | sztring | Optional |

Ezeket a tulajdonságokat az információk a [feladat lépés tulajdonságai](#task-step-properties) című szakaszát.

### <a name="examples-cmd"></a>Példák: cmd

#### <a name="run-hello-world-image"></a>Hello-world rendszerképet futtatása

Ez a parancs végrehajtása a `hello-world.yaml` feladat fájlt, amely hivatkozik a [hello-world](https://hub.docker.com/_/hello-world/) rendszerképet a Docker hubon.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml --> [!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Futtassa a bash kép- és echo "hello world"

Ez a parancs végrehajtása a `bash-echo.yaml` feladat fájlt, amely hivatkozik a [bash](https://hub.docker.com/_/bash/) rendszerképet a Docker hubon.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml --> [!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Adott bash képcímke futtatása

Egy adott rendszerkép verziója fut, adja meg a címke a `cmd`.

Ez a parancs végrehajtása a `bash-echo-3.yaml` feladat fájlt, amely hivatkozik a [bash: 3.0](https://hub.docker.com/_/bash/) rendszerképet a Docker hubon.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml --> [!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Egyéni rendszerképek futtatása

A `cmd` lépés típusát hivatkozik a képek normál használatával `docker run` formátumban. Beállításjegyzék mintametódus nem használ rendszerképeket rendszer feltételezi, hogy docker.io származik. Az előző példában egyaránt jeleníthető meg:

```yaml
version: 1.0-preview-1
steps:
    - cmd: docker.io/bash:3.0 echo hello world
```

A standard használatával `docker run` lemezkép a referencia-egyezmény `cmd` szereplő bármelyik privát tárolójegyzék vagy a nyilvános Docker Hub lemezképeket futtathat. Ha az ACR-tevékenység végrehajtása, amelyben ugyanazon regisztrációs adatbázisban található rendszerképek már hivatkozik, nem kell semmilyen a tárolójegyzék hitelesítő adatainak megadása.

* Futtassa az Azure container registry a rendszerképet

    Cserélje le `[myregistry]` a tárolójegyzék nevére:

    ```yaml
    version: 1.0-preview-1
    steps:
        - cmd: [myregistry].azurecr.io/bash:3.0 echo hello world
    ```

* A beállításjegyzék-hivatkozás Futtatás változóhoz generalize

    Fix kódolása a beállításjegyzék neve a helyett egy `acr-task.yaml` fájlt, azt teheti, hogy könnyebben hordozható használatával egy [változó futtatása](#run-variables). A `Run.Registry` változó, amelyben a feladat végrehajtása a beállításjegyzék nevére futásidőben bővül.

    Az előző feladat általánosítani, így az összes Azure container registryben működik, hivatkozhat a [Run.Registry](#runregistry) változót a rendszerkép neve:

    ```yaml
    version: 1.0-preview-1
    steps:
        - cmd: {{.Run.Registry}}/bash:3.0 echo hello world
    ```

## <a name="task-step-properties"></a>A feladat lépés tulajdonságai

Minden lépés típusát támogatja a megfelelő ehhez a típushoz több tulajdonságát. Az alábbi táblázat az összes rendelkezésre álló lépés tulajdonság határozza meg. Nem minden lépés esetében támogatja az összes tulajdonság. Ezek a tulajdonságok közül melyik érhetők el az egyes lépés, olvassa el a [cmd](#cmd), [hozhat létre](#build), és [leküldéses](#push) típus szakaszok lépést.

| Tulajdonság | Típus | Optional | Leírás |
| -------- | ---- | -------- | ----------- |
| `detach` | Logikai | Igen | Hogy a tároló kell lehet leválasztani futtatásakor. |
| `entryPoint` | sztring | Igen | Felülbírálja a `[ENTRYPOINT]` egy lépés tároló. |
| `env` | [string, string,...] | Igen | A karakterláncok tömbje `key=value` formátuma, hogy a lépés a környezeti változókat határozhat meg. |
| [`id`](#example-id) | sztring | Igen | A lépés a feladatütemezés belül egyedileg azonosítja. A feladatütemezés más lépései egy lépés hivatkozhat `id`, mint a függőség ellenőrzése az ilyen `when`.<br /><br />A `id` egyben a futó tároló nevét. A feladat egyéb tárolókban futó folyamatok olvassa el a `id` a DNS-állomás név, vagy például eléréséhez a docker-naplók [id],. |
| `ignoreErrors` | Logikai | Igen | Ha a beállítása `true`, a lépés kész, függetlenül attól, hogy hiba történt a végrehajtása során van megjelölve. Alapértelmezett: `false`. |
| `keep` | Logikai | Igen | E tároló a lépés végrehajtása után meg kell őrizni. |
| `startDelay` | Int (másodperc) | Igen | A lépés végrehajtása késleltető másodpercek száma. |
| `timeout` | Int (másodperc) | Igen | Legfeljebb ennyi másodpercig lépés előtt leállt lehet végrehajtani. |
| [`when`](#example-when) | [string, string,...] | Igen | Egy vagy több lépést jelentenek a tevékenység egy lépés függőségi konfigurálja. |
| `workingDirectory` | sztring | Igen | Beállítja a munkakönyvtárban történő egy lépésben. Alapértelmezés szerint ACR feladatok, a munkakönyvtárban gyökérkönyvtár hoz létre. Azonban ha a build számos lépést tartalmaz, korábbi lépések megoszthatja összetevők későbbi lépésekben munkakönyvtára megadásával. |

### <a name="examples-task-step-properties"></a>Példák: A feladat lépés tulajdonságai

#### <a name="example-id"></a>Példa: azonosítója

Két lemezképet, a példány egy funkcionális tesztelés céljára szolgál rendszerképet hozhat létre. Az egyes lépések egy egyedi által azonosított `id` a feladatütemezés más lépései a hivatkozó azok `when` tulajdonság.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml --> [!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Példa: Ha

A `when` tulajdonság a további lépések belül a tevékenység egy lépés függőségi határozza meg. A paraméter két értéket támogatja:

* `when: ["-"]` – Nincs a többi függőségi jelzi. A lépés megadása `when: ["-"]` azonnal megkezdődik végrehajtási, és lehetővé teszi a párhuzamos lépés végrehajtása.
* `when: ["id1", "id2"]` – Azt jelzi, a lépés lépést olyan függővé `id` "id1" és a `id` "id2". Ez a lépés nem hajtható végre, amíg "id1" és "id2" is lépéseinek végrehajtása.

Ha `when` nincs megadva egy lépésben ezt a lépést az adott nyelvtől függ az előző lépés befejezése után a `acr-task.yaml` fájlt.

Egymás utáni lépés végrehajtási nélkül `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml --> [!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Az egymás utáni lépés végrehajtási `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml --> [!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Párhuzamos rendszerkép összeállítását:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml --> [!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Párhuzamos rendszerkép összeállítását és a függő tesztelése:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml --> [!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Futtatási változók

ACR-feladatok által elérhető lépések, ha azok végrehajtása változók alapértelmezett készletét tartalmazza. Ezek a változók a következő formátumban elérhetők `{{.Run.VariableName}}`, ahol `VariableName` a következők valamelyike:

* `Run.ID`
* `Run.Registry`
* `Run.Date`

### <a name="run46id"></a>Futtatás&#46;azonosítója

Minden egyes Futtatás keresztül `az acr run`, vagy az alapján létrehozott feladatok aktiválásához `az acr task create` rendelkezik egy egyedi azonosítót. Az azonosító a jelenleg végrehajtás alatt Futtatás jelöli.

Jellemzően a egyedileg címkézés képet:

```yaml
version: 1.0-preview-1
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="runregistry"></a>Run.Registry

A beállításjegyzék kiszolgáló teljes neve. Általában általános hivatkozhat a beállításjegyzékben, ahol a feladat futtatásához használható.

```yaml
version: 1.0-preview-1
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="rundate"></a>Run.Date

Az aktuális UTC időpontig a Futtatás kezdete.

## <a name="next-steps"></a>További lépések

Többlépéses feladatok áttekintéséhez tekintse meg a [többlépéses buildelési, tesztelési és patch feladatok futtatása az ACR-feladatokban](container-registry-tasks-multi-step.md).

Egylépéses épít, lásd: a [ACR feladatok áttekintése](container-registry-tasks-overview.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr/run#az-acr-run
[az-configure]: /cli/azure/reference-index#az-configure
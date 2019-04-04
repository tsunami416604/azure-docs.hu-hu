---
title: Az Azure Container Registry feladatok leírása – YAML
description: Útmutató a tevékenységek meghatározása a YAML ACR feladatokhoz, beleértve a feladat tulajdonságai, a lépés típusok, a lépés tulajdonságai és a beépített változók.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/28/2019
ms.author: danlep
ms.openlocfilehash: b2398e7db7ed91dee8d85c0c50058bb15b9f4c7e
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58894132"
---
# <a name="acr-tasks-reference-yaml"></a>ACR-feladatok leírása: YAML

Többlépéses feladat meghatározása az ACR-feladatokban biztosít egy tároló-központú számítási primitívet létrehozásához, teszteléséhez és a javítással tárolók összpontosítanak. Ez a cikk ismerteti a parancsok, paraméterek, tulajdonságok és szintaxisa a YAML-fájlokat, amelyek meghatározzák a több lépésből álló feladatokat.

Ez a cikk az ACR feladatok YAML-fájlok létrehozása a többlépéses feladat hivatkozást tartalmaz. Ha szeretné, egy ACR-feladatok bemutatása, tekintse meg a [ACR feladatok áttekintése](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>ACR-task.yaml fájlformátum

ACR-feladatok többlépéses feladat deklarace támogatja a szabványos YAML-szintaxisban. A tevékenység lépéseket egy YAML-fájlt határozhatja meg. Ezután a feladat futtatható manuálisan adja át a fájlt a [futtatása az acr] [ az-acr-run] parancsot. Vagy hozzon létre egy feladatot a fájl segítségével [az acr-feladat létrehozása] [ az-acr-task-create] , amely a Git commit vagy alap rendszerképet update automatikusan aktiválódik. Bár ez a cikk a webalkalmazásokra `acr-task.yaml` lépéseket tartalmazó fájlként ACR feladatok bármilyen érvényes fájlnevet az támogatja a [bővítmény támogatott](#supported-task-filename-extensions).

A legfelső szintű `acr-task.yaml` primitívekhez van **feladatok Tulajdonságok párbeszédpanelén**, **típusok lépés**, és **. lépés: Tulajdonságok**:

* [Feladatok Tulajdonságok párbeszédpanelén](#task-properties) vonatkozik az összes feladat a végrehajtás lépéseit. Van néhány globális feladat tulajdonságai, például:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [Tevékenység-lépés típusok](#task-step-types) képviseli, amely egy feladat elvégezhető műveletek a típusú. Három lépés típusa van:
  * `build`
  * `push`
  * `cmd`
* [Lépés tulajdonságai feladat](#task-step-properties) paraméterek, amelyek egy adott lépés vonatkoznak. Van néhány lépés tulajdonságokat, beleértve:
  * `startDelay`
  * `timeout`
  * `when`
  * .. és sok más.

Alapszintű formátumát egy `acr-task.yaml` fájlt, beleértve néhány gyakori lépés tulajdonságai követi. Nem egy teljes körű ábrázolása az összes elérhető lépés tulajdonságai vagy lépés adaptertípusok használata terén, míg az alapszintű fájlformátum gyors áttekintést biztosít.

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

Feladat tulajdonságai általában tetején jelennek meg egy `acr-task.yaml` fájlt, és a lépések végrehajtásának teljes egészében vonatkozó globális tulajdonságok vannak. Ezek a globális tulajdonságok némelyike belül egy adott lépés felülbírálható.

| Tulajdonság | Typo | Optional | Leírás | Támogatott felülbírálása | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | sztring | Igen | A következő verziója a `acr-task.yaml` az ACR-feladatok szolgáltatás által elemzett fájlok. ACR-feladatok nagy hangsúlyt fektet a előző verziókkal való kompatibilitás megőrzése érdekében, miközben ez az érték engedélyezi ACR feladatok fenntartja a kompatibilitást belül egy meghatározott verzióra. Ha nincs megadva, alapértelmezés szerint a legújabb verzióra. | Nem | None |
| `stepTimeout` | Int (másodperc) | Igen | Egy lépés futtathatja másodperc maximális számát. Ha a tulajdonság egy tevékenység van megadva, alapértelmezett beállítása `timeout` minden lépést tulajdonságát. Ha a `timeout` tulajdonság meg van adva egy lépésben, ez a beállítás felülbírálja a tulajdonság a tevékenységhez. | Igen | 600 (10 perc) |
| `workingDirectory` | sztring | Igen | A tároló futásidőben munkakönyvtár. Ha a tulajdonság egy tevékenység van megadva, alapértelmezett beállítása `workingDirectory` minden lépést tulajdonságát. Ha meg van adva egy lépésben, ez a beállítás felülbírálja a tulajdonság a tevékenységhez. | Igen | `$HOME` |
| `env` | [string, string,...] | Igen |  A karakterláncok tömbje `key=value` formátuma, hogy a feladat a környezeti változókat határozhat meg. Ha a tulajdonság egy tevékenység van megadva, alapértelmezett beállítása `env` minden lépést tulajdonságát. Ha meg van adva egy lépésben, ez a beállítás felülbírálja a feladat örökli a környezeti változók. | None |
| `secrets` | [titkos, titkos kulcsot,...] | Igen | A tömb [titkos](#secret) objektumokat. | None |
| `networks` | [hálózati, hálózati,...] | Igen | A tömb [hálózati](#network) objektumokat. | None |

### <a name="secret"></a>titkos kód

A titkos kód objektum a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Typo | Optional | Leírás | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | sztring | Nem | A titkos kulcs azonosítóját. | None |
| `akv` | sztring | Igen | Az Azure Key Vault (AKV) titkos URL-címe. | None |
| `clientID` | sztring | Igen | Az ügyfél-azonosítója a felhasználó által hozzárendelt felügyelt identitás az Azure-erőforrásokhoz. | None |

### <a name="network"></a>hálózat

A hálózati objektum a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Typo | Optional | Leírás | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | sztring | Nem | A hálózat nevét. | None |
| `driver` | sztring | Igen | Az illesztőprogram, a hálózat kezeléséhez. | None |
| `ipv6` | logikai | Igen | IPv6 hálózatra engedélyezve van-e. | `false` |
| `skipCreation` | logikai | Igen | Hálózati teszthálózatban kell-e. | `false` |
| `isDefault` | logikai | Igen | A hálózat-e egy alapértelmezett hálózati biztosított az Azure Container Registryvel | `false` |

## <a name="task-step-types"></a>Lépés tevékenységtípust

ACR-feladatok három lépés típusát támogatja. Minden lépés számos tulajdonságát, az egyes lépés a szakaszban részletes támogatja.

| Lépés típusa | Leírás |
| --------- | ----------- |
| [`build`](#build) | Létrehozza egy tároló rendszerképet ismerős `docker build` szintaxist. |
| [`push`](#push) | Végrehajt egy `docker push` újonnan létrehozott vagy retagged lemezképek továbbíthat egy tárolóregisztrációs adatbázisba. A nyilvános Docker Hub, az Azure Container Registry és az egyéb privát tárolójegyzékek támogatottak. |
| [`cmd`](#cmd) | A tároló átadott tárolóban, a parancs paraméterekkel fut `[ENTRYPOINT]`. A `cmd` lépés típusát támogatja a paraméterek `env`, `detach`, és más ismerős `docker run` parancs kapcsolóival, egység és funkcionális tesztelés egyidejű tároló végrehajtási engedélyezése. |

## <a name="build"></a>Build

Állítson össze egy tárolórendszerképet. A `build` lépés típusát jelöli futó több-bérlős, biztonságos módszert `docker build` egy első osztályú primitívet, a felhőben.

### <a name="syntax-build"></a>Szintaxis: létrehozása

```yml
version: v1.0.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

A `build` lépés típusát a következő táblázatban a következő paramétereket támogatja. A `build` lépés típusát is támogatja az összes build beállításának a [docker build](https://docs.docker.com/engine/reference/commandline/build/) parancsot, mint például `--build-arg` buildelés változók beállítása.

| Paraméter | Leírás | Optional |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Meghatározza a teljesen minősített `image:tag` , az összeállított rendszerképet.<br /><br />A feladat belső ellenőrzések funkcióteszteket, például képek használhatók, nem az összes rendszerkép szükséges `push` egy regisztrációs adatbázisba. Azonban egy feladat a végrehajtás belüli lemezkép példányt, a lemezkép kell hivatkozni egy nevet.<br /><br />Ellentétben `az acr build`, ACR-feladatok futtatása nem biztosít alapértelmezett leküldéses működését. ACR-feladatokat, az alapértelmezett forgatókönyv feltételezi, hogy hozhat létre, ellenőrizze, majd a rendszerkép leküldése lehetővé teszi. Lásd: [leküldéses](#push) igény szerint létrehozott leküldéses lemezképek kapcsolatban. | Igen |
| `-f` &#124; `--file` | Adja meg a docker-fájlban átadott `docker build`. Ha nincs megadva, az alapértelmezett a környezet gyökérkönyvtárában található docker-fájlban feltételezzük. Adjon meg egy docker-fájlban, át kell adnia a fájlnevet a környezet gyökeréhez viszonyítva. | Igen |
| `context` | A gyökérkönyvtár átadott `docker build`. Minden tevékenység gyökérkönyvtárában van beállítva, a megosztott [workingDirectory](#task-step-properties), és a kapcsolódó klónozott Git-könyvtár gyökerében tartalmazza. | Nem |

### <a name="properties-build"></a>Tulajdonságok: létrehozása

A `build` lépés az alábbi tulajdonságokat támogatja. Ezeket a tulajdonságokat, a részletek a [feladat lépés tulajdonságai](#task-step-properties) című szakaszát.

| | | |
| -------- | ---- | -------- |
| `detach` | logikai | Optional |
| `disableWorkingDirectoryOverride` | logikai | Optional |
| `entryPoint` | sztring | Optional |
| `env` | [string, string,...] | Optional |
| `expose` | [string, string,...] | Optional |
| `id` | sztring | Optional |
| `ignoreErrors` | logikai | Optional |
| `isolation` | sztring | Optional |
| `keep` | logikai | Optional |
| `network` | objektum | Optional |
| `ports` | [string, string,...] | Optional |
| `pull` | logikai | Optional |
| `repeat` | int | Optional |
| `retries` | int | Optional |
| `retryDelay` | Int (másodperc) | Optional |
| `secret` | objektum | Optional |
| `startDelay` | Int (másodperc) | Optional |
| `timeout` | Int (másodperc) | Optional |
| `when` | [string, string,...] | Optional |
| `workingDirectory` | sztring | Optional |

### <a name="examples-build"></a>Példák: létrehozása

#### <a name="build-image---context-in-root"></a>Lemezkép - környezet gyökérszintű létrehozása

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Lemezkép - alkönyvtárában található cikkre hivatkozik környezet létrehozása

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>Leküldéses

Leküldéses egyet, vagy több beépített vagy retagged lemezképek továbbíthat egy tárolóregisztrációs adatbázisba. Hogyan lehet továbbítani rá, például az Azure Container Registry privát beállításjegyzékek, vagy a nyilvános Docker Hub támogatja.

### <a name="syntax-push"></a>Szintaxis: leküldéses

A `push` lépés típusát támogatja a képek gyűjteménye. YAML-gyűjtemény szintaxis soron belüli és a beágyazott formátumokat támogatja. Hogyan lehet továbbítani rá egy lemezkép általában jelölt belső szintaxis használatával:

```yml
version: v1.0.0
steps:
  # Inline YAML collection syntax
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

A jobb olvashatóság érdekében szintaxissal beágyazott több lemezképet ügyelni:

```yml
version: v1.0.0
steps:
  # Nested YAML collection syntax
  - push:
    - {{.Run.Registry}}/hello-world:{{.Run.ID}}
    - {{.Run.Registry}}/hello-world:latest
```

### <a name="properties-push"></a>Tulajdonságok: leküldéses

A `push` lépés az alábbi tulajdonságokat támogatja. Ezeket a tulajdonságokat, a részletek a [feladat lépés tulajdonságai](#task-step-properties) című szakaszát.

| | | |
| -------- | ---- | -------- |
| `env` | [string, string,...] | Optional |
| `id` | sztring | Optional |
| `ignoreErrors` | logikai | Optional |
| `startDelay` | Int (másodperc) | Optional |
| `timeout` | Int (másodperc) | Optional |
| `when` | [string, string,...] | Optional |

### <a name="examples-push"></a>Példák: leküldéses

#### <a name="push-multiple-images"></a>Több rendszerképeket

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Hozhat létre, push és futtatása

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

A `cmd` lépés típus fut egy tárolót.

### <a name="syntax-cmd"></a>Szintaxis: cmd

```yml
version: v1.0.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Tulajdonságok: cmd

A `cmd` lépés típusát támogatja a következő tulajdonságokkal:

| | | |
| -------- | ---- | -------- |
| `detach` | logikai | Optional |
| `disableWorkingDirectoryOverride` | logikai | Optional |
| `entryPoint` | sztring | Optional |
| `env` | [string, string,...] | Optional |
| `expose` | [string, string,...] | Optional |
| `id` | sztring | Optional |
| `ignoreErrors` | logikai | Optional |
| `isolation` | sztring | Optional |
| `keep` | logikai | Optional |
| `network` | objektum | Optional |
| `ports` | [string, string,...] | Optional |
| `pull` | logikai | Optional |
| `repeat` | int | Optional |
| `retries` | int | Optional |
| `retryDelay` | Int (másodperc) | Optional |
| `secret` | objektum | Optional |
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

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Futtassa a bash kép- és echo "hello world"

Ez a parancs végrehajtása a `bash-echo.yaml` feladat fájlt, amely hivatkozik a [bash](https://hub.docker.com/_/bash/) rendszerképet a Docker hubon.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Adott bash képcímke futtatása

Egy adott rendszerkép verziója fut, adja meg a címke a `cmd`.

Ez a parancs végrehajtása a `bash-echo-3.yaml` feladat fájlt, amely hivatkozik a [bash: 3.0](https://hub.docker.com/_/bash/) rendszerképet a Docker hubon.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Egyéni rendszerképek futtatása

A `cmd` lépés típusát hivatkozik a képek normál használatával `docker run` formátumban. Beállításjegyzék mintametódus nem használ rendszerképeket rendszer feltételezi, hogy docker.io származik. Az előző példában egyaránt jeleníthető meg:

```yml
version: v1.0.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

A standard használatával `docker run` lemezkép a referencia-egyezmény `cmd` bármelyik privát tárolójegyzék vagy a nyilvános Docker Hub lemezképeket futtathat. Ha az ACR-tevékenység végrehajtása, amelyben ugyanazon regisztrációs adatbázisban található rendszerképek már hivatkozik, nem kell semmilyen a tárolójegyzék hitelesítő adatainak megadása.

* Egy rendszerkép, amely egy Azure container registryből

    Cserélje le `[myregistry]` a tárolójegyzék nevére:

    ```yml
    version: v1.0.0
    steps:
        - cmd: [myregistry].azurecr.io/bash:3.0 echo hello world
    ```

* A beállításjegyzék-hivatkozás Futtatás változóhoz generalize

    Fix kódolása a beállításjegyzék neve a helyett egy `acr-task.yaml` fájlt, azt teheti, hogy könnyebben hordozható használatával egy [változó futtatása](#run-variables). A `Run.Registry` változó, amelyben a feladat végrehajtása a beállításjegyzék nevére futásidőben bővül.

    Az előző feladat általánosítani, így az összes Azure container registryben működik, hivatkozhat a [Run.Registry](#runregistry) változót a rendszerkép neve:

    ```yml
    version: v1.0.0
    steps:
      - cmd: {{.Run.Registry}}/bash:3.0 echo hello world
    ```

## <a name="task-step-properties"></a>A feladat lépés tulajdonságai

Minden lépés típusát támogatja a megfelelő ehhez a típushoz több tulajdonságát. Az alábbi táblázat az összes rendelkezésre álló lépés tulajdonság határozza meg. Nem minden lépés esetében támogatja az összes tulajdonság. Ezek a tulajdonságok közül melyik érhetők el az egyes lépés, olvassa el a [cmd](#cmd), [hozhat létre](#build), és [leküldéses](#push) típus szakaszok lépést.

| Tulajdonság | Typo | Optional | Leírás | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | logikai | Igen | Hogy a tároló kell lehet leválasztani futtatásakor. | `false` |
| `disableWorkingDirectoryOverride` | logikai | Igen | Hogy letiltandó-e `workingDirectory` felülírása funkciót. Ezzel együtt `workingDirectory` a munkakönyvtárban a tároló teljes körű irányítása. | `false` |
| `entryPoint` | sztring | Igen | Felülbírálja a `[ENTRYPOINT]` egy lépés tároló. | None |
| `env` | [string, string,...] | Igen | A karakterláncok tömbje `key=value` formátuma, hogy a lépés a környezeti változókat határozhat meg. | None |
| `expose` | [string, string,...] | Igen | A tárolóból érhetők el portok tömbje. |  None |
| [`id`](#example-id) | sztring | Igen | A lépés a feladatütemezés belül egyedileg azonosítja. A feladatütemezés más lépései egy lépés hivatkozhat `id`, mint a függőség ellenőrzése az ilyen `when`.<br /><br />A `id` egyben a futó tároló nevét. A feladat egyéb tárolókban futó folyamatok olvassa el a `id` a DNS-állomás név, vagy például eléréséhez a docker-naplók [id],. | `acb_step_%d`, ahol `%d` a lépés fentről lefelé a YAML-fájl 0-alapú indexe |
| `ignoreErrors` | logikai | Igen | Jelölje meg a sikeres, függetlenül attól, hogy a tároló végrehajtása során egy hiba történt a lépést kell-e. | `false` |
| `isolation` | sztring | Igen | A tároló elkülönítési szintjét. | `default` |
| `keep` | logikai | Igen | E tároló a lépés végrehajtása után meg kell őrizni. | `false` |
| `network` | objektum | Igen | Egy hálózatot, amelyben a tároló fut azonosítja. | None |
| `ports` | [string, string,...] | Igen | Portok, a tárolóból a gazdagépre közzétett tömbje. |  None |
| `pull` | logikai | Igen | -E a tároló egy lekéréses kényszerítése minden gyorsítótárazási viselkedés megakadályozása végrehajtása előtt. | `false` |
| `privileged` | logikai | Igen | Kell védett módban a tároló fut-e. | `false` |
| `repeat` | int | Igen | Ismételje meg a tároló végrehajtásának való próbálkozások száma. | 0 |
| `retries` | int | Igen | Ha egy tárolót a végrehajtása sikertelen bejelentkezési kísérletet az újrapróbálkozások száma. Egy újrapróbálkozási kísérlet csak történik, ha a tároló kilépési kódja nullától eltérő. | 0 |
| `retryDelay` | Int (másodperc) | Igen | A késleltetés másodpercben. a tároló végrehajtásának újrapróbálkozások között. | 0 |
| `secret` | objektum | Igen | Egy Azure Key Vault titkos kulcsából, vagy az Azure-erőforrások felügyelt identitás azonosítja. | None |
| `startDelay` | Int (másodperc) | Igen | Egy tároló végrehajtási késleltető másodpercek száma. | 0 |
| `timeout` | Int (másodperc) | Igen | Legfeljebb ennyi másodpercig lépés előtt leállt lehet végrehajtani. | 600 |
| [`when`](#example-when) | [string, string,...] | Igen | Egy vagy több lépést jelentenek a tevékenység egy lépés függőségi konfigurálja. | None |
| `user` | sztring | Igen | A felhasználónév vagy a tároló egyedi azonosítója | None |
| `workingDirectory` | sztring | Igen | Beállítja a munkakönyvtárban történő egy lépésben. Alapértelmezés szerint ACR feladatok, a munkakönyvtárban gyökérkönyvtár hoz létre. Azonban ha a build számos lépést tartalmaz, korábbi lépések megoszthatja összetevők későbbi lépésekben munkakönyvtára megadásával. | `$HOME` |

### <a name="examples-task-step-properties"></a>Példák: A feladat lépés tulajdonságai

#### <a name="example-id"></a>Példa: azonosítója

Két lemezképet, a példány egy funkcionális tesztelés céljára szolgál rendszerképet hozhat létre. Az egyes lépések egy egyedi által azonosított `id` a feladatütemezés más lépései a hivatkozó azok `when` tulajdonság.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Példa: Ha

A `when` tulajdonság a további lépések belül a tevékenység egy lépés függőségi határozza meg. A paraméter két értéket támogatja:

* `when: ["-"]` – Nincs a többi függőségi jelzi. A lépés megadása `when: ["-"]` azonnal megkezdődik végrehajtási, és lehetővé teszi a párhuzamos lépés végrehajtása.
* `when: ["id1", "id2"]` – Azt jelzi, a lépés lépést olyan függővé `id` "id1" és a `id` "id2". Ez a lépés nem hajtható végre, amíg "id1" és "id2" is lépéseinek végrehajtása.

Ha `when` nincs megadva egy lépésben ezt a lépést az adott nyelvtől függ az előző lépés befejezése után a `acr-task.yaml` fájlt.

Egymás utáni lépés végrehajtási nélkül `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Az egymás utáni lépés végrehajtási `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Párhuzamos képeket hozhat létre:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Párhuzamos rendszerkép összeállítását és a függő tesztelése:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Futtatási változók

ACR-feladatok által elérhető lépések, ha azok végrehajtása változók alapértelmezett készletét tartalmazza. Ezek a változók a következő formátumban elérhetők `{{.Run.VariableName}}`, ahol `VariableName` a következők valamelyike:

* `Run.ID`
* `Run.Registry`
* `Run.Date`

### <a name="run46id"></a>Run&#46;ID

Minden egyes Futtatás keresztül `az acr run`, vagy az alapján létrehozott feladatok aktiválásához `az acr task create` rendelkezik egy egyedi azonosítót. Az azonosító a jelenleg végrehajtás alatt Futtatás jelöli.

Jellemzően a egyedileg címkézés képet:

```yml
version: v1.0.0
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="runregistry"></a>Run.Registry

A beállításjegyzék kiszolgáló teljes neve. Általában általános hivatkozhat a beállításjegyzékben, ahol a feladat futtatásához használható.

```yml
version: v1.0.0
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

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure

---
title: YAML-hivatkozás – ACR-feladatok
description: Hivatkozás az ACR-feladatok YAML-ben lévő feladatainak definiálására, beleértve a feladattulajdonságokat, a lépéstípusokat, a lépéstulajdonságokat és a beépített változókat.
ms.topic: article
ms.date: 10/23/2019
ms.openlocfilehash: 9558f698b4a9dbca46431fc02ced6ae30de29121
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246980"
---
# <a name="acr-tasks-reference-yaml"></a>ACR-feladatok hivatkozása: YAML

Az ACR-feladatok többlépéses feladatdefiníciója tárolóközpontú számítási primitívet biztosít, amely a tárolók készítésére, tesztelésére és javítására összpontosít. Ez a cikk a többlépéses feladatokat meghatározó YAML-fájlok parancsait, paramétereit, tulajdonságait és szintaxisát ismerteti.

Ez a cikk az ACR-feladatok többlépéses feladatokkal végzett YAML-fájljainak létrehozására vonatkozó hivatkozást tartalmaz. Ha be szeretné mutatkozni az ACR-feladatok bemutatása, olvassa el az [ACR-feladatok áttekintése című témakört.](container-registry-tasks-overview.md)

## <a name="acr-taskyaml-file-format"></a>acr-task.yaml fájlformátum

Az ACR-feladatok támogatják a többlépéses feladatdeklarációt a szabványos YAML szintaxisban. A feladat lépéseit YAML-fájlban adhatja meg. Ezután manuálisan futtathatja a feladatot, ha átadja a fájlt az [az acr run][az-acr-run] parancsnak. Vagy használja a fájlt, hogy hozzon létre egy feladatot [az acr feladat létrehozása,][az-acr-task-create] amely automatikusan aktiválódik a Git véglegesítése vagy alapkép frissítése. Bár ez a `acr-task.yaml` cikk a lépéseket tartalmazó fájlként hivatkozik, az ACR-feladatok minden [támogatott kiterjesztéssel](#supported-task-filename-extensions)rendelkező érvényes fájlnevet támogat.

A legfelső `acr-task.yaml` szintű primitívek **a feladat tulajdonságok,** **a lépéstípusok**és a **lépéstulajdonságok:**

* [A feladattulajdonságok](#task-properties) a feladat végrehajtása során az összes lépésre vonatkoznak. Számos globális feladattulajdonság létezik, többek között:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [A tevékenységlépés-típusok](#task-step-types) a tevékenységekben végrehajtható műveletek típusait jelölik. Három lépéstípus létezik:
  * `build`
  * `push`
  * `cmd`
* [A feladatlépés tulajdonságai](#task-step-properties) olyan paraméterek, amelyek egy adott lépésre vonatkoznak. Több lépés tulajdonsága is van, többek között:
  * `startDelay`
  * `timeout`
  * `when`
  * ... és még sok más.

A `acr-task.yaml` fájl alapformátuma, beleértve néhány gyakori lépéstulajdonságot is, a következőképpen következik. Bár nem teljes körűen ábrázolja az összes rendelkezésre álló lépéstulajdonságot vagy lépéstípus-használatot, gyors áttekintést nyújt az alapvető fájlformátumról.

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

### <a name="supported-task-filename-extensions"></a>Támogatott feladatfájlnév-kiterjesztések

Az ACR Tasks több fájlnévkiterjesztést foglalt le, beleértve `.yaml`a , hogy feladatfájlként fog feldolgozni. Az ACR-feladatok a következő listában *nem* szereplő kiterjesztéseket Docker-fájlnak tekintik: .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua

A YAML az egyetlen olyan fájlformátum, amelyet jelenleg az ACR-feladatok támogatnak. A többi fájlnév-kiterjesztés a későbbi támogatás számára van fenntartva.

## <a name="run-the-sample-tasks"></a>A mintafeladatok futtatása

A cikk következő szakaszaiban több mintafeladatfájl található. A mintafeladatok egy nyilvános GitHub-tárházban, [az Azure-Samples/acr-tasks.][acr-tasks] Futtathatja őket az Azure CLI parancs [az acr run][az-acr-run]. A mintaparancsok hasonlóak a következőkhöz:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

A mintaparancsok formázása feltételezi, hogy beállított egy alapértelmezett beállításjegyzéket az `--registry` Azure CLI-ben, ezért kihagyják a paramétert. Az alapértelmezett beállításjegyzék konfigurálásához használja az [az configure][az-configure] parancsot a `--defaults` paraméterrel, amely elfogad egy `acr=REGISTRY_NAME` értéket.

Például az Azure CLI konfigurálása egy "myregistry" nevű alapértelmezett beállításjegyzékkel:

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Feladat tulajdonságai

A feladattulajdonságok általában a `acr-task.yaml` fájl tetején jelennek meg, és olyan globális tulajdonságok, amelyek a feladatlépések teljes végrehajtása során érvényesek. Ezek közül a globális tulajdonságok közül néhány felülírható egy adott lépésen belül.

| Tulajdonság | Típus | Optional | Leírás | Támogatott felülbírálás | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | sztring | Igen | A fájl `acr-task.yaml` nak az ACR-feladatok szolgáltatás által imitált verziója. Bár az ACR-feladatok a visszamenőleges kompatibilitás fenntartására törekszenek, ez az érték lehetővé teszi, hogy az ACR-feladatok egy meghatározott verzión belül is fenntartsák a kompatibilitást. Ha nincs megadva, az alapértelmezett verzió a legújabb verzióra történik. | Nem | None |
| `stepTimeout` | int (másodperc) | Igen | A lépés maximális másodperce. Ha a tulajdonság meg van adva egy `timeout` feladaton, akkor az összes lépés alapértelmezett tulajdonságát állítja be. Ha `timeout` a tulajdonság egy lépésben van megadva, felülírja a feladat által biztosított tulajdonságot. | Igen | 600 (10 perc) |
| `workingDirectory` | sztring | Igen | A tároló munkakönyvtára futásközben. Ha a tulajdonság meg van adva egy `workingDirectory` feladaton, akkor az összes lépés alapértelmezett tulajdonságát állítja be. Ha egy lépésben meg van adva, felülírja a feladat által biztosított tulajdonságot. | Igen | `/workspace` |
| `env` | [string, string, ...] | Igen |  A feladat környezeti `key=value` változóit meghatározó karakterláncok tömbje. Ha a tulajdonság meg van adva egy `env` feladaton, akkor az összes lépés alapértelmezett tulajdonságát állítja be. Ha egy lépésben van megadva, felülírja a feladatból örökölt környezeti változókat. | None |
| `secrets` | [titkos, titkos, ...] | Igen | [Titkos](#secret) objektumok tömbje. | None |
| `networks` | [hálózat, hálózat, ...] | Igen | [Hálózati](#network) objektumok tömbje. | None |

### <a name="secret"></a>titkos kód

A titkos objektum a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Típus | Optional | Leírás | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | sztring | Nem | A titok azonosítója. | None |
| `keyvault` | sztring | Igen | Az Azure Key Vault titkos URL-címe. | None |
| `clientID` | sztring | Igen | A felhasználó által [hozzárendelt felügyelt identitás](container-registry-tasks-authentication-managed-identity.md) ügyfélazonosítója az Azure-erőforrásokhoz. | None |

### <a name="network"></a>network

A hálózati objektum a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Típus | Optional | Leírás | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | sztring | Nem | A hálózat neve. | None |
| `driver` | sztring | Igen | A hálózatot kezelő illesztőprogram. | None |
| `ipv6` | Bool | Igen | Azt jelzi, hogy engedélyezve van-e az IPv6-hálózat. | `false` |
| `skipCreation` | Bool | Igen | Kihagyja-e a hálózat létrehozását. | `false` |
| `isDefault` | Bool | Igen | Azt jelzi, hogy a hálózat az Azure Container Registry alapértelmezett hálózata-e | `false` |

## <a name="task-step-types"></a>Tevékenységlépés-típusok

Az ACR-feladatok három lépéstípust támogatnak. Minden lépéstípus több tulajdonságot támogat, amelyeket az egyes lépéstípusokszakaszban részleteznek.

| Lépés típusa | Leírás |
| --------- | ----------- |
| [`build`](#build) | Tárolólemezképet hoz létre `docker build` ismerős szintaxissal. |
| [`push`](#push) | Végrehajtja `docker push` az újonnan létrehozott vagy újracímkézett lemezképek et egy tároló beállításjegyzékébe. Az Azure Container Registry, más privát beállításjegyzékek és a nyilvános Docker Hub támogatottak. |
| [`cmd`](#cmd) | Egy tárolót parancsként futtat, és a paraméterek `[ENTRYPOINT]`et átadják a tárolónak. A `cmd` lépéstípus támogatja a `env` `detach`paramétereket, `docker run` például a , a , és más ismerős parancsbeállításokat, lehetővé téve az egység és a funkcionális tesztelést egyidejű tárolóvégrehajtással. |

## <a name="build"></a>Épít

Hozzon létre egy tárolórendszerképet. A `build` lépéstípus egy több-bérlős, biztonságos `docker build` eszköz a felhőben első osztályú primitívként való futtatáshoz.

### <a name="syntax-build"></a>Szintaxis: build

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

A `build` lépéstípus támogatja az alábbi táblázatban szereplő paramétereket. A `build` lépéstípus a [docker buildparancs](https://docs.docker.com/engine/reference/commandline/build/) összes buildelési beállítását is támogatja, például `--build-arg` a buildidő-változók beállításához.

| Paraméter | Leírás | Optional |
| --------- | ----------- | :-------: |
| `-t`&#124;`--image` | A beépített kép `image:tag` teljes mértékben minősítettet határoz meg.<br /><br />Mivel a rendszerképek belső feladat-ellenőrzéshez, például funkcionális `push` tesztekhez is használhatók, nem minden lemezképhez van szükség a beállításjegyzékhez. Azonban egy feladat végrehajtásán belüli lemezkép példányához a lemezképnek szüksége van egy névre, amelyre hivatkozni kell.<br /><br />A `az acr build`programtól eltérően az ACR-feladatok futtatása nem biztosítja az alapértelmezett leküldéses viselkedést. Az ACR-feladatok esetén az alapértelmezett forgatókönyv feltételezi a lemezkép létrehozásának, érvényesítésének, majd leküldéses képességét. Tekintse meg a [leküldéses](#push) képet, hogyan lehet opcionálisan lenyomni a beépített képeket. | Igen |
| `-f`&#124;`--file` | Megadja a docker-fájlt. `docker build` Ha nincs megadva, a környezet gyökerében lévő alapértelmezett Docker-fájl a rendszer feltételezi. Docker-fájl megadásához adja át a fájlnevet a környezet gyökeréhez viszonyítva. | Igen |
| `context` | A gyökérkönyvtár `docker build`átadott . Az egyes feladatok gyökérkönyvtára megosztott [workingDirectory](#task-step-properties)könyvtárra van állítva, és tartalmazza a társított Git klónozott könyvtár gyökerét. | Nem |

### <a name="properties-build"></a>Tulajdonságok: build

A `build` lépéstípus a következő tulajdonságokat támogatja. Ezeknek a tulajdonságoknak a részleteit a cikk [Feladat lépés tulajdonságai](#task-step-properties) című szakaszában találja.

| | | |
| -------- | ---- | -------- |
| `detach` | Bool | Optional |
| `disableWorkingDirectoryOverride` | Bool | Optional |
| `entryPoint` | sztring | Optional |
| `env` | [string, string, ...] | Optional |
| `expose` | [string, string, ...] | Optional |
| `id` | sztring | Optional |
| `ignoreErrors` | Bool | Optional |
| `isolation` | sztring | Optional |
| `keep` | Bool | Optional |
| `network` | objektum | Optional |
| `ports` | [string, string, ...] | Optional |
| `pull` | Bool | Optional |
| `repeat` | int | Optional |
| `retries` | int | Optional |
| `retryDelay` | int (másodperc) | Optional |
| `secret` | objektum | Optional |
| `startDelay` | int (másodperc) | Optional |
| `timeout` | int (másodperc) | Optional |
| `when` | [string, string, ...] | Optional |
| `workingDirectory` | sztring | Optional |

### <a name="examples-build"></a>Példák: build

#### <a name="build-image---context-in-root"></a>Kép létrehozása - környezet a gyökérben

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Kép létrehozása - környezet az alkönyvtárban

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>Nyomja

Egy vagy több beépített vagy újracímkézett lemezkép leküldése egy tárolóbeállítás-jegyzékbe. Támogatja a privát beállításjegyzékek, például az Azure Container Registry vagy a nyilvános Docker Hub lenyomását.

### <a name="syntax-push"></a>Szintaxis: push

A `push` lépéstípus támogatja a képek gyűjteményét. A YAML gyűjtemény szintaxisa támogatja a beágyazott és beágyazott formátumokat. Az egyetlen kép lenyomása általában szövegsor-szintaxissal jelenik meg:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

A jobb olvashatóság érdekében használjon beágyazott szintaxist több kép lenyomásakor:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Tulajdonságok: push

A `push` lépéstípus a következő tulajdonságokat támogatja. Ezeknek a tulajdonságoknak a részleteit a cikk [Feladat lépés tulajdonságai](#task-step-properties) című szakaszában találja.

| | | |
| -------- | ---- | -------- |
| `env` | [string, string, ...] | Optional |
| `id` | sztring | Optional |
| `ignoreErrors` | Bool | Optional |
| `startDelay` | int (másodperc) | Optional |
| `timeout` | int (másodperc) | Optional |
| `when` | [string, string, ...] | Optional |

### <a name="examples-push"></a>Példák: push

#### <a name="push-multiple-images"></a>Több kép leküldése

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Építés, push és futtatás

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>Cmd

A `cmd` lépéstípus egy tárolót futtat.

### <a name="syntax-cmd"></a>Szintaxis: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Tulajdonságok: cmd

A `cmd` lépéstípus a következő tulajdonságokat támogatja:

| | | |
| -------- | ---- | -------- |
| `detach` | Bool | Optional |
| `disableWorkingDirectoryOverride` | Bool | Optional |
| `entryPoint` | sztring | Optional |
| `env` | [string, string, ...] | Optional |
| `expose` | [string, string, ...] | Optional |
| `id` | sztring | Optional |
| `ignoreErrors` | Bool | Optional |
| `isolation` | sztring | Optional |
| `keep` | Bool | Optional |
| `network` | objektum | Optional |
| `ports` | [string, string, ...] | Optional |
| `pull` | Bool | Optional |
| `repeat` | int | Optional |
| `retries` | int | Optional |
| `retryDelay` | int (másodperc) | Optional |
| `secret` | objektum | Optional |
| `startDelay` | int (másodperc) | Optional |
| `timeout` | int (másodperc) | Optional |
| `when` | [string, string, ...] | Optional |
| `workingDirectory` | sztring | Optional |

Ezeknek a tulajdonságoknak a részleteit a cikk [Feladat lépés tulajdonságai](#task-step-properties) című szakaszában találja.

### <a name="examples-cmd"></a>Példák: cmd

#### <a name="run-hello-world-image"></a>Hello-world kép futtatása

Ez a parancs `hello-world.yaml` végrehajtja a feladatfájlt, amely a Docker Hub [hello-world](https://hub.docker.com/_/hello-world/) rendszerképére hivatkozik.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Fuss bash kép és echo "hello world"

Ez a parancs `bash-echo.yaml` végrehajtja a feladatfájlt, amely a Docker Hub bash-lemezképére hivatkozik. [bash](https://hub.docker.com/_/bash/)

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Adott bash képcímke futtatása

Adott képverzió futtatásához adja meg `cmd`a címkét a ban.

Ez a parancs `bash-echo-3.yaml` végrehajtja a feladatfájlt, amely hivatkozik a [bash:3.0](https://hub.docker.com/_/bash/) rendszerkép a Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Egyéni lemezképek futtatása

A `cmd` lépéstípus a szabványos `docker run` formátumot használó képekre hivatkozik. A rendszerleíró adatbázissal nem előzött képek ről feltételezik, hogy docker.io származnak. Az előző példa a következőképpen ábrázolható:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

A szabványos `docker run` rendszerkép-referencia `cmd` konvenció használatával a rendszerképek bármely privát beállításjegyzékből vagy a nyilvános Docker Hubból futtathatók. Ha ugyanabban a beállításjegyzékben hivatkozik, amelyben az ACR-feladat végrehajtás alatt áll, nem kell megadnia a rendszerleíró adatbázis hitelesítő adatait.

* Futtasson egy Azure-tároló beállításjegyzékből származó lemezképet. A következő példa feltételezi, hogy `myregistry`van egy `myimage:mytag`rendszerleíró adatbázis neve , és egy egyéni lemezkép .

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* A rendszerleíró adatbázis hivatkozásának általánosítása Futtatás változóval vagy aliassal

    Ahelyett, hogy a rendszerleíró adatbázis `acr-task.yaml` nevét egy fájlban kódolna, hordozhatóbbá teheti a [Futtatás változó](#run-variables) vagy [alias](#aliases)használatával. A `Run.Registry` változó `$Registry` vagy alias futásidőben kibővül annak a rendszerleíró adatbázisnak a nevére, amelyben a feladat végrehajtásra kerül.

    Ha például általánosítani szeretné az előző feladatot, hogy az bármely Azure-tároló beállításjegyzékében működjön, hivatkozzon a $Registry változóra a lemezkép nevében:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>Feladatlépés tulajdonságai

Minden lépéstípus több, a típusának megfelelő tulajdonságot támogat. Az alábbi táblázat az összes rendelkezésre álló lépéstulajdonságot határozza meg. Nem minden lépéstípus támogatja az összes tulajdonságot. Az egyes lépéstípusokhoz tartozó tulajdonságok megtekintéséhez tekintse meg a [cmd](#cmd), [build](#build)és [push](#push) lépéstípus referenciaszakaszait.

| Tulajdonság | Típus | Optional | Leírás | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | Bool | Igen | Azt jelzi, hogy a tárolót le kell-e választani futás közben. | `false` |
| `disableWorkingDirectoryOverride` | Bool | Igen | A felülbírálási funkció letiltása. `workingDirectory` Ezzel együtt `workingDirectory` teljes mértékben szabályozhatja a tároló munkakönyvtárát. | `false` |
| `entryPoint` | sztring | Igen | `[ENTRYPOINT]` Felülbírálja egy lépés tárolóját. | None |
| `env` | [string, string, ...] | Igen | A lépés környezeti `key=value` változóit meghatározó karakterláncok tömbje. | None |
| `expose` | [string, string, ...] | Igen | A tárolóból elérhető portok tömbje. |  None |
| [`id`](#example-id) | sztring | Igen | Egyedileg azonosítja a tevékenységen belüli lépést. A tevékenység egyéb lépései egy `id`lépésre hivatkozhatnak, `when`például a függőség ellenőrzésére a segítségével.<br /><br />A `id` a futó tároló neve is. A feladat más tárolóiban futó folyamatok `id` hivatkozhatnak a DNS-állomás nevére, vagy például docker-naplókkal [id] való elérésére. | `acb_step_%d`, `%d` hol van a lépés 0-alapú indexe felülről lefelé a YAML-fájlban |
| `ignoreErrors` | Bool | Igen | Azt jelzi, hogy a lépés sikeresként jelenjen-e meg, függetlenül attól, hogy hiba történt-e a tároló végrehajtása során. | `false` |
| `isolation` | sztring | Igen | A tároló elkülönítési szintje. | `default` |
| `keep` | Bool | Igen | Azt jelzi, hogy a lépés tárolóját meg kell-e őrizni a végrehajtás után. | `false` |
| `network` | objektum | Igen | Azonosítja azt a hálózatot, amelyben a tároló fut. | None |
| `ports` | [string, string, ...] | Igen | A tárolóból az állomásnak közzétett porttömbök. |  None |
| `pull` | Bool | Igen | Azt jelzi, hogy a gyorsítótárazás imitálásának megakadályozása érdekében kényszerítse-e a tároló lekéréseit. | `false` |
| `privileged` | Bool | Igen | Azt jelzi, hogy a tárolót kiemelt módban kell-e futtatni. | `false` |
| `repeat` | int | Igen | A tároló végrehajtásának megismétléséhez újrapróbálkozások száma. | 0 |
| `retries` | int | Igen | A tároló sikertelen végrehajtása esetén megkísérelt újrapróbálkozások száma. Az újrapróbálkozáscsak akkor történik meg, ha a tároló kilépési kódja nem nulla. | 0 |
| `retryDelay` | int (másodperc) | Igen | A tároló végrehajtásának újrapróbálkozásai közötti késleltetés másodpercben. | 0 |
| `secret` | objektum | Igen | Azonosítja az Azure Key Vault titkos vagy [felügyelt identitását az Azure-erőforrásokhoz.](container-registry-tasks-authentication-managed-identity.md) | None |
| `startDelay` | int (másodperc) | Igen | A tároló végrehajtásának késleltetésére eltelt másodpercek száma. | 0 |
| `timeout` | int (másodperc) | Igen | A lépés legfeljebb ennyi másodpercig tarthat, mielőtt befejeznék. | 600 |
| [`when`](#example-when) | [string, string, ...] | Igen | Egy lépés függőségét a feladat egy vagy több más lépésétismerteti kontinami(k) adja meg. | None |
| `user` | sztring | Igen | Tároló felhasználóneve vagy UID azonosítója | None |
| `workingDirectory` | sztring | Igen | Beállítja a munkakönyvtárat egy lépéshez. Alapértelmezés szerint az ACR-feladatok egy gyökérkönyvtárat hoz létre munkakönyvtárként. Ha azonban a build több lépésből áll, a korábbi lépések megoszthatják az összetevőket a későbbi lépésekkel, ha ugyanazt a munkakönyvtárat adják meg. | `/workspace` |

### <a name="examples-task-step-properties"></a>Példák: Feladatlépés tulajdonságai

#### <a name="example-id"></a>Példa: id

Hozzon létre két képet, amely egy funkcionális tesztképet indukál. Minden lépést egy egyedi, `id` amely a feladat hivatkozásának egyéb lépéseit azonosítja a tulajdonukban. `when`

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Példa: ha

A `when` tulajdonság meghatározza egy lépés függőségét a feladaton belüli egyéb lépésektől. Két paraméterértéket támogat:

* `when: ["-"]`- Azt jelzi, hogy nincs függőség más lépésektől. A megadott `when: ["-"]` lépés azonnal megkezdi a végrehajtást, és lehetővé teszi az egyidejű lépésvégrehajtását.
* `when: ["id1", "id2"]`- Azt jelzi, hogy a `id` lépés az "id1" és `id` az "id2" lépéstől függ. Ez a lépés nem hajtható végre, amíg az "id1" és az "id2" lépések nem fejeződnek be.

Ha `when` nincs megadva egy lépésben, ez a lépés a `acr-task.yaml` fájl előző lépésének teljesítésétől függ.

Egymást követő `when`lépésvégrehajtás nélkül:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Egymást követő `when`lépésvégrehajtás a következővel:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Párhuzamos képek létrehozása:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Párhuzamos képösszeállítás és függő tesztelés:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Változók futtatása

Az ACR-feladatok olyan változók alapértelmezett készletét tartalmazzák, amelyek végrehajtásakor a feladatlépésekhez elérhetők. Ezek a változók a formátum `{{.Run.VariableName}}`használatával `VariableName` érhetők el, ahol az alábbi formátum valamelyike látható:

* `Run.ID`
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

A változónevek általában magától értetődőek. A részleteket a leggyakrabban használt változók követik. A YAML `v1.1.0`verziótól rövidített, előre definiált [feladataliast](#aliases) használhat a legtöbb futó változó helyett. Például a helyett `{{.Run.Registry}}`használja `$Registry` az aliast.

### <a name="runid"></a>Run.ID

Minden futtatás, `az acr run`keresztül , vagy eseményindító `az acr task create`alapú végrehajtása létrehozott feladatok , egyedi azonosítóval rendelkezik. Az azonosító a jelenleg végrehajtás alatt álló futtatást jelöli.

Általában egy kép egyedi címkézéséhez használatos:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistry"></a>Futtatás.Rendszerleíró adatbázis

A rendszerleíró adatbázis teljesen minősített kiszolgálóneve. Általában arra használják, hogy általánosan hivatkozzon a rendszerleíró adatbázisra, ahol a feladat fut.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Futtatás.Rendszerleíró név

A tárolóbeállításjegyzék neve. Általában olyan feladatlépésekben használatos, amelyek nem igényelnek teljesen `cmd` minősített kiszolgálónevet, például az Azure CLI-parancsokat a beállításjegyzékeken futó lépéseket.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Futtatás.Dátum

A futtatás aktuális UTC-időpontja.

### <a name="runcommit"></a>Futtatás.Véglegesítés

A GitHub-tárház véglegesítése által indított feladat hozadékát a véglegesítési azonosító.

### <a name="runbranch"></a>Futtatás.Ág

A GitHub-tárház véglegesítése által indított feladathoz az ág neve.

## <a name="aliases"></a>Aliasok

A `v1.1.0`programtól az ACR-feladatok olyan aliasokat támogatnak, amelyek végrehajtásakor a feladatlépésekhez elérhetők. Az aliasok koncepciója hasonló a bash-ben és más parancshéjakban támogatott aliasokhoz (parancshivatkozásokhoz). 

Alias használatával bármely parancsot vagy parancscsoportot elindíthatja (beleértve a beállításokat és a fájlneveket) egyetlen szó beírásával.

Az ACR-feladatok számos előre definiált aliast és egyéni aliast támogatnak.

### <a name="predefined-aliases"></a>Előre definiált aliasok

A következő feladataliasok érhetők el a [futtatási változók](#run-variables)helyett:

| Alias | Változó futtatása |
| ----- | ------------ |
| `ID` | `Run.ID` |
| `SharedVolume` | `Run.SharedVolume` |
| `Registry` | `Run.Registry` |
| `RegistryName` | `Run.RegistryName` |
| `Date` | `Run.Date` |
| `OS` | `Run.OS` |
| `Architecture` | `Run.Architecture` |
| `Commit` | `Run.Commit` |
| `Branch` | `Run.Branch` |

A feladatlépésekben előzze `$` meg egy aliast az utasítással, ahogy ebben a példában is:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Képaliasok

Az alábbi aliasok mindegyike stabil lemezképre mutat a Microsoft Container Registry (MCR) alkalmazásban. Mindegyikre hivatkozhat a `cmd` Feladatfájl részben irányelv használata nélkül.

| Alias | Kép |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

A következő példafeladat több aliast használ a 7 napnál `samples/hello-world` régebbi lemezképek [törlésére](container-registry-auto-purge.md) a futtatási beállításjegyzékben:

```yml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Egyéni alias

Egyéni aliast definiáljon a YAML-fájlban, és használja azt az alábbi példában látható módon. Az alias csak alfanumerikus karaktereket tartalmazhat. Az alias kibontásának alapértelmezett `$` direktíva a karakter.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

Az egyéni aliasdefiníciókhoz távoli vagy helyi YAML-fájlra mutató hivatkozással rendelkezhet. A következő példa egy YAML-fájlra mutató hivatkozásaz Azure blob storage-ban:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>További lépések

A többlépéses feladatok áttekintését az [ACR-feladatok többlépéses build- és tesztelési feladatainak futtatása című témakörben találja.](container-registry-tasks-multi-step.md)

Az egylépéses buildeket az [ACR-feladatok áttekintése című témakörben találja.](container-registry-tasks-overview.md)



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure

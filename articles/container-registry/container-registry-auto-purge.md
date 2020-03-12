---
title: Címkék és jegyzékek kiürítése
description: A kiürítési parancs használatával több címkét és jegyzékfájlt törölhet egy Azure Container registryből az életkor és a címke szűrője alapján, és opcionálisan ütemezhet törlési műveleteket.
ms.topic: article
ms.date: 08/14/2019
ms.openlocfilehash: f9d86b628bdd0ce0db3067b02a47517d8aadcba3
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087333"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Lemezképek automatikus törlése az Azure Container registryből

Ha egy fejlesztési munkafolyamat részeként Azure Container registryt használ, a beállításjegyzék gyorsan kitöltheti azokat a képeket vagy egyéb összetevőket, amelyek rövid idő után nem szükségesek. Előfordulhat, hogy törölni kívánja az összes olyan címkét, amely egy adott időtartamnál régebbi, vagy megfelel a megadott szűrőnek. Ha több összetevőt szeretne gyorsan törölni, ez a cikk bemutatja a `acr purge` parancsot, amelyet igény szerinti vagy [ütemezett](container-registry-tasks-scheduled.md) ACR-feladatként futtathat. 

A `acr purge` parancs jelenleg egy nyilvános tároló-rendszerképben (`mcr.microsoft.com/acr/acr-cli:0.1`) van elosztva, amely forráskódból épül fel az [ACR-CLI-](https://github.com/Azure/acr-cli) tárházban a githubban.

Ebben a cikkben a Azure Cloud Shell vagy az Azure CLI helyi telepítését használhatja az ACR-feladat példáinak futtatásához. Ha helyileg szeretné használni, a 2.0.69 vagy újabb verziót kötelező megadni. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install]. 

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

> [!WARNING]
> A `acr purge` parancs körültekintő használata – a törölt képadatok nem állíthatók helyre. Ha olyan rendszerekkel rendelkezik, amelyekben a manifest Digest (a rendszerkép neve helyett) lekéri a képeket, ne törölje a címkézetlen lemezképek törlését. A címkézetlen lemezképek törlésével megakadályozhatja, hogy ezek a rendszerek kihúzzanak a lemezképeket a beállításjegyzékből. A jegyzékfájlok helyett érdemes lehet egy *egyedi címkézési* sémát alkalmazni, amely [ajánlott eljárás](container-registry-image-tag-version.md).

Ha egyetlen képcímkét vagy jegyzékfájlokat szeretne törölni az Azure CLI-parancsokkal, tekintse meg [a tároló lemezképek törlése a Azure Container Registryban](container-registry-delete.md)című témakört.

## <a name="use-the-purge-command"></a>A kiürítési parancs használata

A `acr purge` Container parancs a képeket címkével törli egy olyan adattárban, amely megfelel egy szűrőnek, és a megadott időtartamnál régebbi. Alapértelmezés szerint a rendszer csak a címkékre mutató hivatkozásokat törli, nem pedig az alapul szolgáló [jegyzékfájlokat](container-registry-concepts.md#manifest) és adatrétegeket. A parancshoz lehetőség van a jegyzékfájlok törlésére is. 

> [!NOTE]
> `acr purge` nem töröl egy képcímkét vagy-tárat, amelyben a `write-enabled` attribútum értéke `false`. További információ: [Container-rendszerkép zárolása egy Azure Container registryben](container-registry-image-lock.md).

`acr purge` úgy lett kialakítva, hogy Container parancsként fusson egy [ACR-feladatban](container-registry-tasks-overview.md), hogy automatikusan hitelesítse magát a beállításjegyzékben, ahol a feladat fut, és műveleteket hajt végre. A cikkben szereplő példák a `acr purge` parancs- [aliast](container-registry-tasks-reference-yaml.md#aliases) használják a teljes tárolót ábrázoló rendszerkép parancs helyett.

A `acr purge`futtatásakor legalább a következőket kell megadnia:

* `--filter` – egy adattárat és egy *reguláris kifejezést* , amellyel szűrheti a címkéket a tárházban. Példák: a `--filter "hello-world:.*"` az `hello-world` adattár összes címkéjét egyezteti, és a `--filter "hello-world:^1.*"` a `1`kezdődő címkékkel egyezik meg. Több adattárház kiürítéséhez több `--filter` paramétert adjon meg.
* `--ago` – A Go-Style [időtartam karakterlánca](https://golang.org/pkg/time/) , amely azt jelzi, hogy milyen időtartamot kell törölni a képekből. Az időtartam egy vagy több decimális számokból áll, amelyek mindegyike egység utótaggal rendelkezik. Az érvényes időegységek a "d" napok, a "h" órák, az "m" pedig percek közé tartoznak. Például `--ago 2d3h6m` kijelöli az összes szűrt képet, amelynek utolsó módosítása több mint 2 nap, 3 óra és 6 perccel ezelőtt történt, és `--ago 1.5h` a képeket a legutóbb módosított 1,5 órája.

`acr purge` több választható paramétert is támogat. Ebben a cikkben a következő két példát használjuk:

* `--untagged` – megadja, hogy a rendszer törli a társított címkékkel nem rendelkező jegyzékfájlokat (*címkézetlen jegyzékfájlokat*).
* `--dry-run` – megadja, hogy a rendszer nem törli az adatokat, de a kimenet ugyanaz, mint ha a parancsot ezen jelző nélkül futtatja. Ez a paraméter a kiürítési parancsok teszteléséhez hasznos, így meggyőződhet róla, hogy nem törli a megőrizni kívánt információkat.

További paraméterek: `acr purge --help`futtatása. 

`acr purge` támogatja az ACR-feladatok egyéb funkcióit, beleértve a [futtatási változókat](container-registry-tasks-reference-yaml.md#run-variables) és a [tevékenységek futtatására szolgáló naplókat](container-registry-tasks-logs.md) , amelyeket a rendszer a későbbi lekéréshez is ment.

### <a name="run-in-an-on-demand-task"></a>Futtatás igény szerinti feladatban

Az alábbi példa az az [ACR Run][az-acr-run] paranccsal futtatja az `acr purge` parancsot igény szerint. Ez a példa törli az összes képcímkét és jegyzékfájlt a *myregistry* `hello-world` adattárában, amely több mint 1 nappal ezelőtt módosult. A Container parancs egy környezeti változó használatával lett átadva. A feladat forrás környezet nélkül fut.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>Futtatás ütemezett feladatban

Az alábbi példa az az [ACR Task Create][az-acr-task-create] parancsot használja egy napi [ütemezett ACR-feladat](container-registry-tasks-scheduled.md)létrehozásához. A feladat kiüríti a több mint 7 napja módosított címkéket a `hello-world` adattárában. A Container parancs egy környezeti változó használatával lett átadva. A feladat forrás környezet nélkül fut.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

Az az [ACR Task show][az-acr-task-show] parancs futtatásával ellenőrizze, hogy az időzítő trigger konfigurálva van-e.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Nagy számú címke és jegyzék kiürítése

Számos címke és jegyzékfájl kiürítése több percet is igénybe vehet. Több ezer címke és jegyzékfájl törléséhez előfordulhat, hogy a parancsnak hosszabb ideig kell futnia, mint az alapértelmezett időkorlát 600 másodpercnél egy igény szerinti feladathoz, vagy 3600 másodperc az ütemezett feladathoz. Ha túllépi az időtúllépési időt, a rendszer csak a címkék és a jegyzékfájlok egy részhalmazát törli. Ha biztosítani szeretné, hogy a nagyméretű kiürítés sikeres legyen, adja át a `--timeout` paramétert az érték növeléséhez. 

Az alábbi igény szerinti feladat például 3600 másodperc (1 óra) időtartamot állít be:

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Példa: több tárház ütemezett kiürítése egy beállításjegyzékben

Ez a példa végigvezeti a `acr purge` használatával, hogy rendszeres időközönként törölje a több tárházat a beállításjegyzékben. Előfordulhat például, hogy rendelkezik egy fejlesztési folyamattal, amely leküldi a rendszerképeket a `samples/devimage1` és `samples/devimage2` adattárakba. A fejlesztői lemezképeket rendszeresen importálhatja üzemi adattárba az üzemelő példányok számára, így már nincs szüksége a fejlesztői lemezképekre. Heti rendszerességgel kiüríti a `samples/devimage1` és `samples/devimage2` adattárakat a következő heti munka előkészítéséhez.

### <a name="preview-the-purge"></a>A kiürítés előzetes verziója

Az adatok törlése előtt javasoljuk, hogy igény szerinti kiürítési feladatot futtasson a `--dry-run` paraméter használatával. Ez a beállítás lehetővé teszi, hogy megtekintse azokat a címkéket és jegyzékfájlokat, amelyeket a parancs töröl, és nem távolítja el az összes információt. 

A következő példában az egyes Tárházak szűrői az összes címkét kijelölik. A `--ago 0d` paraméter a szűrőknek megfelelő adattárakban lévő összes korszak képeire illeszkedik. Módosítsa a kiválasztási feltételeket a forgatókönyvhöz szükséges módon. A `--untagged` paraméter azt jelzi, hogy a címkék mellett törölni kell a jegyzékfájlokat is. A Container parancsot egy környezeti változó használatával adja át az az [ACR Run][az-acr-run] parancsnak.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

Tekintse át a parancs kimenetét a kiválasztási paramétereknek megfelelő címkék és jegyzékfájlok megtekintéséhez. Mivel a parancs `--dry-run`fut, a rendszer nem törli az adatvesztést.

Példa a kimenetre:

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>A kiürítés beütemezett időpontja

A száraz Futtatás ellenőrzése után hozzon létre egy ütemezett feladatot a kiürítés automatizálásához. A következő példa heti feladatot ütemezhet be vasárnap 1:00 UTC időpontban az előző törlési parancs futtatásához:

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

Az az [ACR Task show][az-acr-task-show] parancs futtatásával ellenőrizze, hogy az időzítő trigger konfigurálva van-e.

## <a name="next-steps"></a>Következő lépések

További információ a képadatok Azure Container Registryban való [törlésének](container-registry-delete.md) egyéb lehetőségeiről.

További információ a képtárolásról: [tároló képtárolója Azure Container Registryban](container-registry-storage.md).

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show


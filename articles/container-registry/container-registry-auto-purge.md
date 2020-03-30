---
title: Címkék és jegyzékek törlése
description: A kiürítési parancs segítségével töröljön több címkét és jegyzéket egy Azure-tároló beállításjegyzékéből az életkor és a címkeszűrő alapján, és szükség esetén ütemezze a kiürítési műveleteket.
ms.topic: article
ms.date: 08/14/2019
ms.openlocfilehash: f9d86b628bdd0ce0db3067b02a47517d8aadcba3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087333"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Lemezképek automatikus törlése egy Azure-tároló beállításjegyzékéből

Ha egy Azure-tároló beállításjegyzék et használ egy fejlesztési munkafolyamat részeként, a beállításjegyzék gyorsan megtöltheti a lemezképek vagy egyéb összetevők, amelyek rövid idő után nem szükséges. Előfordulhat, hogy törölni szeretné az összes olyan címkét, amely régebbi egy bizonyos időtartamnál, vagy megfelel egy megadott névszűrőnek. Több összetevő gyors törléséhez ez a `acr purge` cikk bemutatja az igény szerinti vagy ütemezett ACR-feladatként futtatható [parancsot.](container-registry-tasks-scheduled.md) 

A `acr purge` parancs jelenleg egy nyilvános tárolórendszerkép (`mcr.microsoft.com/acr/acr-cli:0.1`) között van elosztva, amely a GitHub [acr-cli](https://github.com/Azure/acr-cli) tártárában található forráskódból készült.

Használhatja az Azure Cloud Shell vagy az Azure CLI helyi telepítését az ACR-feladat példák futtatásához ebben a cikkben. Ha helyileg szeretné használni, a 2.0.69-es vagy újabb verzió szükséges. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install]. 

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

> [!WARNING]
> Óvatosan `acr purge` használja a parancsot - a törölt képadatok nem helyreállíthatók. Ha olyan rendszerekkel rendelkezik, amelyek a listákkivonatalapján lekérik a képeket (ellentétben a képnevével), ne ürítse ki a címkézetlen képeket. A címkézetlen képek törlése megakadályozza, hogy ezek a rendszerek lehúzzák a képeket a rendszerleíró adatbázisból. Ahelyett, hogy a manifesztet húzná, fontolja meg egy *egyedi címkézési* rendszer elfogadását, amely [ajánlott ajánlott eljárás.](container-registry-image-tag-version.md)

Ha az Azure CLI-parancsokkal szeretné törölni az egyes lemezképeket vagy jegyzékeket, olvassa el [a Tárolórendszerképek törlése az Azure Container Registry alkalmazásban című témakört.](container-registry-delete.md)

## <a name="use-the-purge-command"></a>A kiürítés parancs használata

A `acr purge` container parancs törli a képeket címke szerint egy tárházban, amely megfelel egy névszűrőnek, és amely régebbi, mint egy megadott időtartam. Alapértelmezés szerint csak a címkehivatkozások törlődnek, az alapul szolgáló [jegyzékek](container-registry-concepts.md#manifest) és rétegadatok nem. A parancs rendelkezik a jegyzékek törlésére is. 

> [!NOTE]
> `acr purge`nem törli a képcímkét vagy `write-enabled` adattárat, `false`ha az attribútum beállítása . További információ: [Tárolólemezkép zárolása egy Azure-tároló beállításjegyzékében.](container-registry-image-lock.md)

`acr purge`úgy tervezték, hogy tárolóparancsként fusson egy [ACR-feladatban,](container-registry-tasks-overview.md)így automatikusan hitelesíti magát azzal a beállításjegyzékkel, ahol a feladat fut, és ott műveleteket hajt végre. A cikkben szereplő feladatpéldák a `acr purge` [parancsaliast](container-registry-tasks-reference-yaml.md#aliases) használják a teljesen minősített tárolólemezkép-parancs helyett.

Legalább a következőket adja meg `acr purge`a futtatáskor:

* `--filter`- Egy tárház és egy *reguláris kifejezés* a címkék szűrésére a tárházban. Példák: `--filter "hello-world:.*"` megfelel a `hello-world` tárház `--filter "hello-world:^1.*"` összes címkéjének, `1`és a . Több `--filter` paraméter átadása több adattárak törléséhez.
* `--ago`- A Go-stílusú [időtartam karakterlánc](https://golang.org/pkg/time/) jelzi azt az időtartamot, amelyen túl a képek törlődnek. Az időtartam egy vagy több decimális számsorozatból áll, amelyek mindegyike egységutótaggal van elszámolva. Az érvényes időegységek közé tartozik a "d" napokra, a "h" órákra és az "m" a percekre. Például `--ago 2d3h6m` kiválasztja az összes szűrt képet, amelyet utoljára módosítottak `--ago 1.5h` több mint 2 nappal, 3 órával és 6 perccel ezelőtt, és kiválasztja az utoljára módosított képeket, mint 1,5 órával ezelőtt.

`acr purge`számos választható paramétert támogat. A következő két példa ebben a cikkben:

* `--untagged`- Megadja, hogy a nem társított címkékkel *(címkézetlen jegyzékfájlokkal)* nem tartalmazó jegyzékek törlődnek.
* `--dry-run`- Itt adhatja meg, hogy a rendszer ne töröljön adatokat, de a kimenet megegyezik azzal, ha a parancs e jelző nélkül futna. Ez a paraméter akkor hasznos, ha egy kiürítési parancsot tesztel, hogy megbizonyosodjon arról, hogy véletlenül nem törli a megőrizni kívánt adatokat.

További paraméterekért futtassa a futtassa a futtassa a futtassa `acr purge --help`a 

`acr purge`támogatja az ACR-feladatok parancsainak egyéb funkcióit, beleértve [a futtatási változókat](container-registry-tasks-reference-yaml.md#run-variables) és [a feladatfuttatást naplónaplókat,](container-registry-tasks-logs.md) amelyeket a program streamel, és későbbi lekérésre is ment.

### <a name="run-in-an-on-demand-task"></a>Futtatás igény szerinti feladatban

A következő példa az [az acr][az-acr-run] `acr purge` run parancsot használja a parancs igény szerinti futtatásához. Ez a példa törli az összes `hello-world` képcímkék és jegyzékek a tárházban *myregistry,* amelyek több mint 1 nappal ezelőtt módosult. A tárolóparancs egy környezeti változó használatával kerül átadásra. A feladat forráskörnyezet nélkül fut.

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

A következő példa az [az acr feladat létrehozási][az-acr-task-create] parancsával hoz létre egy napi [ütemezett ACR-feladatot.](container-registry-tasks-scheduled.md) A feladat törli a több mint 7 `hello-world` nappal ezelőtt módosított címkéket a tárházban. A tárolóparancs egy környezeti változó használatával kerül átadásra. A feladat forráskörnyezet nélkül fut.

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

Futtassa az [az acr feladatshow][az-acr-task-show] parancsot, és tekintse meg, hogy az időzítő eseményindító konfigurálva van.Run the the acr task show command to see that the timer trigger is configured.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Nagy számú címke és jegyzékfájl kiürítése

Nagy számú címke és jegyzékfájl eltávolítása több percet vagy még tovább is eltarthat. Több ezer címke és jegyzék törléséhez előfordulhat, hogy a parancsnak hosszabb ideig kell futnia, mint egy igény szerinti feladat esetében az alapértelmezett 600 másodperces időtúlfutás, illetve egy ütemezett feladat esetében 3600 másodperc. Ha túllépi az időtúllépési időt, a címkéknek és jegyzéklistáknak csak egy részhalmaza törlődik. Annak érdekében, hogy egy nagyméretű `--timeout` kiürítés befejeződött, adja át a paramétert az érték növeléséhez. 

Például a következő igény szerinti tevékenység 3600 másodperces (1 órás) időkérési időt állít be:

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

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Példa: Több adattár ütemezett kiürítése a rendszerleíró adatbázisban

Ez a példa `acr purge` végigvezeti a használatával rendszeresen megtisztítani több tárolók a rendszerleíró adatbázisban. Például előfordulhat, hogy egy fejlesztési folyamat, `samples/devimage1` amely `samples/devimage2` leküldéses képeket a és a tárolók. Rendszeresen importálja a fejlesztési lemezképeket egy éles tárházba a központi telepítések, így már nincs szüksége a fejlesztési lemezképek. Heti rendszerességgel megtisztítja `samples/devimage1` `samples/devimage2` a tárolókat és az adattárakat, felkészülve a következő heti munkára.

### <a name="preview-the-purge"></a>A kiürítés előnézetének megtekintése

Az adatok törlése előtt javasoljuk, hogy futtasson egy igény szerinti kiürítési feladatot a `--dry-run` paraméter használatával. Ez a beállítás lehetővé teszi, hogy a címkék és a jegyzékek, hogy a parancs kiürítése, eltávolítása nélkül az adatokat. 

A következő példában az egyes tárházak szűrője az összes címkét kijelöli. A `--ago 0d` paraméter a szűrőknek megfelelő tárolókban lévő összes korosztály képeinek felel meg. Szükség szerint módosítsa a kiválasztási feltételeket. A `--untagged` paraméter azt jelzi, hogy a címkék mellett a jegyzékek is törlődnek. A tároló parancs átkerül az [az acr run][az-acr-run] parancs egy környezeti változó használatával.

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

Tekintse át a parancs kimenetét a kijelölési paramétereknek megfelelő címkék és jegyzékek megtekintéséhez. Mivel a parancs `--dry-run`a rendszerrel fut, a rendszer nem töröl adatokat.

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

### <a name="schedule-the-purge"></a>A kiürítés ütemezése

Miután ellenőrizte a próbafuttatást, hozzon létre egy ütemezett feladatot a kiürítés automatizálásához. A következő példa egy heti feladatot ütemez vasárnap 1:00-kor UTC-kor az előző kiürítési parancs futtatásához:

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

Futtassa az [az acr feladatshow][az-acr-task-show] parancsot, és tekintse meg, hogy az időzítő eseményindító konfigurálva van.Run the the acr task show command to see that the timer trigger is configured.

## <a name="next-steps"></a>További lépések

További információ a lemezképek adatainak az Azure Container Registry [szolgáltatásban való törlésére](container-registry-delete.md) vonatkozó egyéb lehetőségekről.

A lemezképek tárolásáról további információt a Container image storage in Azure Container Registry című [témakörben talál.](container-registry-storage.md)

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show


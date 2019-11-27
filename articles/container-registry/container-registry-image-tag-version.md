---
title: Ajánlott eljárások a képcímkéhez
description: Ajánlott eljárások a Docker-tároló rendszerképeinek címkézéséhez és verziószámozásához, amikor képeket küld és húz le egy Azure Container registryből
author: stevelasker
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: 2d407f041456ea3856fbeedf98147356eaeb61d6
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454994"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>A tároló lemezképének címkézésére és verziószámozására vonatkozó javaslatok

Amikor egy tároló-beállításjegyzékbe helyezi üzembe a tároló-lemezképek telepítését, majd telepíti őket, szüksége lesz egy, a képcímkézésre és a verziószámozásra vonatkozó stratégiára. Ebből a cikkből megtudhatja, hogyan illeszkedik a tároló életciklusa során a következő két módszerre:

* **Stabil címkék** – felhasználható címkék, például egy fő vagy alverzió (például *mycontainerimage: 1.0*) jelzése.
* **Egyedi címkék** – egy másik címkét minden olyan képhez, amelyet egy beállításjegyzékbe küld, például *mycontainerimage: abc123*.

## <a name="stable-tags"></a>Stabil Címkék

**Javaslat**: használjon stabil címkéket a tároló-buildek **alapképeinek** fenntartásához. Kerülje a stabil címkékkel való üzembe helyezést, mivel ezek a címkék továbbra is megkapják a frissítéseket, és következetlenségeket vezethetnek be az éles környezetekben.

A *stabil címkék* azt jelentik, hogy egy fejlesztő vagy egy összeállítási rendszer továbbra is lehívhat egy adott címkét, amely továbbra is frissítéseket kap. A stabil nem jelenti azt, hogy a tartalom zárolva van. Ehelyett a stabil a rendszerképnek stabilnak kell lennie az adott verzió céljához. A "stabil" maradni a biztonsági javítások vagy a keretrendszer frissítéseinek alkalmazásával lehet kiszolgálni.

### <a name="example"></a>Példa

A Framework Team-hajók 1,0-es verzióval rendelkeznek. Tudják, hogy frissítéseket küldenek, beleértve a kisebb frissítéseket is. Egy adott fő-és alverzióhoz tartozó stabil címkék támogatásához két stabil címkével rendelkeznek.

* `:1` – a főverzió stabil címkéje. a `1` a "legújabb" vagy "legújabb" 1. * verziót jelöli.
* `:1.0`– a 1,0-es verzióhoz tartozó stabil címke, amely lehetővé teszi a fejlesztők számára, hogy a 1,0-es frissítésekhez kapcsolódjanak, és ne legyenek felkészülve a 1,1-ra a kiadáskor.

A csapat a `:latest` címkét is használja, amely a legújabb stabil címkére mutat, függetlenül attól, hogy milyen a jelenlegi főverzió.

Ha az alapszintű lemezkép frissítései elérhetők, vagy a keretrendszer bármely típusú karbantartási kiadása, a stabil címkékkel rendelkező rendszerképek frissülnek a legújabb kivonatoló értékre, amely az adott verzió legújabb stabil kiadását jelöli.

Ebben az esetben a fő és a másodlagos címkéket is folyamatosan szervizeli a rendszer. Alaprendszerkép-forgatókönyv esetén ez lehetővé teszi, hogy a rendszerkép tulajdonosa kiszolgált lemezképeket biztosítson.

## <a name="unique-tags"></a>Egyedi címkék

**Javaslat**: használjon egyedi címkéket az üzemelő **példányokhoz**, különösen olyan környezetben, amely több csomópontra is méretezhető. Valószínűleg az összetevők konzisztens verziójának szándékos telepítését szeretné használni. Ha a tároló újraindul, vagy egy Orchestrator több példányra van kibővítve, a gazdagépek nem fognak véletlenül újabb verziót lekérni, és nem konzisztensek a többi csomóponttal.

Az egyedi címkézés egyszerűen azt jelenti, hogy a beállításjegyzékbe leküldött összes rendszerkép egyedi címkével rendelkezik. A címkék nem lesznek újra felhasználva. Az egyedi címkék létrehozásához több minta is készíthető, többek között a következők:

* **Dátum-idő bélyegző** – ez a megközelítés meglehetősen gyakori, mivel a rendszerkép létrehozásakor egyértelműen megtudhatja, hogy mikor készült. De hogyan lehet összekapcsolni a Build-rendszerrel? Meg kell keresnie az adott időpontban befejezett buildet? Milyen időzóna van? Az összes Build rendszerét UTC-re kalibrálták?
* **Git-véglegesítés** – ez a megközelítés csak az alapszintű rendszerkép frissítéseinek támogatásának megkezdése után működik. Ha egy alaprendszerkép frissítése történik, a Build rendszer a korábbi buildtel megegyező git-véglegesítés után indul el. Az alaprendszerkép azonban új tartalommal rendelkezik. Általánosságban elmondható, hogy a git-véglegesítés egy *félig*stabil címkét biztosít.
* **Manifest Digest** – a tároló-beállításjegyzékbe leküldött összes tároló-rendszerkép egy jegyzékfájlhoz van társítva, amely egyedi SHA-256 kivonattal vagy kivonattal van azonosítva. Míg egyedi, a kivonat hosszú, nehezen olvasható és nem korrelál a Build-környezettel.
* **Build azonosítója** – ez a lehetőség lehet a legjobb megoldás, mivel ez valószínűleg növekményes, és lehetővé teszi, hogy az összes összetevő és napló megtalálásához visszakapcsolja az adott buildet. A manifest-kivonatokhoz hasonlóan előfordulhat, hogy az embernek nem kell elolvasnia a problémát.

  Ha a szervezet több Build-rendszerrel rendelkezik, a címke előállítása a létrehozási rendszer nevével a következő beállítás egyik változata: `<build-system>-<build-id>`. Megkülönböztetni például az API csapat Jenkins Build rendszerét és a webes csapat Azure-folyamatait.

## <a name="next-steps"></a>Következő lépések

Az ebben a cikkben szereplő fogalmak részletesebb ismertetését lásd [: Docker-címkék címkézése és a Docker-rendszerképek verziószámozására vonatkozó ajánlott eljárások](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/).

Az Azure Container Registry teljesítményének és költséghatékony használatának maximalizálása érdekében tekintse meg az [ajánlott eljárásokat a Azure Container Registryhoz](container-registry-best-practices.md).

<!-- IMAGES -->


<!-- LINKS - Internal -->


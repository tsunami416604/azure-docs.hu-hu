---
title: A címkék és a verziók képei a Azure Container Registry
description: Ajánlott eljárások a Docker-tároló rendszerképeinek címkézéséhez és verziószámozásához
services: container-registry
author: stevelasker
ms.service: container-registry
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: ea7c0831f4ecc345cbcd8a9b8eb6d6566e8c5023
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297769"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>A tároló lemezképének címkézésére és verziószámozására vonatkozó javaslatok

Amikor egy tároló-beállításjegyzékbe helyezi üzembe a tároló-lemezképek telepítését, majd telepíti őket, szüksége lesz egy, a képcímkézésre és a verziószámozásra vonatkozó stratégiára. Ebből a cikkből megtudhatja, hogyan illeszkedik a tároló életciklusa során a következő két módszerre:

* **Stabil címkék** – felhasználható címkék, például egy fő vagy alverzió (például *mycontainerimage: 1.0*) jelzése.
* **Egyedi címkék** – egy másik címkét minden olyan képhez, amelyet egy beállításjegyzékbe küld, például *mycontainerimage: abc123*.

## <a name="stable-tags"></a>Stabil Címkék

**Javaslat**: Használjon stabil címkéket a tárolók alapképeinek karbantartásához. Kerülje a stabil címkékkel való üzembe helyezést, mivel ezek a címkék továbbra is megkapják a frissítéseket, és következetlenségeket vezethetnek be az éles környezetekben.

A *stabil címkék* azt jelentik, hogy egy fejlesztő vagy egy összeállítási rendszer továbbra is lehívhat egy adott címkét, amely továbbra is frissítéseket kap. A stabil nem jelenti azt, hogy a tartalom zárolva van. Ehelyett a stabil a rendszerképnek stabilnak kell lennie az adott verzió céljához. A "stabil" maradni a biztonsági javítások vagy a keretrendszer frissítéseinek alkalmazásával lehet kiszolgálni.

### <a name="example"></a>Példa

A Framework Team-hajók 1,0-es verzióval rendelkeznek. Tudják, hogy frissítéseket küldenek, beleértve a kisebb frissítéseket is. Egy adott fő-és alverzióhoz tartozó stabil címkék támogatásához két stabil címkével rendelkeznek.

* `:1`– a főverzió stabil címkéje. `1`a "legújabb" vagy "legújabb" 1. * verziót jelöli.
* `:1.0`– az 1,0-es verzió stabil címkéje, amely lehetővé teszi a fejlesztők számára, hogy a 1,0-es frissítésekhez kapcsolódjanak, és ne legyenek továbbítva a 1,1-ra a kiadáskor.

A csapat a `:latest` címkét is használja, amely a legújabb stabil címkére mutat, függetlenül attól, hogy milyen a jelenlegi főverzió.

Ha az alapszintű lemezkép frissítései elérhetők, vagy a keretrendszer bármely típusú karbantartási kiadása, a stabil címkékkel rendelkező rendszerképek frissülnek a legújabb kivonatoló értékre, amely az adott verzió legújabb stabil kiadását jelöli.

Ebben az esetben a fő és a másodlagos címkéket is folyamatosan szervizeli a rendszer. Alaprendszerkép-forgatókönyv esetén ez lehetővé teszi, hogy a rendszerkép tulajdonosa kiszolgált lemezképeket biztosítson.

## <a name="unique-tags"></a>Egyedi címkék

**Javaslat**: Egyedi címkéket használhat a **központi telepítésekhez**, különösen olyan környezetben, amely több csomópontra is méretezhető. Valószínűleg az összetevők konzisztens verziójának szándékos telepítését szeretné használni. Ha a tároló újraindul, vagy egy Orchestrator több példányra van kibővítve, a gazdagépek nem fognak véletlenül újabb verziót lekérni, és nem konzisztensek a többi csomóponttal.

Az egyedi címkézés egyszerűen azt jelenti, hogy a beállításjegyzékbe leküldött összes rendszerkép egyedi címkével rendelkezik. A címkék nem lesznek újra felhasználva. Az egyedi címkék létrehozásához több minta is készíthető, többek között a következők:

* **Dátum-idő bélyegző** – ez a megközelítés meglehetősen gyakori, mivel a rendszerkép létrehozásakor egyértelműen megtudhatja, hogy mikor készült. De hogyan lehet összekapcsolni a Build-rendszerrel? Meg kell keresnie az adott időpontban befejezett buildet? Milyen időzóna van? Az összes Build rendszerét UTC-re kalibrálták?
* **Git** -véglegesítés – ez a megközelítés csak az alapszintű rendszerkép frissítéseinek támogatásának megkezdése után működik. Ha egy alaprendszerkép frissítése történik, a Build rendszer a korábbi buildtel megegyező git-véglegesítés után indul el. Az alaprendszerkép azonban új tartalommal rendelkezik. Általánosságban elmondható, hogy a git-véglegesítés egy *félig*stabil címkét biztosít.
* **Manifest Digest** – a tároló-beállításjegyzékbe leküldött összes tároló-rendszerkép egy jegyzékfájlhoz van társítva, amely egyedi SHA-256 kivonattal vagy kivonattal van azonosítva. Míg egyedi, a kivonat hosszú, nehezen olvasható és nem korrelál a Build-környezettel.
* **Build azonosítója** – ez a lehetőség lehet a legjobb megoldás, mivel ez valószínűleg növekményes, és lehetővé teszi, hogy az összes összetevő és napló megtalálásához visszakapcsolja az adott buildet. A manifest-kivonatokhoz hasonlóan előfordulhat, hogy az embernek nem kell elolvasnia a problémát.

  Ha a szervezet több Build-rendszerrel rendelkezik, a címke előállítása a létrehozási rendszer nevével a következő beállítás egyik `<build-system>-<build-id>`változata:. Megkülönböztetni például az API csapat Jenkins Build rendszerét és a webes csapat Azure-folyamatait.

## <a name="next-steps"></a>További lépések

Az ebben a cikkben szereplő fogalmak részletesebb ismertetését a Docker-címkézést követő [blogbejegyzésben találja: Ajánlott eljárások a Docker-rendszerképek](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/)címkézéséhez és verziószámozásához.

Az Azure Container Registry teljesítményének és költséghatékony használatának maximalizálása érdekében tekintse meg az [ajánlott eljárásokat a Azure Container Registryhoz](container-registry-best-practices.md).

<!-- IMAGES -->


<!-- LINKS - Internal -->


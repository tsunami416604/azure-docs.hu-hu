---
title: Konténerkép tárolása
description: A Docker-tárolórendszerképek Azure Container Registry tárolóban való tárolásának részletei, beleértve a biztonságot, a redundanciát és a kapacitást.
ms.topic: article
ms.date: 03/21/2018
ms.openlocfilehash: f66c3dd95edfe5035c46857cb6f9aa59d8a6a0e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456209"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Tárolórendszerkép-tárolás az Azure Container Registry szolgáltatásban

Minden [alapszintű, standard és prémium szintű](container-registry-skus.md) Azure-tároló beállításjegyzék előnyeit fejlett Azure storage-funkciók, mint a titkosítás-in-in a képadatok biztonsága és a geo-redundancia a képvédelem. A következő szakaszok ismertetik a funkciók és a lemezkép-tárolás korlátai az Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Inaktív titkosítás

A beállításjegyzékben lévő összes tárolórendszerkép titkosítva van. Az Azure automatikusan titkosítja a lemezképet, mielőtt tárolnák, és visszafejti azt menet közben, amikor Ön vagy az alkalmazások és szolgáltatások lekérik a lemezképet.

## <a name="geo-redundant-storage"></a>Georedundáns tárolás

Az Azure egy georedundáns tárolási sémát használ a tárolórendszerképek elvesztésének elleni védelemérdekében. Az Azure Container Registry automatikusan replikálja a tárolólemezképeket több földrajzilag távoli adatközpontok, megakadályozva azok elvesztését regionális tárolási hiba esetén.

## <a name="geo-replication"></a>Georeplikáció

A még több magas rendelkezésre állást igénylő biztosítást igénylő forgatókönyvek esetén fontolja meg a prémium szintű [beállításjegyzékek georeplikációs](container-registry-geo-replication.md) szolgáltatásának használatát. A georeplikáció segít megvédeni a rendszerleíró adatbázishoz való hozzáférés elvesztését *teljes* regionális hiba esetén, nem csak tárolási hiba esetén. A georeplikáció más előnyöket is biztosít, például a hálózatközeli lemezképek tárolását a gyorsabb leküldések és lekérések érdekében az elosztott fejlesztési vagy telepítési forgatókönyvekben.

## <a name="image-limits"></a>Képkorlátok

Az alábbi táblázat ismerteti a tároló rendszerkép és az Azure-tároló-jegyzékek érvényben lévő tárolási korlátokat.

| Erőforrás | Korlát |
| -------- | :---- |
| Adattárak | Nincs korlátozás |
| Képek | Nincs korlátozás |
| Rétegek | Nincs korlátozás |
| Címkék | Nincs korlátozás|
| Storage | 5 TB |

Nagyon nagy számú adattárak és címkék hatással lehet a teljesítményét a rendszerleíró adatbázis. Rendszeresen törölje a nem használt adattárakat, címkéket és képeket a rendszerleíró adatbázis karbantartási rutinrészeként. A törölt beállításjegyzék-erőforrások, például az adattárak, a képek és a címkék törlés után *nem* állíthatók helyre. A beállításjegyzék-erőforrások törléséről a [Tárolórendszerképek törlése az Azure Container Registry alkalmazásban című témakörben](container-registry-delete.md)talál további információt.

## <a name="storage-cost"></a>Tárolási költség

A díjszabással kapcsolatos részletekért tekintse meg az [Azure Container Registry díjszabását.][pricing]

## <a name="next-steps"></a>További lépések

A különböző Azure Container Registry skus (alapszintű, standard, prémium) az [Azure Container Registry ska-k](container-registry-skus.md)című témakörben talál további információt.

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->

---
title: Tároló rendszerképének tárolója
description: A Docker-tárolók lemezképeit Azure Container Registry tárolja, beleértve a biztonságot, a redundanciát és a kapacitást.
ms.topic: article
ms.date: 06/18/2020
ms.openlocfilehash: d51014e9e0769091aba42682cce3a6a01cfa19de
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85214060"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Tároló képtárolója Azure Container Registry

Minden [alapszintű, standard és prémium](container-registry-skus.md) szintű Azure Container Registry előnyt élvez az Azure Storage fejlett szolgáltatásait, például a titkosítást, a képadatok biztonságát és a Geo-redundanciát a képadatok védelme érdekében. A következő szakaszok a Azure Container Registry (ACR) képtára funkcióit és korlátait ismertetik.

## <a name="encryption-at-rest"></a>Titkosítás – Rest

A beállításjegyzékben lévő összes tároló lemezkép inaktív állapotban van. Az Azure automatikusan titkosítja a rendszerképet a tárolás előtt, és visszafejti azt menet közben, amikor az alkalmazások és szolgáltatások lekérik a rendszerképet. Igény szerint további titkosítási réteget is alkalmazhat az [ügyfél által felügyelt kulccsal](container-registry-customer-managed-keys.md).

## <a name="geo-redundant-storage"></a>Georedundáns tárolás

Az Azure egy geo-redundáns tárolási sémát használ a tároló-lemezképek elvesztése elleni védelemhez. A Azure Container Registry automatikusan replikálja a tároló lemezképeit több földrajzilag távoli adatközpontba, így megakadályozza az adatvesztést a regionális tárolási hibák esetén.

## <a name="geo-replication"></a>Georeplikáció

A még nagyobb rendelkezésre állást biztosító forgatókönyvek esetében érdemes lehet a prémium szintű kibocsátásiegység-forgalmi jegyzékek [geo-replikálás](container-registry-geo-replication.md) funkcióját használni. A Geo-replikáció a *teljes* regionális meghibásodás esetén megakadályozza a beállításjegyzékhez való hozzáférés elvesztését, nem csak a tárolási hibát. A Geo-replikáció más előnyöket is biztosít, például a hálózati bezárást, amely gyorsabb leküldést és lekérést tesz lehetővé elosztott fejlesztési vagy üzembe helyezési forgatókönyvekben.

## <a name="scalable-storage"></a>Skálázható tároló

Azure Container Registry lehetővé teszi, hogy annyi tárházat, képet, réteget vagy címkét hozzon létre, amennyire csak szüksége [van.](container-registry-skus.md#service-tier-features-and-limits) 

Nagyon nagy számú tárház és címke befolyásolhatja a beállításjegyzék teljesítményét. Rendszeresen törölje a nem használt adattárakat, címkéket és képeket a beállításjegyzék karbantartási rutinjának részeként, és opcionálisan állítsa be a címkézetlen jegyzékfájlok [megőrzési szabályát](container-registry-retention-policy.md) . A törölt beállításjegyzék-erőforrások, például adattárak, lemezképek és címkék törlés után *nem* állíthatók vissza. A beállításjegyzék-erőforrások törlésével kapcsolatos további információkért lásd: [tároló lemezképek törlése a Azure Container Registryban](container-registry-delete.md).

## <a name="storage-cost"></a>Tárolási költség

A díjszabással kapcsolatos részletes információkért tekintse meg a [Azure Container Registry díjszabását][pricing].

## <a name="next-steps"></a>További lépések

Az alapszintű, a standard és a Premium Container-jegyzékekkel kapcsolatos további információkért lásd: [Azure Container Registry szolgáltatási szintek](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->

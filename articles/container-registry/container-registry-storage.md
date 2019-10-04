---
title: Rendszerkép-tároló Azure Container Registry
description: A Docker-tárolók lemezképeit Azure Container Registry tárolja, beleértve a biztonságot, a redundanciát és a kapacitást.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/21/2018
ms.author: danlep
ms.openlocfilehash: 4517cc21ca0087358e750cd480288d4ec3718791
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310537"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Tároló képtárolója Azure Container Registry

Minden [alapszintű, standard és prémium](container-registry-skus.md) szintű Azure Container Registry előnyt élvez az Azure Storage fejlett szolgáltatásait, például a titkosítást, a képadatok biztonságát és a Geo-redundanciát a képadatok védelme érdekében. A következő szakaszok a Azure Container Registry (ACR) képtára funkcióit és korlátait ismertetik.

## <a name="encryption-at-rest"></a>Titkosítás – Rest

A beállításjegyzékben lévő összes tároló lemezkép inaktív állapotban van. Az Azure automatikusan titkosítja a rendszerképet a tárolás előtt, és visszafejti azt menet közben, amikor az alkalmazások és szolgáltatások lekérik a rendszerképet.

## <a name="geo-redundant-storage"></a>Georedundáns tárolás

Az Azure egy geo-redundáns tárolási sémát használ a tároló-lemezképek elvesztése elleni védelemhez. A Azure Container Registry automatikusan replikálja a tároló lemezképeit több földrajzilag távoli adatközpontba, így megakadályozza az adatvesztést a regionális tárolási hibák esetén.

## <a name="geo-replication"></a>Georeplikáció

A még nagyobb rendelkezésre állást biztosító forgatókönyvek esetében érdemes lehet a prémium szintű kibocsátásiegység-forgalmi jegyzékek [geo-replikálás](container-registry-geo-replication.md) funkcióját használni. A Geo-replikáció a *teljes* regionális meghibásodás esetén megakadályozza a beállításjegyzékhez való hozzáférés elvesztését, nem csak a tárolási hibát. A Geo-replikáció más előnyöket is biztosít, például a hálózati bezárást, amely gyorsabb leküldést és lekérést tesz lehetővé elosztott fejlesztési vagy üzembe helyezési forgatókönyvekben.

## <a name="image-limits"></a>Képkorlátok

Az alábbi táblázat az Azure Container-nyilvántartók számára elérhető tároló-rendszerképet és tárolási korlátokat ismerteti.

| Resource | Korlát |
| -------- | :---- |
| Adattárak | Korlátlan |
| Képek | Korlátlan |
| Rétegek | Korlátlan |
| Tags | Korlátlan|
| Storage | 5 TB |

Nagyon nagy számú tárház és címke befolyásolhatja a beállításjegyzék teljesítményét. Rendszeresen törölje a nem használt adattárakat, címkéket és képeket a beállításjegyzék karbantartási rutinjának részeként. A törölt beállításjegyzék-erőforrások, például a Tárházak, a képek és a címkék törlés után *nem* állíthatók vissza. A beállításjegyzék-erőforrások törlésével kapcsolatos további információkért lásd: [tároló lemezképek törlése a Azure Container Registryban](container-registry-delete.md).

## <a name="storage-cost"></a>Tárhely költsége

A díjszabással kapcsolatos részletes információkért tekintse meg a [Azure Container Registry díjszabását][pricing].

## <a name="next-steps"></a>További lépések

További információ a különböző Azure Container Registry SKU-ról (alapszintű, standard, prémium): [Azure Container Registry SKU](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->

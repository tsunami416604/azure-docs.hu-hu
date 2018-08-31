---
title: Az Azure Container Registry képtárolás
description: Az Azure Container Registry, beleértve a biztonsági, redundanciát és kapacitást a Docker-tárolórendszerképekhez módjára részleteiért.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/21/2018
ms.author: marsma
ms.openlocfilehash: 65ff60be992440c69e50a084b467a8efbb19574e
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307149"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Az Azure Container Registry tároló képtárolás

Minden [alapszintű, Standard és prémium szintű](container-registry-skus.md) speciális az Azure storage szolgáltatások az Azure container registry előnyeit, például titkosítás inaktív a rendszerkép által nyújtott Adatbiztonság és a georedundancia rendszerkép adatainak védelme. A következő szakaszok ismertetik a funkciók és a lemezképet tároló az Azure Container Registry (ACR) határain.

## <a name="encryption-at-rest"></a>Titkosítás inaktív

A regisztrációs adatbázis összes tárolórendszerkép titkosítása inaktív. Az Azure automatikusan titkosítja a képet a tárolás előtt, és visszafejti a működés közbeni során, vagy az alkalmazások és szolgáltatások a rendszerkép lekérése.

## <a name="geo-redundant-storage"></a>Georedundáns tárolás

Az Azure georedundáns tárolási séma a tárolólemezképek adatvesztés ellen védő használ. Az Azure Container Registry automatikusan replikálja a tárolórendszerképek több földrajzilag távoli adatközpontban, az adatveszteség megakadályozása regionális tárolási hiba esetén.

## <a name="geo-replication"></a>Georeplikáció

Forgatókönyvek esetén még több magas rendelkezésre állás biztosítása, hogy fontolja meg a [georeplikációs](container-registry-geo-replication.md) prémium szintű beállításjegyzékek funkcióját. Georeplikáció segít védekezni ellen, a beállításjegyzékbe való hozzáférés elvesztése egy *teljes* a regionális meghibásodással, nem csak tárolási hiba. Georeplikáció egyéb előnyöket biztosít, túl, hálózatközeli képhez hasonlóan a storage gyorsabban leküldéses értesítések, és lekéri az elosztott fejlesztéssel vagy üzembe helyezési forgatókönyvekben.

## <a name="image-limits"></a>Lemezkép-korlátok

A következő táblázat ismerteti a tároló kép- és tárterületi korlátozásai helyen az Azure container registryk.

| Erőforrás | Korlát |
| -------- | :---- |
| Adattárak | Korlátlan |
| Képek | Korlátlan |
| Rétegek | Korlátlan |
| Címkék | Korlátlan|
| Storage | 5 TB |

Nagyon nagy mennyiségű adattárak és címkék hatással lehet a beállításjegyzék teljesítményét. Rendszeres időközönként törölje a fel nem használt adattárak, a címkék és a képek a beállításjegyzék karbantartási gyakorlatának. Beállításjegyzék erőforrásokhoz, mint az adattárak, rendszerképek és címkék törlése *nem* törlése után lehet helyreállítani. Registry erőforrás törlésével kapcsolatos további információkért lásd: [törlése az Azure Container Registry a tárolólemezképek](container-registry-delete.md).

## <a name="storage-cost"></a>Tárhely költsége

További információ a díjszabásról teljes körű információkért lásd: [Azure Container Registry díjszabás][pricing].

## <a name="next-steps"></a>További lépések

A különböző Azure Container Registry Termékváltozatai (alapszintű, Standard, prémium szintű) kapcsolatos további információkért lásd: [Azure Container Registry Termékváltozatai](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: http://aka.ms/acr/pricing

<!-- LINKS - Internal -->

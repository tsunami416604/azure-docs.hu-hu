---
title: Lemezkép tárolási Azure tároló beállításjegyzék
description: A Docker-tároló képek tárolási módját a Azure tároló beállításjegyzék, beleértve a biztonsági, redundanciát és kapacitást az adatokat.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/21/2018
ms.author: marsma
ms.openlocfilehash: 92e60b4213cb80d193a7c35f68b8f9fd099481d7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32165095"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Tároló lemezkép tárolási Azure tároló beállításjegyzék

Minden [Basic, Standard és Premium](container-registry-skus.md) Azure tároló beállításjegyzék számos előnyt biztosít az Azure fejlett tárolási funkciókat, például titkosítási nyugalmi kép adatok biztonsági és adatvédelmi kép georedundancia. A következő szakaszok ismertetik a szolgáltatások és a lemezkép tárolási Azure tároló beállításjegyzék (ACR) határain.

## <a name="encryption-at-rest"></a>Titkosítási nyugalmi

A beállításjegyzékben összes tároló-lemezkép titkosított aktívan. Azure automatikusan titkosítja a lemezkép előtt tárolja, és visszafejti a azonnali során, vagy az alkalmazások és szolgáltatások le a lemezképet.

## <a name="geo-redundant-storage"></a>Georedundáns tárolás

Azure egy georedundáns tárolás sémát használja, amelyek védelmet biztosítanak a tároló képek adatvesztés. Azure tároló beállításjegyzék automatikusan replikálja a tároló-lemezképek több földrajzilag távoli adatközpontok, akadályozza meg, hogy az adatvesztés esetén a regionális hiba.

## <a name="geo-replication"></a>Georeplikáció

Forgatókönyvek igénylő még több magas rendelkezésre állás biztosítása, érdemes lehet a [georeplikáció](container-registry-geo-replication.md) prémium nyilvántartó szolgáltatása. A georeplikáció segít védelmet biztosítanak a beállításjegyzéket, hogy megszűnik egy *teljes* regionális hibája, nem csak a hiba. A georeplikáció más előnyöket nyújtja, túl, például a hálózati zárja be kép a gyorsabb leküldéses értesítések és lekéri a elosztott fejlesztési vagy a központi telepítési forgatókönyvek.

## <a name="image-limits"></a>Kép korlátok

A következő táblázat ismerteti a tároló kép- és tárterületi korlátozásai Azure tároló nyilvántartó biztosítani.

| Erőforrás | Korlát |
| -------- | :---- |
| Adattárak | Korlátlan |
| Képek | Korlátlan |
| Rétegek | Korlátlan |
| Címkék | Korlátlan|
| Tárolás | 5 TB |

Nagyon nagy mennyiségű adattárak és címkék hatással lehet a beállításjegyzék teljesítményére. Rendszeresen törölje a nem használt tárházak találhatók, a címkéket és a képeket használatával a [Azure CLI](/cli/azure/acr), az ACR [REST API](/rest/api/containerregistry/), vagy a [Azure-portálon] [ portal] , a beállításjegyzék karbantartást rutin részét. Beállításjegyzék-erőforrásokhoz, mint a tárházak találhatók, lemezképek és címkék törlése *nem* törlése után lehet helyreállítani.

## <a name="storage-cost"></a>Tárolási költségek

Teljes árképzési, lásd: [Azure tároló beállításjegyzék árképzési][pricing].

## <a name="next-steps"></a>További lépések

A különböző Azure tároló beállításjegyzék termékváltozatok (Basic, Standard, Premium) kapcsolatos további információkért lásd: [Azure tároló beállításjegyzék termékváltozatok](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: http://aka.ms/acr/pricing

<!-- LINKS - Internal -->

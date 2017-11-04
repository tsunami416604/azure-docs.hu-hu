---
title: "Ajánlott eljárások Azure tároló beállításjegyzék"
description: "Útmutató az Azure-tárolót beállításjegyzék hatékonyan használja az alábbi gyakorlati tanácsok követve."
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-registry
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 9ec5573082dbf9de1288e1511f5041f8c20b416e
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="best-practices-for-azure-container-registry"></a>Ajánlott eljárások az Azure-tároló beállításjegyzék

Az alábbi gyakorlati tanácsok követve segítségével maximalizálhatja a teljesítményt és költséghatékony a személyes Docker beállításjegyzék az Azure-ban.

## <a name="network-close-deployment"></a>Hálózati bezárású központi telepítés

Hozza létre a tároló beállításkulcs tárolók üzembe helyezésének azonos Azure-régióban. A beállításjegyzék elhelyezése egy területet, amely hálózati zárja be a tárolóba gazdagépek segítségével késés és a költségek csökkentése.

Hálózati bezárású telepítési egyike egy tároló titkos beállításjegyzékkel elsődleges okait. Docker képeknek nagyszerű [réteges szerkezet](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) , amely lehetővé teszi a növekményes telepítésekhez. Azonban új csomópontok kell lekéréses minden megadott lemezképhez szükséges rétegét lefedi. A kezdeti `docker pull` adhat gyorsan hozzá akár több GB. Rendelkezik egy titkos beállításjegyzék megközelíti a központi telepítés minimálisra csökkenti a hálózati késés korlátozza.
Minden nyilvános felhők, Azure tartalmazza, továbbá hálózati kilépő díjak valósítja meg. Húzza a másik egy datacenter képek hozzáadja a hálózati kilépő díjakat, a késés mellett.

## <a name="geo-replicate-multi-region-deployments"></a>Földrajzi-replikálás több területi központi telepítések

Azure tároló beállításjegyzékben [georeplikáció](container-registry-geo-replication.md) tárolók telepítése esetén több régióba a szolgáltatást. Globális-ügyfelek helyi adatközpontok még kiszolgáló vagy a fejlesztői csapat különböző helyeken, hogy egyszerűbbé teheti a beállításjegyzék kezelését, és késés csökkentése érdekében érdemes földrajzi replikálja a beállításjegyzékben. Jelenleg előzetes, ez a szolgáltatás esetén érhető el [prémium](container-registry-skus.md#premium) nyilvántartó.

Georeplikálási használja, tekintse meg az három részből, hogyan [Azure tároló beállításjegyzék georeplikáció](container-registry-tutorial-prepare-registry.md).

## <a name="repository-namespaces"></a>Tárház-névterek

Tárház névterek használatával engedélyezze a szervezeten belül több csoporttal egyetlen beállításjegyzékbeli megosztása. Nyilvántartó telepítések és a csoportok között megosztható legyen. Azure tároló beállításjegyzék támogatja a beágyazott névterek, csoport-elszigetelési funkciójának engedélyezésével.

Vegyük példaként a következő tároló kép címkék. Vállalati szintű, például a használt képek `aspnetcore`, amíg tároló képek tulajdonában a termelési és a Marketing csoport minden egyes használja saját névterek a gyökérnévtér helyezi.

```
contoso.azurecr.io/aspnetcore:2.0
contoso.azurecr.io/products/widget/web:1
contoso.azurecr.io/products/bettermousetrap/refundapi:12.3
contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42
```

## <a name="dedicated-resource-group"></a>Dedikált erőforráscsoport

Mivel tároló nyilvántartó több tároló gazdagép használt erőforrások, a beállításjegyzék saját erőforráscsoportban kell tárolni.

Bár előfordulhat, hogy kísérletezik azzal, hogy egy adott állomásra típus, például az Azure-tároló példányok, valószínűleg érdemes törölje a tároló-példányt, amikor elkészült. Azonban érdemes is tartsa meg az Azure-tároló beállításjegyzék leküldött képek gyűjteményét. Úgy, hogy a beállításjegyzék saját erőforráscsoportban, minimálisra csökkenthetők a járulékos véletlenül törli a beállításjegyzékben a képek gyűjteményét, a tároló példány erőforrás csoportjának törlésekor.

## <a name="authentication"></a>Authentication

Egy Azure-tárolót beállításjegyzék hitelesítésekor vannak-e két elsődleges forgatókönyv: egyéni és szolgáltatási (vagy "távfelügyeleti") hitelesítést. A következő táblázat forgatókönyvekben rövid áttekintését, és a javasolt az egyes hitelesítési módszer.

| Típus | Példa | Ajánlott módszer |
|---|---|---|
| Egyedi azonosító | Egy fejlesztő, húzza a képet, hogy vagy a fejlesztői gépen képek terjesztése. | [az acr bejelentkezés](/cli/azure/acr?view=azure-cli-latest#az_acr_login) |
| Távfelügyeleti/identitás | Buildelés és üzembe helyezés folyamatok, amikor a felhasználó közvetlenül nem érintettek. | [Egyszerű szolgáltatásnév](container-registry-authentication.md#service-principal) |

Azure-tároló beállításjegyzék hitelesítéssel kapcsolatos részletes információkért lásd: [hitelesítés az Azure-tárolót rendszerleíró](container-registry-authentication.md).

## <a name="next-steps"></a>Következő lépések

Az Azure tároló beállításjegyzék több rétegek, SKU, minden más szolgáltatásokat nyújtanak nevű érhető el. Az elérhető termékváltozatok a részletekért lásd: [Azure tároló beállításjegyzék termékváltozatok](container-registry-skus.md).
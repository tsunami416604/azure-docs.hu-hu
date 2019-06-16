---
title: A mobilitási szolgáltatás adatstruktúrákat az Azure Maps |} A Microsoft Docs
description: Az Azure Maps a mobilitási szolgáltatás adatstruktúrák
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 29e8a9d7555ca836b6266879f3b3c1e32ffd3980
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735556"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Az Azure Maps a mobilitási szolgáltatás adatstruktúrák

Ez a cikk bemutatja a Metro területet [Azure Maps a mobilitási szolgáltatás](https://aka.ms/AzureMapsMobilityService) és néhány gyakori mezőt ad vissza, a szolgáltatások, ha vannak lekérdezve az nyilvános átvitel leállítja és vonalak. Javasoljuk, hogy ez a cikk keresztül a mobilitási szolgáltatás API-kkal megkezdése előtt. Ezek az alábbi általános mezők tárgyaljuk.

## <a name="metro-area"></a>Metro terület

A mobilitási szolgáltatás adatainak támogatott metro területek van felosztva. Metro területek nem követik a város határokat, metro terület tartalmazhat több város, például sűrűn lakott város és a feldolgozóiszerepkör városok; és a egy adott ország/régió egyetlen metro területen lehet. 

A `metroID` metro terület azonosítója, amely hívja használhatók a [első Metro terület adatait API](https://aka.ms/AzureMapsMobilityMetroAreaInfo) támogatott kérések átvitel típusa és a további részleteket a metro terület, például az átviteli szervek és aktív riasztások. Az Azure Maps első Metro API segítségével a támogatott metro területek és metroIDs igényelhetnek. Metro terület azonosítók változhatnak.

**metroID:** 522 **neve:** Seattle-Tacoma-Bellevue

![Seattle-metro-area](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Állítsa le az azonosítók

Átvitel leáll azonosítóval, kétféle szerint lehet hivatkozni a [általános átvitel hírcsatorna-specifikáció (GFTS)](https://gtfs.org/) (néven stopKey) Azonosítóját és az Azure Maps leállítása (néven stopId) azonosítója. Idővel leáll kontextusban való megnevezésekor, ajánlott az Azure Maps stop-azonosító, használja ezt az Azonosítót több stabil és nem valószínű változik, amennyiben a fizikai stop létezik. A GTFS stop azonosító frissül gyakrabban, például abban az esetben a GTFS szolgáltatót kell módosítani, vagy új GTFS verziója, bár a fizikai leállítása nem változik.

Szeretné elindítani, kérheti a közeli átvitel közben leállítja a [közeli átvitel API első](https://aka.ms/AzureMapsMobilityNearbyTransit).

## <a name="line-groups-and-lines"></a>Sor csoportok és az olvasandó sorok

A mobilitási szolgáltatás egy párhuzamos modellt használ a sorok és a változások kezelésére jobban sor csoportok csoporttól [GTFS](https://gtfs.org/) útvonalakat, és lelassítja az adatmodellbe.


### <a name="line-groups"></a>Sor csoportok

Egy csoport egy entitás, amely csoportosítja együtt, amelyek logikailag ugyanabba a csoportba tartozó összes sor. Általában egy sor csoportot fogja tartalmazni, két sort, b, pont egy folyamatban lévő és az egyéb pont b visszatér A, mind az azonos nyilvános átviteli Ügynökség, tartozó és ugyanannyi sort kellene. Azonban előfordulhatnak olyan esetek, amelyben egy sor csoportot már több mint két sort vagy a benne lévő csak egyetlen sor.


### <a name="lines"></a>sorok

Mint már említettük, minden egyes sorokból áll. Gyakran az egyes sorok ismerteti egy irányát, valamint az egyes sorok csoport tevődik össze két sort. Vannak azonban olyan esetekben, mely további sorokat tartalmazza egy sor csoportot, például van egy-egy vonal néha elkerülő megoldásokhoz egy bizonyos hálózatok keresztül, és néha nem létezik, és mindkét esetben sor azonos számú üzemeltetik és más esetekben, amelyben egy sor g virtuális csoportosítás egy egysoros, például egy egyetlen irányú. kör alakú vonal tevődik össze.

Első lépésként kérheti sor csoportok használatával a [első átvitel sor API](https://aka.ms/AzureMapsMobilityTransitLine) és újabb verziók válassza a sorokat.


## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan igényelhető az átvitt adatok a mobilitási szolgáltatás használatával:

> [!div class="nextstepaction"]
> [Hogyan kérhetnek az átvitt adatok](how-to-request-transit-data.md)

Ismerje meg, hogyan kérhetnek a mobilitási szolgáltatás használatával valós idejű adatokat:

> [!div class="nextstepaction"]
> [Hogyan kérhetnek a valós idejű adatok](how-to-request-real-time-data.md)

Ismerkedés az Azure Maps a mobilitási szolgáltatás API-dokumentáció

> [!div class="nextstepaction"]
> [A mobilitási szolgáltatás API-dokumentáció](https://aka.ms/AzureMapsMobilityService)

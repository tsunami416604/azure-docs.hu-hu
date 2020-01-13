---
title: Mobilitási szolgáltatás adatstruktúrája Azure Mapsban | Microsoft Azure térképek
description: Ebből a cikkből megismerheti a Microsoft Azure Maps mobilitási szolgáltatásaival visszaadott általános mezőket és adatstruktúrákat.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 213910ee2439fa958b9f1d4926883eb8e066ba41
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910727"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Adatstruktúrák a Azure Maps mobilitási szolgáltatásban

Ez a cikk bemutatja a Metro terület koncepcióját a [Azure Maps mobilitási szolgáltatásban](https://aka.ms/AzureMapsMobilityService) , valamint a szolgáltatásokon keresztül visszaadott általános mezőket, ha lekérdezi a nyilvános tranzitot és a sorokat. Javasoljuk, hogy a mobilitási szolgáltatás API-jai megkezdése előtt végezze el ezt a cikket. Az alábbi általános mezőket tárgyaljuk.

## <a name="metro-area"></a>Metro térség

A mobilitási szolgáltatáshoz tartozó Adatelosztás a támogatott metró területekre oszlik. A Metro-területek nem követik a város határait, a Metro terület több várost is tartalmazhat, például sűrűn kitöltött városokat és a környező városokat. egy ország/régió lehet egy metró terület is. 

A `metroID` egy Metro-területi azonosító, amellyel meghívhatja a [beolvasás Metro környék info API](https://aka.ms/AzureMapsMobilityMetroAreaInfo) -t a támogatott átviteli típusok és a metró, például az árutovábbítási ügynökségek és az aktív riasztások további részleteinek kéréséhez. A Azure Maps Get Metro API-val a támogatott Metro-területeket és metroIDs kérheti le. A Metro-területek azonosítói változhatnak.

**metroID:** 522 **neve:** Seattle-Tacoma-Bellevue

![Seattle – Metro-térség](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Azonosítók leállítása

A továbbítási leállítások két típusú azonosítóval, az [általános átviteli hírcsatorna specifikációjának (GFTS)](https://gtfs.org/) azonosítójának (stopKey) és a Azure Maps leállítás azonosítójának (ún. stopId) nevezik. Amikor a rendszer az idő múlásával késlelteti a leállást, javasolt a Azure Maps leállítás AZONOSÍTÓjának használata, mivel ez az azonosító stabilabb, és nem valószínű, hogy a fizikai leállítás nem változik. A GTFS leállítása azonosító gyakrabban frissül, például abban az esetben, ha a GTFS-szolgáltatónak módosítania kell vagy új GTFS-verziót szabadít fel, bár a fizikai leállítás nem változott.

Az indításhoz a közeli tranzit [API](https://aka.ms/AzureMapsMobilityNearbyTransit)-k használatával leállíthatja a lekéréses útvonalakat.

## <a name="line-groups-and-lines"></a>Sorok és sorok

A mobilitási szolgáltatás egy párhuzamos adatmodellt használ a vonalak és a sorok csoportjaihoz, hogy jobban kezeljék a [GTFS](https://gtfs.org/) útvonalak és a TRIPS adatmodell által örökölt változásokat.


### <a name="line-groups"></a>Sorcsoportok

A Sorcsoportok olyan entitások, amelyek az összes olyan sort csoportosítják, amelyek logikailag ugyanahhoz a csoporthoz tartoznak. Általában egy sor két sort fog tartalmazni, amelyek közül az "A" és a "B" pont lesz, a másik pedig a B pontról A-re tér vissza, és mindkettő ugyanahhoz a nyilvános szállítási ügynökhöz tartozik, és azonos sorszámmal rendelkezik. Előfordulhatnak azonban olyan esetek, amikor egy csoport kettőnél több sora van, vagy csak egyetlen sor van bennük.


### <a name="lines"></a>Vonalak

A fentiekben leírtak szerint az egyes sorok egy sor halmazból állnak. Az egyes sorok gyakran leírnak egy irányt, az egyes sorok pedig két sorból állnak. Vannak azonban olyan esetek, amikor több sor is tartalmaz egy sornyi csoportot, például egy olyan sort, amely néha egy bizonyos környéken halad át, és néha nem, és mindkét esetben ugyanabban a sorban van üzemeltetve, és vannak olyan esetek is, amikor a g. sorban a oport egyetlen sorból áll, például egy körkörös vonallal, egyetlen iránysal.

Első lépésként a [beolvasás vonal API](https://aka.ms/AzureMapsMobilityTransitLine) -val és a sorokra lebontott részletezéssel is kérheti a csoportok használatát.


## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan kérhet továbbítási információkat a mobilitási szolgáltatással:

> [!div class="nextstepaction"]
> [Adatátviteli adatkérés](how-to-request-transit-data.md)

Megtudhatja, hogyan kérhet valós idejű adatgyűjtést a mobilitási szolgáltatás használatával:

> [!div class="nextstepaction"]
> [Valós idejű adatkérések igénylése](how-to-request-real-time-data.md)

A Azure Maps mobilitási szolgáltatás API dokumentációjának megismerése

> [!div class="nextstepaction"]
> [A mobilitási szolgáltatás API-dokumentációja](https://aka.ms/AzureMapsMobilityService)

---
title: Mobilitási szolgáltatás adatstruktúrája Azure Mapsban | Microsoft Azure térképek
description: Ebből a cikkből megismerheti a Microsoft Azure Maps mobilitási szolgáltatásaival visszaadott általános mezőket és adatstruktúrákat.
author: farah-alyasari
ms.author: v-faalya
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 685810a6efa46c8eb3ad6cee0c2424299f0347d8
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209613"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Adatstruktúrák a Azure Maps mobilitási szolgáltatásban

Ez a cikk bemutatja a Metro terület koncepcióját [Azure Maps mobilitási szolgáltatásban](https://aka.ms/AzureMapsMobilityService). Megbeszéljük azokat a gyakori mezőket, amelyeket a rendszer akkor ad vissza, ha a szolgáltatás lekérdezi a nyilvános tranzitot és a sorokat. Javasoljuk, hogy a mobilitási szolgáltatás API-jai fejlesztése előtt olvassa el ezt a cikket.

## <a name="metro-area"></a>Metro térség

A mobilitási szolgáltatáshoz tartozó adatterületek támogatott Metro-területek szerint vannak csoportosítva. A Metro-területek nem követik a város határait. A Metro területén több város, sűrűn kitöltött város és Környező városok is szerepelhetnek. Valójában egy ország/régió lehet egy metró terület. 

A `metroID` egy olyan Metro-körzet azonosítója, amely felhasználható a [beolvasás Metro környék info API](https://aka.ms/AzureMapsMobilityMetroAreaInfo)meghívására. Használja a "Metro beolvasása" API-t, hogy átAzure Maps tekintse az átviteli típusokat, az átléptető irodákat, az aktív riasztásokat és a kiválasztott Metro további részleteit. A támogatott Metro-területeket és metroIDs is kérheti. A Metro-területek azonosítói változhatnak.

**metroID:** 522 **neve:** Seattle-Tacoma-Bellevue

![Seattle-metro-area](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Azonosítók leállítása

Az adatátviteli leállásokat két típusú azonosító, az [általános árutovábbítási hírcsatorna-specifikáció (GFTS)](https://gtfs.org/) azonosítója és a Azure Maps leállítása azonosító lehet. A GFTS-azonosító neve stopKey, és a Azure Maps leállítás AZONOSÍTÓját stopID-ként nevezik. Ha gyakran hivatkozik az átvitelre, javasoljuk, hogy használja a Azure Maps leállítás azonosítót. a stopID stabilabb és valószínűleg változatlan marad, amíg a fizikai leállítás megtörténik. A GTFS leállítása azonosító gyakrabban frissül. A GTFS leállítási azonosítója például frissíthető a GTFS-szolgáltatói kérelemben, vagy új GTFS-verzió kiadása esetén. Bár a fizikai leállítás nem változott, a GTFS leállítási azonosítója változhat.

Először is kérheti a közeli átvitelt a [közeli tranzit API](https://aka.ms/AzureMapsMobilityNearbyTransit)-k használatával.

## <a name="line-groups-and-lines"></a>Sorok és sorok

A mobilitási szolgáltatás egy párhuzamos adatmodellt használ a vonalak és a sorok csoportjaihoz. Ez a modell a [GTFS](https://gtfs.org/) -útvonalakból és az utazások adatainak öröklésére szolgáló változások jobb kezelésére szolgál.


### <a name="line-groups"></a>Sorcsoportok

A Sorcsoportok olyan entitások, amelyek az összes olyan sort csoportosítják, amelyek logikailag ugyanahhoz a csoporthoz tartoznak. Egy sor általában két sort tartalmaz, egyet az A és a B pont között, a másik pedig a B pontról A A-re tér vissza. Mindkét vonal ugyanahhoz a nyilvános szállítási ügynökhöz tartozik, és ugyanazt a sorszámot fogja tartalmazni. Előfordulhatnak azonban olyan esetek, amikor egy csoport kettőnél több sora van, vagy csak egyetlen sor van bennük.


### <a name="lines"></a>Sorok

A fentiekben leírtak szerint az egyes sorok egy sor halmazból állnak. Az egyes sorok csoportjai két sorból állnak, és mindegyik sor egy irányt ismertet.  Vannak azonban olyan esetek, amikor több sor alkot egy sornyi csoportot. Például van egy olyan sor, amely időnként egy adott szomszédságban található, és néha nem. Mindkét esetben ugyanazon a sorszámon működik. Emellett egy vonalas csoport is állhat egyetlen sorból. Az egyetlen irányú körkörös vonal egy olyan Ling-csoport, amely egy sorral rendelkezik.

Első lépésként a [beolvasás vonal API](https://aka.ms/AzureMapsMobilityTransitLine)-val kérheti a sorokra vonatkozó kérelmeket.


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

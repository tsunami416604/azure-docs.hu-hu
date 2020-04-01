---
title: A mobilitási szolgáltatás adatstruktúrái az Azure Mapsben| Microsoft Azure Maps
description: Ebben a cikkben megismerheti a Microsoft Azure Maps mobilitási szolgáltatásokon keresztül visszaadott gyakori mezőket és adatstruktúrákat.
author: philmea
ms.author: philmea
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4dfc6793bba473c4046863937baa292dde7bf421
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478705"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Adatstruktúrák az Azure Maps mobilitási szolgáltatásában

Ez a cikk bemutatja a Metro Terület fogalmát az [Azure Maps mobilitási szolgáltatásában.](https://aka.ms/AzureMapsMobilityService) Megbeszélünk néhány közös mezőt, amelyeket akkor ad vissza, amikor ezt a szolgáltatást lekérdezik a tömegközlekedési megállókhoz és vonalakhoz. Javasoljuk, hogy olvassa el ezt a cikket, mielőtt a Mobilitási szolgáltatás API-kkal fejlesztene.

## <a name="metro-area"></a>Metró terület

A Mobilitási Szolgáltatás adatai támogatott metróterületek szerint vannak csoportosítva. A metróterületek nem követik a város határokat. A metró területén tartalmazhat több városban, sűrűn lakott város, és a környező városokban. Tény, hogy egy ország / régió lehet egy metró terület. 

A `metroID` egy metró terület azonosítóját, hogy lehet használni, hogy hívja a [Get Metro Area Info API.](https://aka.ms/AzureMapsMobilityMetroAreaInfo) Az Azure Maps "Metro" API-jával kérheti a tranzittípusokat, a tranzitügynökségeket, az aktív riasztásokat és a kiválasztott metró további részleteit. A támogatott metróterületeket és metróazonosítókat is kérheti. A metróterület azonosítói változhatnak.

**metroID:** 522 **Név:** Seattle-Tacoma-Bellevue

![Seattle-metró-terület](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Azonosítók leállítása

A tranzitmegállókra kétféle azonosító hivatkozhat, az [általános tranzitcsatorna-specifikáció (GFTS)](http://gtfs.org/) azonosító és az Azure Maps stop azonosítója. A GFTS-azonosító t stopKey-nak nevezik, és az Azure Maps stop id-t stopid-nak nevezzük. Ha gyakran hivatkozik a tranzit-megállók, javasoljuk, hogy használja az Azure Maps stop id. a stopID stabilabb és valószínűleg ugyanaz marad mindaddig, amíg a fizikai megállás létezik. A GTFS stop id gyakrabban frissül. A GTFS stop id például frissíthető a GTFS-szolgáltató kérésére, vagy ha új GTFS-verzió jelenik meg. Bár a fizikai leállás nem változott, a GTFS stop id változhat.

A kezdéshez a közeli tranzitmegállókat a [Közeli tranzit API bekérése segítségével](https://aka.ms/AzureMapsMobilityNearbyTransit)kérheti.

## <a name="line-groups-and-lines"></a>Sorcsoportok és -vonalak

A Mobility Service párhuzamos adatmodellt használ a vonalakhoz és a vonalcsoportokhoz. Ez a modell a [GTFS-útvonalaktól](http://gtfs.org/) és az utazások adataitól örökölt módosítások jobb kezelésére szolgál.


### <a name="line-groups"></a>Sorcsoportok

A sorcsoport olyan entitás, amely csoportosítja az összes olyan sort, amely logikailag ugyanannak a csoportnak a része. Általában egy vonalcsoport két sort tartalmaz, az egyik az A pontból a B-be, a másik pedig a B pontból A pontba. Mindkét vonal ugyanahhoz a tömegközlekedési ügynökséghez tartozna, és ugyanaz a sorszám. Előfordulhatnak azonban olyan esetek, amikor egy sorcsoportkettőnél több sorral vagy csak egy sorral rendelkezik.


### <a name="lines"></a>Vonalak

A fent tárgyaltak szerint minden sorcsoport egy sorhalmazból áll. Minden sorcsoport két sorból áll, és minden sor egy irányt ír le.  Vannak azonban olyan esetek, amikor több sor alkot egy sorcsoportot. Például, van egy vonal, hogy néha kitérők egy bizonyos környéken, és néha nem. Mindkét esetben azonos sorszám alatt működik. Egyetlen sorból is állhat egy sor. Az egyirányú kör alakú vonal egy egyvonalas ling csoport.

Először is kérheti a vonalcsoportokat a [Tranzitvonal bekérése API használatával.](https://aka.ms/AzureMapsMobilityTransitLine)


## <a name="next-steps"></a>További lépések

További információ az átviteli adatok mobilszolgáltatóval történő igényléséhez:

> [!div class="nextstepaction"]
> [Az átutazási adatok kérése](how-to-request-transit-data.md)

Ismerje meg, hogyan kérhet valós idejű adatokat a Mobility Service használatával:

> [!div class="nextstepaction"]
> [Valós idejű adatok igénylése](how-to-request-real-time-data.md)

Az Azure Maps Mobility Service API dokumentációjának felfedezése

> [!div class="nextstepaction"]
> [A Mobility Service API dokumentációja](https://aka.ms/AzureMapsMobilityService)

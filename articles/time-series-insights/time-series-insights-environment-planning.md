---
title: "A skála Azure idő adatsorozat Insights környezet tervezése |} Microsoft Docs"
description: "Ez a cikk egy Azure idő adatsorozat Insights környezetben, például a tárolási kapacitás, az adatmegőrzés, érkező kapacitás, és a figyelést tervezése során kövesse a bevált gyakorlatokat ismerteti."
services: time-series-insights
ms.service: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 5fb158ba162dd199f419f9568de08a7a18c833dd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="plan-your-azure-time-series-insights-environment"></a>Az Azure idő adatsorozat Insights környezet megtervezése

Ez a cikk ismerteti a tervezze meg a várt érkező arányról és az adatmegőrzési követelmények alapján Azure idő adatsorozat Insights környezetéhez.

## <a name="best-practices"></a>Ajánlott eljárások

Idő adatsorozat Insights megkezdéséhez, célszerű Ha tudja, hogy mennyi adatot várt leküldéses perc is, hogy mennyi ideig kell tárolni az adatokat.  

Mindkét idő adatsorozat Insights termékváltozatok kapacitás és a megőrzési kapcsolatos további információk: [idő adatsorozat Insights árképzési](https://azure.microsoft.com/pricing/details/time-series-insights/).

Vegye figyelembe a következő attribútumok legjobb terv a hosszú távú sikert környezetet: 
- Tárkapacitás
- Adatmegőrzés időtartama
- Érkező kapacitás 

## <a name="understand-storage-capacity"></a>Tárolási kapacitás
Alapértelmezés szerint a idő adatsorozat Insights adatokat ellátta tárolókapacitást alapján (egységenként tárolási egység alkalommal összeg) és a bejövő adatok megőrzi.

## <a name="understand-data-retention"></a>Az adatmegőrzés ismertetése
Konfigurálhatja a idő adatsorozat Insights környezet **adatmegőrzési időtartam** beállítás, legfeljebb 400 napos adatmegőrzési engedélyezése.  Idő adatsorozat Insights szolgáltatásnak két módja van, egy, a környezet biztosításáért optimalizálja a legfrissebb adatokat tartalmaz (az alapértelmezés szerint), és egy másik, ami optimalizálja a korlátok teljesülnek, ahol érkező szünetel, ha a megőrzési biztosításáért az általános tárolási kapacitását a Kattintson az környezetben.  Módosíthatja a megőrzési és a környezet konfigurálása lapon az Azure-portálon a két mód közötti váltás.

Legfeljebb 400 napig az adatok megőrzési idő adatsorozat Insights környezetében konfigurálhatja.

## <a name="configure-data-retention"></a>Az adatmegőrzés konfigurálása

1. Az a [Azure-portálon](https://portal.azure.com), válassza ki azt az időt adatsorozat Insights környezetet.

2. A a **idő adatsorozat Insights környezet lap**a a **beállítások** elemcsoportban válasszon **konfigurálása**. 

3. Az a **adatok megőrzési ideje (nap)** mezőben adjon meg egy értéket 1 400.

   ![Megőrzési konfigurálása](media/environment-mitigate-latency/configure-retention.png)

## <a name="understand-ingress-capacity"></a>Érkező kapacitás

Az egyéb területen tervezési összpontosítani érkező kapacitás, amely leszármazottja, a perc foglalási. 

A sávszélesség-szabályozási szempontjából egy 32 KB-os csomag méretű ingressed adatcsomag 32 eseményeket a rendszer, egyes 1 KB méretű. A maximális engedélyezett eseményről mérete 32 KB; csak adatcsomagok 32 KB-nál nagyobb.

A következő táblázat összefoglalja a érkező kapacitás minden termékváltozat:

|SKU  |Események száma havonta, egységenként  |Automatikus oszlopszélesség egy hónap, egység / események  |Események száma percenként egységenként  | Percenként, egység mérete   |
|---------|---------|---------|---------|---------|
|S1     |   30 millió     |  30 GB     |  700    |  700 KB   |
|S2     |   300 millió    |   300 GB   | 7,000   | 7000 KB  |

Egyetlen környezetben 10 egységek S1 vagy S2 SKU kapacitása növelhető. Egy S2 S1 környezet vagy egy S1 S2 környezet nem telepíthetők át. 

Érkező kapacitás a teljes hónap alapon szükséges belépési elsőként azt kell meghatározni. A következő meghatározására, hogy mi a percalapú igények, ez ugyanis, ha sávszélesség-szabályozás és a késleltetés szerepet játszanak.

Ha már rendelkezik egy csúcs a 24 óránál tovább tartó adatok érkező, idő adatsorozat Insights "olvasottként" a fent felsorolt díjszabás x 2 érkező ütemben is. 

Például ha van egy egyetlen S1 SKU és érkező adatok egy percet, és csúcs 700 események sebessége 1 400 esemény vagy kevesebb sebessége kisebb, mint 1 óra, nem lenne nincs észrevehető várakozási ideje a környezetben. Azonban ha 1 400 eseményt küld percenként több mint egy óráig túllépi volna valószínűleg tapasztal késési adatok feladatkonfigurációkat és lekérdezhetők a környezetben. 

Nem lehet felmérni előre mennyi adatot várt leküldéses. Ebben az esetben az adatok telemetriai található [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) és [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) az Azure-portálon. A telemetria segítségével meghatározhatja, hogy a környezet kiépítése. Használja a **metrikák** oldalon a megfelelő forrás a telemetriai adatok megtekintéséhez az Azure-portálon. Ha megismeri a esemény forrás metrikákat, hatékonyabb megtervezése és jogosultságok kiosztása a idő adatsorozat Insights környezet.

## <a name="calculate-ingress-requirements"></a>Érkező követelmények kiszámítása

- Ellenőrizze a perc átlagos túllépi ezt az értéket a bejövő adatok kapacitás, és, hogy elég nagy a kapacitás kisebb, mint 1 óra x 2 egyenértékű a várható érkező-e a környezetében.

- Érkező igényeiben jelentkező fordulhat elő, ha, amelyek utolsó több mint 1 óra csúcs sebessége használják az átlagos, és jogosultságok kiosztása a kapacitás a csúcs sebessége tartalmazó környezetben.
 
## <a name="mitigate-throttling-and-latency"></a>Sávszélesség-szabályozás és a késés csökkentése

Sávszélesség-szabályozás és a késés kapcsolatos információkért lásd: [csökkenthető a késleltetés és a sávszélesség-szabályozás](time-series-insights-environment-mitigate-latency.md). 

## <a name="next-steps"></a>Következő lépések
- [Az Event Hubs forrás hozzáadása](time-series-insights-how-to-add-an-event-source-eventhub.md)
- [Az IoT-központ forrás hozzáadása](time-series-insights-how-to-add-an-event-source-iothub.md)

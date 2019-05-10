---
title: A méretezési csoport az Azure Time Series Insights-környezet tervezése |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan ajánlott eljárások követése az Azure Time Series Insights környezetekben, beleértve a tárolási kapacitás, az adatmegőrzés, betöltési kapacitás, figyelés és üzleti vészhelyreállítási (bcdr funkciók) tervezése során.
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: bf1f570319370fab99e2f52086bc81df259e3d35
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236500"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Az Azure Time Series Insights általánosan elérhető környezet megtervezése

Ez a cikk ismerteti az Azure Time Series Insights-általánosan (elérhetővé tétel GA) a várt bejövő forgalom és az adatmegőrzési követelmények alapján környezet megtervezése.

## <a name="video"></a>Videó

### <a name="learn-more-about-data-retention-in-azuretime-series-insights-and-how-to-plan-for-itbr"></a>További információ az adatok megőrzése AzureTime Series Insights, és tervezze meg, hogyan.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Ajánlott eljárások

Ismerkedés a Time Series Insights, az a legcélszerűbb, ha tudja, hogy mennyi adatot tervez, hogy hogyan mennyi ideig kell tárolni az adatokat a percalapú leküldéses.  

Mindkét Time Series Insights termékváltozatok kapacitás és a megőrzési kapcsolatos további információkért lásd: [Time Series Insights díjszabása](https://azure.microsoft.com/pricing/details/time-series-insights/).

Vegye figyelembe a következő attribútumokat az igényeinek leginkább megfelelő csomagot a hosszú távú sikert környezetet:

- <a href="#understand-storage-capacity">Tárkapacitás</a>
- <a href="#understand-data-retention">Adatmegőrzés időtartama</a>
- <a href="#understand-ingress-capacity">Betöltési kapacitás</a>
- <a href="#shape-your-events">Az események alakításra.</a>
- <a href="#ensure-you-have-reference-data">Arról, hogy a referenciaadatok rendelkezik helyben</a>

## <a name="understand-storage-capacity"></a>Megismerheti a tárolási kapacitás

Alapértelmezés szerint az Azure Time Series Insights adatokat rendelkezik kiépített tárterület mérete alapján (egységek idő mennyisége tárhely / egység) a bejövő és kimenő őrzi meg.

## <a name="understand-data-retention"></a>Az adatmegőrzés ismertetése

Konfigurálhatja a Time Series Insights-környezet **adatmegőrzési idő** beállítás, akár 400 napos adatmegőrzés engedélyezése. A Time Series Insights szolgáltatásnak két módja van, a, amely optimalizálja a környezet biztosítása érdekében a lehető legfrissebb adatokat (az alapértelmezés szerint), és egy másikat, amely optimalizálja az, hogy az adatmegőrzési korlátok teljesülnek, ahol bejövő szünetel, ha a teljes tárolási kapacitásának a elérte a környezetben.  Módosíthatja a megőrzési és a környezet konfigurációs lapja az Azure Portalon a két mód közötti váltás.

A Time Series Insights környezetben legfeljebb 400 napos adatmegőrzés konfigurálhatja.

### <a name="configure-data-retention"></a>Az adatmegőrzés konfigurálása

1. Az a [az Azure portal](https://portal.azure.com), válassza ki a Time Series Insights-környezetet.

1. Az a **Time Series Insights-környezetek lap**alatt a **beállítások** szakaszban kattintson **konfigurálása**.

1. Az a **adatok megőrzési ideje (nap)** mezőben adjon meg egy értéket 1-től a 400-as.

   [![Megőrzés konfigurálása](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> További tudnivalók a megfelelő adatmegőrzési szabályzat megvalósítása áttekintésével [megőrzés konfigurálása](./time-series-insights-how-to-configure-retention.md).

## <a name="understand-ingress-capacity"></a>Betöltési kapacitás ismertetése

Az egyéb tervezési összpontosíthat területen betöltési kapacitás, amely leszármazottja, a percalapú foglalási.

A sávszélesség-szabályozási szempontjából egy ingressed adatok csomagot a csomag mérete 32 KB-os 32 események számít, minden egyes 1 KB méretű. Engedélyezett események maximális mérete 32 KB; 32 KB-nál nagyobb adatcsomagok csonkolva lesznek.

A következő táblázat összefoglalja a belépő kapacitás esetében egyes Termékváltozat:

|Termékváltozat  |Események száma / hó / egység  |Események mérete / hó / egység  |Események száma / perc / egység  | Méret / perc / egység   |
|---------|---------|---------|---------|---------|
|S1     |   30 millió     |  30 GB     |  720    |  720 KB   |
|S2     |   300 millió    |   300 GB   | 7,200   | A 7200 KB  |

Az S1 vagy S2 Termékváltozat kapacitása 10 egység egyetlen környezetben lehet növelni. S2 szintű, S1 környezet vagy egy S1, S2 környezet nem telepíthetők át.

Betöltési kapacitás, a havi alapon van szüksége a belépő elsőként azt kell meghatározni. Következő lépésként állapítsa meg, mi a percalapú igények kielégítéséhez, mivel ez a, ha szabályozás és a késés játszik szerepet.

Ha ugrásszerű a beérkező adatok 24 óránál rövidebb ideig tart, Time Series Insights "olvasottként", 2 x a fent felsorolt díjak a bejövő forgalom is.

Például ha egy egyetlen S1 Termékváltozat és a bejövő adatok 720 perc, és a megnövekedett eseményt gyakorisággal kevesebb mint 1 órája 1440 események vagy annál kisebb gyakorisággal, lenne a környezetben nincs észrevehető késés. Azonban Ha elfogynak 1440 események száma percenként több mint egy óráig, lenne valószínűleg tapasztal késést teszi és a lekérdezés a környezetben elérhető adatokat.

Előfordulhat, hogy nem tudja előre mennyi adatot tervez leküldéses. Ebben az esetben annak telemetriai adatok [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) és [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) az Azure Portalon. A telemetriai adatok segítségével meghatározhatja, hogyan építheti ki a környezet. Használja a **metrikák** oldal az Azure Portalon a megfelelő esemény-adatforráshoz a telemetriájának megtekintéséhez. Ha megismeri az esemény az adatforrás mérőszámainak, hatékonyabban tervezhet és a Time Series Insights-környezet kiépítése.

### <a name="calculate-ingress-requirements"></a>Belépő kiszámítására

- Győződjön meg arról, hogy a környezet áll elég nagy legyen a 2-a kapacitás, kisebb, mint 1 órája egyenértékű várható bejövő forgalom kezelésére, és a belépő kapacitás fent az átlagos percalapú díját.

- Bejövő adatforgalmi csúcsokhoz fordulhat elő, ha, amely utoljára több mint 1 óra, használja a megnövekedett forgalom az átlag, és kiépítése a környezet kapacitása a megnövekedett forgalom kezelésére.

### <a name="mitigate-throttling-and-latency"></a>Szabályozás és a késés csökkentése

Szabályozás és a késés kiküszöbölése kapcsolatos információkért olvassa el hogyan [csökkentheti a késést és a sávszélesség-szabályozási](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Az események formázása

Fontos, hogy ellenőrizze a TSI eseményeket küldeni módon támogatja szeretne kiépíteni a környezet méretétől (ezzel szemben, leképezheti a hány eseményt TSI olvassa be a környezetet, és minden egyes esemény méretét). Hasonlóan fontos gondolja át az attribútumokat, érdemes lehet szeletelni és szerinti szűrést, amikor az adatok lekérdezése.

> [!TIP]
> Tekintse át a JSON-dokumentáció alakításra [az események](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).

## <a name="ensure-you-have-reference-data"></a>Ellenőrizze, hogy a referenciaadatok

A **referencia-adatkészlet** kiolvassa az eseményeket az eseményforrás bővítésével elemek gyűjteménye. Time Series Insights bejövő forgalmat kezelő motorja a referencia-adatkészlet minden eseményt az eseményforrás a megfelelő adatok sor csatlakozik. Ez a kibővített esemény ezután lekérdezhető. A csatolás a referencia-adatkészlet meghatározott elsődleges kulcs oszlopoknak alapul.

Vegye figyelembe, hogy a referenciaadatok visszamenőlegesen nem csatlakozik tartományhoz. Ez azt jelenti, hogy csak a jelenlegi és jövőbeli bejövő adatainak matched és referencia meghatározott időpontban csatlakozik, konfigurálása és feltöltése után.  Ha azt tervezi, nagy mennyiségű előzményadatok küldeni a TSI és ne töltse fel, vagy hozzon létre referenciaadatok a TSI-ben először, akkor előfordulhat, hogy újra ezt a munkát (mutatót, nem szórakozást).  

Hozzon létre, töltse fel, és a TSI-ben a referenciaadatok kezelése kapcsolatos további tudnivalókért lépjen a [adatkészlet Referenciadokumentációt](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>További lépések

- Első lépésként hozza létre [egy új Time Series Insights-környezet az Azure Portalon](time-series-insights-get-started.md).

- Ismerje meg, hogyan [Event Hub-eseményforrás hozzáadása](time-series-insights-how-to-add-an-event-source-eventhub.md) a Time Series Insights.

- Megtudhatja, hogyan lehet a [konfigurálása az IoT Hub-eseményforrás](time-series-insights-how-to-add-an-event-source-iothub.md).

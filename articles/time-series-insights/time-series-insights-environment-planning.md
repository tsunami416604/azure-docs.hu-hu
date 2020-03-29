---
title: Tervezze meg ga környezetét - Azure Time Series Insights | Microsoft dokumentumok
description: Az Azure Time Series Insights GA-környezet előkészítésére, konfigurálására és üzembe helyezésére vonatkozó gyakorlati tanácsok.
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 972bb2a804057037deedb448674abafcc175b21f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314810"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Tervezze meg az Azure Time Series Insights GA-környezetét

Ez a cikk ismerteti, hogyan tervezze meg az Azure Time Series Insights általános rendelkezésre állási (GA) környezet a várható be-és adatmegőrzési követelmények alapján.

## <a name="video"></a>Videó

**Ebből a videóból többet is megtudhat az Azure Time Series Insights ban való adatmegőrzésről és a tervezésről:**<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Ajánlott eljárások

Az Azure Time Series Insights használatával való ismerkedés hez a legjobb, ha tudja, hogy mennyi adatot szeretne leadni percig, és mennyi ideig kell tárolnia az adatokat.  

A Time Series Insights számos másodszereplőjének kapacitásáról és megőrzéséről a [Time Series Insights díjszabása](https://azure.microsoft.com/pricing/details/time-series-insights/)című olvasni tudó további információt.

A Time Series Insights-környezet hosszú távú sikerének legjobb megtervezéséhez vegye figyelembe az alábbi attribútumokat:

- [Tárolókapacitás](#storage-capacity)
- [Adatmegőrzés időtartama](#data-retention)
- [A be- és nagykapacitás](#ingress-capacity)
- [Az események alakítása](#shape-your-events)
- [Annak biztosítása, hogy a referenciaadatok a helyükön legyenek](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>Tárkapacitás

Alapértelmezés szerint a Time Series Insights megőrzi az adatokat a kiépített tárterület (egység &#215; egységegység egységegység egység) és a be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és

## <a name="data-retention"></a>Adatmegőrzés

Módosíthatja az **adatmegőrzési idő** beállítást az Azure Time Series Insights környezetben. Legfeljebb 400 napos megőrzést engedélyezhet. 

Az Azure Time Series Insights két móddal rendelkezik:

* Az egyik mód a legfrissebb adatokra optimalizál. Kényszeríti a **házirendet a régi adatok törlése** így a legutóbbi adatok elérhetővé a példány. Ez a mód alapértelmezés szerint be van kapcsolva. 
* A másik úgy optimalizálja az adatokat, hogy a konfigurált adatmegőrzési korlátok alatt maradjanak. **A be- és visszalépések** szüneteltetése megakadályozza az új adatok be- és belépését, ha a tárolási korlát túllépte a **működést.**

Módosíthatja a megőrzési és a két mód közötti váltást a környezet konfigurációs lapján az Azure Portalon.

> [!IMPORTANT]
> Az Azure Time Series Insights GA-környezetben legfeljebb 400 napos adatmegőrzést konfigurálhat.

### <a name="configure-data-retention"></a>Az adatmegőrzés konfigurálása

1. Az [Azure Portalon](https://portal.azure.com)válassza ki a Time Series Insights-környezetben.

1. A **Time Series Insights környezet** ablaktábláján a **Beállítások**csoportban válassza a Storage **configuration (Tároló konfigurációja)** lehetőséget.

1. Az **Adatmegőrzési idő (napokban)** mezőbe írjon be egy 1 és 400 közötti értéket.

   [![Megőrzés konfigurálása](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> Ha többet szeretne tudni a megfelelő adatmegőrzési házirend megvalósításáról, olvassa [el az Adatmegőrzés konfigurálása című cikket.](./time-series-insights-how-to-configure-retention.md)

## <a name="ingress-capacity"></a>A be- és nagykapacitás

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>Környezettervezés

A második terület, amely a Time Series Insights-környezet tervezésére összpontosít, a be- és telepítési kapacitás. A be- ésnagykapacitás a percenkénti allokáció származéka.

A szabályozás szempontjából a 32 KB-os csomagméretű bejövő adatcsomag 32 eseményként lesz kezelve, egyenként 1 KB méretben. A megengedett maximális eseményméret 32 KB. A 32 KB-nál nagyobb adatcsomagok csonkulnak.

Az S1 vagy S2 Termékváltozat kapacitása egyetlen környezetben 10 egységre növelhető. S1 környezetből nem lehet áttérni egy S2 környezetbe. S2 környezetből nem lehet áttérni egy S1-be.

A be- ésnagykapacitás esetén először határozza meg a teljes be- ésbekerülési díjat havonta. Ezután határozza meg, hogy mire van szüksége a percenkénti igényeinek. 

A sávszélesség-szabályozás és a késés percenkénti kapacitásban játszik szerepet. Ha az adatok betöltése 24 óránál rövidebb ideig tart, a Time Series Insights "felzárkózhat" az előző táblázatban felsorolt díjak kétszeresének bejövő sebességével.

Ha például egyetlen S1 termékváltozattal rendelkezik, az adatok percenként 720 eseménysebességével érkeznek, és az adatátviteli sebesség egy óránál rövidebb ideig, legfeljebb 1440 esemény, de nincs észrevehető késés a környezetben. Ha azonban több mint egy órán keresztül meghaladja az 1440 eseményt percenként, valószínűleg késést fog tapasztalni az adatokban, amelyek láthatóvá válnak, és elérhetők a környezetben lekérdezéshez.

Előfordulhat, hogy nem tudja előre, hogy mennyi adatot szeretne leadni. Ebben az esetben az [Azure IoT Hub](../iot-hub/iot-hub-metrics.md) és az [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) adattelemetriai adatait az Azure Portal-előfizetésben találja. A telemetriai adatok segítségével meghatározhatja, hogyan kell kiépíteni a környezetet. Használja a **Metrikák** ablaktáblán az Azure Portalon a megfelelő eseményforrás telemetriai adatok megtekintéséhez. Ha ismeri az eseményforrás-mutatókat, hatékonyabban tervezheti meg és építheti ki a Time Series Insights-környezetet.

### <a name="calculate-ingress-requirements"></a>A be- és nagybe- és be- és visszaképzési követelmények kiszámítása

A be- és nagybe- és be- és nagybe- és be- és nagybe- és be- és be- és be

- Ellenőrizze, hogy a be- éstelepítési kapacitás meghaladja-e az átlagos percenkénti sebességet, és hogy a környezet elég nagy-e ahhoz, hogy a várható be- és be- és átfutási idő kétszeresét, mint egy órával a kapacitást kezelje.

- Ha a be- és nagymértékű kiugrások 1 óránál tovább tartanak, használja a csúcsarányt átlagosan. A csúcssebesség kezeléséhez képes környezet kiépítése.

### <a name="mitigate-throttling-and-latency"></a>A szabályozás és a késés csökkentése

A sávszélesség-szabályozás és a késés megelőzéséről a [Késés és a szabályozás csökkentése című](time-series-insights-environment-mitigate-latency.md)lapban olvashat bővebben.

## <a name="shape-your-events"></a>Az események alakítása

Fontos annak biztosítása, hogy az események időbetöltése a Time Series Insights-ba történő küldésének módja támogassa a kiépített környezet méretét. (Ezzel szemben a környezet méretét leképezheti a Time Series Insights által beolvasások száma és az egyes események mérete alapján.) Azt is fontos átgondolni, hogy milyen attribútumokat érdemes szeletelni és szűrni az adatok lekérdezésekekénél.

> [!TIP]
> Tekintse át a JSON-formázási dokumentációt az [Események küldése című részben.](time-series-insights-send-events.md)

## <a name="ensure-that-you-have-reference-data"></a>Győződjön meg arról, hogy rendelkezik referenciaadatokkal

A *referencia-adatkészlet* olyan elemek gyűjteménye, amelyek az eseményforrásból származó eseményeket bővítik. A Time Series Insights bejövő műveletek motorja az eseményforrás minden eseményét a referencia-adatkészlet megfelelő adatsorával egyesíti. A bővített esemény ezután lekérdezésre érhető el. Az illesztés a referencia-adatkészletben definiált **elsődleges kulcs** oszlopokon alapul.

> [!NOTE]
> A referenciaadatok visszamenőleges en nincsenek összefűzve. Csak a jelenlegi és a jövőbeli belső kapcsolatadatait egyezteti, és a referencia-adatkészlethez csatlakozik a konfigurálás és a feltöltés után. Ha nagy mennyiségű előzményadatot szeretne küldeni a Time Series Insights számára, és nem tölt fel vagy hoz létre referenciaadatokat a Time Series Insights ban, előfordulhat, hogy újra kell végeznie a munkáját (tipp: nem szórakoztató).  

Ha többet szeretne megtudni arról, hogyan hozhat létre, tölthet fel és kezelhet referenciaadatokat a Time Series Insights ban, olvassa el [a Referencia adatkészlet dokumentációját.](time-series-insights-add-reference-data-set.md)

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>További lépések

- Első lépések: [új Time Series Insights-környezet](time-series-insights-get-started.md)et hozhat létre az Azure Portalon.

- További információ arról, hogyan [vehet fel eseményközpontok eseményforrását](time-series-insights-how-to-add-an-event-source-eventhub.md) a Time Series Insights-ba.

- Az [IoT Hub eseményforrásának konfigurálásáról](time-series-insights-how-to-add-an-event-source-iothub.md)olvashat.

---
title: A méretezési csoport az Azure Time Series Insights-környezet tervezése |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan ajánlott eljárások követése, ha azt tervezi, hogy az Azure Time Series Insights-környezetet. Területeket, amelyekre a tárolási kapacitást, az adatmegőrzés, betöltési kapacitás, figyelés és üzleti folytonossági és vészhelyreállítási helyreállítási (BCDR) tartalmazza.
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 9d9f9b7fe7aafa927f023e1d4e30e079a26f7fab
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431050"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Az Azure Time Series Insights általánosan elérhető környezet megtervezése

Ez a cikk ismerteti az Azure Time Series Insights-általánosan (elérhetővé tétel GA) a várt bejövő forgalom és az adatmegőrzési követelmények alapján környezet megtervezése.

## <a name="video"></a>Videó

**Ebben a videóban további információ az Azure Time Series Insights, és tervezze meg, hogyan adatmegőrzés**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Ajánlott eljárások

Ismerkedés a Time Series Insights, az a legcélszerűbb, ha tudja, hogy mennyi adatot tervez küldje le a percet, és mennyi ideig kell tárolni az adatokat.  

Mindkét Time Series Insights termékváltozatok kapacitás és a megőrzési kapcsolatos további információkért lásd: [Time Series Insights díjszabása](https://azure.microsoft.com/pricing/details/time-series-insights/).

A Time Series Insights-környezet a hosszú távú sikert legjobb megtervezéséhez, vegye figyelembe a következő attribútumokat:

- <a href="#storage-capacity">Tárkapacitás</a>
- <a href="#data-retention">Adatmegőrzés időtartama</a>
- <a href="#ingress-capacity">Betöltési kapacitás</a>
- <a href="#shape-your-events">Az események alakításra.</a>
- <a href="#ensure-that-you-have-reference-data">Annak biztosítása, hogy már működik a referenciaadatok</a>

## <a name="storage-capacity"></a>Tárkapacitás

Alapértelmezés szerint a Time Series Insights üzembe helyezésekor tárterület mérete alapján adatait őrzi meg (egységek &#215; tárhely / egység) a bejövő és kimenő.

## <a name="data-retention"></a>Adatmegőrzés

Módosíthatja a **adatmegőrzési idő** a Time Series Insights-környezet beállítását. Legfeljebb 400 napos adatmegőrzés engedélyezheti. 

A Time Series Insights szolgáltatásnak két módja van. Egy mód annak biztosítására, hogy a környezet rendelkezik-e a legújabb adatok szerepeljenek optimalizálja. Ez a mód alapértelmezés szerint be van kapcsolva. 

A másik módra annak biztosítására, hogy teljesülnek-e az adatmegőrzési korlátok optimalizálja. A második módban bejövő szünetel a környezet teljes tárolási kapacitásának teljesülése esetén. 

Módosíthatja az adatmegőrzéssel és a be-vagy kikapcsolása között a két mód a lapon a környezet az Azure Portalon.

A Time Series Insights környezetben legfeljebb 400 napos adatmegőrzés konfigurálhatja.

### <a name="configure-data-retention"></a>Az adatmegőrzés konfigurálása

1. Az a [az Azure portal](https://portal.azure.com), válassza ki a Time Series Insights-környezetet.

1. Az a **Time Series Insights-környezet** panel alatt **beállítások**, jelölje be **konfigurálása**.

1. Az a **adatok megőrzési ideje (nap)** mezőben adjon meg egy értéket 1 és 400-as között.

   [![Megőrzés konfigurálása](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> Egy megfelelő adatmegőrzési szabályzat megvalósítása kapcsolatos további információkért lásd: [megőrzés konfigurálása](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Betöltési kapacitás

A Time Series Insights-környezet tervezésének összpontosíthat a második terület a belépő kapacitás. Betöltési kapacitás leszármazottja, a percalapú foglalási.

A sávszélesség-szabályozási szempontjából egy csomag mérete 32 KB-os ingressed adatcsomag 32 események, minden egyes 1 KB méretű számít. Az engedélyezett események maximális mérete 32 KB. 32 KB-nál nagyobb adatcsomagok csonkolva lesznek.

A következő táblázat összefoglalja az egyes Time Series Insights Termékváltozat egységenként betöltési kapacitás:

|SKU  |Események száma / hó  |Esemény mérete / hó  |Események száma percenként  |Esemény mérete / perc  |
|---------|---------|---------|---------|---------|
|S1     |   30 millió     |  30 GB     |  720    |  720 KB   |
|S2     |   300 millió    |   300 GB   | 7,200   | A 7200 KB  |

Az S1 vagy S2 Termékváltozat kapacitása 10 egység egyetlen környezetben lehet növelni. S2 szintű, nem telepíthetők át egy S1-környezetből. Egy S1-re nem telepíthető át olyan S2 környezetből.

A belépő kapacitás először határozza meg a teljes bejövő forgalom havi alapon van szüksége. Következő lépésként állapítsa meg, mi a percalapú igények kielégítéséhez. 

Szabályozás és a késleltetés szerepet játszik a percenkénti kapacitás. Ha ugrásszerű a 24 óránál rövidebb ideig tart beérkező adatok, Time Series Insights is "olvasásra" az előző táblázatban szereplő díjak kétszer egy bejövő forgalom sebessége.

Például ha egy egyetlen S1 Termékváltozat, akkor bejövő adatok 720 esemény, percenkénti díj és az adatokra vonatkozó hirtelen megugró kihasználtság, egy óránál kevesebb 1440 események vagy annál kisebb gyakorisággal, nincs észrevehető késleltetés a környezetben. Azonban Ha elfogynak 1440 események száma percenként több mint egy óráig, valószínűleg tapasztalható késés áll a teszi és a lekérdezés a környezetben elérhető adatokat.

Előfordulhat, hogy nem tudja előre mennyi adatot tervez leküldéses. Ebben az esetben annak telemetriai adatok [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) és [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) az Azure portal-előfizetésében. A telemetriai adatok segítségével meghatározhatja, hogyan építheti ki a környezet. Használja a **metrikák** panel az Azure Portalon a megfelelő esemény-adatforráshoz a telemetriájának megtekintéséhez. Ha megismeri az esemény az adatforrás mérőszámainak, hatékonyabban tervezhet és a Time Series Insights-környezet kiépítése.

### <a name="calculate-ingress-requirements"></a>Belépő kiszámítására

A bejövő forgalom követelmények kiszámításához:

- Ellenőrizze, hogy a belépő kapacitás az átlagos percalapú díját fölött van-e, és hogy a környezet elég nagy legyen a kétszer a kapacitás egy óránál kevesebb egyenértékű várható bejövő forgalom kezelésére.

- Ha a bejövő adatforgalmi csúcsokhoz fordulhat elő, amelyek a legutóbbi hosszabb mint 1 óra, használja a megnövekedett forgalom pedig az átlag. Kiépítése a környezet kapacitása a megnövekedett forgalom kezelésére.

### <a name="mitigate-throttling-and-latency"></a>Szabályozás és a késés csökkentése

Szabályozás és a késleltetés elkerülése érdekében kapcsolatos információkért lásd: [csökkentheti a késést és a sávszélesség-szabályozási](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Az események formázása

Fontos, hogy a küldött események a Time Series Insights támogatja szeretne kiépíteni a környezet méretének módon. (Ezzel szemben, leképezheti a hány események a Time Series Insights beolvassa a környezetről, és minden egyes esemény méretét.) Emellett az is fontos, gondolja át az attribútumokat, amelyek akkor érdemes használni és szűrés az adatok lekérdezésénél.

> [!TIP]
> Tekintse át a JSON-dokumentáció alakításra [az események](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).

## <a name="ensure-that-you-have-reference-data"></a>Gondoskodjon arról, hogy a referenciaadatok

A *hivatkozik adatkészlet* kiolvassa az eseményeket az eseményforrás bővítésével elemek gyűjteménye. A Time Series Insights bejövő forgalmat kezelő motorja a referencia-adatkészlet minden eseményt az eseményforrás a megfelelő adatok sor csatlakozik. A kibővített esemény ezután lekérdezés érhető el. Az illesztési alapul a **elsődleges kulcs** a referencia-adatkészletben definiált oszlopokat.

> [!NOTE]
> Referenciaadatok visszamenőlegesen a nem csatlakozik tartományhoz. Csak a jelenlegi és jövőbeli bejövő adatainak matched és konfigurálva, és feltöltése után a referencia-adatkészlet csatlakozik. Ha azt tervezi, előzményadatok nagy mennyiségű küldése Time Series Insights és az első feltöltése nem vagy a Time Series Insights referencia-adatok létrehozása, előfordulhat, hogy rendelkezik, ismételje meg a munkahelyi (Tipp: nem szórakozást).  

Hozzon létre, töltse fel és kezelheti a Time Series Insights referencia-adatok kapcsolatos további tudnivalókért lásd: a [adatkészlet Referenciadokumentációt](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>További lépések

- Első lépésként hozza létre [egy új Time Series Insights-környezet az Azure Portalon](time-series-insights-get-started.md).

- Ismerje meg, hogyan [az Event Hubs eseményforrás hozzáadása](time-series-insights-how-to-add-an-event-source-eventhub.md) a Time Series Insights.

- Megtudhatja, hogyan lehet a [konfigurálása az IoT Hub-eseményforrás](time-series-insights-how-to-add-an-event-source-iothub.md).

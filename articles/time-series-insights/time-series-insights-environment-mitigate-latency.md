---
title: Figyelése és csökkentheti a szabályozás, az Azure Time Series Insightsban |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan figyelheti, diagnosztizálhatja és kezelheti az Azure Time Series Insightsban késés és sávszélesség-szabályozási okozó teljesítményével kapcsolatos problémákat.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 11/27/2017
ms.openlocfilehash: a404eb1393f9e99c2e2932c2d23724051f1b72a0
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628487"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Figyelheti, és az Azure Time Series Insightsban késés csökkentése érdekében a szabályozás csökkentése
A bejövő adatok mennyisége meghaladja a környezet konfigurációját, amikor a késleltetés vagy az Azure Time Series Insights-szabályozás tapasztalhat.

Elkerülheti a késés és a sávszélesség-szabályozási adatok elemezni szeretné a környezet megfelelő konfigurálásával.

Legnagyobb valószínűséggel késés és a szabályozás, amikor Ön:

- Adjon hozzá egy eseményforrás, amely tartalmazza a régi adatok haladhatja meg a meghatározott időn belül bejövő forgalom (a Time Series Insights olvasásra kell).
- Adjon hozzá további eseményforrásokkal környezethez, további események (ami túllépheti a környezet kapacitása) ugrásszerű eredményez.
- Korábbi események nagy mennyiségű leküldése egy eseményforrás (a Time Series Insights olvasásra kell) késéssel eredményez.
- Csatlakozzon a referenciaadatok telemetriával, nagyobb esemény-méretet eredményez.  A sávszélesség-szabályozási szempontjából egy ingressed adatok csomagot a csomag mérete 32 KB-os 32 események számít, minden egyes 1 KB méretű. Engedélyezett események maximális mérete 32 KB; 32 KB-nál nagyobb adatcsomagok csonkolva lesznek.


## <a name="monitor-latency-and-throttling-with-alerts"></a>Késés és a szabályozás és a riasztások figyelése

Riasztások segíthetnek a környezet által okozott késési problémák diagnosztizálásához és érdekében. 

1. Az Azure Portalon kattintson a **metrikák**. 

   ![Mérőszámok](media/environment-mitigate-latency/add-metrics.png)

2. Kattintson a **metrikariasztás hozzáadása**.  

    ![Metrikariasztás hozzáadása](media/environment-mitigate-latency/add-metric-alert.png)

Itt konfigurálhatja a riasztások a következő mérőszámok segítségével:

|Metrika  |Leírás  |
|---------|---------|
|**Bejövő bájtokat fogadott**     | Nyers bájt száma eseményforrások olvasni. Nyers száma általában a tulajdonság nevét és értékét tartalmazza.  |  
|**Bejövő forgalom érvénytelen üzenet érkezett**     | Érvénytelen üzeneteinek száma az összes Azure Event Hubs vagy Azure IoT Hub eseményforrások olvasni.      |
|**Bejövő üzenetek fogadása**   | Üzenetek száma az összes Event Hubs vagy IoT-központok eseményforrások olvasni.        |
|**Bejövő bájtok tárolt**     | Összes tárolt események méretét és lekérdezhető. A tulajdonság értéke csak a számított méretét.        |
|**Bejövő forgalom tárolt események**     |   A tárolt és lekérdezhető egybesimított események száma.      |
|**Bejövő forgalom fogadott üzenethez idő elteltével**    |  Forrás- és az idő a feldolgozása a bejövő forgalom a különbözet között az, hogy az üzenet az eseménynaplóban-e várólistán lévő idő másodpercben.      |
|**Bejövő üzenetek száma időbeli kapott**    |  Utolsó várólistán lévő üzenetek sorszáma közötti különbség az adatforrás a bejövő forgalom feldolgozott üzenet partíciót és feladatütemezési számát.      |


![Késés](media/environment-mitigate-latency/latency.png)

Ha meg van szabályozva, látni fogja a egy értéke a *bejövő érkezett üzenet időeltolódás*, hány másodpercet TSI mögött van a tényleges idő a az üzenet tájékoztat eléri az eseményforrás (kivéve az appx-alapú indexelési idő. 30 – 60 másodpercet).  *Bejövő forgalom Beérkezett üzenetek száma időbeli* értékkel, lehetővé téve annak meghatározására, hogy hány üzenetek mögött található, akkor is kell rendelkeznie.  Az első szerepelnek legegyszerűbben, amely lehetővé teszi a különbség az, hogy a környezet kapacitás bővítéséhez.  

Ha egyetlen egység S1 környezettel rendelkezik, és tekintse meg, hogy nincs-e 5 millió üzenet késéssel, például sikerült hat egységeket a napi körül első szerepelnek a környezet méretének növeléséhez.  Sikerült növelheti még tovább catch fel gyorsabban.  Az utólagos idő pedig gyakran előfordul, kiépítésekor kezdetben környezetekben, különösen akkor, ha csatlakoztatja, amely már rendelkezik az események, az eseményforrás, vagy ha Ön tömeges feltöltés nagy mennyiségű előzményadatok.

Egy másik eljárás az, hogy állítsa be egy **belépő tárolt események** riasztás > = némileg alább 2 órán át a teljes környezet kapacitása a küszöbértéket.  Ez a riasztás segíthetnek megérteni, ha Ön folyamatosan kapacitással, amely azt jelzi, hogy a késés nagy valószínűséggel.  

Például, ha három S1 szintű egységek kiépítése (vagy perces betöltési kapacitás 2100 esemény), beállíthat egy **belépő tárolt események** a riasztási > 1900 események = 2 óra alatt. Ha folyamatosan ezt a küszöbértéket meghaladó, és ezért a a riasztást kiváltó, valószínűleg alatt-regisztrálva van.  

Is, ha azt gyanítja, hogy van szabályozva, összehasonlíthatja a **bejövő Beérkezett üzenetek** az eseménnyel a forrás egressed az üzeneteket.  Ha az Event Hubs-eseményközpontba bejövő nagyobb, mint a **bejövő Beérkezett üzenetek**, a Time Series Insights valószínűleg szabályozva.

## <a name="improving-performance"></a>A teljesítmény fokozása 
Szabályozási vagy tapasztalt késés csökkentése érdekében javítsa ki a legjobb módszer az növeli a kapacitást a környezet. 

Elkerülheti a késés és a sávszélesség-szabályozási adatok elemezni szeretné a környezet megfelelő konfigurálásával. A környezet kapacitása hozzáadása kapcsolatos további információkért lásd: [Skálázhatja környezetét](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>További lépések
- További hibaelhárítási [diagnosztizálása és kapcsolatos problémák megoldásához a Time Series Insights-környezet a](time-series-insights-diagnose-and-solve-problems.md).
- További segítségért beszélgetés indítása a a [MSDN-fórum](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) vagy [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). Is [az Azure-támogatás](https://azure.microsoft.com/support/options/) személyes támogatás lehetőségeket.

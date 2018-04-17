---
title: Figyelése, és csökkenti a sávszélesség-szabályozás Azure idő adatsorozat insightsban |} Microsoft Docs
description: A cikkből megtudhatja, hogyan figyelése, diagnosztizálása és csökkenthető a késleltetés és a sávszélesség-szabályozás Azure idő adatsorozat Insights okozó teljesítményproblémákat.
services: time-series-insights
ms.service: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 11/27/2017
ms.openlocfilehash: ac59359eb6af268f311534d90e1529fc5e41094f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Figyelheti és csökkenthető a sávszélesség-szabályozás Azure idő adatsorozat elemzések a késés csökkentése érdekében
Ha a bejövő adatok mennyisége meghaladja a környezet konfigurációjának, tapasztalhatja várakozási ideje vagy Azure idő adatsorozat Insights szabályozását.

Elkerülheti a késleltetés és a sávszélesség-szabályozás a környezetében az elemezni kívánt adatok megfelelő beállításával.

Legtöbb valószínű, hogy a késés és a sávszélesség-szabályozás mikor meg:

- Adjon hozzá egy eseményforrás, amely tartalmazza a régi adatok túlléphetik a meghatározott időn belül érkező gyakorisága (idő adatsorozat Insights kell fel).
- Adja hozzá a további eseményforrások környezethez, további események (amely lépheti túl a környezet kapacitás) egy csúcs eredményezi.
- Nagy mennyiségű korábbi események leküldése egy eseményforrás, ami azt eredményezi, a késés (idő adatsorozat Insights kell fel).
- Csatlakoztassa a referenciaadatok a telemetriai adatokat nagyobb esemény-méretet eredményez.  A sávszélesség-szabályozási szempontjából egy 32 KB-os csomag méretű ingressed adatcsomag 32 eseményeket a rendszer, egyes 1 KB méretű. A maximális engedélyezett eseményről mérete 32 KB; csak adatcsomagok 32 KB-nál nagyobb.


## <a name="monitor-latency-and-throttling-with-alerts"></a>Késleltetés és a sávszélesség-szabályozás a riasztások figyelése

Figyelmeztetések segíthetnek diagnosztizálni és megoldani az késési problémák okozta. a környezet érdekében. 

1. Az Azure portálon kattintson **metrikák**. 

   ![Mérőszámok](media/environment-mitigate-latency/add-metrics.png)

2. Kattintson a **metrika riasztás hozzáadása**.  

    ![Metrikariasztás hozzáadása](media/environment-mitigate-latency/add-metric-alert.png)

Ott a riasztások a következő metrikák használatával is konfigurálhatja:

|Metrika  |Leírás  |
|---------|---------|
|**Érkező fogadott bájtok**     | Nyers bájtok száma eseményforrások olvasni. Nyers száma általában a tulajdonság nevét és értékét tartalmazza.  |  
|**Érvénytelen üzeneteket érkező kapott**     | Érvénytelen üzenetek száma az összes Azure Event Hubs vagy Azure IoT Hub eseményforrások olvasni.      |
|**Érkező üzenetek fogadása**   | Minden Event Hubs vagy IoT-központok eseményforrások olvasható üzenetek száma.        |
|**Érkező tárolt bájt**     | Teljes méret tárolt események és lekérdezhetők. A tulajdonság értéke csak a számított méretét.        |
|**Érkező tárolt események**     |   A tárolt és lekérdezhetők egybesimított események száma.      |
|**Érkező száma üzenet Időeltolódást**    |  A, amely az üzenet a várólistában levő a forrás és az idő érkező feldolgozásra közötti különbség.      |
|**Érkező száma üzenetek száma késés**    |  Utolsó várólistán lévő üzenetek sorszáma különbségének a forrás-érkező a feldolgozás alatt álló üzenet partíció és a sorrend számát.      |


![Késés](media/environment-mitigate-latency/latency.png)

Ha meg vannak szabályozva, látni fogja a értéket a *érkező száma üzenet Időeltolódást*, tájékoztat a tényleges óta az hány perces késésben ÁME az üzenet találatok az eseményforrás (kivéve a appx indexelési idején. 30 – 60 másodperc).  *Érkező száma üzenetek száma Lag* értéket, hogy lehetővé teszi annak meghatározására, hogy mögött, hogy hány üzenet lehet.  A legegyszerűbb gyorsan naprakész módja a megfelelő méretűre, amely lehetővé teszi a kapcsolatos a különbség a környezet kapacitás növelése érdekében.  

Ha egyetlen egység – S1 környezettel rendelkezik, és itt látható egy öt millió üzenetet lag, például sikerült hat egységeket az egy nap körül gyorsan naprakész a környezet méretének növelése.  Sikerült növelheti is tovább catch mentése gyorsabb.  Ez az általános elemként létesítésekor kezdetben környezethez, különösen akkor, amikor egy eseményforrás, amely már rendelkezik az események azt csatlakoztatja, vagy Ön tömeges előzményadatokat sok feltöltési.

Egy másik módszerrel is, hogy egy **érkező tárolt jelzések** riasztás > = némileg alatt a teljes környezet kapacitása 2 órán át a küszöbértéket.  Ez a riasztás azt segítenek megérteni, ha biztos folyamatosan kapacitással, amely megadja, hogy a késés nagy valószínűséggel  

Például ha három S1 egységek kiosztása (vagy a percben érkező kapacitás 2100 események) rendelkezik, akkor megadhatja egy **érkező tárolt jelzések** riasztás > 1900 események = 2 órán át. Ha ezt a küszöbértéket meghaladó, és ezért a a riasztást kiváltó folyamatosan, akkor valószínűleg alatt-törlődnek.  

Is, ha azt gyanítja, amelyek szabályozva, összehasonlíthatja a **érkező fogadott üzenetek** a eseménnyel a forrás egressed tartozó üzenetek.  Ha az Eseményközpont történő érkező érték nagyobb, mint a **érkező fogadott üzenetek**, az idő adatsorozat elemzések valószínűleg szabályozva.

## <a name="improving-performance"></a>A teljesítmény fokozása 
Csökkenteni a sávszélesség-szabályozás vagy késést tapasztal, javítsa ki a legjobb módszer, a környezet kapacitás növelése érdekében. 

Elkerülheti a késleltetés és a sávszélesség-szabályozás a környezetében az elemezni kívánt adatok megfelelő beállításával. A környezet kapacitás adásával kapcsolatos további információkért lásd: [a környezet skálázása](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>További lépések
- A további hibaelhárítási lépéseket [Diagnosztizálás és a idő adatsorozat Insights környezetében előforduló problémák megoldásához](time-series-insights-diagnose-and-solve-problems.md).
- Ha további segítségre van szüksége, indítsa el a beszélgetés a a [MSDN fórum](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) vagy [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). Akkor is kapcsolatba [az Azure támogatási](https://azure.microsoft.com/support/options/) a személyes támogatási lehetőségeket.

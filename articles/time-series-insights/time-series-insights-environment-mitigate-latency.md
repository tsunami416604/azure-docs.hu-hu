---
title: "Teljesítmény figyeléséhez, csökkenti a sávszélesség-szabályozás és Azure idő adatsorozat insightsban késleltetés megakadályozza |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan figyelése, diagnosztizálása és csökkenthető a késleltetés és a sávszélesség-szabályozás Azure idő adatsorozat Insights okozó teljesítményproblémákat."
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
ms.date: 11/15/2017
ms.openlocfilehash: 5af5589922ded802703b9ba8f3b0aa8c5524bbad
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="improve-performance-reduce-latency-and-mitigate-throttling-in-azure-time-series-insights"></a>A teljesítmény javítása, késés csökkentésére, és csökkenthető a sávszélesség-szabályozás Azure idő adatsorozat insightsban
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

    ![Metrika riasztások hozzáadása](media/environment-mitigate-latency/add-metric-alert.png)

Ott a riasztások a következő metrikák használatával is konfigurálhatja:

|Metrika  |Leírás  |
|---------|---------|
|**Érkező fogadott bájtok**     | Nyers bájtok száma eseményforrások olvasni. Nyers száma általában a tulajdonság nevét és értékét tartalmazza.  |  
|**Érvénytelen üzeneteket érkező kapott**     | Érvénytelen üzenetek száma az összes Azure Event Hubs vagy Azure IoT Hub eseményforrások olvasni.      |
|**Érkező üzenetek fogadása**   | Minden Event Hubs vagy IoT-központok eseményforrások olvasható üzenetek száma.        |
|**Érkező tárolt bájt**     | Teljes méret tárolt események és lekérdezhetők. A tulajdonság értéke csak a számított méretét.        |
|**Érkező tárolt események**     |   A tárolt és lekérdezhetők egybesimított események száma.      |

![Késés](media/environment-mitigate-latency/latency.png)

Egyik módszer is, hogy egy **érkező tárolt jelzések** riasztás > = némileg alatt a teljes környezet kapacitása 2 órán át a küszöbértéket.  Ez a riasztás azt segítenek megérteni, ha biztos folyamatosan kapacitással, amely megadja, hogy a késés nagy valószínűséggel  

Például ha három S1 egységek kiosztása (vagy a percben érkező kapacitás 2100 események) rendelkezik, akkor megadhatja egy **érkező tárolt jelzések** riasztás > 1900 események = 2 órán át. Ha ezt a küszöbértéket meghaladó, és ezért a a riasztást kiváltó folyamatosan, akkor valószínűleg alatt-törlődnek.  

Is, ha azt gyanítja, amelyek szabályozva, összehasonlíthatja a **érkező fogadott üzenetek** a eseménnyel a forrás egressed tartozó üzenetek.  Ha az Eseményközpont történő érkező érték nagyobb, mint a **érkező fogadott üzenetek**, az idő adatsorozat elemzések valószínűleg szabályozva.

## <a name="improving-performance"></a>A teljesítmény fokozása 
Csökkenteni a sávszélesség-szabályozás vagy késést tapasztal, javítsa ki a legjobb módszer, a környezet kapacitás növelése érdekében. 

Elkerülheti a késleltetés és a sávszélesség-szabályozás a környezetében az elemezni kívánt adatok megfelelő beállításával. A környezet kapacitás adásával kapcsolatos további információkért lásd: [a környezet skálázása](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Következő lépések
- A további hibaelhárítási lépéseket [Diagnosztizálás és a idő adatsorozat Insights környezetében előforduló problémák megoldásához](time-series-insights-diagnose-and-solve-problems.md).
- Ha további segítségre van szüksége, indítsa el a beszélgetés a a [MSDN fórum](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) vagy [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). Akkor is kapcsolatba [az Azure támogatási](https://azure.microsoft.com/support/options/) a személyes támogatási lehetőségeket.

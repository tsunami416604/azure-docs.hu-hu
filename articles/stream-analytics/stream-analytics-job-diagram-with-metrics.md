---
title: Az adatvezérelt Hibakeresés az Azure Stream Analytics szolgáltatásban
description: Ez a cikk ismerteti az Azure Stream Analytics-feladat hibaelhárítása az Azure Portalon a feladatábra és metrikák használatával.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 4a6d359b27b9a2e52d71ed5f8547041645147605
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479918"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Az adatvezérelt hibakeresés a feladatábra használatával

A feladat ábra: a **figyelés** panel az Azure Portal segítségével jelenítheti meg a feladat folyamatot. Az ábra a bemeneteket, kimeneteket és a lekérdezések lépéseit mutatja. A feladatábrával ellenőrizheti az egyes lépések metrikáit, és a problémák elhárítása során gyorsabban felmérheti az egyes hibák eredetét.

## <a name="using-the-job-diagram"></a>A feladatdiagramról használatával

Az Azure Portalon alatt a Stream Analytics-feladat, miközben **támogatás + hibaelhárítás**válassza **feladatábra**:

![Feladatábra metrikákkal - helye](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Válassza ki a megfelelő szakaszában egy lekérdezés-szerkesztő ablakban megtekintheti a lekérdezés mindegyik lépése. A lépés egy metrikadiagram egy kisebb ablaktábla az oldalon jelenik meg.

![Feladatábra metrikákkal – alapszintű feladat](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Az Azure Event Hubs-bemenet a partíciók megtekintéséhez válasszon **...** Megjelenik egy helyi menü. A bemeneti egyesülés is láthatja.

![Feladatábra metrikákkal - bontsa ki a partíciós](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

A metrika csak egyetlen partition diagramot, jelölje ki a partíciós csomópontot. A metrikák jelennek meg a lap alján.

![Feladatábra metrikákkal – további metrikák megtekintése](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

A teljesítménymetrikák diagramja ahhoz, hogy válassza ki a egyesülés. A következő diagram mutatja, hogy nem eseményeket a rendszer eldobott vagy módosított.

![Feladatábra metrikákkal - rács](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

A metrika értékének és az idő a részletek megtekintéséhez, majd a diagram.

![Feladat-metrikákkal diagramja – rámutatáskor](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Hibaelhárítás mérőszámok segítségével

A **QueryLastProcessedTime** metrika azt jelzi, ha egy adott fogadtak adatokat. A topológia megnézzük, is dolgozhat visszafelé a kimeneti processzor melyik lépés nem fogadott adatok megtekintéséhez. Ha egy lépés nem fogad adatokat, ugorjon a lekérdezés azt megelőzően. Ellenőrizze, hogy rendelkezik-e az előző lekérdezés lépés egy olyan időkeretet, és ha elég idő telt-e a kimeneti adatokat. (Megjegyzés: az időpont windows rendszer diagramobjektum a órára.)
 
Ha az előző lekérdezés lépés egy, akkor a bemeneti metrikákból választ kaphat az arra irányuló célzott kérdésekre. Ezek segítségével meghatározhatja, hogy egy feladat az adatok beolvasása a bemeneti forrásoktól. Ha a lekérdezés particionálva van, vizsgálja meg az összes partíciót.
 
### <a name="how-much-data-is-being-read"></a>Olvasott adatok mennyiségét?

*   **InputEventsSourcesTotal** olvasási adategységek száma. Ha például a blobok száma.
*   **InputEventsTotal** beolvasott események számát. Ez a metrika partíciónként érhető el.
*   **InputEventsInBytesTotal** beolvasott bájtok száma.
*   **InputEventsLastArrivalTime** frissül az egyes fogadott események sorba helyezésekor.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Is time moving forward? Tényleges eseményeket olvasási vannak, előfordulhat, hogy nem adható ki írásjelek.

*   Az **InputEventsLastPunctuationTime** megadja, hogy mikor adtak ki írásjelek, az idő előrehaladása érdekében. Ha a rendszer nem állítja ki írásjelek, az adatfolyam megakadhat.
 
### <a name="are-there-any-errors-in-the-input"></a>Vannak-e hibák a bemenetben?

*   **InputEventsEventDataNullTotal** null adatot tartalmazó események száma.
*   **InputEventsSerializerErrorsTotal** események, amelyek deszerializálása nem lehet száma.
*   **InputEventsDegradedTotal** száma eseményeket, amelyek nem a Deszerializálási hiba történt.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Azok az éppen eldobott vagy módosított események?

*   **InputEventsEarlyTotal** száma, események, amelyek a felső korlát előtti alkalmazás-időbélyeggel rendelkeznek.
*   **InputEventsLateTotal** a felső korlát utáni alkalmazás-időbélyeggel rendelkező események száma.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** a feladat indítási ideje előtt eldobott események számát.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Hogy csökken az adatok beolvasása?

*   **Várakozó események száma (összesen) bemeneti** jelzi, hogy hány több üzenetet kell olvasni az Event Hubs és az Azure IoT Hub-bemenet szükséges. Ha ez a szám nagyobb, mint 0, az azt jelenti, a feladat nem dolgozható fel az adatok gyors, várható. Ebben az esetben szükség lehet növelni a folyamatos átviteli egységek számát és/vagy ellenőrizze, hogy a feladat is méretezésnek megfelelően. Látható, ezzel kapcsolatban további információ a [lekérdezés ezerszer lap](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). 


## <a name="get-help"></a>Segítségkérés
További segítségért, próbálja meg [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>További lépések
* [Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Stream Analytics használatának első lépései](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-feladatok méretezése](stream-analytics-scale-jobs.md)
* [Stream Analytics lekérdezési nyelv leírása](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics felügyeleti REST API-referencia](https://msdn.microsoft.com/library/azure/dn835031.aspx)

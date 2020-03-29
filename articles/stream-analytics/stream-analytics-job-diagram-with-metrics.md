---
title: Adatalapú hibakeresés az Azure Stream Analytics szolgáltatásban
description: Ez a cikk ismerteti, hogyan háríthatja el az Azure Stream Analytics-feladat a feladatdiagram és a metrikák az Azure Portalon használatával.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 3c0c29e1793e56efae8d13cb01d57faf257d8805
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426081"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Adatvezérelt hibakeresés a feladatábra használatával

A feladatdiagram az Azure Portalon a **figyelési** panelen segíthet a feladat-folyamat megjelenítésében. Az ábra a bemeneteket, kimeneteket és a lekérdezések lépéseit mutatja. A feladatábrával ellenőrizheti az egyes lépések metrikáit, és a problémák elhárítása során gyorsabban felmérheti az egyes hibák eredetét.

## <a name="using-the-job-diagram"></a>A feladatdiagram használata

Az Azure Portalon a Stream Analytics-feladat ban a **SUPPORT + TROUBLESHOOTING**területen válassza a **Feladatdiagram lehetőséget:**

![Feladatdiagram mérőszámokkal – hely](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Jelölje ki az egyes lekérdezési lépéseket, ha meg szeretné tekinteni a megfelelő szakaszt a lekérdezésszerkesztő ablaktáblában. A lépés mérőszámai az oldal alsó ablaktáblájában jelennek meg.

![Feladatdiagram mérőszámokkal – alapfeladat](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Az Azure Event Hubs bemenetpartícióinak megtekintéséhez válassza a **lehetőséget. . . .** Megjelenik egy helyi menü. Ön is láthatja a bemeneti egyesülés.

![Feladatdiagram mérőszámokkal – partíció kibontása](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Csak egyetlen partíció metrikadiagramjának megtekintéséhez jelölje ki a partíciócsomópontot. A mérőszámok az oldal alján jelennek meg.

![Feladatdiagram mérőszámokkal – további mérőszámok](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Az egyesülés metrikadiagramjának megtekintéséhez válassza ki az egyesülési csomópontot. A következő diagram azt mutatja, hogy egyetlen eseményt sem ejtettek vagy igazítottak ki.

![Feladatdiagram mérőszámokkal – rács](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

A metrikaérték és az idő részleteinek megtekintéséhez mutasson a diagramra.

![Feladatdiagram mérőszámokkal – rámutatás](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Hibaelhárítás mérőszámok használatával

A **QueryLastProcessedTime** metrika azt jelzi, hogy egy adott lépés mikor fogadott adatokat. A topológia megtekintésével a kimeneti processzortól visszafelé haladva megnézheti, hogy melyik lépés nem fogad adatokat. Ha egy lépés nem kap adatokat, folytassa a lekérdezési lépéssel előtte. Ellenőrizze, hogy az előző lekérdezési lépés rendelkezik-e időablakkal, és hogy elegendő idő telt-e el az adatok kimenetéhez. (Ne feledje, hogy az időablakok az órára vannak dokkolva.)
 
Ha az előző lekérdezési lépés egy bemeneti processzor, használja a bemeneti metrikák segítségével a következő célzott kérdések megválaszolása. Segítségével meghatározhatja, hogy egy feladat adatokat kap-e a bemeneti forrásokból. Ha a lekérdezés particionálva van, vizsgálja meg az összes partíciót.
 
### <a name="how-much-data-is-being-read"></a>Mennyi adatot olvas fel?

*   **Az InputEventsSourcesTotal** az olvasott adategységek száma. Például a blobok száma.
*   **InputEventsTotal** az olvasott események száma. Ez a metrika partíciónként érhető el.
*   **InputEventsInBytesTotal** az olvasott bájtok száma.
*   **Az InputEventsLastArrivalTime** minden fogadott esemény várólistára helyezett idejével frissül.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Közeledik az idő? Ha a rendszer tényleges eseményeket olvas be, előfordulhat, hogy lemaradnak az eseményzáró jelek.

*   Az **InputEventsLastPunctuationTime** megadja, hogy mikor adtak ki írásjelek, az idő előrehaladása érdekében. Ha nincs írásjelek kiadása, az adatfolyam blokkolva lehet.
 
### <a name="are-there-any-errors-in-the-input"></a>Vannak hibák a bemenetben?

*   **Az InputEventsEventDataNullTotal** a null adattal rendelkező események száma.
*   **Az InputEventsSerializerErrorsTotal** olyan események száma, amelyeket nem lehetett megfelelően deszerializálni.
*   **Az InputEventsDegradedTotal** azoknak az eseményeknek a száma, amelyek nem deszerializálással kapcsolatos problémát.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Az eseményeket eldobják vagy módosítják?

*   **InputEventsEarlyTotal** azon események száma, amelyek alkalmazás időbélyeggel rendelkeznek a magas vízjel előtt.
*   **Az InputEventsLateTotal** azon események száma, amelyek a magas vízjel után alkalmazás időbélyeggel rendelkeznek.
*   **Az InputEventsDroppedBeforeApplicationStartTimeTotal** a feladat kezdési időpontja előtt eldobott események száma.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Lemaradunk az olvasási adatokban?

*   **Input Események Backlogged (Összesen)** megmondja, hogy hány további üzeneteket kell olvasni az Event Hubs és az Azure IoT Hub bemenetek. Ha ez a szám nagyobb, mint 0, az azt jelenti, hogy a feladat nem tudja feldolgozni az adatokat olyan gyorsan, mint ahogy az beérkezik. Ebben az esetben szükség lehet a streamelési egységek számának növelésére és/vagy győződjön meg arról, hogy a feladat párhuzamos lehet. Erről további információkat a [lekérdezés párhuzamosítási oldalán](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization)láthat. 


## <a name="get-help"></a>Segítségkérés
További segítségért próbálja ki [az Azure Stream Analytics fórumunkat.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) 

## <a name="next-steps"></a>További lépések
* [Bevezetés a Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Ismerkedés a Stream Analytics szolgáltatással](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-feladatok skálázása](stream-analytics-scale-jobs.md)
* [A Stream Analytics lekérdezési nyelvének hivatkozása](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics felügyeleti REST API-hivatkozás](https://msdn.microsoft.com/library/azure/dn835031.aspx)

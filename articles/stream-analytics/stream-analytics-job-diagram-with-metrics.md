---
title: Adatalapú Azure Stream Analytics-hibakeresés
description: Ez a cikk ismerteti az Azure Stream Analytics-feladat hibaelhárítása az Azure-portálon a feladat ábra és metrikák használatával.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: e6b2b7f4c01a5280c2fb3a5c17158b8dcc2797d4
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Adatalapú hibakeresést keresztül a feladat ábra

A feladat ábra: a **figyelés** panel az Azure portál segítségével jelenítheti meg a feladat folyamat. Azt illusztrálja, bemenetek, kimenetek és lekérdezések lépéseit. A feladat ábra segítségével vizsgálja meg a metrikák minden egyes lépést, a problémák elhárításakor gyorsabban elkülöníteni a probléma forrását.

## <a name="using-the-job-diagram"></a>A feladat ábra használatával

Az Azure-portálon alatt a Stream Analytics-feladatot, miközben **támogatási + hibaelhárítás**, jelölje be **feladat ábra**:

![A metrika - hely feladat diagramja](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Válassza ki az összes lekérdezés lépést a lekérdezés-szerkesztő ablakban megfelelő szakaszában talál. A lépés metrika diagramot az oldalon egy alsó ablaktáblán jelennek meg.

![A metrika - alapvető feladat feladat diagramja](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

A partíciók az Azure Event Hubs bemeneti megtekintéséhez válasszon **...** A helyi menü megjelenik. A bemeneti egyesülés is látható.

![A metrika - feladat ábra bontsa ki a partíció](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

A metrika csak egyetlen partícióra diagramot, jelölje ki a partíció csomópont. A metrikák jelennek meg a lap alján.

![A metrika - további metrikák feladat diagramja](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

A metrikák diagramot egyesülés megtekintéséhez jelölje ki az egyesülés csomópontot. Az alábbi ábra mutatja, hogy események nem eldobni vagy módosítani.

![A metrika - feladat ábra rács](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

A metrika értékét, és az idő a részletek megtekintéséhez, majd a diagram.

![Diagram metrikákat a sikertelen feladat - vigye](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Hibaelhárítása mérőszámok segítségével

A **QueryLastProcessedTime** metrika azt jelzi, ha egy adott lépésre adatokat kapott. A topológia megtekintésével is dolgozhat visszafelé a kimeneti processzor, hogy mely lépés nem kapja az adatokat. Ha a lépés nem sikerül adatokat, ugorjon a lekérdezés azt megelőzően. Ellenőrizze, hogy rendelkezik-e az előző lekérdezés lépés egy olyan időkeretet, és ha elegendő idő az megfelelt kimeneti adatokat. (Vegye figyelembe, időpont windows rendszer diagramobjektum a óra.)
 
Ha az előző lekérdezés lépés egy bemeneti processzor, használja a bemeneti metrikák amely választ adhat ezekre a következő célzott kérdésekre. Segít meghatározni, hogy egy feladat van adat lekérésekor a bemeneti forrásból. Ha a lekérdezés particionálva van, vizsgálja meg az összes partíciót.
 
### <a name="how-much-data-is-being-read"></a>Mennyi adatot olvasása?

*   **InputEventsSourcesTotal** olvasható adatok egységek száma. Ha például a blobok száma.
*   **InputEventsTotal** olvasási események száma. Ez a metrika partíciónként érhető el.
*   **InputEventsInBytesTotal** olvasott bájtok száma.
*   **InputEventsLastArrivalTime** minden fogadott események a várólistában levő időpontja frissül.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Az idő soron? Ha tényleges események olvasható, előfordulhat, hogy nem adható ki absztrakt.

*   Az **InputEventsLastPunctuationTime** megadja, hogy mikor adtak ki írásjelek, az idő előrehaladása érdekében. Ha nem jelenik meg absztrakt, adatfolyama is blokkolnánk a hozzáférését.
 
### <a name="are-there-any-errors-in-the-input"></a>Vannak-e hibák a bemeneti adatok?

*   **InputEventsEventDataNullTotal** null adattal rendelkező események száma.
*   **InputEventsSerializerErrorsTotal** nem deszerializálhatók megfelelően események száma.
*   **InputEventsDegradedTotal** a szerializálás megszüntetése nem problémába ütközött események száma.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Események eldobott vagy igazítva?

*   **InputEventsEarlyTotal** eseményeket, amelyek egy alkalmazás időbélyeg előtt a magas vízjel száma.
*   **InputEventsLateTotal** eseményeket, amelyek egy alkalmazás időbélyeg után a magas vízjel száma.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** a szám események eldobása előtt a feladat kezdési időpont.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Amelyek azt alá tartozó az adatok olvasása közben?

*   **InputEventsSourcesBackloggedTotal** jelzi, hogy hány több üzenetet kell az Event Hubs és Azure IoT Hub bemeneti adatok olvasását.


## <a name="get-help"></a>Segítségkérés
Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések
* [A Stream Analytics bemutatása](stream-analytics-introduction.md)
* [A Stream Analytics használatába](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-feladatok méretezése](stream-analytics-scale-jobs.md)
* [Stream Analytics lekérdezési nyelvi referencia](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [A Stream Analytics felügyeleti REST API-referencia](https://msdn.microsoft.com/library/azure/dn835031.aspx)

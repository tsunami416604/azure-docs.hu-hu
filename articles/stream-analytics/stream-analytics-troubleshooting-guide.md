---
title: Azure Stream Analytics a hibaelhárítási útmutatója
description: Ez a cikk ismerteti a technikák hibaelhárítása az Azure Stream Analytics-feladatok, kapcsolatok, bemenetek, kimenetek, lekérdezések és adatokat.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 2eefabcc0484fca0e6e3ad1dd5037684a759d010
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850446"
---
# <a name="troubleshooting-guide-for-azure-stream-analytics"></a>Azure Stream Analytics a hibaelhárítási útmutatója

Első ránézésre kell egy összetett elérhető Azure Stream Analytics hibaelhárítási is megjelenhetnek. Után számos felhasználó dolgozik, ez az útmutató a folyamatot egyszerűbbé és távolítsa el a bemenetek, kimenetek, lekérdezések és funkciók a munka bizonytalanságát létrehoztunk.

## <a name="troubleshoot-your-stream-analytics-job"></a>A Stream Analytics-feladat hibaelhárítása

A legjobb eredmény érdekében a Stream Analytics-feladat hibaelhárítása kövesse az alábbi iránymutatásokat:

1.  A kapcsolat ellenőrzése:
    - Ellenőrizze a kapcsolatot bemenetekhez és kimenetekhez használatával a **kapcsolat tesztelése** az egyes bemeneti és kimeneti gombra.

2.  Vizsgálja meg a bemeneti adatokat:
    - Győződjön meg arról, hogy bemeneti adat áramlik az Eseményközpont, használja a [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) (Ha használja az Event Hubs bemeneti) Azure Event Hubs való kapcsolódáshoz.  
    - Használja a [ **mintaadatok** ](stream-analytics-sample-data-input.md) egyes bemeneti gombra, és töltse le a bemeneti adatot.
    - Vizsgálja meg a mintaadatokat az adatok az alakzat megértése: a séma és a [adattípusok](https://msdn.microsoft.com/library/azure/dn835065.aspx).

3.  A lekérdezés tesztelése:
    - Az a **lekérdezés** lapon a **tesztelése** a lekérdezés tesztelése gombra, és használja a letöltött mintaadatok [a lekérdezés teszteléséhez](stream-analytics-test-query.md). Vizsgálja meg az esetleges hibákat, és próbálja meg kijavítani őket.
    - Ha [ **Timestamp By**](https://msdn.microsoft.com/library/azure/mt573293.aspx), ellenőrizze, hogy az események időbélyegeket nagyobb, mint a [feladat kezdési ideje](stream-analytics-out-of-order-and-late-events.md).

4.  A lekérdezés Debug:
    - Építse újra a lekérdezést fokozatosan egyszerű select utasítás összetettebb összesítések lépések segítségével. A lekérdezés logika részletes készítéséhez használható [WITH](https://msdn.microsoft.com/library/azure/dn835049.aspx) záradékban.
    - Használjon [SELECT INTO](stream-analytics-select-into.md) hibakeresési lekérdezések lépéseit.

5.  Közös nehézségek, például a kiküszöbölése:
    - A [ **ahol** ](https://msdn.microsoft.com/library/azure/dn835048.aspx) záradék a lekérdezés a program kiszűri összes eseményt, és meggátolja, hogy a kimenetet létrehozása folyamatban.
    - A [ **TÍPUSKONVERZIÓ** ](https://msdn.microsoft.com/azure/stream-analytics/reference/cast-azure-stream-analytics) meghiúsul, a feladat sikertelenségét okozó működik. Típus típuskonverzió hibák elkerülése érdekében használja [ **TRY_CAST** ](https://msdn.microsoft.com/azure/stream-analytics/reference/try-cast-azure-stream-analytics) helyette.
    - Ablak funkciók használata esetén várja meg a teljes ablakidőtartam egy a lekérdezés eredményének megtekintéséhez.
    - Az időbélyeg események megelőzi a feladat kezdési időpontja, és ezért események eldobott.

6.  Használja az esemény Rendezés:
    - Ha az előző lépések működött, folytassa a **beállítások** panelhez, és válassza [ **esemény rendelési**](stream-analytics-out-of-order-and-late-events.md). Győződjön meg arról, hogy ez a házirend beállítása adott esetben mi teljesen logikus. A házirend *nem* alkalmazni a **tesztelése** a lekérdezés tesztelése gombra. Az eredménye egy különbségének a tesztelés böngészőn és a feladat futtatásával éles környezetben.

7.  Hibakeresési metrikák használatával:
    - Ha nincs kimenet (a lekérdezés alapján) várható időtartama után beszerezni, próbálja meg a következőket:
        - Nézze meg [ **figyelési metrikákat** ](stream-analytics-monitoring.md) a a **figyelő** fülre. Az értékek összesítése, mert a metrikák késnek, néhány perc múlva.
            - Ha a bemeneti események > 0, a feladat a tudják olvasni a bemeneti adatok. Ha a bemeneti események nem > 0, majd:
                - Hogy az adatforrás tartozik-e érvényes adatokat, ellenőrizze, hogy használatával [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Ez az ellenőrzés vonatkozik, ha a feladat az Event Hubs használja bemeneti adatként.
                - Ellenőrizze, hogy találhatók-e az adatok szerializálási formátum és adatok kódolást várt módon.
                - Ha a feladat az Eseményközpontok használ, ellenőrizze, hogy az üzenet törzsét *Null*.
            - Ha adatok konvertálási hibák > 0 és kúszó, a következő okai lehetnek:
                - Előfordulhat, hogy a feladat nem deszerializálható a események.
                - Az esemény-séma nem egyeznek a definiálva, vagy a várt séma a lekérdezésben az események.
                - Az egyes mezőit a adattípusokat az esemény nem egyeznek elvárásainak.
            - Ha futásidejű hibák > 0, az azt jelenti, hogy a feladat tud adatfogadás, de a lekérdezés feldolgozása közben hibákat generál.
                - Találja a hibák, a [naplók](../azure-resource-manager/resource-group-audit.md) és szűrést végezni *sikertelen* állapotát.
            - Ha InputEvents > 0 és OutputEvents = 0, az azt jelenti, hogy a következők egyike igaz:
                - A lekérdezés feldolgozása nulla kimeneti eseményt eredményezett.
                - Események mezők lehet vagy helytelen formátumú, ami azt eredményezi, nulla kimeneti lekérdezés feldolgozása után.
                - A feladat nem tudta elküldik az adatokat a [kimeneti fogadóját](stream-analytics-select-into.md) kapcsolatot vagy a hitelesítés okokból.
        - A korábban említett hiba esetekben műveletek naplóüzenetek magyarázó további információt talál (többek között, mi történik), kivéve azokat az eseteket, ahol a lekérdezés logika program kiszűri az összes esemény. Több esemény feldolgozását hibák állít elő, ha a Stream Analytics naplózza az első három hibaüzenetek azonos típusú műveletek naplókba 10 percen belül. Azután letiltja a további azonos hibát jelzett üzenetet kapja, "Hibák hogy túl gyorsan ezek van letiltva."

8. Hibakeresési naplózási és diagnosztikai naplók segítségével:
    - Használjon [naplók](../azure-resource-manager/resource-group-audit.md), és a szűrő azonosításához, és a hibakeresési hibákat.
    - Használjon [diagnosztikai naplók feladat](stream-analytics-job-diagnostic-logs.md) azonosításához, és a hibakeresési hibákat.

9. Vizsgálja meg a kimenetek:
    - Ha a feladat állapota: *futtató*, attól függően, hogy meghatározzák a lekérdezésben, láthatja, hogy a kimenet a fogadó adatforrás mennyi.
    - Ha nem látható, hogy egy adott kimeneti típust kimenetek, átirányítja őket, amelyek kevésbé összetett, például egy Azure Blob kimeneti típusát. Tártallózó használatával ellenőrizze, hogy a kimenet látható. Győződjön meg arról, hogy kimenetében szabályozási korlátai vannak megakadályozza az adatok fogadása.

10. Adatfolyam elemzés használata a feladat ábra metrikák:
    - Adatfolyam elemzése és a problémák azonosításához használja a [metrikákat a feladat ábra](stream-analytics-job-diagram-with-metrics.md).

11. Nyissa meg a támogatási esetet:
    - Végül, ha minden más meghiúsul, nyissa meg a Microsoft támogatási esetet, az előfizetés-azonosító használatával, amely tartalmazza a feladatot.

## <a name="get-help"></a>Segítségkérés

Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

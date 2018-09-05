---
title: Az Azure Stream Analytics – hibaelhárítási útmutató
description: Ez a cikk ismerteti a technikák hibaelhárítása az Azure Stream Analytics-feladatok, kapcsolatok, bemenetek, kimenetek, lekérdezések és adatokat.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: b1b5d0af3f2b149959bcb97ddaf29ba2fe1f4668
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702221"
---
# <a name="troubleshooting-guide-for-azure-stream-analytics"></a>Az Azure Stream Analytics – hibaelhárítási útmutató

Az Azure Stream Analytics hibáinak elhárítása bonyolult erőfeszítés első ránézésre kell is megjelenhetnek. Után több felhasználó dolgozik, ez az útmutató segítségével folyamatot, és a bemenetek, kimenetek, lekérdezések és funkciók érvényesíthetőek hoztunk létre.

## <a name="troubleshoot-your-stream-analytics-job"></a>A Stream Analytics-feladat hibaelhárítása

A legjobb eredmény érdekében a Stream Analytics-feladat hibaelhárítása használja az alábbi irányelveket:

1.  A kapcsolat ellenőrzése:
    - Ellenőrizze a csatlakozási bemenetekhez és kimenetekhez a **kapcsolat tesztelése** az egyes bemeneti és kimeneti gombra.

2.  Vizsgálja meg a bemeneti adatok:
    - Győződjön meg arról, hogy a bemeneti adatok beérkeznek Event Hub, használja a [Service Bus Explorerrel](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) szeretne csatlakozni az Azure Event Hubs (ha az Event Hub-bemenet használata esetén).  
    - Használja a [ **mintaadatok** ](stream-analytics-sample-data-input.md) minden egyes bemenet gombra, és töltse le a bemeneti mintaadatokat.
    - Vizsgálja meg a mintaadatokat az adatok megértéséhez: a séma és a [adattípusok](https://msdn.microsoft.com/library/azure/dn835065.aspx).

3.  A lekérdezés teszteléséhez:
    - Az a **lekérdezés** lapján a **tesztelése** gombra a lekérdezés teszteléséhez, és a letöltött mintaadatok használatával [a lekérdezés teszteléséhez](stream-analytics-test-query.md). Vizsgáljon meg minden hibát, és próbálja meg kijavítani a őket.
    - Ha [ **Timestamp By**](https://msdn.microsoft.com/library/azure/mt573293.aspx), győződjön meg arról, hogy az események időbélyege későbbi, mint a [feladat kezdési ideje](stream-analytics-out-of-order-and-late-events.md).

4.  A lekérdezés Debug:
    - Építse újra a lekérdezést fokozatosan az egyszerű kiválasztási utasításoktól összetettebb összesítések lépések segítségével. Építse fel a lekérdezés logikája lépésről lépésre, használjon [WITH](https://msdn.microsoft.com/library/azure/dn835049.aspx) záradékot.
    - Használat [a SELECT INTO](stream-analytics-select-into.md) hibakeresése a lekérdezés lépéseit.

5.  Megszüntetheti a közös buktatókat, például:
    - A [ **ahol** ](https://msdn.microsoft.com/library/azure/dn835048.aspx) a lekérdezésben záradék kiszűrte az összes esemény meggátolja, hogy a kimenetet generált.
    - A [ **CAST** ](https://msdn.microsoft.com/azure/stream-analytics/reference/cast-azure-stream-analytics) függvény futása, a feladat sikertelenségét okozó. Írja be a cast hibák elkerülése érdekében használja [ **TRY_CAST** ](https://msdn.microsoft.com/azure/stream-analytics/reference/try-cast-azure-stream-analytics) helyette.
    - Ablakfüggvények használatakor, várjon, amíg a teljes ablak időtartamot a lekérdezés kimenetének megtekintéséhez.
    - Az események időbélyege megelőzi a feladat megkezdési ideje, és ezért azokat a rendszer eldobja.

6.  Használja az események rendezése:
    - Ha az előző lépések megfelelően működtek, ugorjon a **beállítások** panelhez, és válassza [ **események rendezése**](stream-analytics-out-of-order-and-late-events.md). Győződjön meg arról, hogy ez a szabályzat konfigurálva van értelme mi ebben az esetben. A házirend *nem* használata esetén alkalmazza a **tesztelése** gombra a lekérdezés teszteléséhez. Ez az eredmény egy különbség a tesztelés böngészőben és a feladat az éles üzemben futó.

7.  Hibakeresési metrikák használatával:
    - Ha nincs kimenet (lekérdezés alapján) várt időtartam után, megpróbálkozhat a következőkkel:
        - Tekintse meg [ **figyelési metrikákat** ](stream-analytics-monitoring.md) a a **figyelő** fülre. Összesítjük az értékeket, mert a metrikák késnek, néhány perc alatt.
            - Ha a bemeneti események > 0, a feladat a bemeneti adatok olvashatja. Ha bemeneti események nem > 0, majd:
                - Hogy az adatforrás tartozik-e érvényes adatokat, ellenőrizze az azt [Service Bus Explorerrel](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Ez az ellenőrzés érvényes, ha a feladat használ Event Hub bemeneti adatként.
                - Ellenőrizze, hogy az adatok szerializálási formátum és az adatok kódolásának vannak-e a várt módon.
                - Ha a feladat egy Eseményközpontba, ellenőrizze, hogy van-e az üzenet törzse *Null*.
            - Ha az Adatátalakítási hibák > 0 nagyobb és emelkedik, a következő okai lehetnek:
                - A feladat nem tudja deszerializálni az eseményeket.
                - Az esemény sémája nem egyeznek a meghatározott vagy a várt séma a lekérdezésben az események.
                - Az egyes mezőit a adattípusokat az lehet, hogy nem egyezik meg elvárásainak.
            - Ha a futásidejű hibák > 0, az azt jelenti, hogy a feladat fogadhat az adatokat, de hibáinak generáló a lekérdezés feldolgozása során.
                - A hibák megkereséséhez nyissa meg a [Auditnaplók](../azure-resource-manager/resource-group-audit.md) és szűrheti a *sikertelen* állapotát.
            - Ha InputEvents outputevents értéke pedig > 0 = 0, az azt jelenti, hogy az alábbiak egyike igaz:
                - A lekérdezés feldolgozása nulla kimeneti eseményt eredményezett.
                - Események vagy a helytelen formátumú, nulla kimenetet eredményez a lekérdezés feldolgozása után lehet.
                - A feladat nem tudott adatokat a [kimeneti fogadó](stream-analytics-select-into.md) kapcsolat vagy a hitelesítési okokból.
        - A korábban említett hiba esetekben a műveletnaplók üzenetei további részleteket (beleértve a Mi történik), kivéve azokban az esetekben, ahol a lekérdezés logikája minden eseményt kiszűrt. Több esemény feldolgozása hibákat okoz, ha a Stream Analytics az első három hibaüzenetek azonos típusú naplók 10 percen belül. Ezután elrejti a további hibákat jelenik meg: "Hiba történik túl gyorsan ezek elrejtéséről."

8. Hibakeresési naplózási és diagnosztikai naplók segítségével:
    - Használat [Auditnaplók](../azure-resource-manager/resource-group-audit.md), és a szűrőt a azonosíthatja és elháríthatja a hibákat.
    - Használat [feladat-diagnosztikai naplók](stream-analytics-job-diagnostic-logs.md) alapján azonosíthatja és elháríthatja a hibákat.

9. Vizsgálja meg a kimeneteket:
    - Ha a feladat állapota: *futó*, attól függően, az időtartam, amelyek meghatározzák a lekérdezés, láthatja, hogy a kimenet a fogadó adatforrásban.
    - Ha nem látja a kimenetek, amelyek egy adott kimeneti típus, átirányítja őket egy kevésbé összetett, például az Azure Blob kimeneti típus. Storage Explorer használatával ellenőrizze, hogy látható-e a kimenet. Ezenkívül ellenőrizze, hogy kimenetében szabályozási korlátok megakadályozza, hogy az adatok fogadását.

10. Az adatfolyam-elemző használata a feladat ábra metrikák:
    - Az adatfolyam elemzését, és azonosíthatja a problémákat, használja a [feladatábra és metrikák](stream-analytics-job-diagram-with-metrics.md).

11. Nyisson meg egy támogatási esetet:
    - Végül megoldás sem működik, ha a Microsoft támogatási eset nyitása a SubscriptionID, amely tartalmazza a feladat használatával.

## <a name="get-help"></a>Segítségkérés

További segítségre van szüksége, próbálja meg [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

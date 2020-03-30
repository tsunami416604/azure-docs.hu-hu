---
title: A környezetben lévő adatmegőrzés ismertetése – Azure Time Series Insight | Microsoft dokumentumok
description: Ez a cikk két olyan beállítást ismertet, amelyek az Azure Time Series Insights-környezetben az adatmegőrzést szabályozzák.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/10/2020
ms.custom: seodec18
ms.openlocfilehash: 34cf1e91b1fe5aae516c77bf2c280dfe70000611
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894752"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>Az Azure Time Series Insights adatmegőrzési adatainak megismerése

Ez a cikk két elsődleges beállítást ismertet, amelyek hatással vannak az adatok megőrzésére az Azure Time Series Insights környezetben.

## <a name="video"></a>Videó

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>Az alábbi videó összefoglalja a Time Series Insights adatmegőrzési adatait, és azt, hogy hogyan tervezd meg.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Az Azure Time Series Insights-környezetek mindegyike rendelkezik egy beállítással, amely szabályozza **az adatmegőrzési időt.** Az érték 1 és 400 nap között tart. Az adatok törlődnek a környezeti tárolókapacitás vagy a megőrzési időtartam alapján, amelyik előbb következik be.

Emellett az Azure Time Series Insights-környezetben egy **tárolási korlát túllépte a viselkedési** beállítást. Szabályozza a be- és törlési viselkedést, amikor egy környezet maximális kapacitása elérhető. A konfigurálássorán két viselkedés közül választhat:

- **Régi adatok törlése** (alapértelmezett)  
- **Be- és be- és betolatás szüneteltetése**

> [!NOTE]
> Alapértelmezés szerint új környezet létrehozásakor a megőrzés a **régi adatok kiürítésére**van konfigurálva. Ez a beállítás szükség szerint válthat az Azure Portal on the Configure (A Time Series Insights környezet **konfigurálása)** lapon az Azure Portal használatával történő létrehozási idő után.
> * Az adatmegőrzési házirendek konfigurálásáról a [Time Series Insights adatmegőrzéskonfigurálása](time-series-insights-how-to-configure-retention.md)című témakörben olvashat.

Mindkét adatmegőrzési szabályzatot az alábbiakban részletesebben ismertetjük.

## <a name="purge-old-data"></a>Régi adatok törlése

- **Régi adatok törlése** az Azure Time Series Insights-környezetek alapértelmezett beállítása.  
- **A régi adatok törlése** akkor ajánlott, ha a felhasználók mindig szeretnék, hogy a *legfrissebb adatok* a Time Series Insights környezetben.
- A **régi adatok kiürítése** beállítás *törli* az adatokat, ha a környezet korlátait (megőrzési idő, méret, vagy a szám, amelyik előbb éri el). A megőrzés alapértelmezés szerint 30 napra van állítva.
- A legrégebbi bevitt adatok törlődnek először (a "First In First Out" megközelítés).

### <a name="example-one"></a>Példa egy

Vegyünk egy példa környezet megőrzési viselkedés **folytatása a be-és a régi adatok törlése:**

**Az adatmegőrzési idő** 400 napra van állítva. **A kapacitás** S1 egységre van állítva, amely 30 GB teljes kapacitást tartalmaz. Tegyük fel, hogy a bejövő adatok átlagosan napi 500 MB-ra halmozódnak fel. Ez a környezet csak 60 nap nyi adat megőrzési értékben, tekintettel a bejövő adatok arányára, mivel a maximális kapacitás 60 nap alatt érhető el. A bejövő adatok a következőképpen halmozódnak fel: 500 MB naponta x 60 nap = 30 GB.

A 61. napon a környezet a legfrissebb adatokat jeleníti meg, de törli a legrégebbi, 60 napnál régebbi adatokat. A kiürítés helyet ad az új adatfolyam-továbbításnak, így az új adatok továbbra is felderíthetők. Ha a felhasználó hosszabb ideig szeretné megőrizni az adatokat, további egységek hozzáadásával növelheti a környezet méretét, vagy kevesebb adatot adhat le.  

### <a name="example-two"></a>Példa két

Fontolja meg egy környezet is konfigurált megőrzési viselkedés **Folytassa a be-és régi adatok törlése.** Ebben a példában **az adatmegőrzési idő** 180 napos alacsonyabb értékre van beállítva. **A kapacitás** S1 egységre van állítva, amely 30 GB teljes kapacitást tartalmaz. Annak érdekében, hogy az adatok tárolása a teljes 180 nap, a napi bejövő kapcsolat nem haladhatja meg a 0,166 GB (166 MB) naponta.  

Ha a környezet napi be- és écsaránya meghaladja a napi 0,166 GB-ot, az adatok 180 napig nem tárolhatók, mivel egyes adatok törlődnek. Vegye figyelembe ezt a környezetet egy forgalmas időkeretben. Tegyük fel, hogy a környezet be- és écsaránya átlagosan napi 0,189 GB-ra emelkedhet. Ebben a forgalmas időkeretben körülbelül 158 nap nyi adat őrződik meg (30 GB/0,189 = 158,73 nap megőrzés). Ez az idő kevesebb, mint a kívánt adatmegőrzési időkeret.

## <a name="pause-ingress"></a>Be- és be- és betolatás szüneteltetése

- A **szüneteltetett be- és rezisztencia** beállítás célja annak biztosítása, hogy az adatok ne törlődjenek, ha a méret- és számlálási korlátokat a megőrzési időszak előtt eléri.  
- **A kiürítés szüneteltetése** további időt biztosít a felhasználók számára, hogy növeljék a környezet kapacitását, mielőtt az adatok törlődnek a megőrzési időszak megsértése miatt.
- Segít megvédeni az adatvesztés, de lehetőséget teremt a legutóbbi adatok elvesztését, ha a be- ésé, hogy az eseményforrás megőrzési időszakán túl szünetel.
- Azonban, ha egy környezet maximális kapacitása elérte, a környezet szünetelteti az adatok be- és beutazását, amíg a következő további műveletek nem történnek meg:

   - Növeli a környezet maximális kapacitását, hogy további méretezési egységeket adjon hozzá a Time Series Insights környezet méretezése című részben [leírtak szerint.](time-series-insights-how-to-scale-your-environment.md)
   - Az adatmegőrzési időszak elérése kor, és az adatok törlődnek, így a környezet a maximális kapacitás a.

### <a name="example-three"></a>Példa három

Fontolja meg egy olyan környezetet, ahol a megőrzési viselkedés **a be- és be- és akta szüneteltetésére**van konfigurálva. Ebben a példában az **adatmegőrzési időszak** 60 napra van konfigurálva. **A kapacitás** az S1 három (3) egységére van beállítva. Tegyük fel, hogy ez a környezet naponta 2 GB-os adatokat tartalmazó adatokat hoz. Ebben a környezetben a bejövő támadások szünetelnek, amint elérte a maximális kapacitást.

Abban az időben a környezet ugyanazt az adatkészletet jeleníti meg, amíg a betöltés folytatódik, vagy amíg **a további be- ésé,** hogy engedélyezve van (amely kiüríti a régebbi adatokat, hogy helyet adjon az új adatok).

Amikor a be- és visszatöltés folytatódik:

- Adatfolyamok az eseményforrás által kapott sorrendben
- Az események indexelése az időbélyegük alapján lesz, kivéve, ha túllépte az eseményforrás adatmegőrzési szabályzatait. Az eseményforrás-megőrzési konfigurációval kapcsolatos további információkért az [Eseményközpontok – gyakori kérdések](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Be kell állítania riasztásokat, hogy értesítést, hogy ne lépjen fel a szüneteltetése. Adatvesztés lehetséges, mivel az alapértelmezett megőrzési 1 nap az Azure-eseményforrások. Ezért a be- és énekaután valószínűleg elveszíti a legfrissebb adatokat, kivéve, ha további műveletet hoz. Növelnie kell a kapacitást, vagy át kell váltania a **viselkedést a régi adatok kiürítése** az adatvesztési potenciál elkerülése érdekében.

Az érintett eseményközpontokban fontolja meg az **Üzenetmegőrzés** tulajdonság módosítását az adatvesztés minimalizálása érdekében, amikor a time series insightsban szüneteltetés történik.

[![Az eseményközpont üzenetének megőrzése.](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

Ha nincs enek tulajdonságok konfigurálva az eseményforráson ( ),`timeStampPropertyName`a Time Series Insights alapértelmezés szerint az eseményközpontba érkezés időbélyege az X tengely. Ha `timeStampPropertyName` valami másra van konfigurálva, a `timeStampPropertyName` környezet az események elemzésekénél az adatcsomagban konfigurált beállításokat keresi.

Olvassa [el A Time Series Insights-környezet méretezése](time-series-insights-how-to-scale-your-environment.md) a környezet további kapacitásnak való befogadása vagy a megőrzés időtartamának növelése érdekében című elolvasását.

## <a name="next-steps"></a>További lépések

- Az adatmegőrzési beállítások konfigurálásával és módosításával kapcsolatos további tudnivalókért tekintse [át az Adatmegőrzés konfigurálása a Time Series Insights alkalmazásban című témakört.](time-series-insights-how-to-configure-retention.md)

- Ismerje meg [a késés ek ként való csökkentéséről az Azure Time Series Insights ban.](time-series-insights-environment-mitigate-latency.md)

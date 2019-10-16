---
title: A szabályozás figyelése és csökkentése a Azure Time Series Insightsban | Microsoft Docs
description: Ez a cikk bemutatja, hogyan figyelheti, diagnosztizálhatja és csökkentheti a késést és a szabályozást okozó teljesítménnyel kapcsolatos problémákat a Azure Time Series Insightsban.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 389e1472e1e1fcbed6dd3b6c1d155199246d877f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332971"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>A Azure Time Series Insights késésének csökkentése a szabályozás monitorozásával és enyhítésével

Ha a bejövő adatmennyiség meghaladja a környezete konfigurációját, késést vagy szabályozást tapasztalhat Azure Time Series Insightsban.

A késleltetés és a szabályozás elkerülése érdekében az elemezni kívánt adatmennyiséghez megfelelően konfigurálja a környezetet.

A következő esetekben valószínűleg késést és szabályozást tapasztal:

- Adjon hozzá olyan eseményforrás, amely olyan régi adatforrásokat tartalmaz, amelyek túllépik a megadott bejövő forgalom arányát (Time Series Insights fel kell vennie).
- További eseményforrás hozzáadása egy környezethez, amely a további eseményekből származó tüske (ami túllépheti a környezet kapacitását).
- Nagy mennyiségű történelmi eseményt küldhet egy adott esemény forrásaként, ami késést eredményez (Time Series Insights fel kell fognia).
- Összekapcsolhatja a telemetria-vel való hivatkozási adatmennyiséget, ami nagyobb méretű eseményt eredményez.  A szabályozás szempontjából egy 32 KB méretű ingressed-adatcsomag 32-eseményként van kezelve, minden méret 1 KB. Az esemény maximálisan megengedett mérete 32 KB; az 32 KB-nál nagyobb adatcsomagok csonkítva vannak.

## <a name="video"></a>Videó

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Ismerkedjen meg Time Series Insights adatforgalom viselkedésével és a tervezésével.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Késés és szabályozás figyelése riasztásokkal

A riasztások segíthetnek a környezete által okozott késési problémák diagnosztizálásában és enyhítésében.

1. A Azure Portal válassza a **riasztások**elemet.

   [@no__t – 1Alerts](media/environment-mitigate-latency/add-alerts.png)](media/environment-mitigate-latency/add-alerts.png#lightbox)

1. Ekkor megjelenik a **szabály létrehozása** panel. Válassza a **Hozzáadás** lehetőséget a **feltétel**alatt.

   [@no__t – 1Add riasztás](media/environment-mitigate-latency/alert-pane.png)](media/environment-mitigate-latency/alert-pane.png#lightbox)

1. Ezután konfigurálja a jel logikájának pontos feltételeit.

   [@no__t – 1Configure jel logikája](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   Innentől kezdve a következő feltételek valamelyikével konfigurálhatja a riasztásokat:

   |Metrika  |Leírás  |
   |---------|---------|
   |**Bejövő fogadott bájtok száma**     | Az esemény forrásaiból beolvasott nyers bájtok száma. A nyers darabszám általában a tulajdonság nevét és értékét tartalmazza.  |  
   |**A bejövő forgalom érvénytelen üzeneteket kapott**     | Az összes Azure-Event Hubs vagy az Azure IoT Hub-eseményforrás által beolvasott érvénytelen üzenetek száma.      |
   |**Bejövő fogadott üzenetek**   | Az összes Event Hubs vagy IoT hub-eseményforrás által olvasott üzenetek száma.        |
   |**Bejövő forgalomban tárolt bájtok**     | A lekérdezéshez tárolt és elérhető események teljes mérete. A méret kiszámítása csak a tulajdonság értékén történik.        |
   |**Beáramló tárolt események** @no__t – 1 |   A lekérdezéshez rendelkezésre álló és elérhető összeolvasztott események száma.      |
   |**Bejövő fogadott üzenet Időeltolódása**    |  Az üzenet az várólistán lévő való beérkezése és a bejövő forgalomban történő feldolgozás időpontja közötti különbség másodpercben kifejezve.      |
   |**Bejövő fogadott üzenetek száma – késés** @no__t – 1|  Az eseményforrás-partíció utolsó várólistán lévő-üzenetének sorszáma és a bejövő forgalomban feldolgozott üzenet sorszáma közötti különbség.      |

   Válassza a **Done** (Kész) lehetőséget.

1. A kívánt jel logikájának konfigurálása után vizuálisan tekintse át a kiválasztott riasztási szabályt.

   [@no__t – 1Ingress](media/environment-mitigate-latency/ingress.png)](media/environment-mitigate-latency/ingress.png#lightbox)

## <a name="throttling-and-ingress-management"></a>Szabályozás és bejövő forgalom kezelése

* Ha a szabályozása folyamatban van, megjelenik a *Bejövő üzenetek fogadásának időkorlátja*, amely arról tájékoztatja, hogy hány másodpercig tart az idősorozat-elemzési környezete, és az üzenet tényleges időpontja (az indexelési idő kizárása nélkül). Appx. 30-60 másodperc).  

  A *bejövő fogadott üzenetek számának késési* értékének is szerepelnie kell, ami lehetővé teszi, hogy megtudja, hány üzenet mögött van.  A legkönnyebben elsajátítható, hogy növelje a környezet kapacitását olyan méretre, amely lehetővé teszi a különbség leküzdését.  

  Ha például úgy látja, hogy S1-környezete 5 000 000-üzenetek késését mutatja be, a környezet méretét akár hat egységre is növelheti, hogy felkészüljön.  A gyorsabb felzárkózás érdekében még tovább növelheti. A felmerülési időszak gyakori esemény, amikor először telepítenek egy környezetet, különösen akkor, ha olyan eseménnyel csatlakozik, amely már rendelkezik eseményekkel, vagy ha nagy mennyiségű előzményt tölt fel.

* Egy másik módszer egy **bejövő tárolt események** riasztásának beállítása > = egy küszöbérték kis mértékben a teljes környezeti kapacitás alatt 2 órán keresztül.  Ez a riasztás segít megismerni, hogy folyamatosan van-e kapacitása, ami nagy valószínűséggel jelzi a késést. 

  Ha például három S1 egység van kiépítve (vagy 2100 eseményt percenként beáramló kapacitással), megadhat egy **bejövő tárolt események** riasztást > = 1900 eseményekhez 2 órán keresztül. Ha folyamatosan túllépi ezt a küszöbértéket, és így aktiválja a riasztást, valószínűleg kiosztották.  

* Ha azt gyanítja, hogy a szabályozása folyamatban van, összehasonlíthatja a **bejövő beérkező üzeneteket** az eseményforrás egressed üzeneteivel.  Ha az Event hub-ba való belépés nagyobb, mint a **bejövő fogadott üzenetek**, a Time Series Insights valószínűleg szabályozva lesznek.

## <a name="improving-performance"></a>A teljesítmény javítása

A szabályozás és a késések csökkentése érdekében a legjobb megoldás a környezet kapacitásának növelésére.

A késleltetés és a szabályozás elkerülése érdekében az elemezni kívánt adatmennyiséghez megfelelően konfigurálja a környezetet. A kapacitásnak a környezethez való hozzáadásával kapcsolatos további információkért lásd: [a környezet skálázása](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Következő lépések

- További információ a [Time Series Insights-környezetben felmerülő problémák diagnosztizálásáról és megoldásáról](time-series-insights-diagnose-and-solve-problems.md).

- Ismerje meg [, hogyan méretezheti Time Series Insights-környezetét](time-series-insights-how-to-scale-your-environment.md).
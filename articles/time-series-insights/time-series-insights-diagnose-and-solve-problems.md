---
title: Problémák diagnosztizálása, hibaelhárítása és megoldása – Azure Time Series Insights | Microsoft Docs
description: Ez a cikk a Azure Time Series Insights-környezetében előforduló gyakori problémák diagnosztizálását, hibaelhárítását és megoldását ismerteti.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 35b330f27ba87aa18ce2c2f275a7b19fdae3cb65
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024415"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>A Time Series Insights-környezet problémáinak diagnosztizálása és megoldása

Ez a cikk a Azure Time Series Insights-környezetben felmerülő problémákat ismerteti. A cikk a megoldás lehetséges okait és megoldásait kínálja.

## <a name="video"></a>Videó

### <a name="learn-about-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>Ismerkedjen meg a gyakori Time Series Insights felhasználói kihívásokkal és megoldásokkal.</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Probléma: nincs megjeleníthető érték

A [Azure Time Series Insights Explorer](https://insights.timeseries.azure.com) számos gyakori okból nem fordulhat elő:

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>A ok: az eseményforrás-adatforrások nem JSON formátumúak

A Azure Time Series Insights csak a JSON-adatkezelést támogatja. JSON-minták esetén olvassa el a [támogatott JSON-alakzatokat](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>B ok: az eseményforrás kulcsa hiányzik egy szükséges engedély

* Az Azure IoT Hub IoT hub esetében meg kell adnia a **szolgáltatás-csatlakozási** engedélyekkel rendelkező kulcsot. Válassza ki a **iothubowner** vagy a **szolgáltatási** házirendeket, mivel mindkettő rendelkezik **Service-csatlakozási** engedéllyel.

   [![IoT Hub szolgáltatás csatlakozási engedélyei](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Az Azure Event Hubsban található Event hub esetében meg kell adnia a **figyelési** engedélyekkel rendelkező kulcsot. Az **olvasási** vagy a **kezelési** szabályzatok egyike sem fog működni, mert mindkettő **figyelési** engedéllyel rendelkezik.

   [az Event hub figyelési engedélyeinek ![](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>C: a megadott fogyasztói csoport nem kizárólag Time Series Insights

IoT hub vagy Event hub regisztrálása esetén fontos beállítani az adatolvasáshoz használni kívánt fogyasztói csoportot. Ez a fogyasztói csoport *nem*osztható meg. Ha a fogyasztói csoport meg van osztva, a mögöttes IoT hub vagy Event hub automatikusan és véletlenszerűen bontja le az egyik olvasót. Adjon meg egy egyedi fogyasztói csoportot, amelyből a Time Series Insights olvasható.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>D ok: a környezet üzembe helyezése megtörtént

Az adat a környezet és az adatai első létrehozása után néhány percen belül megjelenik a Time Series Insights Explorerben.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Probléma: bizonyos adatértékek megjelennek, de hiányoznak az adathalmazok.

Ha az adathalmazok csak részben jelennek meg, és az adatvesztésnek tűnik, érdemes több lehetőséget is megfontolni.

### <a name="cause-a-your-environment-is-being-throttled"></a>A ok: a környezet szabályozása folyamatban van

A [szabályozás](time-series-insights-environment-mitigate-latency.md) gyakori probléma, ha a környezetek kiépítését az adatforrást tartalmazó eseményforrás létrehozása után kell kiépíteni. Az Azure IoT Hub és az Azure Events hubok akár hét napig tárolják az adattárolást. Time Series Insights mindig a legrégebbi eseménysel kezdjen az esemény forrásában (első, első vagy *FIFO*).

5 000 000 ha például egy S1-hez, egy egység Time Series Insights környezethez csatlakozik, akkor a Time Series Insights naponta körülbelül 1 000 000 eseményt olvas be. Előfordulhat, hogy a Time Series Insights öt nap késést tapasztal. Azonban mi történik, a környezet szabályozása folyamatban van.

Ha a régi események szerepelnek az eseményforrásban, kétféleképpen is megközelítheti a szabályozást:

- Módosítsa az eseményforrás megőrzési korlátait, hogy eltávolítsa a régi, Time Series Insightsban nem megjeleníteni kívánt eseményeket.
- A régi események átviteli sebességének növeléséhez hozzon létre nagyobb méretű környezeti méretet (egységek száma). Ha ugyanezt az S1-környezetet egy nap alatt 5 egységre emeli, akkor az előző példában a környezetnek egy napon belül kell megjelennie. Ha az állandó állapotú események termelése naponta 1 000 000 vagy kevesebb eseményt mutat be, akkor az esemény kapacitását egy egységre csökkentheti.

A szabályozási korlátot a környezet SKU-típusa és kapacitása alapján kényszerítjük ki. A környezetben lévő összes eseményforrás megosztja ezt a kapacitást. Ha az IoT hub vagy az Event hub eseményforrás a kényszerített határértékeken felül küldi az adatforrást, a szabályozás és a késés is megtapasztalható.

Az alábbi ábrán egy olyan Time Series Insights-környezet látható, amely egy S1-es SKU-t és 3 kapacitást tartalmaz. Naponta 3 000 000 eseményt tud beáramlani.

[![környezeti SKU jelenlegi kapacitása](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Tegyük fel például, hogy egy környezet az Event hub üzeneteit tölti be. A napi bejövő forgalom aránya ~ 67 000 üzenet. Ez a sebesség percenként körülbelül 46 üzenetet fordít. 

* Ha az Event hub minden üzenete egyetlen Time Series Insightsi eseményre van lelapulva, a szabályozás nem történik meg. 
* Ha az Event hub összes üzenete 100 Time Series Insights eseményre van lelapulva, a 4 600-es eseményeket percenként kell bevezetni. 

A 3 kapacitású S1 SKU-környezetek percenként csak 2 100 eseményt tudnak beáramlani (naponta 1 000 000 esemény = 700 esemény percenként három egységben = 2 100 esemény percenként). 

Ha magas szintű ismereteket kíván arról, hogyan működik az összevonási logika, olvassa el a [támogatott JSON-alakzatok](./how-to-shape-query-json.md)című témakört.

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Ajánlott megoldások a túlzott szabályozáshoz

A késés kijavításához növelje a környezet SKU-kapacitását. További információért olvassa el [a Time Series Insights-környezet skálázása](time-series-insights-how-to-scale-your-environment.md)című témakört.

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>B ok: a korábbi adatmennyiség kezdeti betöltése lassú a bejövő forgalomtól

Meglévő eseményforrás összekapcsolásakor valószínű, hogy az IoT hub vagy az Event hub már tartalmaz adatforrást. A környezet elkezdi az adatok áthúzását az eseményforrás üzenetének megőrzési időszakának elejétől. Ez az alapértelmezett feldolgozás nem bírálható felül. A szabályozást is elvégezheti. A szabályozás hosszabb időt is igénybe vehet, miközben betölti a korábbi adatmennyiséget.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Ajánlott megoldások a nagyméretű kezdeti betöltéshez

A késés kijavítása:

1. Növelje az SKU kapacitását a maximálisan megengedett értékre (ebben az esetben 10). A kapacitás növelését követően a beáramló folyamat sokkal gyorsabban fog megjelenni. A megnövekedett kapacitásért díjat számítunk fel. A rendelkezésre állási diagram megjelenítéséhez a [Time Series Insights Explorerben](https://insights.timeseries.azure.com)tekintheti meg a rendelkezésre állási diagramot.

2. Ha a késés bekövetkezik, csökkentse az SKU kapacitását a normál bejövő forgalom arányára.

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Probléma: az esemény forrásának időbélyeg-tulajdonságának neve beállítás nem működik

Győződjön meg arról, hogy az időbélyeg-tulajdonság neve és értéke megfelel a következő szabályoknak:

* Az időbélyeg-tulajdonság neve megkülönbözteti a kis-és nagybetűket.
* Az eseményforrás egy JSON-karakterláncként kapott időbélyeg-tulajdonságának az _éééé-hh-NNTóó: PP: mm formátumúnak kell lennie. FFFFFFFK formátumban_. Példa **: 2008-04-12T12:53Z**.

Az időbélyeg-tulajdonságnév rögzítésének és megfelelő működésének legegyszerűbb módja a Time Series Insights Explorer használata. A Time Series Insights Explorerben a diagramot használva válasszon ki egy időszakot, miután megadta az időbélyeg-tulajdonság nevét. Kattintson a jobb gombbal a kijelölésre, majd válassza az **események feltárása** lehetőséget.

Az első oszlop fejlécének az időbélyeg-tulajdonságnév kell lennie. A Word **időbélyeg**mellett **($TS)** jelenik meg.

A következő értékek nem jelennek meg:

- *(ABC)* : azt jelzi, hogy Time Series Insights karakterláncként olvassa be az adatértékeket.
- *Naptár ikonja*: azt jelzi, hogy Time Series Insights az adatértéket *datetime*típusúként olvassa.
- *#* : azt jelzi, hogy a Time Series Insights egész számként olvassa az adatértékeket.

## <a name="next-steps"></a>Következő lépések

- Olvassa el a [Azure Time Series Insights késésének enyhítését](time-series-insights-environment-mitigate-latency.md)ismertető témakört.

- Ismerje meg [, hogyan méretezheti Time Series Insights-környezetét](time-series-insights-how-to-scale-your-environment.md).
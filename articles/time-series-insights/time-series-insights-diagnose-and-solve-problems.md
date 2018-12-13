---
title: Az Azure Time Series Insights hibaelhárítása – diagnosztizálhatja és az Azure Time Series Insightsban előforduló problémák megoldására |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan diagnosztizálhatja, hibaelhárításához és találkozhat az Azure Time Series Insights környezetben gyakran előforduló problémák megoldására.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 04/09/2018
ms.custom: seodec18
ms.openlocfilehash: 8e9a2bc8378f71e510d11b3e28438489d620ff6f
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322328"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Diagnosztizálása és megoldása problémák egy részét a Time Series Insights-környezet

Ez a cikk ismerteti az egyes Azure Time Series Insights-környezete fellépő esetleges problémákat. A cikk nyújt, lehetséges okait és megoldásait a feloldásához.

## <a name="video"></a>Videó: 

Ez a videó ismerteti közös Time Series Insights ügyfél kihívások és megoldások:</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-1-no-data-is-shown"></a>1. hiba: Nincs adat jelenik meg

Az adatok nem a [Azure Time Series Insights explorer](https://insights.timeseries.azure.com) számos gyakori okok miatt fordulhat elő:

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>V: OK Eseményadatok forrása nem JSON formátumban

Csak az Azure Time Series Insights támogatja a JSON-adatokat. JSON-minták, lásd: [támogatott JSON-alakzatok](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>"B" OK Az esemény forrása kulcs hiányzik egy szükséges engedély

* Az Azure IoT Hub IoT hub, meg kell adnia a kulcsot, amelynek **szolgáltatás csatlakozása** engedélyeket. Valamelyikét a **iothubowner** vagy **szolgáltatás** házirendek fog működni, mert mindkét **szolgáltatás csatlakozása** engedélyeket.

   ![Az IoT Hub szolgáltatás csatlakozása engedélyek](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)


* Az Azure Event hubs eseményközpontok felé, meg kell adnia a kulcsot, amelynek **figyelésére** engedélyeket. Valamelyikét a **olvasási** vagy **kezelése** házirendek fog működni, mert mindkét **figyelésére** engedélyeket.

   ![Event hub listen engedélyek](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>OK: C: A megadott fogyasztói csoportot nem kizárólagos, a Time Series Insights

Amikor regisztrál egy IoT hubot vagy egy eseményközpontba, fontos a fogyasztói csoportot, amely az adatok olvasásához használni kívánt beállítása. Ezt a fogyasztói csoportot *nem oszthatók meg*. A fogyasztói csoportot meg van osztva, ha az alapul szolgáló IoT hub- vagy event hub véletlenszerűen és automatikusan bontja a kapcsolatot az olvasók egyikét. Adjon meg egy egyedi felhasználói csoport számára a Time Series Insights olvasni.

## <a name="problem-2-some-data-is-shown-but-data-is-missing"></a>2. hiba: Bizonyos adatokat, de hiányzik néhány adat

Amikor az adatok úgy tűnik, hogy lehet elmaradt adatok csak részben jelenik meg, érdemes több lehetőség.

### <a name="cause-a-your-environment-is-being-throttled"></a>V: OK A környezet szabályozás alatt áll

Általános hiba szabályozás akkor, ha a környezetben felhasznált adatokat eseményforrás létrehozása után. Az Azure IoT Hub és az Azure-események Hubs legfeljebb 7 napig tárolhatja az adatokat. A Time Series Insights mindig kezdje a legrégebbi esemény az adatforrás (érkezési sorrendben, vagy *FIFO*). 

Például ha 5 millió eseményt az eseményforrás egy S1 szintű való csatlakozáskor, single-egységet a Time Series Insights környezetet, Time Series Insights körülbelül 1 millió esemény naponta beolvasása. A Time Series Insights öt nappal késést tapasztal, előfordulhat, hogy keresse meg. Azonban mi történik, hogy a környezet szabályozás alatt áll. 

Ha az eseményforrás a régi események, megközelítést a szabályozást a két módszer egyikével:

- Módosítsa az eseményforrás adatmegőrzési korlátok, távolítsa el a régi események, amelyet szeretne megjelenjen a Time Series Insights segítségével.
- Üzembe helyezhető egy nagyobb méretű környezet (egység száma) növelhető a régi események. Az előző példában használja, ha egy nap növeli az azonos környezetben S1 egység öt, a környezetet kell olvasásra egy napon belül. Ha az egyenletes esemény éles üzemben futó 1 millió vagy kevesebb esemény naponta, csökkentheti a eseményhez, és a egy egység kapacitását, után ez behozza.

A szabályozási korlát kényszerítve van a környezet Termékváltozatának típusa és a kapacitás alapján. A környezet összes eseményforrások megosztani a kapacitást. Ha az eseményforrás az IoT hub vagy az eseményközpont leküldi az adatok meghaladják a beállított a kényszerített korlátokat, szabályozás és a késéssel láthatja.

A következő ábrán látható egy Time Series Insights-környezet, amely rendelkezik egy S1 Termékváltozat és a egy 3 kapacitását. Azt is, hogy a bejövő forgalom 3 millió esemény naponta.

![Környezet Termékváltozata jelenlegi kapacitás](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Tegyük fel azt feltételezik, hogy ebben a környezetben fogadnak-e az üzeneteket egy eseményközpontból. A következő ábrán látható, a bejövő forgalom:

![Bejövő forgalom például egy eseményközpont](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

A napi bejövő forgalom ~ 67,000 üzenetek. Ez a díjszabás a rendszer lefordítja arra körülbelül 46 üzenetek percenként. Minden event hub üzenet lett simítva egyetlen Time Series Insights esemény, ha szabályozás nem jelentkezik. Ha minden event hub üzenet 100 Time Series Insights-eseményekre lett simítva, érdemes 4,600 események betöltött percenként. 3 kapacitása S1 Termékváltozat környezetben is csak 2,100 beáramlási események minden perc (1 millió esemény naponta három egységet a percenkénti 700 események 2,100 események / perc). A telepítő szabályozás miatt késéssel láthatja. 

Összefoglaló jellegű ismertetése, hogyan működik az egybesimítás logikai, lásd: [támogatott JSON-alakzatok](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Túl sok szabályozási ajánlott felbontás

A lag javításához a környezet Termékváltozata kapacitásának növelése. További információkért lásd: [a Time Series Insights-környezet skálázása](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>"B" OK Előzményadatok kezdeti betöltési lelassítja a bejövő forgalom

Ha csatlakoztat egy meglévő eseményforrás, akkor valószínű, hogy az IoT hub- vagy event hub-adatokat tartalmaz. A környezet elindítja az adatgyűjtés az eseményforrás üzenet megőrzési időszak kezdetétől fogva. Ez az alapértelmezett feldolgozása, és nem bírálható felül. Is vegye fel a kapcsolatot szabályozás. Szabályozás eltarthat egy ideig, az azt betöltő előzményadatok olvasásra.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Nagy méretű kezdeti támogatunk ajánlott felbontás

A lag elhárításához:

1. Növelje a Termékváltozat-kapacitást, a megengedett maximális értéket (10, ebben az esetben). Miután növeli a kapacitást, a bejövő forgalom folyamat elindul, sokkal gyorsabban feltárkózni. A nagyobb kapacitást díjkötelesek. Milyen gyorsan kölcsönhatásai megjelenítése akár, megtekintheti a rendelkezésre állási diagram a [Time Series Insights explorer](https://insights.timeseries.azure.com). 

2. Amikor a késés naprakész, a normál bejövő forgalom Termékváltozat kapacitása csökkenthető.

## <a name="problem-3-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>3. hiba: A forrás timestamp tulajdonság neve beállítás nem működik

Győződjön meg arról, hogy az időbélyegző-tulajdonság neve és értéke megfelelnek-e a következő szabályok:
* Az időbélyeg-tulajdonság neve megkülönbözteti a kis-és nagybetűket.
* A timestamp tulajdonság értéke, amely az esemény forrásból származnak, JSON-karakterláncot kell rendelkeznie a formátum _éééé-hh-nnTóó: pp:. FFFFFFFK_. Például **2008-04-12T12:53Z**.

Győződjön meg arról, hogy az időbélyegző-tulajdonság neveként rögzített, és megfelelően működik a Time Series Insights explorer használandó legegyszerűbb módja. A Time Series Insights explorer használatával a diagramot válassza ki egy bizonyos idő után az időbélyegző-tulajdonság neve a beírt. Kattintson a jobb gombbal a kijelölt, és válassza a **események tallózása** lehetőséget. 

Az első oszlop fejlécére kell lennie az időbélyegző-tulajdonság neve. A word mellett **időbélyeg**, megtekintheti az **($ts)**. 

Nem kell megjelennie a következő értékeket:
- *(abc)* : Azt jelzi, hogy a Time Series Insights éppen olvas az adatértékek karakterláncként.
- *Naptár ikonra*: Azt jelzi, hogy a Time Series Insights éppen olvas az adatérték, *datetime*.
- *#*: Azt jelzi, hogy a Time Series Insights az adatértékek olvasó egész számként.


## <a name="next-steps"></a>További lépések

- További segítségért beszélgetés indítása a [MSDN-fórum](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) vagy [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). 
- Személyes támogatási lehetőségek, használja a [az Azure-támogatás](https://azure.microsoft.com/support/options/).

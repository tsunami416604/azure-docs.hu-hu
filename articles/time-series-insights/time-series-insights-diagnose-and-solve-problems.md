---
title: Az Azure Time Series Insightsban problémák diagnosztizálása és megoldása |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan diagnosztizálhatja, elhárítása és az Azure Time Series Insights-környezet esetleg felmerülő gyakori problémák megoldásában.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 04/09/2018
ms.openlocfilehash: ef06e7b1abd66a2204ef982943fe24354bd7f122
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52837443"
---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>A Time Series Insights-környezet a problémák diagnosztizálása és megoldása

Ez a cikk ismerteti a Time Series Insights-környezet láthat kapcsolatos problémákat. Lehetséges okait és megoldásait a feloldásához kínál.

## <a name="video"></a>Videó: 

### <a name="in-this-video-we-cover-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>Ez a videó ismerteti közös Time Series Insights ügyfél kihívások és megoldásokkal kapcsolatban.</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-1-no-data-is-shown"></a>1. hiba: Nincs adat látható
Miért nem láthatja az adatokat számos gyakori oka lehet a [Azure Time Series Insights Explorerben](https://insights.timeseries.azure.com):

### <a name="possible-cause-a-event-source-data-is-not-in-json-format"></a>Lehetséges ok v: esemény forrásadatok nem szerepel a JSON-formátumban
Az Azure Time Series Insights támogatja a csak a JSON-adatokat. JSON-minták, lásd: [támogatott JSON-alakzatok](./how-to-shape-query-json.md).

### <a name="possible-cause-b-event-source-key-is-missing-a-required-permission"></a>Lehetséges ok "b" esemény forrása kulcs hiányzik egy szükséges engedély
* Egy IoT hubot, meg kell adnia a kulcsot, amelynek **szolgáltatás csatlakozása** engedéllyel.

   ![IoT Hub szolgáltatás a connect engedély](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   A szabályzatok közül választhat az előző képen látható módon **iothubowner** és **szolgáltatás** akkor működik, mert mindkét **szolgáltatás csatlakozása** engedéllyel.

* Az eseményközpontok felé, meg kell adnia a kulcsot, amelynek **figyelésére** engedéllyel.

   ![Event hub listen engedély](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   A szabályzatok közül választhat az előző képen látható módon **olvasása** és **kezelése** akkor működik, mert egyaránt rendelkezik **figyelésére** engedéllyel.

### <a name="possible-cause-c-the-consumer-group-provided-is-not-exclusive-to-time-series-insights"></a>Lehetséges ok, a fogyasztói csoportot megadott, nem kizárólagos, a Time Series Insights C:
Során, de az IoT Hub eszközregisztrációs vagy egy eseményközpontba adja meg a fogyasztói csoportot, amely az adatok olvasása kell használni. Ezt a fogyasztói csoportot kell **nem** oszthatók meg. A fogyasztói csoportot meg van osztva, ha az alapul szolgáló eseményközpont automatikusan bontja a kapcsolatot az olvasók egy véletlenszerűen. Adjon meg egy egyedi felhasználói csoport számára a Time Series Insights olvasni.

## <a name="problem-2-some-data-is-shown-but-some-is-missing"></a>2 probléma: Néhány adat jelenik meg, de néhány hiányzik
Adatok részben megtekintheti, de az adatok lemaradt, több oka lehet figyelembe venni:

### <a name="possible-cause-a-your-environment-is-getting-throttled"></a>V: a környezet. lehetséges ok szabályozott beolvasása
Ez akkor gyakran okoz problémát, ha a környezetben felhasznált adatok eseményforrás létrehozása után.  Az Azure IoT hub és az események Hubs tárolhatja az adatokat hét nap.  A TSI mindig megkezdi a legrégebbi esemény (FIFO), az eseményforrás belül.  Így ha 5 millió eseményt az eseményforrás egy S1, az egyetlen-egység TSI környezetben való csatlakozáskor a TSI körülbelül 1 millió esemény naponta fogja olvasni.  Ez úgy tűnhet, keresse meg, hogy a TSI első ránézésre öt nappal a késés tapasztalható.  Valójában mi történik, hogy a környezet szabályozva.  Ha az eseményforrás a régi események, megközelítést a két módszer egyikével:

- Az eseményforrás adatmegőrzési korlátok segítségével távolíthatja el a régi események, amelyet szeretne megjelenjen a TSI módosítása
- Növelheti a teljesítményt a régi események üzembe helyezhető egy nagyobb méretű környezet mérete (tekintetében egységek száma).  A fenti példa használatával, ha növeli a, hogy öt egységeket azonos S1 környezetben egy nap, a környezetet kell sugárzott terjed, az adott napon belüli.  Ha az egyenletes állapotú esemény éles üzemben futó 1M vagy kevesebb események/nap, majd csökkentheti a kapacitás egy egység le újra az esemény után azt még szerepelnek.  

A szabályozási korlát kényszerítve van a környezet Termékváltozatának típusa és a kapacitás alapján. A környezet összes eseményforrások megosztani a kapacitást. Ha az eseményforrás az IoT Hub vagy az eseményközpont van küld adatokat a kényszerített korlátozások mellett, szabályozás és a késéssel láthatja.

Az alábbi ábrán látható egy Time Series Insights-környezet, amely rendelkezik egy S1 Termékváltozat és a egy 3 kapacitását. Azt is, hogy a bejövő forgalom 3 millió esemény naponta.

![Környezet Termékváltozata jelenlegi kapacitás](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Tegyük fel például, hogy ebben a környezetben van fürtjét üzeneteket egy eseményközpontból. Figyelje meg a bejövő forgalom az alábbi ábrán látható:

![Bejövő forgalom például egy eseményközpont](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Ahogy az ábrán látható, a napi bejövő forgalom ~ 67,000 üzeneteket. Ez a díjszabás a rendszer lefordítja nagyjából 46 üzenetek percenként. Minden event hub üzenet lett simítva egyetlen Time Series Insights esemény, ha ebben a környezetben nincs szabályozás fog látni. Ha minden event hub üzenet 100 Time Series Insights-eseményekre lett simítva, majd 4,600 eseményeket kell fogyasztanak percenként. 3 kapacitása S1 Termékváltozat környezetben is csak 2,100 beáramlási események percenként (1 millió esemény naponta 3 egységet a percenkénti 700 események 2,100 események száma percenként). Ezért a szabályozás miatt késéssel láthatja. 

Összefoglaló jellegű ismertetése, hogyan működik az egybesimítás logikai, lásd: [támogatott JSON-alakzatok](./how-to-shape-query-json.md).

### <a name="recommended-resolution-steps-for-excessive-throttling"></a>Túl sok szabályozási javasolt megoldási lépések
A lag javításához a környezet Termékváltozata kapacitásának növelése. További információkért lásd: [a Time Series Insights-környezet méretezése](time-series-insights-how-to-scale-your-environment.md).

### <a name="possible-cause-b-initial-ingestion-of-historical-data-is-causing-slow-ingress"></a>"B" kezdeti betöltési lehetséges oka az előzményadatok okozza-e lassú bejövő forgalom
Ha egy meglévő eseményforrás csatlakozik, akkor valószínű, hogy az IoT Hub- vagy event hub már tartalmaz adatokat. A környezet elindítja az adatgyűjtés az eseményforrás üzenet megőrzési időszak kezdetétől fogva.

Ez a viselkedés az alapértelmezett viselkedést, és nem bírálható felül. Is vegye fel a kapcsolatot szabályozás és eltarthat egy ideig, hogy értesülhessen az előzményadatok fürtjét.

#### <a name="recommended-resolution-steps-of-large-initial-ingestion"></a>A nagy méretű kezdeti betöltési javasolt megoldási lépések
Javítsa ki a késés, hajtsa végre az alábbi lépéseket:
1. Növelje a megengedett maximális értéket (ebben az esetben 10) Termékváltozat kapacitása. A kapacitás nő, miután a bejövő forgalom folyamat elindul, másolatot sokkal gyorsabban megelőzésével. Mennyi idő alatt, még elfogja-e keresztül a rendelkezésre állási diagram segítségével megjelenítheti a [Time Series Insights explorer](https://insights.timeseries.azure.com). A nagyobb kapacitást díjkötelesek.
2. Után a késés van szerepelnek, csökkentse a Termékváltozat-kapacitást, térjen vissza a normál bejövő forgalom.

## <a name="problem-3-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>3. hiba: Az eseményforrás *időbélyegző-tulajdonság neveként* beállítás nem működik
Győződjön meg arról, hogy a nevét és értékét megfelelnek-e a következő szabályok:
* Az időbélyegző-tulajdonság neve megkülönbözteti _kis-és nagybetűket_.
* Az időbélyeg tulajdonság értéke, amely egy JSON-karakterlánc, mint az eseményforrás származó kell rendelkeznie a formátum _éééé-hh-nnTóó: pp:. FFFFFFFK_. Például ilyen egy karakterlánc "2008-04-12T12:53Z".

Ügyeljen arra, hogy a legegyszerűbben a *időbélyegző-tulajdonság neveként* rögzített, és megfelelően működik-e használni a TSI Explorerben engedélyezett.  A TSI Explorerben a diagramon, válassza ki egy bizonyos idő után a megadott a *időbélyegző-tulajdonság neveként*.  Kattintson a jobb gombbal a kijelölt, és válassza ki a *események tallózása* lehetőséget.  Az első oszlop fejlécére kell lennie a *időbélyegző-tulajdonság neveként* , és rendelkeznie kell egy *($ts)* mellett a word *időbélyeg*, helyett:
- *(abc)* , azt jelzi, amely a TSI az adatértékek karakterláncként van olvasása
- *Naptár ikonra*, azt jelzi, amely a TSI éppen olvas az adatérték, *dátum és idő*
- *#*, amely azt jelzi a TSI éppen olvas az adatértékek egész számként


## <a name="next-steps"></a>További lépések
- További segítségért beszélgetés indítása a a [MSDN-fórum](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) vagy [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). 
- Is [az Azure-támogatás](https://azure.microsoft.com/support/options/) személyes támogatás lehetőségeket.

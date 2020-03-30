---
title: Problémák diagnosztizálása, elhárítása és megoldása – Azure Time Series Insights | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan diagnosztizálhatja, elháríthatja és oldhatja meg a gyakori problémákat az Azure Time Series Insights környezetben.
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
ms.openlocfilehash: 209df97169c71d910677ffdb2e2b12593882445b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152584"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Problémák diagnosztizálása és megoldása a Time Series Insights környezetben

Ez a cikk ismerteti az Azure Time Series Insights környezetben esetleg felmerülő problémákat. A cikk lehetséges okokat és megoldásokat kínál a megoldáshoz.

## <a name="video"></a>Videó

### <a name="learn-about-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>Ismerje meg a Time Series Insights általános fogyasztói kihívásait és megoldásait.</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Probléma: nem jelenik meg adat

Az Azure [Time Series Insights-kezelőben](https://insights.timeseries.azure.com) több gyakori oka lehet:

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>A ok: az eseményforrás adatai nem JSON formátumúak

Az Azure Time Series Insights csak a JSON-adatokat támogatja. JSON-minták esetén olvassa el [a Támogatott JSON-alakzatok című szöveget.](./how-to-shape-query-json.md)

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>B. ok: az eseményforrás kulcsból hiányzik egy szükséges engedély

* Az Azure IoT Hub-ban egy IoT hub, biztosítania kell a kulcsot, amely **rendelkezik a szolgáltatás csatlakozási** engedélyekkel. Válassza ki az **iothubowner** vagy **a szolgáltatás** szabályzatok, mivel mindkettő **rendelkezik szolgáltatás csatlakozási** engedélyekkel.

   [![Az IoT Hub szolgáltatás csatlakozási engedélyei](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Az Azure Event Hubs eseményközpontjában meg kell adnia a **figyelő** engedélyekkel rendelkező kulcsot. Az **olvasási** vagy **kezelési** házirendek bármelyike működni fog, mert mindkettő **rendelkezik figyelési** engedéllyel.

   [![Eseményközpont figyelési engedélyei](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>C. ok: a megadott fogyasztói csoport nem kizárólag a Time Series Insights számára

Amikor regisztrál egy IoT hubot vagy egy eseményközpontot, fontos, hogy állítsa be az adatok olvasásához használni kívánt fogyasztói csoportot. Ez a fogyasztói csoport *nem osztható meg.* Ha a fogyasztói csoport meg van osztva, az alapul szolgáló IoT hub vagy eseményközpont automatikusan és véletlenszerűen bontja az egyik olvasó. Adjon meg egy egyedi fogyasztói csoportot a Time Series Insights számára, amelyből olvasni lehet.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>D ok: a környezet nemrég lett kiépítve

Az adatok a Time Series Insights-kezelőben a környezet és az adatok első létrehozása után néhány percen belül megjelennek.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Probléma: néhány adat megjelenik, de az adatok hiányoznak

Ha az adatok csak részben jelennek meg, és az adatok úgy tűnik, hogy elmaradnak, érdemes több lehetőséget is figyelembe venni.

### <a name="cause-a-your-environment-is-being-throttled"></a>A ok: a környezet szabályozása folyamatban van

[Szabályozás a](time-series-insights-environment-mitigate-latency.md) gyakori probléma, ha a környezetek kiépítése után hozzon létre egy eseményforrás, amely adatokat. Az Azure IoT Hub és az Azure Events Hubs legfeljebb hét napig tárolja az adatokat. A Time Series Insights mindig az eseményforrás legrégebbi eseményével kezdődik (első be, első vagy *FIFO).*

Ha például 5 millió esemény van egy eseményforrásban, amikor egy S1, egyegyegységes Time Series Insights-környezethez csatlakozik, a Time Series Insights naponta körülbelül 1 millió eseményt olvas fel. Úgy tűnhet, hogy a Time Series Insights öt napos késést tapasztal. Azonban, mi történik, hogy a környezet is fojtják.

Ha régi események vannak az eseményforrásban, kétféleképpen közelítheti meg a szabályozást:

- Módosítsa az eseményforrás adatmegőrzési korlátait, hogy könnyebben eltávolíthassa azokat a régi eseményeket, amelyeket nem szeretne megjeleníteni a Time Series Insights ban.
- Nagyobb környezeti méret (egységek száma) kiépítése a régi események átviteli értékének növelése érdekében. Az előző példában, ha növeli ugyanazt az S1 környezet öt egység egy napra, a környezet kell felzárkózni egy napon belül. Ha az állandósult állapotú eseménytermelés napi 1 millió vagy kevesebb esemény, az esemény kapacitását egy egységre csökkentheti, miután utoléri.

A sávszélesség-szabályozási korlát a környezet termékváltozat-típusa és kapacitása alapján van kényszerítve. A környezetben lévő összes eseményforrás osztozik ezzel a kapacitással. Ha az IoT hub vagy az eseményközpont eseményforrása az adatokat a kényszerített korlátokon túlra lenyomja, szabályozást és késést fog tapasztalni.

Az alábbi ábrán egy Time Series Insights-környezet látható, amely s1 termékváltozattal és 3-as kapacitással rendelkezik. Naponta 3 millió eseményt tud be- és beboldogulni.

[![Környezeti termékváltozat jelenlegi kapacitása](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Tegyük fel például, hogy egy környezet belátja az üzeneteket egy eseményközpontból. A napi be- és visszajáratok aránya ~67 000 üzenet. Ez az arány percenként körülbelül 46 üzenetet jelent. 

* Ha minden egyes eseményközpont-üzenet egyetlen Time Series Insights-eseménybe van összeolvasztva, a szabályozás nem történik meg. 
* Ha minden eseményközpont-üzenet 100 Time Series Insights-eseményre van összeolvasztva, percenként 4600 eseményt kell bevenni. 

Egy S1 Termékváltozat-környezet, amelynek kapacitása 3 csak percenként 2100 eseményt tud be- és be- és beérkezni (1 millió esemény naponta = 700 esemény percenként három egység = 2100 esemény percenként). 

A szövegösszeolvasztási működés magas szintű megértéséhez olvassa el [a Támogatott JSON-alakzatok című témakört.](./how-to-shape-query-json.md)

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Ajánlott felbontások a túlzott szabályozáshoz

A késés kijavításához növelje a környezet termékváltozat-kapacitását. További információért olvassa el [a Time Series Insights-környezet méretezése című részt.](time-series-insights-how-to-scale-your-environment.md)

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>B. ok: a korábbi adatok kezdeti betöltése lelassítja a betöltést

Ha egy meglévő eseményforrást csatlakoztat, valószínű, hogy az IoT hub vagy az eseményközpont már tartalmaz adatokat. A környezet az eseményforrás üzenetmegőrzési időszakának kezdetétől kezdi meg az adatok lekérődése. Ez az alapértelmezett feldolgozás nem bírálható felül. Meglehet fojtani a fojtás. A szabályozás eltarthat egy ideig, amíg felzárkózik, mivel betöltése előzményadatok.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Ajánlott felbontások a nagy kezdeti lenyeléshez

A késés javítása:

1. Növelje a termékváltozat kapacitását a maximálisan megengedett értékre (ebben az esetben 10). A kapacitás növelése után a be- és be- és be- és be- és bekerülési folyamat sokkal gyorsabban kezd felzárkózni. A megnövekedett kapacitásért díjat számítunk fel. A felzárkózás gyorsan való megjelenítéséhez tekintse meg az elérhetőségi diagramot a [Time Series Insights felfedezőben.](https://insights.timeseries.azure.com)

2. Ha a késés felzárkózik, csökkentse a termékváltozat kapacitása a normál be- ésémérték.

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>Probléma: az adatok korábban megvoltak jelenítve, de már nem jelennek meg

Az ÁME már nem adatokat továbbít, de az események továbbra is az Iot Hub vagy az Event Hub szolgáltatásba továbbítódnak

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-updating"></a>A ok: a hub hozzáférési kulcsa újralett létrehozva, és a környezetet frissíteni kell

Ez a probléma akkor fordul elő, ha az eseményforrás létrehozásakor megadott kulcs már nem érvényes. Telemetriai adatokat a központban láthat, de a Time Series Insights ban nem érkezik meg bejövő üzenetek. Ha nem biztos abban, hogy a kulcs újralett-e létrehozva, kereshet az Event Hubs tevékenységnaplójában a "Névtér engedélyezési szabályainak létrehozása vagy frissítése" kifejezésre, vagy az IoT hub "IotHub-erőforrás létrehozása vagy frissítése" kifejezésre keresve.

A Time Series Insights-környezet frissítése az új kulccsal nyissa meg a központi erőforrást az Azure Portalon, és másolja az új kulcsot. Keresse meg az 1SI-erőforrást, és kattintson az Eseményforrások elemre. 

   [![Frissítési kulcs.](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

Válassza ki azokat az eseményforrás(oka)t, amelyekből a betöltés leállt, illessze be az új kulcsot, és kattintson a Mentés gombra.

   [![Frissítési kulcs.](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Probléma: az eseményforrás időbélyegtulajdonság-beállítása nem működik

Győződjön meg arról, hogy az időbélyeg tulajdonság neve és értéke megfelel a következő szabályoknak:

* Az időbélyeg tulajdonság neve a kis- és nagybetűket nem figyelembe vevő.
* Az eseményforrásból JSON-karakterláncként érkező időbélyeg tulajdonság értékének _yyyy-MM-ddTHH:mm:ss formátumúnak kell lennie. FFFFFFFK_. Ilyen például **a 2008-04-12T12:53Z**.

A legegyszerűbb en, hogy az időbélyeg-tulajdonság neve rögzítésre kerül, és megfelelően működik, hogy a Time Series Insights explorer. A Time Series Insights explorer a diagram segítségével válasszon ki egy időszakot az időbélyegtulajdonság nevének megadása után. Kattintson a jobb gombbal a kijelölésre, és válassza az **Események felfedezése** lehetőséget.

Az első oszlopfejlécnek az időbélyeg-tulajdonság nevének kell lennie. Az **Időbélyeg**szó **($ts)** felirat mellett jelenik meg.

A következő értékek nem jelennek meg:

- *(abc)*: Azt jelzi, hogy a Time Series Insights az adatértékeket karakterláncként olvassa.
- *Naptár ikon*: Azt jelzi, hogy a Time Series Insights az adatértéket *datetime-ként*olvassa.
- *#*: Azt jelzi, hogy a Time Series Insights egész számként olvassa az adatértékeket.

## <a name="next-steps"></a>További lépések

- A [késés csökkentése az Azure Time Series Insightsban](time-series-insights-environment-mitigate-latency.md)című információból.

- [Ismerje meg, hogyan méretezheti a Time Series Insights-környezetet.](time-series-insights-how-to-scale-your-environment.md)
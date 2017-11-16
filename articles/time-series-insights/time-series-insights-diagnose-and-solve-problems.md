---
title: "Hibáinak diagnosztizálásához és elhárításához Azure idő adatsorozat insightsban |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan diagnosztizálhatja, hibaelhárításához és Azure idő adatsorozat Insights környezetében előforduló gyakori problémák megoldásában."
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 11/15/2017
ms.openlocfilehash: 4216b245fd480003cfa4a34452f87efade964f8d
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>Hibáinak diagnosztizálásához és elhárításához idő adatsorozat Insights környezetében

## <a name="problem-1-no-data-is-shown"></a>Probléma 1: Adatot nem látható
Miért nem láthatja az adatok több közös okból a [Azure idő adatsorozat Insights Explorer](https://insights.timeseries.azure.com):

### <a name="possible-cause-a-event-source-data-is-not-in-json-format"></a>Lehetséges ok: megfigyelendő események forrásadatok nem JSON formátumban van.
Az Azure idő adatsorozat Insights támogatja a csak a JSON-adatokat. JSON-mintákat, lásd: [támogatott JSON alakzatok](time-series-insights-send-events.md#supported-json-shapes).

### <a name="possible-cause-b-event-source-key-is-missing-a-required-permission"></a>Lehetséges ok b esemény forrás kulcs hiányzik egy szükséges engedéllyel
* Az IoT-központ számára meg kell adnia a kulcsot, amelynek **service csatlakozás** engedéllyel.

   ![Az IoT-központ szolgáltatás a connect engedély](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   A házirendek egyikét az előző ábrának megfelelően **iothubowner** és **szolgáltatás** csatlakoztatás működik, mert mindkét **service csatlakozás** engedéllyel.
   
* Az eseményközpontban, meg kell adnia a kulcsot, amelynek **figyelésére** engedéllyel.

   ![Event hub figyelési engedély](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   A házirendek egyikét az előző ábrának megfelelően **olvassa el** és **kezelése** csatlakoztatás működik, mert mindkét **figyelésére** engedéllyel.

### <a name="possible-cause-c-the-consumer-group-provided-is-not-exclusive-to-time-series-insights"></a>Lehetséges ok, a felhasználói csoportban megadott esetében kizárólag az idő adatsorozat Insights C:
Során am IoT hub-regisztráció vagy egy eseményközpontot adja meg a fogyasztói csoportot, amely használja a rendszer az adatok olvasása. A fogyasztói csoportot kell **nem** osztható meg. A fogyasztói csoportot meg van osztva, ha az alapul szolgáló eseményközpont automatikusan bontja a kapcsolatot az olvasók egy véletlenszerűen. Adjon meg egy egyedi felhasználói csoport, az idő adatsorozat elemzések olvasni.

## <a name="problem-2-some-data-is-shown-but-some-is-missing"></a>2. hiba: Bizonyos adatok jelenik meg, de néhány hiányzik
Adatok részben látható, de az adatok elmaradt van, több oka lehet figyelembe venni:

### <a name="possible-cause-a-your-environment-is-getting-throttled"></a>Lehetséges ok A: a környezet első szabályozva van
A szabályozási korlát a rendszer érvényesíti a környezet SKU típusát és a kapacitás alapján. A környezet összes eseményforrások oszthassák meg a kapacitást. Az IoT-központ vagy az event hubs az eseményforrás beállításban megadott érvényben lévő korlát adatok küldését, ha sávszélesség-szabályozás és a késés témakörben talál.

Az alábbi ábrán egy S1 SKU és 3 kapacitású egy idő adatsorozat Insights-környezetet. Azt is érkező 3 millió esemény naponkénti.

![Környezet SKU aktuális kapacitás](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Tegyük fel például, hogy ebben a környezetben van választásával dolgozhat fel az eseményközpontban lévő üzeneteket. Figyelje meg a bejövő adatok sebességét az alábbi ábrán is látható:

![Az eseményközpontok érkező arány – példa](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Ahogy az ábrán is látható, napi érkező ez ~ 67,000 üzeneteket. Ez az eszköz nagyjából 46 üzenetek percenként. Minden event hub üzenet egybesimított egyetlen alkalommal adatsorozat Insights esemény, ha az ebben a környezetben látja, nincs. Ha minden event hub üzenet egybesimított 100 alkalommal adatsorozat Insights eseményekre, majd 4,600 események kell fogyasztanak percenként. 3 / kapacitású S1 SKU környezet is csak 2,100 érkező jelzések percenként (1 millió esemény naponkénti 700 eseményt küld percenként 3 egység = = 2,100 eseményt küld percenként). Ezért tekintse meg a késés szabályozás miatt. 

Magas szintű megértéséhez logikai egybesimítását működését, tekintse meg a [támogatott JSON alakzatok](time-series-insights-send-events.md#supported-json-shapes).

### <a name="recommended-resolution-steps-for-excessive-throttling"></a>Javasolt megoldás lépései túlzott mértékű sávszélesség-szabályozás
A lag kijavításához növelheti a Termékváltozat környezet. További információkért lásd: [idő adatsorozat Insights környezetét méretezése](time-series-insights-how-to-scale-your-environment.md).

### <a name="possible-cause-b-initial-ingestion-of-historical-data-is-causing-slow-ingress"></a>Lehetséges ok b kezdeti adatfeldolgozást előzményadatoknak lassú érkező okozza.
Ha egy meglévő eseményforrás kapcsolódik, valószínű, hogy az IoT-központ vagy az eseményközpont már tartalmaznak adatokat azt. A környezet elindul, húzza az eseményforrás üzenet megőrzési időszak kezdete adatait.

Ez a viselkedés az alapértelmezett beállítás, és nem bírálható felül. Is végezhetnek a sávszélesség-szabályozás és eltarthat egy darabig a korábbi adatok bevitele szinkronizálásához.

#### <a name="recommended-resolution-steps-of-large-initial-ingestion"></a>A nagy méretű kezdeti adatfeldolgozást ajánlott megoldási lépések
A lag megoldása érdekében tegye a következőket:
1. A Termékváltozat kapacitásbővítés a megengedett maximális értéket (ebben az esetben 10). A kapacitás nő, miután a érkező folyamat elindul, mentése sokkal gyorsabban alatt. Milyen gyorsan meg most elfogja keresztül a rendelkezésre állási diagram jelenítheti az [idő adatsorozat Insights explorer](https://insights.timeseries.azure.com). Van szó, a nagyobb kapacitást.
2. Után a késés naprakész, csökkentheti az SKU kapacitása vissza a normál érkező sebessége.

## <a name="problem-3-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>3. hiba: A eseményforrás *időbélyeg-tulajdonság neve* beállítás nem működik
Győződjön meg arról, hogy a név- és megfelelnek-e a következő szabályokat:
* Az időbélyeg-tulajdonság neve _kis-és nagybetűket_.
* A Timestamp típusú tulajdonság értéke adatforrásból származó az eseményforrás JSON karakterláncként kell rendelkeznie a formátum _éééé-hh-nnTóó: pp:. FFFFFFFK_. Példa ilyen karakterlánc: "2008-04-12T12:53Z".

## <a name="next-steps"></a>Következő lépések
- Ha további segítségre van szüksége, indítsa el a beszélgetés a a [MSDN fórum](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) vagy [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). 
- Is [az Azure támogatási](https://azure.microsoft.com/support/options/) a személyes támogatási lehetőségeket.

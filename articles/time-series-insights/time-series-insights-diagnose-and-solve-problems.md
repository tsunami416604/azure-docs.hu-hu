---
title: "Hibáinak diagnosztizálásához és elhárításához |} Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan adhat hibáinak diagnosztizálásához és elhárításához idő adatsorozat Insights környezetében"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/24/2017
ms.author: venkatja
ms.openlocfilehash: 4e10a009eb67706d927ece5692134d802094cdf9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>Hibáinak diagnosztizálásához és elhárításához idő adatsorozat Insights környezetében

## <a name="i-dont-see-my-data"></a>Az adatok nem látható
Az alábbiakban néhány ok, amiért miért nem láthatja az adatokat az adott környezetben a [Azure idő adatsorozat Insights portálon találja meg](https://insights.timeseries.azure.com).

### <a name="your-event-source-doesnt-have-data-in-json-format"></a>A forrás nincs adatok JSON formátumban
Az Azure idő adatsorozat Insights támogat csak a JSON-adatokat. JSON-mintákat, lásd: [támogatott JSON alakzatok](time-series-insights-send-events.md#supported-json-shapes).

### <a name="when-you-registered-your-event-source-you-didnt-provide-the-key-that-has-the-required-permission"></a>A forrás regisztrálásakor nem adott meg a kulcsot, amely rendelkezik a szükséges engedéllyel
* Az IoT-központ számára meg kell adnia a kulcsot, amelynek **service csatlakozás** engedéllyel.

   ![Az IoT-központ szolgáltatás a connect engedély](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   A házirendek egyikét az előző ábrának megfelelően **iothubowner** és **szolgáltatás** csatlakoztatás működik, mert mindkét **service csatlakozás** engedéllyel.
* Az eseményközpontban, meg kell adnia a kulcsot, amelynek **figyelésére** engedéllyel.

   ![Event hub figyelési engedély](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   A házirendek egyikét az előző ábrának megfelelően **olvassa el** és **kezelése** csatlakoztatás működik, mert mindkét **figyelésére** engedéllyel.

### <a name="the-provided-consumer-group-is-not-exclusive-to-time-series-insights"></a>A megadott felhasználói csoport nincs kizárólagos idő adatsorozat insights szolgáltatáshoz
Az IoT-központ vagy az eseményközpont, a regisztráció során kérjük, adja meg a fogyasztói csoportot, amely használja a rendszer az adatok olvasása. Nem kell osztani a fogyasztói csoportot. Megosztott, ha az alapul szolgáló eseményközpont automatikusan bontja a kapcsolatot az olvasók egy véletlenszerűen.

## <a name="i-see-my-data-but-theres-a-lag"></a>Az adatok látható, de a késés van
Az alábbiakban okok miért részleges adatokat láthatja a környezetében a [idő adatsorozat Insights portálon találja meg](https://insights.timeseries.azure.com).

### <a name="your-environment-is-getting-throttled"></a>A környezet első szabályozva van
A szabályozási korlát a rendszer érvényesíti a környezet SKU típusát és a kapacitás alapján. A környezet összes eseményforrások oszthassák meg a kapacitást. Az IoT-központ vagy az event hubs az eseményforrás beállításban megadott érvényben lévő korlát adatok küldését, ha sávszélesség-szabályozás és a késés témakörben talál.

Az alábbi ábrán egy S1 SKU és 3 kapacitású egy idő adatsorozat Insights-környezetet. Azt is érkező 3 millió esemény naponkénti.

![Környezet SKU aktuális kapacitás](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Tegyük fel, hogy ebben a környezetben van választásával dolgozhat fel az eseményközpontban lévő üzenetek a érkező sebességet az alábbi ábrán is látható:

![Az eseményközpontok érkező arány – példa](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Ahogy az ábrán is látható, napi érkező ez ~ 67,000 üzeneteket. Ez az eszköz nagyjából 46 üzenetek percenként. Minden event hub üzenet egybesimított egyetlen alkalommal adatsorozat Insights esemény, ha az ebben a környezetben látja, nincs. Ha minden event hub üzenet egybesimított 100 alkalommal adatsorozat Insights eseményekre, majd 4,600 események kell fogyasztanak percenként. 3 / kapacitású S1 SKU környezet is csak 2,100 érkező jelzések percenként (1 millió esemény naponkénti 700 eseményt küld percenként 3 egység = = 2,100 eseményt küld percenként). Ezért tekintse meg a késés szabályozás miatt. 

Magas szintű megértéséhez logikai egybesimítását működését, tekintse meg a [támogatott JSON alakzatok](time-series-insights-send-events.md#supported-json-shapes).

#### <a name="recommended-steps"></a>Javasolt lépések
A lag kijavításához növelheti a Termékváltozat környezet. További információkért lásd: [idő adatsorozat Insights környezetét méretezése](time-series-insights-how-to-scale-your-environment.md).

### <a name="youre-pushing-historical-data-and-causing-slow-ingress"></a>Ön előzményadatokat kérdez le, és lassú érkező okozó
Ha egy meglévő eseményforrás kapcsolódik, valószínű, hogy az IoT-központ vagy az eseményközpont már tartalmaznak adatokat azt. Ezért a környezet elindul, húzza az eseményforrás üzenet megőrzési időszak kezdete adatait. 

Ez a viselkedés az alapértelmezett beállítás, és nem bírálható felül. Is végezhetnek a sávszélesség-szabályozás és eltarthat egy darabig a korábbi adatok bevitele szinkronizálásához.

#### <a name="recommended-steps"></a>Javasolt lépések
A lag megoldása érdekében tegye a következőket:
1. A Termékváltozat kapacitásbővítés a megengedett maximális értéket (ebben az esetben 10). A kapacitás nő, miután a érkező folyamat elindul, mentése sokkal gyorsabban alatt. Milyen gyorsan meg most elfogja keresztül a rendelkezésre állási diagram jelenítheti az [idő adatsorozat Insights portálon találja meg](https://insights.timeseries.azure.com). Van szó, a nagyobb kapacitást.
2. Után a késés naprakész, csökkentheti az SKU kapacitása vissza a normál érkező sebessége.

## <a name="my-event-sources-timestamp-property-name-setting-doesnt-work"></a>A forrás *időbélyeg-tulajdonság neve* beállítás nem működik
Győződjön meg arról, hogy a név- és megfelelnek-e a következő szabályokat:
* Az időbélyeg-tulajdonság neve _kis-és nagybetűket_.
* A Timestamp típusú tulajdonság értéke adatforrásból származó az eseményforrás JSON karakterláncként kell rendelkeznie a formátum _éééé-hh-nnTóó: pp:. FFFFFFFK_. Példa ilyen karakterlánc: "2008-04-12T12:53Z".

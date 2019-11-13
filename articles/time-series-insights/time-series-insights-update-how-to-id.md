---
title: Ajánlott eljárások idősorozat-AZONOSÍTÓk kiválasztásához – Azure Time Series Insights | Microsoft Docs
description: Ismerje meg az idősorozat-azonosító Azure Time Series Insights előzetes verzióban való kiválasztásakor ajánlott eljárásokat.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/22/2019
ms.custom: seodec18
ms.openlocfilehash: cf826c47c61e3714a05dd81fe3eea4e6ee0b03f4
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012496"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>A Time Series ID kiválasztására vonatkozó ajánlott eljárások

Ez a cikk összefoglalja az idősorozat-azonosító fontosságát a Azure Time Series Insights előzetes verziójának környezetében, valamint az ajánlott eljárásokat az első kiválasztásához.

## <a name="choose-a-time-series-id"></a>Time Series-azonosító kiválasztása

A Time Series ID kiválasztása hasonlóan olyan partíciókulcsot, egy adatbázis van. Time Series Insights előnézeti környezet létrehozásakor ki kell választani. Ez *egy nem* módosítható tulajdonság. Ez azt ismerteti, hogy miután létrehozott egy Time Series Insights előnézeti környezetet egy idősorozat-AZONOSÍTÓval, az adott környezet esetében nem módosítható. 

> [!IMPORTANT]
> Az idősorozat-azonosító megkülönbözteti a kis-és nagybetűket.

A megfelelő idősorozat-azonosító kiválasztása kritikus. Íme néhány ajánlott eljárás, amelyet követheti:

* Válasszon egy olyan partíciót, amely sok különböző értékkel rendelkezik (például több száz vagy ezer). Sok esetben ez lehet az eszköz azonosítója, az érzékelő azonosítója vagy a címke azonosítója a JSON-ban.
* A Time Series azonosító egyedinek kell lennie a levél csomópont szintjén a [Idősorozat-modell](./time-series-insights-update-tsm.md).
* Ha az eseményforrás egy IoT hub, az idősorozat-azonosító valószínűleg *iothub-kapcsolat-eszköz-azonosító*lesz.
* Az idősorozat-azonosító tulajdonságának Name karakterláncának karakteres korlátja 128. Az idősorozat-azonosító tulajdonságának értékeként a karakteres korlát 1 024.
* Ha hiányzik az idősorozat-azonosító egyedi tulajdonságának értéke, akkor a rendszer null értékként kezeli, és az egyediségi megkötésre vonatkozó szabályt követi.
* Az idősorozat-azonosító legfeljebb *három* fő tulajdonságot választhat. A kombinációjuk egy összetett kulcs lesz, amely az idősorozat AZONOSÍTÓját jelöli.  

  > [!NOTE]
  > A három fő tulajdonságnak karakterláncnak kell lennie.
  > Egyszerre csak egy tulajdonság helyett kell lekérdezni ezt az összetett kulcsot.

Az alábbi forgatókönyvek több Key tulajdonság kiválasztását írják le idősorozat-AZONOSÍTÓként.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>1\. példa: idősorozat-azonosító egyedi kulccsal

* Régi típusú flottája van. Mindegyik egyedi kulccsal rendelkezik.
* Az egyik flottát a **deviceId**tulajdonság egyedileg azonosítja. Egy másik flotta esetében az egyedi tulajdonság a **objectId**. Egyik flotta sem tartalmazza a másik flotta egyedi tulajdonságát. Ebben a példában két kulcsot, **deviceId** és **objectId**választ ki egyedi kulcsként.
* A rendszer null értékeket fogad el, és a tulajdonság jelenlétének hiánya az esemény-adattartalomban null értéknek számít. Ez azt is lehetővé teszi, hogy az adatküldés két eseményforrás számára legyen kezelhető, ahol az egyes eseményforrás-forrásokban lévő adatok egyedi idősorozat-AZONOSÍTÓval rendelkeznek.

### <a name="example-2-time-series-id-with-a-composite-key"></a>2\. példa: idősorozat-azonosító összetett kulccsal

* Az eszközök a azonos flotta belül egyedinek kell lennie több tulajdonságra van szüksége. 
* Az intelligens épületek gyártója és az érzékelők üzembe helyezése minden helyiségben. Minden helyiségben általában ugyanazok az értékek szerepelnek a **sensorId**. Ilyenek például a következők: **sensor1**, **sensor2**és **sensor3**.
* Az épület átfedésben van az alapszintű és a szobai számokkal a **flrRm**tulajdonságban található helyek között. Ezek a számok olyan értékekkel rendelkeznek, mint az **1a**, **2b**és **3A**.
* Van egy tulajdonsága, **helye**, amely olyan értékeket tartalmaz, mint a **Redmond**, a **Barcelona**és a **Tokió**. Az egyediség létrehozásához a következő három tulajdonságot kell kijelölnie idősorozat-azonosító kulcsaiként: **sensorId**, **flrRm**és **Location**.

Példa nyers eseményre:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

A Azure Portal a következőként adhatja meg ezt az összetett kulcsot: 

`[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]`

## <a name="next-steps"></a>Következő lépések

* További információ az [adatmodellezésről](./time-series-insights-update-tsm.md).

* Tervezze meg [Azure Time Series Insights előnézeti környezetét](./time-series-insights-update-plan.md).
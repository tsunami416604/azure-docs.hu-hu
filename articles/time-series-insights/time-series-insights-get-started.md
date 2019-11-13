---
title: Környezet létrehozása – Azure Time Series Insights | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre új Time Series Insights-környezetet a Azure Portal használatával.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: fc3f21c95ad6f4bb033336018dddb9e966e9cc9a
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006903"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Új Time Series Insights-környezet létrehozása az Azure Portalon

Ez a cikk azt ismerteti, hogyan hozható létre új Time Series Insights-környezet a Azure Portal használatával.

Time Series Insights lehetővé teszi az Azure IoT-Event Hubs hubokba való adatforgalom megjelenítésének és lekérdezésének megkezdését percek alatt, így másodpercek alatt lekérdezheti a nagy mennyiségű idősoros adatsorozatot.  A szolgáltatás az IoT-méretezéshez lett tervezve, és képes terabájtos adatmennyiség kezelésére.

## <a name="steps-to-create-the-environment"></a>A környezet létrehozásának lépései

A következő lépések végrehajtásával hozhat létre környezetet:

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).

1. Válassza az **+ erőforrás létrehozása** gombot.

1. Válassza ki a **eszközök internetes hálózata** kategóriát, és válassza a **Time Series Insights**lehetőséget.

   [![a Time Series Insights-környezet létrehozása](media/time-series-insights-get-started/1-new-tsi.png)](media/time-series-insights-get-started/1-new-tsi.png#lightbox))

1. A **Time Series Insights** lapon válassza a **Létrehozás**lehetőséget.

1. Adja meg a szükséges paramétereket. Az alábbi táblázat az egyes paramétereket ismerteti:
   
   [![az Time Series Insights erőforráscsoport létrehozása](media/time-series-insights-get-started/2-create-tsi.png)](media/time-series-insights-get-started/2-create-tsi.png#lightbox)
   
   Beállítás|Ajánlott érték|Leírás
   ---|---|---
   Környezet neve | Egyedi név | Ez a név jelenti a környezetet a [Time Series Explorerben](https://insights.timeseries.azure.com)
   Előfizetést | Az Ön előfizetése | Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amelyik az adott eseményforrás tartalmazza. A Time Series Insights képes automatikusan észlelni az azonos előfizetésben lévő Azure IoT Hub- és Event Hub-erőforrásokat.
   Erőforráscsoport | Új létrehozása vagy meglévő használata | Az erőforráscsoport olyan Azure-erőforrások gyűjteménye, amelyeket együtt használnak. Kiválaszthat egy meglévő erőforráscsoportot, például azt, amely az Event hub-t vagy IoT Hub tartalmazza. Egy újat is létrehozhat, ha az erőforrás nem kapcsolódik a többi erőforráshoz.
   Hely | Az eseményforrás legközelebbi forrása | Lehetőleg válassza ki ugyanazt az adatközpont-helyet, amely tartalmazza az eseményforrás adatait, így elkerülhető, hogy a régióból kifelé irányuló adatáthelyezés során ne legyenek hozzáadva a régiók közötti és a zónák közötti sávszélesség-költségek.
   Tarifacsomag | S1 | Válassza ki a szükséges teljesítményt. A legalacsonyabb költségek és a kezdő kapacitás esetében válassza az S1 elemet.
   Kapacitás | 1 | A kapacitás a szorzó a bejövő forgalom arányára, a tárolókapacitásra és a kiválasztott SKU-ra vonatkozó díjakra vonatkozik.  A környezet kapacitása a létrehozást követően módosítható. A legalacsonyabb költségekhez válasszon 1 kapacitást. 
  
1. A létesítési folyamat megkezdéséhez válassza a **Létrehozás** lehetőséget. Néhány percet is igénybe vehet.

1. Az üzembe helyezési folyamat figyeléséhez válassza az **értesítések** szimbólumot (harang ikon).

   [![tekintse meg az értesítéseket](media/time-series-insights-get-started/3-notifications.png)](media/time-series-insights-get-started/3-notifications.png#lightbox)

    Ha az üzembe helyezés sikeres, a további tulajdonságok konfigurálásához válassza az **erőforrás** megnyitása lehetőséget, állítsa be a biztonságot az adatelérési házirendekkel, az eseményforrás hozzáadásával és egyéb műveletekkel.

1. Az erőforrás- **Áttekintés**lapon válassza a jobb felső sarokban található **rögzítés ikont** , hogy a későbbiekben könnyen hozzáférhessen a Time Series Insights-környezethez.

   [![az Time Series Insights PIN-kód létrehozása az irányítópulton](media/time-series-insights-get-started/4-pin-create.png)](media/time-series-insights-get-started/4-pin-create.png#lightbox)

## <a name="next-steps"></a>Következő lépések

* [Adja meg az adatelérési szabályzatokat](time-series-insights-data-access.md) a környezet biztonságossá tételéhez.

* [Adja hozzá az Event hub-esemény forrását](time-series-insights-how-to-add-an-event-source-eventhub.md) a Azure Time Series Insights-környezethez.

* [Események küldése](time-series-insights-send-events.md) esemény forrását.

* A környezet megtekintése [Time Series Insights Explorerben](https://insights.timeseries.azure.com).

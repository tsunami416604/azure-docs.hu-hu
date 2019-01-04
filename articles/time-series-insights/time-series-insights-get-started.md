---
title: Azure Time Series Insights-környezet létrehozása | Microsoft Docs
description: Ez a cikk ismerteti, hogyan hozzon létre egy új Time Series Insights-környezetet az Azure portal használatával.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.custom: seodec18
ms.openlocfilehash: 74cd56f5a8bfe8717927c13e6bf30eb27b43fbc9
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558531"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Új Time Series Insights-környezet létrehozása az Azure Portalon
Ez a cikk azt ismerteti, hogyan hozhat létre egy új Time Series Insights-környezet az Azure portal használatával.

A Time Series Insights lehetővé teszi a bevezetés jelenítenek meg, és percek alatt az Azure IoT hub és az Event Hubs áramló adatok lekérdezése lehetővé teszi, hogy nagy mennyiségű lekérdezés idősorozat-adatok másodpercek alatt.  Ez az eszközök internetes hálózata (IoT) méretezési csoport lett tervezve, és terabájt adat kezelésére is.

## <a name="steps-to-create-the-environment"></a>A környezet létrehozásának lépései
Kövesse az alábbi lépéseket, olyan környezetet hozhat létre:

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2.  Válassza ki a **+ új** gombra.

3.  Válassza ki a **IOT-** kategóriát, majd válassza ki **Time Series Insights**.

   ![A Time Series Insights-környezet létrehozása](media/time-series-insights-get-started/1-new-tsi.png)

4.  Az a **Time Series Insights** lapon jelölje be **létrehozás**.

5. Adja meg a szükséges paramétereket. A következő táblázat ismerteti az egyes paraméterek:
   
   ![A Time Series Insights-erőforráscsoport létrehozása](media/time-series-insights-get-started/2-create-tsi.png)
   
   Beállítás|Ajánlott érték|Leírás
   ---|---|---
   Környezet neve | Egy egyedi nevet | Ez a név azt a környezetet a [time series Explorerben](https://insights.timeseries.azure.com)
   Előfizetés | Az Ön előfizetése | Ha több előfizetéssel rendelkezik, válassza ki az előfizetést, lehetőleg tartalmazza az eseményforrást. A Time Series Insights képes automatikusan észlelni az azonos előfizetésben lévő Azure IoT Hub- és Event Hub-erőforrásokat.
   Erőforráscsoport | Hozzon létre egy új vagy meglévő használata | Az erőforráscsoport olyan Azure-erőforrások gyűjteménye, amelyeket együtt használnak. Választhat egy meglévő erőforráscsoportot, például azt, amelyik tartalmazza az Eseményközpontot vagy IoT hubot. Vagy egy új teheti meg, ha a többi erőforrást nem kapcsolódik ehhez az erőforráshoz.
   Hely | Az eseményforrás a legközelebbi | Lehetőleg válassza ki az ugyanazon adatokat szolgáltató adatközpont elhelyezkedése, amely tartalmazza az eseményadatok forrása annak érdekében, hogy kerülje a hozzáadott régiók és zónák közötti sávszélességgel kapcsolatos költségek, és hozzá a késés, amikor az adatok áthelyezése a régión kívül.
   Tarifacsomag | S1 | Válassza ki a szükséges teljesítményt. Legalacsonyabb költségek, és alapszintű kapacitás válassza az S1.
   Kapacitás | 1 | A bejövő forgalom, a tárolási kapacitás és a költsége a kiválasztott Termékváltozat vonatkozik szorzó kapacitása.  A környezet kapacitása a létrehozást követően módosítható. Legalacsonyabb költségek válassza az 1 kapacitását. 
  
6. Ellenőrizze **rögzítés az irányítópulton** legjobb könnyedén elérheti a Time Series-környezetet a jövőben.

   ![A Time Series Insights rögzítése az irányítópulton](media/time-series-insights-get-started/3-pin-create.png)

7. Válassza ki **létrehozás** a kiépítési folyamat megkezdéséhez. Eltarthat néhány percig.

8. Az üzembe helyezési folyamat megfigyeléséhez, válassza ki a **értesítések** szimbólum (Harang ikon).

   ![Az értesítések megtekintése](media/time-series-insights-get-started/4-notifications.png)

Amikor az üzembe helyezés sikeres **erőforrás megnyitása** egyéb tulajdonságok konfigurálása, állítsa az adathozzáférési házirendek a biztonsági, adja hozzá a megadott eseményforrásokból és egyéb műveleteket.

## <a name="next-steps"></a>További lépések
* [Adathozzáférési házirendek meghatározása](time-series-insights-data-access.md) a környezet biztonságossá tételéhez.
* [Event Hub-eseményforrás hozzáadása](time-series-insights-how-to-add-an-event-source-eventhub.md) az Azure Time Series Insights-környezethez. 
* [Események küldése](time-series-insights-send-events.md) esemény forrását.
* A környezet megtekintése a [Time Series Insights explorer](https://insights.timeseries.azure.com).

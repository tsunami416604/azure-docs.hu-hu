---
title: Azure Time Series Insights-környezet létrehozása | Microsoft Docs
description: Ez a cikk ismerteti, hogyan hozzon létre egy új Time Series Insights-környezetet az Azure portal használatával.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/01/2019
ms.custom: seodec18
ms.openlocfilehash: 9e074282b477fe620170cd8672dc3d050fce6afe
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67061640"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Új Time Series Insights-környezet létrehozása az Azure Portalon

Ez a cikk azt ismerteti, hogyan hozhat létre egy új Time Series Insights-környezet az Azure portal használatával.

A Time Series Insights lehetővé teszi a bevezetés jelenítenek meg, és percek alatt az Azure IoT hub és az Event Hubs áramló adatok lekérdezése lehetővé teszi, hogy nagy mennyiségű lekérdezés idősorozat-adatok másodpercek alatt.  Ez az eszközök internetes hálózata (IoT) méretezési csoport lett tervezve, és terabájt adat kezelésére is.

## <a name="steps-to-create-the-environment"></a>A környezet létrehozásának lépései

Kövesse az alábbi lépéseket, olyan környezetet hozhat létre:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki a **+ új** gombra.

1. Válassza ki a **IOT-** kategóriát, majd válassza ki **Time Series Insights**.

   ![A Time Series Insights-környezet létrehozása](media/time-series-insights-get-started/1-new-tsi.png)

1. Az a **Time Series Insights** lapon jelölje be **létrehozás**.

1. Adja meg a szükséges paramétereket. A következő táblázat ismerteti az egyes paraméterek:
   
   ![A Time Series Insights-erőforráscsoport létrehozása](media/time-series-insights-get-started/2-create-tsi.png)
   
   Beállítás|Ajánlott érték|Leírás
   ---|---|---
   Környezet neve | Egy egyedi nevet | Ez a név azt a környezetet a [time series Explorerben](https://insights.timeseries.azure.com)
   Előfizetés | Az Ön előfizetése | Ha több előfizetéssel rendelkezik, válassza ki az előfizetést, lehetőleg tartalmazza az eseményforrást. A Time Series Insights képes automatikusan észlelni az azonos előfizetésben lévő Azure IoT Hub- és Event Hub-erőforrásokat.
   Erőforráscsoport | Hozzon létre egy új vagy meglévő használata | Az erőforráscsoport olyan Azure-erőforrások gyűjteménye, amelyeket együtt használnak. Választhat egy meglévő erőforráscsoportot, például azt, amelyik tartalmazza az Eseményközpontot vagy IoT hubot. Vagy egy új teheti meg, ha a többi erőforrást nem kapcsolódik ehhez az erőforráshoz.
   Location egység | Az eseményforrás a legközelebbi | Lehetőleg válassza ki az ugyanazon adatokat szolgáltató adatközpont elhelyezkedése, amely tartalmazza az eseményadatok forrása annak érdekében, hogy kerülje a hozzáadott régiók és zónák közötti sávszélességgel kapcsolatos költségek, és hozzá a késés, amikor az adatok áthelyezése a régión kívül.
   Tarifacsomag | S1 | Válassza ki a szükséges teljesítményt. Legalacsonyabb költségek, és alapszintű kapacitás válassza az S1.
   Kapacitás | 1 | A bejövő forgalom, a tárolási kapacitás és a költsége a kiválasztott Termékváltozat vonatkozik szorzó kapacitása.  A környezet kapacitása a létrehozást követően módosítható. Legalacsonyabb költségek válassza az 1 kapacitását. 
  
1. Ellenőrizze **rögzítés az irányítópulton** is egyszerűen hozzáférhet a jövőben a Time Series-környezetet.

   ![A Time Series Insights rögzítése az irányítópulton](media/time-series-insights-get-started/3-pin-create.png)

1. Válassza ki **létrehozás** a kiépítési folyamat megkezdéséhez. Eltarthat néhány percig.

1. Az üzembe helyezési folyamat megfigyeléséhez, válassza ki a **értesítések** szimbólum (Harang ikon).

   ![Az értesítések megtekintése](media/time-series-insights-get-started/4-notifications.png)

Amikor az üzembe helyezés sikeres **erőforrás megnyitása** egyéb tulajdonságok konfigurálása, állítsa az adathozzáférési házirendek a biztonsági, adja hozzá a megadott eseményforrásokból és egyéb műveleteket.

## <a name="next-steps"></a>További lépések

* [Adathozzáférési házirendek meghatározása](time-series-insights-data-access.md) a környezet biztonságossá tételéhez.

* [Event Hub-eseményforrás hozzáadása](time-series-insights-how-to-add-an-event-source-eventhub.md) az Azure Time Series Insights-környezethez.

* [Események küldése](time-series-insights-send-events.md) esemény forrását.

* A környezet megtekintése a [Time Series Insights explorer](https://insights.timeseries.azure.com).

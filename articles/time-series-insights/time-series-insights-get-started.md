---
title: Környezet létrehozása – Azure Time Series Insights | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja az Azure Portalon egy új Time Series Insights-környezet létrehozásához.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: seodec18
ms.openlocfilehash: 7459c6afc775aa0df43d6f9285191c4c7e1b8cb8
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81602366"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Új Time Series Insights-környezet létrehozása az Azure Portalon

Ez a cikk ismerteti, hogyan hozhat létre egy új Time Series Insights-környezet az Azure Portalhasználatával.

A Time Series Insights lehetővé teszi, hogy percek alatt vizualizálja és lekérdezi az Azure IoT Hubs-ba és az Event Hubs-ba áramló adatokat, így másodpercek alatt lekérdezheti az idősorozat-adatok nagy mennyiségű mennyiségét.  Úgy tervezték, az internet-of-things (IoT) skála, és képes kezelni terabájtnyi adatot.

## <a name="steps-to-create-the-environment"></a>A környezet létrehozásának lépései

Környezet létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza a **+ Erőforrás létrehozása** gombot.

1. Válassza ki a **Dolgok Internete kategóriát,** majd a **Time Series Insights**lehetőséget.

   [![A Time Series Insights-környezet létrehozása](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. A **Time Series Insights (Idősorozat-elemzések)** lapon válassza a **Létrehozás gombot.**

1. Töltse ki a szükséges paramétereket. Az alábbi táblázat az egyes paramétereket ismerteti:

   [![A Time Series Insights-erőforráscsoport létrehozása](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)

   Beállítás|Ajánlott érték|Leírás
   ---|---|---
   Környezet neve | Egyedi név | Ez a név a környezetet jelöli az [idősorozat-kezelőben](https://insights.timeseries.azure.com)
   Előfizetés | Az Ön előfizetése | Ha több előfizetéssel rendelkezik, válassza ki az eseményforrást tartalmazó előfizetést. A Time Series Insights automatikusan észleli az Azure IoT Hub és az Event Hub ugyanazon előfizetésben meglévő erőforrásait.
   Erőforráscsoport | Új létrehozása vagy meglévő használata | Az erőforráscsoport olyan Azure-erőforrások gyűjteménye, amelyeket együtt használnak. Választhat egy meglévő erőforráscsoportot, például azt, amely az Event Hubot vagy az IoT Hubot tartalmazza. Vagy létrehozhat egy újat, ha ez az erőforrás nem kapcsolódik a többi erőforráshoz.
   Hely | Az esemény forrásának legközelebbi | Lehetőleg válassza ki ugyanazt az adatközpont-helyet, amely az eseményforrás-adatokat tartalmazza, annak érdekében, hogy elkerülje a régiók közötti és a zónák közötti sávszélesség-költségeket és a hozzáadott késést, amikor adatokat távolít el a régióból.
   Tarifacsomag | S1 | Válassza ki a szükséges teljesítményt. A legalacsonyabb költségek és az induló kapacitás, válassza s1.
   Kapacitás | 1 | Kapacitás a szorzó a kiválasztott termékváltozathoz kapcsolódó be- és tárolási sebességre, tárolási kapacitásra és költségre vonatkozik.  A környezet kapacitása a létrehozást követően módosítható. A legalacsonyabb költségek hez válassza ki az 1-es kapacitást.
  
1. A **létrehozási** folyamat megkezdéséhez válassza a Létrehozás lehetőséget. Ez eltarthat pár percig.

1. A telepítési folyamat figyeléséhez válassza az **Értesítések** szimbólumot (csengő ikon).

   [![Az értesítések figyelése](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. Az erőforrás **áttekintése párbeszédpanelen**ellenőrizze a telepítési konfigurációs beállításokat.

   [![A Time Series Insights rögzítése az irányítópulton](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **(Nem kötelező)** A jobb felső sarokban lévő **pin ikonra** választva a jövőben könnyedén elérheti a Time Series Insights környezetet.

## <a name="next-steps"></a>További lépések

* A környezet védelméhez határozza meg az [adatelérési házirendeket.](time-series-insights-data-access.md)

* [Adjon hozzá egy Event Hub-eseményforrást](time-series-insights-how-to-add-an-event-source-eventhub.md) az Azure Time Series Insights-környezethez.

* [Események küldése](time-series-insights-send-events.md) az eseményforrásra.

* Tekintse meg környezetét a [Time Series Insights felfedezőjében.](https://insights.timeseries.azure.com)

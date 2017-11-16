---
title: "Azure Time Series Insights-környezet létrehozása | Microsoft Docs"
description: "A cikkből megtudhatja, hogyan használható az Azure-portálon hozzon létre egy új idő adatsorozat Insights környezetet."
services: time-series-insights
ms.service: time-series-insights
author: op-ravi
ms.author: omravi
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 6dba703851161a1eebce0101be8076682f09c76f
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Új Time Series Insights-környezet létrehozása az Azure Portalon
A cikkből megtudhatja, hogyan hozzon létre egy új idő adatsorozat Insights környezetet az Azure portál használatával.

Idő adatsorozat Insights lehetővé teszi a kezdéshez megjelenítése, és lekérdezi-e (percben), Azure IoT hub és az Event Hubs áramló adatok lehetővé teszik lekérdezés nagy adatkötetek idő adatsorozat másodpercben.  Az internetes hálózatával (IoT) bővített úgy lett kialakítva, és kezelni tud a terabájtos adatkészleteket.

## <a name="steps-to-create-the-environment"></a>A környezet létrehozásának lépései
Kövesse az alábbi lépéseket olyan környezetet hozhat létre:

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2.  Válassza ki a **+ új** gombra.

3.  Válassza ki a **az eszközök internetes hálózatát** kategóriát, és válassza ki **idő adatsorozat Insights**.

   ![A Time Series Insights-környezet létrehozása](media/time-series-insights-get-started/1-new-tsi.png)

4.  Az a **idő adatsorozat Insights** lapon jelölje be **létrehozása**.

5. Töltse ki a szükséges paramétereket. Az alábbi táblázat ismerteti az egyes paramétereket:
   
   ![A Time Series Insights-erőforráscsoport létrehozása](media/time-series-insights-get-started/2-create-tsi.png)
   
   Beállítás|Ajánlott érték|Leírás
   ---|---|---
   A környezeti neve | Egy egyedi nevet | Ez a név azt a környezet [idő adatsorozat explorer](https://insights.timeseries.azure.com)
   Előfizetés | Az Ön előfizetése | Ha több előfizetéssel rendelkezik, válassza ki az előfizetést, az eseményforrás lehetőleg tartalmazza. A Time Series Insights képes automatikusan észlelni az azonos előfizetésben lévő Azure IoT Hub- és Event Hub-erőforrásokat.
   Erőforráscsoport | Hozzon létre egy új vagy meglévő | Az erőforráscsoport olyan Azure-erőforrások gyűjteménye, amelyeket együtt használnak. Választhat egy létező erőforráscsoportot, például azt, amelyik az IoT-központ vagy az Event Hubs tartalmazza. Vagy egy új teheti meg, ha az egyéb erőforrások nem kapcsolódik ehhez az erőforráshoz.
   Hely | A forrás legközelebbi | Lehetőleg válassza a data center ugyanott elkerülése érdekében a esemény forrásadatok tartalmazó hozzáadja a kereszt-régió, és a kereszt-zóna sávszélességgel kapcsolatos költségek, és késés hozzá, amikor a régió kívül megköveteli az adatok.
   Tarifacsomag | S1 | Válassza ki a szükséges teljesítményt. S1 válassza legalacsonyabb költségek és a starter kapacitás.
   Kapacitás | 1 | A többszöröző alkalmaz a érkező sebességét, a tárolási kapacitás és a társítva a kiválasztott Termékváltozat kapacitása.  A környezet kapacitása a létrehozást követően módosítható. A legalacsonyabb költségek válassza a 1 kapacitása. 
  
6. Ellenőrizze **rögzítés az irányítópulton** legjobb egyszerűen elérhetők az adatsorozat környezet a jövőben.

   ![A Time Series Insights rögzítése az irányítópulton](media/time-series-insights-get-started/3-pin-create.png)

7. Válassza ki **létrehozása** a kiépítési folyamat megkezdéséhez. Néhány percig is eltarthat.

8. A telepítési folyamat figyeléséhez, válassza ki a **értesítések** szimbólum (harang ikonra).

   ![Tekintse meg az értesítések](media/time-series-insights-get-started/4-notifications.png)

Amikor a telepítés sikeres, választhat **forrást** tulajdonságainak beállítása, állítsa be a biztonsági adatok hozzáférési házirendekkel, adja hozzá a eseményforrások és egyéb műveleteket.

## <a name="next-steps"></a>Következő lépések
* [Adja meg az adat-hozzáférési házirendjeit](time-series-insights-data-access.md) biztonságossá tételéhez a környezetben.
* [Adja hozzá az Eseményközpont eseményforrást](time-series-insights-how-to-add-an-event-source-eventhub.md) az Azure idő adatsorozat Insights környezethez. 
* [Események küldése](time-series-insights-send-events.md) esemény forrását.
* A környezet megtekintése [idő adatsorozat Insights explorer](https://insights.timeseries.azure.com).

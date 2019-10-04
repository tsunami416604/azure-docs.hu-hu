---
title: Azure Time Series előzetes verziójának kiépítése és kezelése | Microsoft Docs
description: Azure Time Series Insights előnézet kiépítésének és kezelésének megismerése.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: seodec18
ms.openlocfilehash: a84c587a2666982e862f0640b30d2d6bef3b9a42
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744674"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Azure Time Series Insights előzetes verziójának kiépítése és kezelése

Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhet egy Azure Time Series Insights előnézeti környezetet a [Azure Portal](https://portal.azure.com/)használatával.

## <a name="overview"></a>Áttekintés

Azure Time Series Insights előnézeti környezetek TB-környezetek.

Azure Time Series Insights előnézeti környezet kiépítésekor két Azure-erőforrást hoz létre:

* Azure Time Series Insights előnézeti környezet  
* Azure Storage általános célú v1-fiók
  
Ismerje meg [, hogyan tervezze meg a környezetét](./time-series-insights-update-plan.md).

>[!IMPORTANT]
> Az előzetes verzióban győződjön meg róla, hogy Azure Storage általános célú v1-(GPv1-) fiókot használ.

Szükség esetén az egyes Azure Time Series Insights előnézeti környezeteket társíthatja egy adott eseményforrás használatával. További információért olvassa el az [Event hub-forrás hozzáadása](./time-series-insights-how-to-add-an-event-source-eventhub.md) és [az IoT hub-forrás hozzáadása](./time-series-insights-how-to-add-an-event-source-iothub.md)című témakört. Ebben a lépésben egy időbélyeg-azonosító tulajdonságot és egy egyedi fogyasztói csoportot kell megadnia. Így biztosíthatja, hogy a környezet hozzáférjen a megfelelő eseményekhez.

A kiépítés befejezése után módosíthatja a hozzáférési házirendeket és az egyéb környezeti attribútumokat az üzleti követelményeinek megfelelően.

## <a name="create-the-environment"></a>A környezet létrehozása

A következő lépések bemutatják, hogyan hozhat létre Azure Time Series Insights előnézeti környezetet:

1. Kattintson a **TB** gombra az **SKU** menüben. Adja meg a környezet nevét, és válassza ki, hogy melyik előfizetési csoportot és melyik erőforráscsoportot kívánja használni. Ezután válasszon ki egy támogatott helyet a környezet számára a szolgáltatásban.

   [![Hozzon létre egy Azure Time Series Insights példányt.](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. Adjon meg egy idősorozat-azonosítót.

    >[!NOTE]
    > * Az idősorozat-azonosító megkülönbözteti a kis-és nagybetűket. (Ez a beállítás után nem módosítható.)
    > * Az idősorozat-azonosítók legfeljebb három kulccsal rendelkezhetnek.
    > * Az idősorozat-azonosító kiválasztásával kapcsolatos további információkért olvassa el [az Idősorozat-azonosító](./time-series-insights-update-how-to-id.md)kiválasztása című témakört.

1. Hozzon létre egy Azure Storage-fiókot a Storage-fiók nevének kiválasztásával, majd válasszon ki egy replikálási lehetőséget. Ez automatikusan létrehoz egy Azure Storage általános célú v1-fiókot. A rendszer ugyanabban a régióban jön létre, mint a korábban kiválasztott Azure Time Series Insights-előnézeti környezet.

    [![Azure Storage-fiók létrehozása a példányhoz](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. Lehetőség van egy eseményforrás hozzáadására is.

   * Time Series Insights az [azure IoT hub](./time-series-insights-how-to-add-an-event-source-iothub.md) és az [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) használatát is támogatja. Bár a környezet létrehozási idején csak egyetlen eseményforrás adható hozzá, később további eseményforrás is felvehető. Érdemes létrehozni egy egyedi fogyasztói csoportot, amely biztosítja, hogy minden esemény látható legyen a Azure Time Series Insights előzetes verziójának példánya számára. Az eseményforrás hozzáadásakor kiválaszthat egy meglévő fogyasztói csoportot, vagy létrehozhat egy új fogyasztói csoportot is.

   * Válassza ki a megfelelő timestamp tulajdonságot is. Alapértelmezés szerint a Azure Time Series Insights az várólistán lévő időt használja az egyes eseményforrás számára.

     > [!TIP]
     > Előfordulhat, hogy az üzenet várólistán lévő időpontja nem a legalkalmasabb a Batch-eseményekben vagy a korábbi adatfeltöltési forgatókönyvekben való használathoz. Ügyeljen arra, hogy az ilyen esetekben ne használjon timestamp tulajdonságot.

     [![Eseményforrás lap](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. Győződjön meg arról, hogy a környezet a kívánt beállításokkal lett kiépítve.

    [![Felülvizsgálat + Létrehozás lap](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

## <a name="manage-the-environment"></a>A környezet kezelése

A Azure Portal használatával felügyelheti Azure Time Series Insights előnézeti környezetét. A következőkben a TB Azure Time Series Insights előzetes verzió környezetének főbb eltérései az S1 vagy S2 környezettel szemben a Azure Portal használatával:

* A Azure Portal **áttekintő** panelje nem változik Azure Time Series Insights, kivéve a következő módokon:
  * A rendszer eltávolítja a kapacitást, mivel ez a koncepció nem vonatkozik a TB-környezetekre.
  * Az idősorozat-azonosító tulajdonság hozzá lett adva. Meghatározza, hogyan particionálja az adatait.
  * A rendszer eltávolítja a hivatkozási adatkészleteket.
  * A megjelenített URL-cím a [Azure Time Series Insights Preview Explorer böngészőre](./time-series-insights-update-explorer.md)irányítja.
  * Megjelenik az Azure Storage-fiók neve.

* A Azure Portal **konfigurálása** panel el lett távolítva a Azure Time Series Insights Preview-ban, mert a TB-környezetek nem konfigurálhatók.

* A Azure Portal **hivatkozási** adatpanelje el lett távolítva a Azure Time Series Insights Preview-ban, mert a hivatkozási adatterületek nem a TB-környezetek összetevője.

[![Time Series Insights előzetes verziójú környezet a Azure Portal](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>További lépések

- Olvassa el [a környezet megtervezése](./time-series-insights-update-plan.md)című cikkét.

- Ismerje meg, hogyan [adhat hozzá egy Event hub-forrást](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- [IoT hub-forrás](./time-series-insights-how-to-add-an-event-source-iothub.md)konfigurálása.
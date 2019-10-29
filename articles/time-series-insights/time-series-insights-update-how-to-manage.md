---
title: Azure Time Series előzetes verziójának kiépítése és kezelése | Microsoft Docs
description: Azure Time Series Insights előnézet kiépítésének és kezelésének megismerése.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.custom: seodec18
ms.openlocfilehash: 47c96cb14f2e466d02d57f73fb2e66bc1f44a311
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989898"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Azure Time Series Insights előzetes verziójának kiépítése és kezelése

Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhet egy Azure Time Series Insights előnézeti környezetet a [Azure Portal](https://portal.azure.com/)használatával.

## <a name="overview"></a>Áttekintés

Azure Time Series Insights előnézeti környezetek TB-környezetek.

Azure Time Series Insights előnézeti környezet kiépítésekor ezeket az Azure-erőforrásokat hozza létre:

* Azure Time Series Insights előnézeti környezet  
* Azure Storage általános célú v1-fiók
* Egy opcionális meleg tároló a gyorsabb és korlátlan lekérdezésekhez
  
Ismerje meg [, hogyan tervezze meg a környezetét](./time-series-insights-update-plan.md).

Társítsa az egyes Azure Time Series Insights előnézeti környezeteket egy adott eseményforrás esetében. További információért olvassa el az [Event hub-forrás hozzáadása](./time-series-insights-how-to-add-an-event-source-eventhub.md) és [az IoT hub-forrás hozzáadása](./time-series-insights-how-to-add-an-event-source-iothub.md)című témakört. Ebben a lépésben egy időbélyeg-azonosító tulajdonságot és egy egyedi fogyasztói csoportot kell megadnia. Így biztosíthatja, hogy a környezet hozzáférjen a megfelelő eseményekhez.

> [!NOTE]
> Az előző lépés nem kötelező a kiépítési munkafolyamatban az idősorozat-előnézeti környezet létrehozásakor. Ahhoz azonban, hogy az adott környezetben megkezdhető legyen az esemény forrása, csatlakoztatni kell egy eseményt a környezethez.

A kiépítés befejezése után módosíthatja a hozzáférési házirendeket és az egyéb környezeti attribútumokat az üzleti követelményeinek megfelelően.

## <a name="create-the-environment"></a>A környezet létrehozása

Azure Time Series Insights előnézeti környezet létrehozása:

1. Az **SKU** menüben válassza a **TB** gombot. Adja meg a környezet nevét, és válassza ki a használni kívánt előfizetési csoportot és erőforráscsoportot. Ezután válasszon ki egy támogatott helyet a környezet üzemeltetéséhez.

   [![hozzon létre egy Azure Time Series Insights példányt.](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. Adjon meg egy idősorozat-azonosítót.

    >[!NOTE]
    > * Az idősorozat-azonosító megkülönbözteti a kis-és nagybetűket. (Ez a beállítás után nem módosítható.)
    > * Az idősorozat-azonosítók legfeljebb három kulccsal rendelkezhetnek.
    > * Az idősorozat-azonosító kiválasztásával kapcsolatos további információkért olvassa el [az Idősorozat-azonosító](./time-series-insights-update-how-to-id.md)kiválasztása című témakört.

1. Hozzon létre egy Azure Storage-fiókot a Storage-fiók nevének kiválasztásával, majd válasszon ki egy replikálási lehetőséget. Ez automatikusan létrehoz egy Azure Storage általános célú v1-fiókot. A fiók ugyanabban a régióban jön létre, mint a korábban kiválasztott Azure Time Series Insights-előnézeti környezet.

    [![Azure Storage-fiók létrehozása a példányhoz](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. **(Nem kötelező)** Ha szeretné, hogy a környezetében a legutóbbi adatmennyiségnél gyorsabban és korlátlanul lehessen lekérdezéseket használni, engedélyezze a meleg tárolást a környezet számára. A bal oldali navigációs ablaktáblán a **tárolási konfiguráció** lehetőséggel is létrehozhat vagy törölhet egy meleg tárolót, miután létrehozta a Time Series Insights előnézeti környezetet.

1. **(Nem kötelező)** Most hozzáadhat egy eseményforrás-forrást. Másik lehetőségként megvárhatja a példány üzembe helyezését.

   * Time Series Insights támogatja az [azure IoT hub](./time-series-insights-how-to-add-an-event-source-iothub.md) és az [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) használatát az eseményforrás lehetőségeinek megfelelően. Bár a környezet létrehozásakor csak egyetlen eseményforrás adhatók hozzá, később további eseményforrás is felvehető. Az eseményforrás hozzáadásakor kiválaszthat egy meglévő felhasználói csoportot, vagy létrehozhat egy új fogyasztói csoportot is. Érdemes létrehozni egy egyedi fogyasztói csoportot, amely biztosítja, hogy minden esemény látható legyen a Azure Time Series Insights előzetes verziójának környezetében.

   * Válassza ki a megfelelő időbélyeg-tulajdonságot. Alapértelmezés szerint a Azure Time Series Insights az várólistán lévő időt használja az egyes eseményekhez.

     > [!TIP]
     > Előfordulhat, hogy az várólistán lévő idő nem a legjobb konfigurált beállítás a Batch-eseményekben vagy a korábbi adatfeltöltési forgatókönyvekben. Ilyen esetekben ügyeljen arra, hogy ellenőrizze a használati vagy nem időbélyeg-tulajdonság használatát.

     [![eseményforrás lap](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. Győződjön meg arról, hogy a környezet a kívánt beállításokkal lett kiépítve.

    [![felülvizsgálat + Létrehozás lap](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

## <a name="manage-the-environment"></a>A környezet kezelése

A Azure Portal használatával felügyelheti Azure Time Series Insights előnézeti környezetét. Ha a Azure Portal felügyeli, az utólagos elszámolású Azure Time Series Insights előnézeti környezet és az általánosan elérhető S1-vagy S2-környezetek között néhány lényeges különbség jelenik meg:

* A Azure Portal **áttekintő** panelje nem változik Azure Time Series Insights, kivéve a következő módokon:
  * A rendszer eltávolítja a kapacitást, mivel az utólagos elszámolású környezetekre nem vonatkozik.
  * A rendszer hozzáadja az idősorozat-azonosító tulajdonságot. Meghatározza, hogyan particionálja az adatait.
  * A rendszer eltávolítja a hivatkozási adatkészleteket.
  * A megjelenített URL-cím a [Azure Time Series Insights Preview Explorer böngészőre](./time-series-insights-update-explorer.md)irányítja.
  * Megjelenik az Azure Storage-fiók neve.

* A Azure Portal a **configure (Konfigurálás** ) panel el lesz távolítva Azure Time Series Insights előzetes verzióban, mert a TB-környezetek nem konfigurálhatók. A **tárolási konfigurációval** azonban az újonnan bevezetett meleg tárolót is konfigurálhatja.

* A Azure Portal **hivatkozási** adatpanelje el lesz távolítva Azure Time Series Insights Preview-ban, mivel a hivatkozási adatmennyiség nem része az utólagos elszámolású környezeteknek.

[![Time Series Insights előnézeti környezetet a Azure Portal](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>Következő lépések

- További Time Series Insights információ az általánosan elérhető környezetekről és az előzetes verziójú környezetekről: [a környezet megtervezése](./time-series-insights-update-plan.md).

- Ismerje meg, hogyan [adhat hozzá egy Event hub-forrást](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- [IoT hub-forrás](./time-series-insights-how-to-add-an-event-source-iothub.md)konfigurálása.

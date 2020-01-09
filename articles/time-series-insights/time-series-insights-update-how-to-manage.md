---
title: Előzetes verziójú környezet kiépítése és kezelése – Azure Time Series | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és kezelhet Azure Time Series Insights előnézeti környezetet.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/19/2019
ms.custom: seodec18
ms.openlocfilehash: 1f75e7112a8fc90c6c49ede7cd0ac726401fc1a9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452704"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Azure Time Series Insights előzetes verziójának kiépítése és kezelése

Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhet egy Azure Time Series Insights előnézeti környezetet a [Azure Portal](https://portal.azure.com/)használatával.

## <a name="overview"></a>Áttekintés

Azure Time Series Insights előnézeti környezetek *TB-* környezetek.

Azure Time Series Insights előnézeti környezet kiépítésekor ezeket az Azure-erőforrásokat hozza létre:

* Azure Time Series Insights előnézeti környezet  
* Azure Storage általános célú v1-fiók
* Egy opcionális meleg tároló a gyorsabb és korlátlan lekérdezésekhez

> [!TIP]
> * Ismerje meg [, hogyan tervezze meg a környezetét](./time-series-insights-update-plan.md).
> * További információ az [Event hub-források hozzáadásáról](./time-series-insights-how-to-add-an-event-source-eventhub.md) , illetve [az IoT hub-forrás hozzáadásáról](./time-series-insights-how-to-add-an-event-source-iothub.md).

Az alábbiakat fogja elsajátítani:

1. **(Nem kötelező)** Társítsa az egyes Azure Time Series Insights előnézeti környezeteket egy adott eseményforrás esetében. Egy időbélyeg-azonosító tulajdonságot és egy egyedi fogyasztói csoportot is meg kell adnia annak biztosításához, hogy a környezet hozzáférjen a megfelelő eseményekhez.

   > [!NOTE]
   > A környezet kiépítés esetén az előző lépés nem kötelező. Ha kihagyja ezt a lépést, akkor később csatlakoztatnia kell egy eseményforrást a környezethez, hogy az adatelérés a környezetben is elérhető legyen.

1. A kiépítés befejezése után módosíthatja a hozzáférési házirendeket és az egyéb környezeti attribútumokat az üzleti követelményeinek megfelelően.

## <a name="create-the-environment"></a>A környezet létrehozása

Azure Time Series Insights előnézeti környezet létrehozása:

1. Válassza a **TB** lehetőséget **.** Adja meg a környezet nevét, és válassza ki a használni kívánt előfizetési csoportot és erőforráscsoportot. Ezután válasszon ki egy támogatott helyet a környezet üzemeltetéséhez.

   [![hozzon létre egy Azure Time Series Insights példányt.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Adjon meg egy idősorozat-azonosítót.

    > [!NOTE]
    > * Az idősorozat-azonosító *megkülönbözteti a kis-* *és*nagybetűket. (Ez a beállítás után nem módosítható.)
    > * Az idősorozat-azonosítók legfeljebb *három* kulccsal rendelkezhetnek.
    > * További információ az [idősorozat-azonosítók kiválasztásáról](time-series-insights-update-how-to-id.md)

1. Hozzon létre egy Azure Storage-fiókot a Storage-fiók nevének kiválasztásával, majd válasszon ki egy replikálási lehetőséget. Ez automatikusan létrehoz egy Azure Storage általános célú v1-fiókot. A fiók ugyanabban a régióban jön létre, mint a korábban kiválasztott Azure Time Series Insights-előnézeti környezet.

    [![hideg tárolási konfiguráció](media/v2-update-manage/create-and-manage-cold-store.png)](media/v2-update-manage/create-and-manage-cold-store.png#lightbox)

1. **(Nem kötelező)** Ha szeretné, hogy a környezetében a legutóbbi adatmennyiségnél gyorsabban és korlátlanul lehessen lekérdezéseket használni, engedélyezze a meleg tárolást a környezet számára. A bal oldali navigációs ablaktáblán a **tárolási konfiguráció** lehetőséggel is létrehozhat vagy törölhet egy meleg tárolót, miután létrehozta a Time Series Insights előnézeti környezetet.

    [![meleg tárolási konfiguráció](media/v2-update-manage/create-and-manage-warm-storage.png)](media/v2-update-manage/create-and-manage-warm-storage.png#lightbox)

1. **(Nem kötelező)** Most hozzáadhat egy eseményforrás-forrást. Azt is megvárhatja, amíg a példány üzembe lett helyezve.

   * Time Series Insights támogatja az [azure IoT hub](./time-series-insights-how-to-add-an-event-source-iothub.md) és az [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) használatát az eseményforrás lehetőségeinek megfelelően. Bár a környezet létrehozásakor csak egyetlen eseményforrás adhatók hozzá, később további eseményforrás is felvehető. 
   
     Az eseményforrás hozzáadásakor kiválaszthat egy meglévő felhasználói csoportot, vagy létrehozhat egy új fogyasztói csoportot is. Érdemes létrehozni egy egyedi fogyasztói csoportot, amely biztosítja, hogy minden esemény látható legyen a Azure Time Series Insights előzetes verziójának környezetében.

   * Válassza ki a megfelelő időbélyeg-tulajdonságot. Alapértelmezés szerint a Azure Time Series Insights az várólistán lévő időt használja az egyes eseményekhez.

     > [!TIP]
     > Előfordulhat, hogy az várólistán lévő idő nem a legjobb konfigurált beállítás a Batch-eseményekben vagy a korábbi adatfeltöltési forgatókönyvekben. Ilyen esetekben ügyeljen arra, hogy ellenőrizze a használati vagy nem időbélyeg-tulajdonság használatát.

     [![eseményforrás-konfiguráció lap](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Győződjön meg arról, hogy a környezet üzembe lett állítva és konfigurálva van a kívánt módon.

    [![felülvizsgálat + Létrehozás lap](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>A környezet kezelése

A Azure Portal használatával felügyelheti Azure Time Series Insights előnézeti környezetét. Ha a Azure Portalon keresztül kezeli a felügyeletet, a TB Azure Time Series Insights előnézeti környezet és az általánosan elérhető S1-vagy S2-környezetek között néhány lényeges különbség jelenik meg:

* A Azure Portal előnézet **Áttekintés** paneljén a következő változások érhetők el:

  * A rendszer eltávolítja a kapacitást, mert az nem vonatkozik a TB-környezetekre.
  * A rendszer hozzáadja az **idősorozat-azonosító** tulajdonságot. Meghatározza, hogyan particionálja az adatait.
  * A rendszer eltávolítja a hivatkozási adatkészleteket.
  * A megjelenített URL-cím a [Azure Time Series Insights Preview Explorer böngészőre](./time-series-insights-update-explorer.md)irányítja.
  * Megjelenik az Azure Storage-fiók neve.

* A Azure Portal a **configure (Konfigurálás** ) panel el lesz távolítva Azure Time Series Insights előzetes verzióban, mert a TB-környezetek nem konfigurálhatók. A **tárolási konfigurációval** azonban az újonnan bevezetett meleg tárolót is konfigurálhatja.

* A Azure Portal **hivatkozási** adatpanelje el lesz távolítva Azure Time Series Insights előzetes verzióban, mivel a hivatkozási adatkörnyezetek nem részei a TB-környezeteknek.

[![Time Series Insights előnézeti környezetet a Azure Portal](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Következő lépések

- További Time Series Insights információ az általánosan elérhető környezetekről és az előzetes verziójú környezetekről: [a környezet megtervezése](./time-series-insights-update-plan.md).

- Ismerje meg, hogyan [adhat hozzá egy Event hub-forrást](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- [IoT hub-forrás](./time-series-insights-how-to-add-an-event-source-iothub.md)konfigurálása.

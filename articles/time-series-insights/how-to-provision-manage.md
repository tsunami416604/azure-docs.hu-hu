---
title: 2. generációs környezet kiépítése és kezelése – Azure Time Series | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre és kezelhet egy Azure Time Series Insights Gen 2 környezetet.
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/08/2020
ms.custom: seodec18
ms.openlocfilehash: e71c426fee62187bb680c7b8bd1af92d87e85274
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288803"
---
# <a name="provision-and-manage-azure-time-series-insights-gen2"></a>Azure Time Series Insights Gen2 kiépítése és kezelése

Ez a cikk azt ismerteti, hogyan hozhatók létre és kezelhetők Azure Time Series Insights Gen2-környezetek a [Azure Portal](https://portal.azure.com/)használatával.

## <a name="overview"></a>Áttekintés

Azure Time Series Insights Gen2-környezet kiépítésekor ezeket az Azure-erőforrásokat hozza létre:

* Az utólagos elszámolású árképzési modellt követő Azure Time Series Insights Gen2-környezet
* Azure Storage-fiók
* Egy opcionális meleg tároló a gyorsabb és korlátlan lekérdezésekhez

> [!TIP]
> * Ismerje meg [, hogyan tervezze meg a környezetét](./time-series-insights-update-plan.md).
> * További információ az [Event hub-források hozzáadásáról](./time-series-insights-how-to-add-an-event-source-eventhub.md) , illetve [az IoT hub-forrás hozzáadásáról](./time-series-insights-how-to-add-an-event-source-iothub.md).

Az alábbiakat fogja elsajátítani:

1.  Társítson minden Azure Time Series Insights Gen 2 környezetet egy eseményforrás. Egy időbélyeg-azonosító tulajdonságot és egy egyedi fogyasztói csoportot is meg kell adnia annak biztosításához, hogy a környezet hozzáférjen a megfelelő eseményekhez.

1. A kiépítés befejezése után módosíthatja a hozzáférési házirendeket és az egyéb környezeti attribútumokat az üzleti igényeinek megfelelően.

   > [!NOTE]
   > A környezet kiépítés esetén az első lépés nem kötelező. Ha kihagyja ezt a lépést, akkor később csatlakoztatnia kell egy eseményforrást a környezethez, így az adatforgalom a környezetbe áramlik, és a lekérdezésen keresztül érhető el.

## <a name="create-the-environment"></a>A környezet létrehozása

Azure Time Series Insights 2. generációs környezet létrehozása:
1. Hozzon létre egy Azure Time Series Insights erőforrást a [Azure Portal](https://portal.azure.com/) *eszközök internetes hálózata* alatt.

1. Válassza ki a **Gen2 (L1)** **szintet**. Adja meg a környezet nevét, és válassza ki a használni kívánt előfizetési csoportot és erőforráscsoportot. Ezután válasszon ki egy támogatott helyet a környezet üzemeltetéséhez.

   [![Hozzon létre egy Azure Time Series Insights példányt.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Adjon meg egy idősorozat-azonosítót.

    > [!NOTE]
    > * Az idősorozat-azonosító *megkülönbözteti a kis-* *és*nagybetűket. (Ez a beállítás után nem módosítható.)
    > * Az idősorozat-azonosítók legfeljebb *három* kulccsal rendelkezhetnek. Gondoljon rá elsődleges kulcsként egy adatbázisban, amely egyedileg jelöli az összes olyan érzékelőt, amely az adott környezetnek küldi az adatküldést. Ez lehet egy tulajdonság vagy akár három tulajdonság kombinációja is.
    > * További információ az [idősorozat-azonosítók kiválasztásáról](time-series-insights-update-how-to-id.md)

1. Hozzon létre egy Azure Storage-fiókot a Storage-fióknév, a fióktípus és a [replikálási](https://docs.microsoft.com/azure/storage/common/redundancy-migration?tabs=portal) választás kijelölésével. Ezzel automatikusan létrehoz egy Azure Storage-fiókot. Alapértelmezés szerint a rendszer az [általános célú v2](https://docs.microsoft.com/azure/storage/common/storage-account-overview) -fiókot hozza létre. A fiók ugyanabban a régióban jön létre, mint a korábban kiválasztott Azure Time Series Insights Gen2-környezet. Azt is megteheti, hogy az új Azure Time Series Gen2-környezet létrehozásakor saját tárolót (BYOS) is használhat [ARM-sablonon](./time-series-insights-manage-resources-using-azure-resource-manager-template.md) keresztül. 

1. **(Nem kötelező)** Ha szeretné, hogy a környezetében a legutóbbi adatmennyiségnél gyorsabban és korlátlanul lehessen lekérdezéseket használni, engedélyezze a meleg tárolást a környezet számára. Egy Azure Time Series Insights Gen2-környezet létrehozása után a bal oldali navigációs ablaktáblán a **tárolási konfiguráció** lehetőséggel is létrehozhat vagy törölhet egy meleg áruházat.

1. **(Nem kötelező)** Most hozzáadhat egy eseményforrás-forrást. Azt is megvárhatja, amíg a példány üzembe lett helyezve.

   * Azure Time Series Insights támogatja az [azure IoT hub](./time-series-insights-how-to-add-an-event-source-iothub.md) és az [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) használatát az eseményforrás lehetőségeinek megfelelően. Bár a környezet létrehozásakor csak egyetlen eseményforrás adhatók hozzá, később további eseményforrás is felvehető. 
   
     Az eseményforrás hozzáadásakor kiválaszthat egy meglévő felhasználói csoportot, vagy létrehozhat egy új fogyasztói csoportot is. Vegye figyelembe, hogy az eseményforrás egy egyedi fogyasztói csoportot igényel ahhoz, hogy a környezete adatait beolvassa.

   * Válassza ki a megfelelő időbélyeg-tulajdonságot. Alapértelmezés szerint a Azure Time Series Insights az várólistán lévő időt használja az egyes eseményekhez.

     > [!TIP]
     > Előfordulhat, hogy az várólistán lévő idő nem a legjobb konfigurált beállítás a Batch-eseményekben vagy a korábbi adatfeltöltési forgatókönyvekben. Ilyen esetekben ügyeljen arra, hogy ellenőrizze a használati vagy nem időbélyeg-tulajdonság használatát.

     [![Eseményforrás konfigurációja lap](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Győződjön meg arról, hogy a környezet üzembe lett állítva és konfigurálva van a kívánt módon.

    [![Felülvizsgálat + Létrehozás lap](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>A környezet kezelése

Azure Time Series Insights Gen2-környezetét a Azure Portal használatával kezelheti. Van néhány lényeges különbség a Gen2-környezet és a Gen1 S1 vagy Gen1 S2 környezet között, hogy figyelembe vegye a környezetét a Azure Portal:

* Az Azure Portal Gen2 **Áttekintés** paneljén a következő változások szerepelnek:

  * A rendszer eltávolítja a kapacitást, mert az nem vonatkozik a Gen2-környezetekre.
  * A rendszer hozzáadja az **idősorozat-azonosító** tulajdonságot. Meghatározza, hogyan particionálja az adatait.
  * A rendszer eltávolítja a hivatkozási adatkészleteket.
  * A megjelenített URL-cím a [Azure Time Series Insights Explorerre](./time-series-insights-update-explorer.md)irányítja.
  * Megjelenik az Azure Storage-fiók neve.

* A Azure Portal a **configure (Konfigurálás** ) panel el lesz távolítva, mivel a skálázási egységek nem vonatkoznak Azure Time Series Insights Gen2 környezetekre. A **tárolási konfigurációval** azonban az újonnan bevezetett meleg tárolót is konfigurálhatja.

* A Azure Portal **hivatkozási** adatpanelje el lett távolítva a Azure Time Series Insights Gen2, mert a hivatkozási adatkoncepciót a [Time Series Model (TSM)](/azure/time-series-insights/concepts-model-overview)váltotta fel.

[![Azure Time Series Insights Gen2-környezet a Azure Portal](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Következő lépések

- További információ Azure Time Series Insights általánosan elérhető környezetekről és Gen2 környezetekről a [környezet megtervezése](./time-series-insights-update-plan.md)című témakörben olvasható.

- Ismerje meg, hogyan [adhat hozzá egy Event hub-forrást](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- [IoT hub-forrás](./time-series-insights-how-to-add-an-event-source-iothub.md)konfigurálása.

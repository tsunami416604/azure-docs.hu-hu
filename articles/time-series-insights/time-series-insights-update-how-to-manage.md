---
title: Előzetes verziójú környezet kiépítése és kezelése – Azure Time Series | Microsoft dokumentumok
description: Ismerje meg, hogyan építhet ki és kezelhet azure-time series insights előzetes verziójú környezetet.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1ec0d9c7ecf16c60c32abdf08b358268f460edb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087215"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Azure Time Series Insights előzetes verzió kiépítése és kezelése

Ez a cikk ismerteti, hogyan hozhat létre és kezelhet egy Azure Time Series Insights előzetes verziójú környezetet az [Azure Portal](https://portal.azure.com/)használatával.

## <a name="overview"></a>Áttekintés

Az Azure Time Series Insights előzetes verziójú környezetek *csak egyszer (PAYG)* alapú környezetek.

Az Azure Time Series Insights előzetes verziójú környezet kiépítésekor a következő Azure-erőforrásokat hozza létre:

* Azure Time Series Insights előzetes verziójú környezet  
* Általános célú V1-es Azure Storage-fiók
* Opcionális melegbolt a gyorsabb és korlátlan lekérdezésekhez

> [!TIP]
> * További információ [a környezet megtervezéséről.](./time-series-insights-update-plan.md)
> * A következő információ: [Eseményközpont-forrás hozzáadása](./time-series-insights-how-to-add-an-event-source-eventhub.md) vagy [IoT-központ-forrás hozzáadása.](./time-series-insights-how-to-add-an-event-source-iothub.md)

Az alábbiakat fogja elsajátítani:

1. **(Nem kötelező)** Társítson minden Egyes Azure Time Series Insights előzetes környezetet egy eseményforráshoz. Emellett egy időbélyeg-azonosító tulajdonságot és egy egyedi fogyasztói csoportot is biztosít, hogy a környezet hozzáférhessen a megfelelő eseményekhez.

   > [!NOTE]
   > Az előző lépés nem kötelező, ha egy környezet kiépítése. Ha kihagyja ezt a lépést, később csatolnia kell egy eseményforrást a környezethez, hogy az adatok elérhetők legyenek a környezetben.

1. A kiépítés befejezése után módosíthatja a hozzáférési szabályzatokat és más környezeti attribútumokat az üzleti követelményeknek megfelelően.

## <a name="create-the-environment"></a>A környezet létrehozása

Azure Time Series Insights előzetes környezet létrehozása:

1. Válassza a **PAYG-t** **tier ként.** Adjon meg egy környezetnevet, és válassza ki a használni kívánt előfizetési csoportot és erőforráscsoportot. Ezután válassza ki a környezet gazdagépének támogatott helyét.

   [![Hozzon létre egy Azure Time Series Insights-példányt.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Adjon meg egy idősorozat-azonosítót.

    > [!NOTE]
    > * Az idősorozat-azonosító ban a kis- és *nagybetűket nem lehet figyelembe tenni,* és *nem módosítható.* (Nem módosítható a beállítás után.)
    > * Az idősorozat-azonosítók legfeljebb *három* kulcsból állnak.
    > * További információ [az idősorozat-azonosító kiválasztásáról](time-series-insights-update-how-to-id.md)

1. Hozzon létre egy Azure Storage-fiókot a tárfiók nevének kiválasztásával és a replikációs választás kijelölésével. Ezzel automatikusan létrehoz egy Azure Storage általános célú v1-fiókot. A fiók ugyanabban a régióban jön létre, mint az Azure Time Series Insights előzetes verziójú környezet, amely korábban kiválasztott.

    [![Hűtőtároló konfigurációja](media/v2-update-manage/create-and-manage-cold-store.png)](media/v2-update-manage/create-and-manage-cold-store.png#lightbox)

1. **(Nem kötelező)** Engedélyezze a meleg tárolást a környezetében, ha gyorsabb és korlátlan lekérdezéseket szeretne a környezetben lévő legfrissebb adatokhoz képest. A Time Series Insights előzetes környezet létrehozása után a bal oldali navigációs ablak **tárolókonfigurációja** beállítással is létrehozhat vagy törölhet egy meleg tárolót.

    [![Meleg tárolási konfiguráció](media/v2-update-manage/create-and-manage-warm-storage.png)](media/v2-update-manage/create-and-manage-warm-storage.png#lightbox)

1. **(Nem kötelező)** Most már hozzáadhat egy eseményforrást. A példány kiépítéséig is megvárhatja.

   * A Time Series Insights az [Azure IoT Hubot](./time-series-insights-how-to-add-an-event-source-iothub.md) és az [Azure Event Hubs-ot](./time-series-insights-how-to-add-an-event-source-eventhub.md) támogatja eseményforrás-beállításként. Bár a környezet létrehozásakor csak egyetlen eseményforrást adhat hozzá, később is hozzáadhat egy másik eseményforrást. 
   
     Az eseményforrás hozzáadásakor kiválaszthat egy meglévő fogyasztói csoportot, vagy létrehozhat egy új fogyasztói csoportot. A legjobb, ha hozzon létre egy egyedi fogyasztói csoportot annak érdekében, hogy minden esemény látható legyen az Azure Time Series Insights előzetes környezetben.

   * Válassza ki a megfelelő Timestamp tulajdonságot. Alapértelmezés szerint az Azure Time Series Insights az üzenet-várólistára helyezett idő az egyes eseményforrás.

     > [!TIP]
     > Előfordulhat, hogy az üzenetvárólistába helyezett idő nem a legjobban konfigurált beállítás a kötegelt eseményhelyzetekben vagy az előzményadatok feltöltési forgatókönyvekben való használatra. Ilyen esetekben győződjön meg arról, hogy ellenőrizze a timestamp tulajdonság használatára vonatkozó döntését.

     [![Az Eseményforrás konfigurációja lap](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Ellenőrizze, hogy a környezet ki van-e építve, és a kívánt módon van-e konfigurálva.

    [![Véleményezés + Létrehozás lap](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>A környezet kezelése

Az Azure Time Series Insights előzetes környezetét az Azure Portalhasználatával kezelheti. A PAYG Azure Time Series Insights előzetes verziójú környezet és az általánosan elérhető S1 vagy S2 környezetek között néhány fő bb különbség van, amelyeket szem előtt kell tartani, amikor a környezetet az Azure Portalon keresztül kezeli:

* Az Azure Portal előzetes **áttekintése** panel a következő módosításokat:

  * A kapacitás eltávolításra kerül, mert nem vonatkozik a PAYG-környezetekre.
  * A **time series id** tulajdonság hozzáadódik. Ez határozza meg, hogyan particionált az adatok.
  * A referencia-adatkészletek törlődnek.
  * A megjelenített URL-cím az [Azure Time Series Insights előzetes verziójának kezelőjéhez](./time-series-insights-update-explorer.md)irányítja.
  * Az Azure Storage-fiók neve szerepel a listán.

* Az Azure Portal **konfigurálása** panel törlődik az Azure Time Series Insights előzetes verzióban, mert payg-környezetek nem konfigurálhatók. A **Storage Configuration** segítségével azonban konfigurálhatja az újonnan bevezetett melegtárolót.

* Az Azure Portal **referenciaadatok** panel törlődik az Azure Time Series Insights előzetes verzióban, mert a referenciaadatok nem része a PAYG-környezetek.

[![Time Series Insights előzetes környezet az Azure Portalon](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>További lépések

- Tudjon meg többet a Time Series Insights általánosan elérhető környezetekről és az előnézeti környezetekről [a Környezet megtervezése](./time-series-insights-update-plan.md)című olvasni.

- További információ az [eseményközpont-forrás hozzáadásáról.](./time-series-insights-how-to-add-an-event-source-eventhub.md)

- Konfiguráljon egy [IoT hubforrást.](./time-series-insights-how-to-add-an-event-source-iothub.md)

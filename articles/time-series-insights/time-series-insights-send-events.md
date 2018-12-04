---
title: Hogyan küldhet eseményeket egy Azure Time Series Insights-környezetbe |} A Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogyan hozhat létre és konfigurálhatja az event hubs és a mintaalkalmazás futtatása események leküldéséhez jelennek meg az Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 64fb9f72cfd7edef18d56f15cbcce726dd33b50d
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847269"
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Események küldése Time Series Insights-környezetbe eseményközponton keresztül

Ez a cikk azt ismerteti, hogyan hozhat létre és konfigurálhatja az eseményközpont, és futtathat egy mintaalkalmazást események leküldéséhez. Ha egy meglévő eseményközponton eseményekkel rendelkező JSON-formátumban, átugorhatja ezt az oktatóanyagot, és megtekintheti a környezetet a [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Eseményközpont konfigurálása

1. Létrehoz egy Eseményközpontot, kövesse az utasításokat az Event hubs [dokumentáció](https://docs.microsoft.com/azure/event-hubs/).
1. Keresse meg az Eseményközpont a keresősávba. Kattintson a **az Event Hubs** a visszaadott listában.
1. Az Eseményközpont nevére kattintva kiválaszthat.
1. A **entitások** középső konfigurációs ablakában kattintson **az Event Hubs** újra.
1. Válassza ki az Event Hubs konfigurálásához nevét.

    ![Consumer-group][1]

1. A **entitások**válassza **fogyasztói csoportok**.
1. Ellenőrizze, hogy kizárólag a TSI eseményforrás által használt fogyasztói csoportot hoz létre.

> [!IMPORTANT]
> Győződjön meg arról, hogy nem használja ezt a fogyasztói csoportot (például Stream Analytics-feladat vagy egy másik TSI-környezet) bármely más szolgáltatást. Ha a fogyasztói csoportot más által használt szolgáltatások, olvassa el a műveletet, az zavarhatja az ebben a környezetben és az egyéb szolgáltatások. Ha használ `$Default` a fogyasztói csoportként vezethet újra más olvasók által.

1. Alatt a **beállítások** szakaszban kattintson **megosztás hozzáférési szabályzatok**.
1. Az eseményközpontok felé, hozzon létre **MySendPolicy** események küldésére használt a C# minta.

    ![megosztott hozzáférés egy az egyhez =][2]

    ![megosztott hozzáférés – két][3]

## <a name="add-time-series-insights-instances"></a>A Time Series Insights-példányok hozzáadása

A TSI-frissítés példányok környezetfüggő adatok hozzáadása a beérkező telemetriai adatokat használ. Az adatok lekérdezési idő használatával csatlakozik egy **Time Series azonosító**. A **Time Series azonosító** projekt van a minta windmills `Id`. További információ a Time Series példányok és **Time Series azonosítók**, olvassa el [Time Series modellek](./time-series-insights-update-tsm.md).

### <a name="create-time-series-insights-event-source"></a>Time Series Insights-eseményforrás létrehozása

1. Ha még nem hozott létre eseményforrást, kövesse ezeket az utasításokat-eseményforrás létrehozása.
1. Adja meg a `timeSeriesId` – tekintse meg [Time Series modellek](./time-series-insights-update-tsm.md) további részleteket ismerhet meg **Time Series azonosítók**.

### <a name="push-events-sample-windmills"></a>Események leküldéséhez (minta windmills)

1. Event hubs a keresősávban keressen. Kattintson a **az Event Hubs** a visszaadott listában.
1. Az eseményközpont nevére kattintva kiválaszthat.
1. Lépjen a **megosztott hozzáférési házirendek** , majd **RootManageSharedAccessKey**. Másolás a **kapcsolati karakterlánc – elsődleges kulcs**

   ![connection-string][4]

1. Nyissa meg a következőt: https://tsiclientsample.azurewebsites.net/windFarmGen.html. Szimulált Szélmalom eszközök fut.
1. Illessze be a három lépésben másolt kapcsolati karakterláncot a **az Eseményközpont kapcsolati Sztringje**
1. Kattintson a **kattintson ide a Start**
1. Lépjen vissza az eseményközpontba. Az új események a hub által fogadott kell megjelennie:

   ![telemetria][5]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tekintse meg a környezetet a Time Series Insights Explorerben](https://insights.timeseries.azure.com).

<!-- Images -->
[1]: media/send-events/consumer-group.png
[2]: media/send-events/shared-access-policy.png
[3]: media/send-events/shared-access-policy-2.png
[4]: media/send-events/sample-code-connection-string.png
[5]: media/send-events/telemetry.png
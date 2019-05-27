---
title: Események küldése Azure Time Series Insights-környezetben |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja egy eseményközpontot, és futtathat egy mintaalkalmazást események leküldéséhez megtekintheti az Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: 48524020940149f6c67f4859f23c03eea140454b
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991480"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Események küldése Time Series Insights-környezetbe az event hubs használatával

Ez a cikk azt ismerteti, hogyan hozhat létre, és az Azure Event Hubs az eseményközpont konfigurálása. Emellett bemutatja, hogyan lehet egy mintaalkalmazást események leküldéséhez futtatása az Azure Time Series Insights az Event hubs Eseményközpontokból. Ha egy meglévő eseményközponton eseményekkel rendelkező JSON-formátumban, átugorhatja ezt az oktatóanyagot, és megtekintheti a környezetet a [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Eseményközpont konfigurálása

1. Ismerje meg, hogyan hozhat létre egy event hubot, tekintse meg a [Event Hubs – dokumentáció](https://docs.microsoft.com/azure/event-hubs/).
1. A keresőmezőbe keresése **az Event Hubs**. Jelölje ki a visszaadott listában **az Event Hubs**.
1. Az event hubs kiválasztása.
1. Amikor létrehoz egy eseményközpontot, az eseményközpont-névteret hoz létre. Ha Ön még nem hozta létre egy eseményközpont a névtéren belül, a menü alatt **entitások**, létrehoz egy eseményközpontot.  

    [![Az event hubs listája](media/send-events/updated.png)](media/send-events/updated.png#lightbox)

1. Miután létrehozott egy eseményközpontba, válassza ki a listából az event hubs.
1. Kattintson a menü alatt **entitások**válassza **az Event Hubs**.
1. Válassza ki az event hubs konfigurálásához nevét.
1. Alatt **entitások**válassza **fogyasztói csoportok**, majd válassza ki **fogyasztói csoportot**.

    [![Hozzon létre egy fogyasztói csoportot](media/send-events/consumer-group.png)](media/send-events/consumer-group.png#lightbox)

1. Ellenőrizze, hogy kizárólag a Time Series Insights-eseményforrás által használt fogyasztói csoportot hoz létre.

    > [!IMPORTANT]
    > Ellenőrizze, hogy ezt a fogyasztói csoportot nem használja másik szolgáltatás, például az Azure Stream Analytics-feladat vagy másik Time Series Insights-környezetbe. Ha a fogyasztói csoportot használ a másik szolgáltatások, az olvasási műveletek negatívan érinti, ebben a környezetben, és más szolgáltatásokhoz. Ha **$Default** fogyasztói csoportot más olvasók előfordulhat, hogy potenciálisan újból felhasználhatja a fogyasztói csoportot.

1. A menü alatt **beállítások**, jelölje be **megosztott elérési házirendek**, majd válassza ki **hozzáadása**.

    [![Megosztott elérési házirendek kiválasztása, és válassza a Hozzáadás gombra.](media/send-events/shared-access-policy.png)](media/send-events/shared-access-policy.png#lightbox)

1. Az a **új megosztott elérési házirend hozzáadása** panelen hozzon létre egy megosztott hozzáférés – nevesített **MySendPolicy**. A megosztott elérési szabályzat használatával az események küldése az C# később a cikkben szereplő példákat.

    [![(A szabályzat neve) mezőben adja meg a MySendPolicy](media/send-events/shared-access-policy-2.png)](media/send-events/shared-access-policy-2.png#lightbox)

1. A **jogcím**, jelölje be a **küldése** jelölőnégyzetet.

## <a name="add-a-time-series-insights-instance"></a>Egy Time Series Insights-példány hozzáadása

A Time Series Insights frissítés példányok környezetfüggő adatok hozzáadása a beérkező telemetriai adatokat használ. Az adatok használatával lekérdezéskor csatlakozik egy **Time Series azonosító**. A **Time Series azonosító** a minta windmills projekt, amely a cikk későbbi részében használjuk a `id`. További információ a Time Series Insight-példányokról és **Time Series azonosító**, lásd: [Time Series modellek](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Egy Time Series Insights-eseményforrás létrehozása

1. Ha még nem hozott létre eseményforrást, hajtsa végre a lépéseket [-eseményforrás létrehozása](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Beállítható egy érték `timeSeriesId`. Tudjon meg többet a **Time Series azonosító**, lásd: [Time Series modellek](./time-series-insights-update-tsm.md).

### <a name="push-events"></a>Események leküldéséhez (windmills minta)

1. A keresősávban keressen **az Event Hubs**. Jelölje ki a visszaadott listában **az Event Hubs**.

1. Az event hubs kiválasztása.

1. Lépjen a **megosztott hozzáférési házirendek** > **RootManageSharedAccessKey**. Másolja az értéket a **kapcsolati karakterlánc – elsődleges kulcs**.

    [![Másolja az elsődleges kulcs kapcsolati karakterlánc értéke](media/send-events/sample-code-connection-string.png)](media/send-events/sample-code-connection-string.png#lightbox)

1. Nyissa meg a következőt: https://tsiclientsample.azurewebsites.net/windFarmGen.html. Az URL-cím Szélmalom szimulált eszközök futtatja.
1. Az a **az Eseményközpont kapcsolati Sztringje** a weblapon mezőbe illessze be a kimásolt kapcsolati karakterláncot [elküldi az eseményeket](#push-events).
  
    [![Az Eseményközpont kapcsolati Sztringje mezőbe illessze be az elsődleges kulcs kapcsolati karakterlánca](media/send-events/updated_two.png)](media/send-events/updated_two.png#lightbox)

1. Válassza ki **elindításához kattintson**. A szimulátor állít elő, példány JSON-t közvetlenül is használhatja.

1. Lépjen vissza az event hubs az Azure Portalon. Az a **áttekintése** lapon megjelenik az új események az event hub által fogadott.

    [![Event hub áttekintő oldala, amely bemutatja az event hubs-mérőszámai](media/send-events/telemetry.png)](media/send-events/telemetry.png#lightbox)

## <a name="json"></a>Támogatott JSON-alakzatok

### <a name="example-one"></a>Egy példa

* **Bemeneti**: Egyszerű JSON-objektum.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Kimeneti**: Egy esemény.

    |azonosító|időbélyeg|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>A példában két

* **Bemeneti**: JSON-tömb két JSON-objektummal. Minden JSON-objektum eseménnyé lesz konvertálva.

    ```JSON
    [
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
    ```

* **Kimeneti**: Két események.

    |azonosító|időbélyeg|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>A példában három

* **Bemeneti**: A beágyazott JSON-tömb két JSON-objektumot tartalmazó JSON-objektum.

    ```JSON
    {
        "location":"WestUs",
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z"
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z"
            }
        ]
    }
    ```

* **Kimeneti**: Két események. A tulajdonság **hely** minden esemény át van másolva.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>A példában négy

* **Bemeneti**: A beágyazott JSON-tömb két JSON-objektumot tartalmazó JSON-objektum. Ez a bemenet azt szemlélteti, hogy globális tulajdonságok is szerepelhetnek a komplex JSON-objektumot.

    ```JSON
    {
        "location":"WestUs",
        "manufacturer":{
            "name":"manufacturer1",
            "location":"EastUs"
        },
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z",
                "data":{
                    "type":"pressure",
                    "units":"psi",
                    "value":108.09
                }
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z",
                "data":{
                    "type":"vibration",
                    "units":"abs G",
                    "value":217.09
                }
            }
        ]
    }
    ```

* **Kimeneti**: Két események.

    |hely|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>További lépések

- [Tekintse meg a környezetet](https://insights.timeseries.azure.com) a Time Series Insights Explorer.

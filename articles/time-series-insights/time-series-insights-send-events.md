---
title: Események küldése egy környezetbe – Azure Time Series Insights | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat egy Event hubot, hogyan futtathat egy minta alkalmazást, és hogyan küldhet eseményeket a Azure Time Series Insights-környezetbe.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: cdcd64b5281ce16002720072db3b5f29f1978cac
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014833"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Események küldése Time Series Insights-környezetbe az event hubs használatával

Ez a cikk bemutatja, hogyan hozhat létre és konfigurálhat egy Event hub-t az Azure Event Hubsban. Azt is leírja, hogyan futtathat egy minta alkalmazást, hogy leküldéses eseményeket Azure Time Series Insights a Event Hubsról. Ha van egy meglévő Event hub JSON formátumú eseményekkel, ugorja át ezt az oktatóanyagot, és tekintse meg a környezetét [Azure Time Series Insightsban](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Eseményközpont konfigurálása

1. Ismerje meg, hogyan hozhat létre egy event hubot, tekintse meg a [Event Hubs – dokumentáció](https://docs.microsoft.com/azure/event-hubs/).
1. A keresőmezőbe keresése **az Event Hubs**. Jelölje ki a visszaadott listában **az Event Hubs**.
1. Az event hubs kiválasztása.
1. Az Event hub létrehozásakor egy Event hub-névteret hoz létre. Ha még nem hozott létre egy Event hubot a névtéren belül, a menüben az **entitások**alatt hozzon létre egy Event hubot.  

    [az Event hubok ![listája](media/send-events/1-event-hub-namespace.png)](media/send-events/1-event-hub-namespace.png#lightbox)

1. Miután létrehozott egy eseményközpontba, válassza ki a listából az event hubs.
1. A menü **entitások**területén válassza a **Event Hubs**lehetőséget.
1. Válassza ki az event hubs konfigurálásához nevét.
1. Az **Áttekintés**területen válassza a **fogyasztói csoportok**lehetőséget, majd válassza a **fogyasztói csoport**elemet.

    [fogyasztói csoport létrehozása ![](media/send-events/2-consumer-group.png)](media/send-events/2-consumer-group.png#lightbox)

1. Győződjön meg arról, hogy olyan fogyasztói csoportot hoz létre, amelyet kizárólag a Time Series Insights-eseményforrás használ.

    > [!IMPORTANT]
    > Győződjön meg arról, hogy a fogyasztói csoportot nem használja más szolgáltatás, például Azure Stream Analytics vagy más Time Series Insights-környezet. Ha a fogyasztói csoportot használ a másik szolgáltatások, az olvasási műveletek negatívan érinti, ebben a környezetben, és más szolgáltatásokhoz. Ha **$Default** fogyasztói csoportot más olvasók előfordulhat, hogy potenciálisan újból felhasználhatja a fogyasztói csoportot.

1. A menü **Beállítások**területén válassza a **megosztott elérési házirendek**elemet, majd kattintson a **Hozzáadás**gombra.

    [![válassza a megosztott elérési házirendek elemet, majd kattintson a Hozzáadás gombra.](media/send-events/3-shared-access-policy.png)](media/send-events/3-shared-access-policy.png#lightbox)

1. Az a **új megosztott elérési házirend hozzáadása** panelen hozzon létre egy megosztott hozzáférés – nevesített **MySendPolicy**. Ezt a közös hozzáférési szabályzatot használja a C# cikk későbbi részében található példákban szereplő események küldéséhez.

    [![a szabályzat neve mezőbe írja be a MySendPolicy](media/send-events/4-shared-access-policy-confirm.png)](media/send-events/4-shared-access-policy-confirm.png#lightbox)

1. A **jogcím**területen jelölje be a **Küldés** jelölőnégyzetet.

## <a name="add-a-time-series-insights-instance"></a>Egy Time Series Insights-példány hozzáadása

A Time Series Insights frissítés példányok környezetfüggő adatok hozzáadása a beérkező telemetriai adatokat használ. Az adatok használatával lekérdezéskor csatlakozik egy **Time Series azonosító**. A jelen cikk későbbi részében használatos minta szélmalmok projekt **idősorozat-azonosítója** `id`. További információ a Time Series Insight-példányokról és **Time Series azonosító**, lásd: [Time Series modellek](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Egy Time Series Insights-eseményforrás létrehozása

1. Ha még nem hozott létre eseményforrást, hajtsa végre a lépéseket [-eseményforrás létrehozása](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Beállítható egy érték `timeSeriesId`. Tudjon meg többet a **Time Series azonosító**, lásd: [Time Series modellek](./time-series-insights-update-tsm.md).

### <a name="push-events-to-windmills-sample"></a>Leküldéses események a szélmalmok mintába

1. A keresősávban keressen **az Event Hubs**. Jelölje ki a visszaadott listában **az Event Hubs**.

1. Válassza ki az Event hub-példányt.

1. Lépjen a **közös hozzáférési szabályzatok** > **MySendPolicy**. Másolja az értéket a **kapcsolati karakterlánc – elsődleges kulcs**.

    [![az elsődleges kulcshoz tartozó kapcsolatok karakterláncának értékét másolja](media/send-events/5-sample-code-connection-string.png)](media/send-events/5-sample-code-connection-string.png#lightbox)

1. Nyissa meg a következőt: https://tsiclientsample.azurewebsites.net/windFarmGen.html. Az URL-cím Szélmalom szimulált eszközök futtatja.
1. A weblap **esemény hub kapcsolati sztring** mezőjébe illessze be a [szélmalom beviteli mezőjébe](#push-events-to-windmills-sample)másolt kapcsolati karakterláncot.
  
    [![illessze be az elsődleges kulcs kapcsolati karakterláncát az Event hub kapcsolati sztring mezőjébe](media/send-events/6-wind-mill-sim.png)](media/send-events/6-wind-mill-sim.png#lightbox)

1. Válassza ki **elindításához kattintson**. A szimulátor állít elő, példány JSON-t közvetlenül is használhatja.

1. Lépjen vissza az event hubs az Azure Portalon. Az **Áttekintés** oldalon az Event hub által fogadott új események láthatók.

    [![az Event hub áttekintési oldalát, amely az Event hub metrikáit jeleníti meg](media/send-events/7-telemetry.png)](media/send-events/7-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>Támogatott JSON-alakzatok

### <a name="example-one"></a>Példa egy

* **Bemenet**: egy egyszerű JSON-objektum.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Kimenet**: egy esemény.

    |id|időbélyeg|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Példa kettőre

* **Bemenet**: egy JSON-tömb két JSON-objektummal. Minden JSON-objektum eseménnyé lesz konvertálva.

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

* **Kimenet**: két esemény.

    |id|időbélyeg|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Harmadik példa

* **Bemenet**: egy beágyazott JSON-tömböt tartalmazó JSON-objektum, amely két JSON-objektumot tartalmaz.

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

* **Kimenet**: két esemény. A tulajdonság **hely** minden esemény át van másolva.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Négy példa

* **Bemenet**: egy beágyazott JSON-tömböt tartalmazó JSON-objektum, amely két JSON-objektumot tartalmaz. Ez a bemenet azt szemlélteti, hogy globális tulajdonságok is szerepelhetnek a komplex JSON-objektumot.

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

* **Kimenet**: két esemény.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Következő lépések

- [Tekintse meg a környezetet](https://insights.timeseries.azure.com) a Time Series Insights Explorerben.

- További információ a [IoT hub eszköz üzeneteiről](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)

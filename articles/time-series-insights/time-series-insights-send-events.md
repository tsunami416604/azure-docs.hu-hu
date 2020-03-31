---
title: Események küldése környezetbe – Azure Time Series Insights | Microsoft dokumentumok
description: Megtudhatja, hogyan konfigurálhat egy eseményközpontot, futtathat egy mintaalkalmazást, és hogyan küldhet eseményeket az Azure Time Series Insights-környezetbe.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/11/2020
ms.custom: seodec18
ms.openlocfilehash: c3c7f59ecb3a06d80012917e2da4425a899859d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254247"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Események küldése Idősorozat-elemzési környezetbe egy eseményközpont használatával

Ez a cikk bemutatja, hogyan hozhat létre és konfigurálhat egy eseményközpontot az Azure Event Hubs-ban. Azt is ismerteti, hogyan futtathatja a mintaalkalmazás leküldéses események et az Azure Time Series Insights az Event Hubs.Also describes how to run a sample application to push events to Azure Time Series Insights from Event Hubs. Ha json formátumú eseményekkel rendelkező meglévő eseményközponttal rendelkezik, hagyja ki ezt az oktatóanyagot, és tekintse meg a környezetet az [Azure Time Series Insights](./time-series-insights-update-create-environment.md)ban.

## <a name="configure-an-event-hub"></a>Eseményközpont konfigurálása

1. Az eseményközpont létrehozásáról az [Event Hubs dokumentációjában](https://docs.microsoft.com/azure/event-hubs/)tájékozódhat.
1. A keresőmezőben keresse meg az **Eseményközpontok kifejezést.** A visszaadott listában válassza az **Eseményközpontok elemet.**
1. Válassza ki az eseményközpontot.
1. Amikor létrehoz egy eseményközpontot, létrehoz egy eseményközpont névteret. Ha még nem hozott létre eseményközpontot a névtérben, a menü **Entitások**csoportban hozzon létre egy eseményközpontot.  

    [![Eseményközpontok listája](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. Miután létrehozott egy eseményközpontot, jelölje ki azt az eseményközpontok listájában.
1. A menü **Entitások területén**válassza az **Eseményközpontok lehetőséget.**
1. Válassza ki a konfigurálni kívánt eseményközpont nevét.
1. Az **Áttekintés csoportban**válassza a **Fogyasztói csoportok**lehetőséget, majd a Fogyasztói **csoport**lehetőséget.

    [![Fogyasztói csoport létrehozása](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Győződjön meg arról, hogy olyan fogyasztói csoportot hoz létre, amelyet kizárólag a Time Series Insights eseményforrása használ.

    > [!IMPORTANT]
    > Győződjön meg arról, hogy ezt a fogyasztói csoportot nem használja más szolgáltatás, például egy Azure Stream Analytics-feladat vagy egy másik Time Series Insights-környezet. Ha a fogyasztói csoportot a többi szolgáltatás használja, az olvasási műveleteket hátrányosan érinti mind a környezet, mind más szolgáltatások esetében. Ha **$Default** használ fogyasztói csoportként, más olvasók esetleg újra felhasználhatják a fogyasztói csoportot.

1. A menü **Beállítások**területén válassza a **Megosztott hozzáférési házirendek**lehetőséget, majd kattintson a **Hozzáadás gombra.**

    [![Válassza a Megosztott hozzáférési házirendek lehetőséget, majd a Hozzáadás gombot](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. Az **Új megosztott hozzáférésházirend hozzáadása** ablaktáblán hozzon létre egy **MySendPolicy**nevű megosztott hozzáférést. Ezzel a megosztott hozzáférési szabályzattal üzeneteket küldhet a C# példákban a cikk későbbi részében.

    [![A Házirend neve mezőbe írja be a MySendPolicy értéket.](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. A **Jogcím**csoportban jelölje be a **Küldés** jelölőnégyzetet.

## <a name="add-a-time-series-insights-instance"></a>Idősorozat-elemzési példány hozzáadása

A Time Series Insights-frissítés példányok segítségével környezetfüggő adatokat ad hozzá a bejövő telemetriai adatokhoz. Az adatok lekérdezéskor **idősorozat-azonosítóval kapcsolódnak egymáshoz.** A **Time Series ID** a minta szélmalmok `id`projekt, hogy használjuk később ebben a cikkben . Ha többet szeretne megtudni a Time Series Insight példányairól és **az idősorozat-azonosítóról,** olvassa el a [Time Series Models](./time-series-insights-update-tsm.md)című információt.

### <a name="create-a-time-series-insights-event-source"></a>Time Series Insights eseményforrás létrehozása

1. Ha még nem hozott létre eseményforrást, hajtsa végre az [eseményforrás létrehozásának lépéseit.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)

1. Állítson be `timeSeriesId`egy értéket a számára. Ha többet szeretne megtudni az **idősorozat-azonosítóról,** olvassa el a [Time Series Models](./time-series-insights-update-tsm.md)című .

### <a name="push-events-to-windmills-sample"></a>Push események szélmalmok minta

1. A keresősávban keresse meg az **Eseményközpontok kifejezést.** A visszaadott listában válassza az **Eseményközpontok elemet.**

1. Válassza ki az eseményközpont-példányt.

1. Nyissa meg a **MySendPolicy megosztott hozzáférési házirendeket.** > **MySendPolicy** Másolja a **Kapcsolatkarakterlánc-elsődleges kulcs**értékét.

    [![Az elsődleges kulcs kapcsolati karakterláncának másolása](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. Nyissa meg a következőt: https://tsiclientsample.azurewebsites.net/windFarmGen.html. Az URL szimulált szélmalomeszközöket hoz létre és futtat.
1. A weblap **Eseményközpont-kapcsolati karakterlánc** mezőjébe illessze be a [szélmalom beviteli mezőjébe](#push-events-to-windmills-sample)másolt kapcsolati karakterláncot.
  
    [![Az elsődleges kulcs kapcsolati karakterláncának beillesztése az Event Hub kapcsolati karakterlánca mezőbe](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. Válassza **a Kattintson lehetőséget a kezdéshez.** 

    > [!TIP]
    > A szélmalom-szimulátor a [Time Series Insights GA Query API-kkal](https://docs.microsoft.com/rest/api/time-series-insights/ga-query)hasznos adatként használható JSON-t is létrehozza.

    > [!NOTE]
    > A szimulátor a böngészőlap bezárásáig folytatja az adatok küldését.

1. Lépjen vissza az eseményközpontba az Azure Portalon. Az **Áttekintés** lapon jelennek meg az eseményközpont által fogadott új események.

    [![Eseményközpont áttekintése lap, amely az eseményközpont mutatóit jeleníti meg](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>Támogatott JSON-alakzatok

### <a name="example-one"></a>Példa egy

* **Bemenet**: Egyszerű JSON-objektum.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Kimenet**: Egy esemény.

    |id|időbélyeg|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Példa két

* **Bemenet**: Két JSON-objektummal rendelkező JSON-tömb. Minden JSON-objektum eseménygé alakul át.

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

* **Kimenet**: Két esemény.

    |id|időbélyeg|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Példa három

* **Bemenet**: JSON-objektum beágyazott JSON-tömbbel, amely két JSON-objektumot tartalmaz.

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

* **Kimenet**: Két esemény. A rendszer átmásolja a tulajdonság **helyét** az egyes eseményekbe.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Négy példa

* **Bemenet**: JSON-objektum beágyazott JSON-tömbbel, amely két JSON-objektumot tartalmaz. Ez a bemenet azt mutatja, hogy a globális tulajdonságokat az összetett JSON-objektum képviselheti.

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

* **Kimenet**: Két esemény.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>További lépések

- [Tekintse meg környezetét](https://insights.timeseries.azure.com) a Time Series Insights felfedezőjében.

- További információ az [IoT Hub eszközüzeneteiről](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)

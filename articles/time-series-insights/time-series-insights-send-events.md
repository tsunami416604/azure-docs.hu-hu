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
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 424476b91537c60a6d7f0f9a854453353bf98633
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557019"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Események küldése Time Series Insights-környezetbe az event hubs használatával

Ez a cikk bemutatja, hogyan hozhat létre és az Azure Event Hubs az eseményközpont konfigurálása, és futtassa a mintaalkalmazást események leküldéséhez. Ha rendelkezik egy meglévő eseményközponttal, amely rendelkezik az eseményeket JSON formátumban, átugorhatja ezt az oktatóanyagot, és megtekintheti a környezetet a [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Eseményközpont konfigurálása

1. Ismerje meg, hogyan hozhat létre egy event hubot, tekintse meg a [Event Hubs – dokumentáció](https://docs.microsoft.com/azure/event-hubs/).
1. A keresőmezőbe keresése **az Event Hubs**. Jelölje ki a visszaadott listában **az Event Hubs**.
1. Az event hubs kiválasztása.
1. Amikor létrehoz egy eseményközpontot, valóban létrehoz egy eseményközpont-névteret. Ha Ön még nem hozta létre egy eseményközpont a névtéren belül, a menü alatt **entitások**, létrehoz egy eseményközpontot.  

    ![Az event hubs listája][1]

1. Miután létrehozott egy eseményközpontba, válassza ki a listából az event hubs.
1. A menü alatt **entitások**válassza **az Event Hubs**.
1. Válassza ki az event hubs konfigurálásához nevét.
1. Alatt **entitások**válassza **fogyasztói csoportok**, majd válassza ki **fogyasztói csoportot**.

    ![Hozzon létre egy fogyasztói csoportot][2]

1. Olyan fogyasztói csoportot hozzon létre, amelyet csak a Time Series Insights-eseményforrás használ.

    > [!IMPORTANT]
    > Ellenőrizze, hogy nem használja ezt a fogyasztói csoportot (például az Azure Stream Analytics-feladat vagy másik Time Series Insights-környezet) bármely más szolgáltatást. Ha a fogyasztói csoportot használ a másik szolgáltatások, az olvasási műveletek negatívan érinti, ebben a környezetben, és más szolgáltatásokhoz. Ha **$Default** fogyasztói csoportot más olvasók előfordulhat, hogy potenciálisan újból felhasználhatja a fogyasztói csoportot.

1. A menü alatt **beállítások**, jelölje be **megosztott elérési házirendek**, majd válassza ki **Hozzáadás**.

    ![Megosztott elérési házirendek kiválasztása, és válassza a Hozzáadás gombra.][3]

1. Az a **új megosztott elérési házirend hozzáadása** panelen hozzon létre egy megosztott hozzáférés – nevesített **MySendPolicy**. A megosztott hozzáférési szabályzat segítségével események küldése az C# később a cikkben szereplő példákat.

    ![(A szabályzat neve) mezőben adja meg a MySendPolicy][4]

1. A **jogcím**, jelölje be a **küldése** jelölőnégyzetet.

## <a name="add-a-time-series-insights-instance"></a>Egy Time Series Insights-példány hozzáadása

A Time Series Insights frissítés példányok környezetfüggő adatok hozzáadása a beérkező telemetriai adatokat használ. Az adatok használatával lekérdezéskor csatlakozik egy **Time Series azonosító**. A **Time Series azonosító** a minta windmills projekt, amely a cikk későbbi részében használjuk a **azonosító**. További információ a Time Series Insight-példányokról és **Time Series azonosító**, lásd: [Time Series modellek](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Egy Time Series Insights-eseményforrás létrehozása

1. Ha még nem hozott létre eseményforrást, hajtsa végre a lépéseket [-eseményforrás létrehozása](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Beállítható egy érték `timeSeriesId`. Tudjon meg többet a **Time Series azonosító**, lásd: [Time Series modellek](./time-series-insights-update-tsm.md).

### <a name="push-events"></a>Események leküldéséhez (windmills minta)

1. A keresősávban keressen **az Event Hubs**. Jelölje ki a visszaadott listában **az Event Hubs**.

1. Az event hubs kiválasztása.

1. Lépjen a **megosztott hozzáférési házirendek** > **RootManageSharedAccessKey**. Másolja az értéket a **kapcsolati karakterlánc – elsődleges kulcs**.

    ![Másolja az elsődleges kulcs kapcsolati karakterlánc értéke][5]

1. Nyissa meg a következőt: https://tsiclientsample.azurewebsites.net/windFarmGen.html. Az URL-cím Szélmalom szimulált eszközök futtatja.
1. Az a **az Eseményközpont kapcsolati Sztringje** a weblapon mezőbe illessze be a kimásolt kapcsolati karakterláncot [elküldi az eseményeket](#push-events).
  
    ![Az Eseményközpont kapcsolati Sztringje mezőbe illessze be az elsődleges kulcs kapcsolati karakterlánca][6]

1. Válassza ki **elindításához kattintson**. A szimulátor állít elő, példány JSON-t közvetlenül is használhatja.

1. Lépjen vissza az event hubs az Azure Portalon. Az a **áttekintése** lapon kell megjelennie az új, az event hub által fogadott események:

    ![Event hub áttekintő oldala, amely bemutatja az event hubs-mérőszámai][7]

<a id="json"></a>

## <a name="supported-json-shapes"></a>Támogatott JSON-alakzatok

### <a name="sample-1"></a>1. példa

#### <a name="input"></a>Input (Bemenet)

Egyszerű JSON-objektum:

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```

#### <a name="output-one-event"></a>Kimenet: Egy esemény

|id|időbélyeg|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>2. példa

#### <a name="input"></a>Input (Bemenet)

JSON-tömb két JSON-objektummal. Minden JSON-objektum eseménnyé lesz konvertálva.

```json
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

#### <a name="output-two-events"></a>Kimenet: Két esemény

|id|időbélyeg|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>3. példa

#### <a name="input"></a>Input (Bemenet)

A beágyazott JSON-tömb két JSON-objektumot tartalmazó JSON-objektum:

```json
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

#### <a name="output-two-events"></a>Kimenet: Két esemény

A tulajdonság **hely** minden esemény át van másolva.

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>4. példa

#### <a name="input"></a>Input (Bemenet)

A beágyazott JSON-tömb két JSON-objektumot tartalmazó JSON-objektum. Ez a bemenet azt szemlélteti, hogy globális tulajdonságok is szerepelhetnek a komplex JSON-objektumot.

```json
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

#### <a name="output-two-events"></a>Kimenet: Két esemény

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Time Series Insights Explorer környezetében megtekintése](https://insights.timeseries.azure.com)

<!-- Images -->
[1]: media/send-events/updated.png
[2]: media/send-events/consumer-group.png
[3]: media/send-events/shared-access-policy.png
[4]: media/send-events/shared-access-policy-2.png
[5]: media/send-events/sample-code-connection-string.png
[6]: media/send-events/updated_two.png
[7]: media/send-events/telemetry.png

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
ms.date: 12/03/2018
ms.openlocfilehash: c583c2211297acd83f88d23b2b8cbd9f8207927f
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52867603"
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Események küldése Time Series Insights-környezetbe eseményközponton keresztül

Ez a cikk azt ismerteti, hogyan hozhat létre és konfigurálhatja az eseményközpont, és futtathat egy mintaalkalmazást események leküldéséhez. Ha egy meglévő eseményközponton eseményekkel rendelkező JSON-formátumban, átugorhatja ezt az oktatóanyagot, és megtekintheti a környezetet a [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Eseményközpont konfigurálása

1. Az Eseményközpontok létrehozását, kövesse az utasításokat az Event hubs [dokumentáció](https://docs.microsoft.com/azure/event-hubs/).
1. Keresse meg `Event Hub` a keresősávba. Kattintson a **az Event Hubs** a visszaadott listában.
1. Az Eseményközpont nevére kattintva kiválaszthat.
1. Amikor létrehoz egy Eseményközpontot, valóban létrehoz egy Event Hub-Namespace.  Ha létrehoz egy Eseményközpontot, az Namespace belül, még rendelkezik, hozzon létre egyet entitások alatt.  

    ![frissítve][1]

1. Miután létrehozott egy Eseményközpontba, kattintson annak nevére.
1. A **entitások** középső konfigurációs ablakában kattintson **az Event Hubs** újra.
1. Válassza ki az Event Hubs konfigurálásához nevét.

    ![Consumer-group][2]

1. A **entitások**válassza **fogyasztói csoportok**.
1. Ellenőrizze, hogy kizárólag a TSI eseményforrás által használt fogyasztói csoportot hoz létre.

    > [!IMPORTANT]
    > Győződjön meg arról, hogy nem használja ezt a fogyasztói csoportot (például Stream Analytics-feladat vagy egy másik TSI-környezet) bármely más szolgáltatást. Ha a fogyasztói csoportot más által használt szolgáltatások, olvassa el a műveletet, az zavarhatja az ebben a környezetben és az egyéb szolgáltatások. Ha használ `$Default` a fogyasztói csoportként vezethet újra más olvasók által.

1. Alatt a **beállítások** szakaszban kattintson **megosztás hozzáférési szabályzatok**.
1. Az eseményközpontok felé, hozzon létre **MySendPolicy** események küldésére használt a C# minta.

    ![megosztott hozzáférés egy az egyhez =][3]

    ![megosztott hozzáférés – két][4]

## <a name="add-time-series-insights-instances"></a>A Time Series Insights-példányok hozzáadása

A TSI-frissítés példányok környezetfüggő adatok hozzáadása a beérkező telemetriai adatokat használ. Az adatok lekérdezési idő használatával csatlakozik egy **Time Series azonosító**. A **Time Series azonosító** projekt van a minta windmills `Id`. További információ a Time Series példányok és **Time Series azonosítók**, olvassa el [Time Series modellek](./time-series-insights-update-tsm.md).

### <a name="create-time-series-insights-event-source"></a>Time Series Insights-eseményforrás létrehozása

1. Ha még nem hozott létre eseményforrást, tegye ezt meg [ezeket az utasításokat](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub) követve.
1. Adja meg a `timeSeriesId` – tekintse meg [Time Series modellek](./time-series-insights-update-tsm.md) további részleteket ismerhet meg **Time Series azonosítók**.

### <a name="push-events-sample-windmills"></a>Események leküldéséhez (minta windmills)

1. Event hubs a keresősávban keressen. Kattintson a **az Event Hubs** a visszaadott listában.
1. Az eseményközpont nevére kattintva kiválaszthat.
1. Lépjen a **megosztott hozzáférési házirendek** , majd **RootManageSharedAccessKey**. Másolás a **kapcsolati karakterlánc – elsődleges kulcs**

   ![connection-string][5]

1. Nyissa meg a következőt: https://tsiclientsample.azurewebsites.net/windFarmGen.html. Szimulált Szélmalom eszközök fut.
1. Illessze be a három lépésben másolt kapcsolati karakterláncot a **az Eseményközpont kapcsolati Sztringje**.

    ![connection-string][6]

1. Kattintson a **kattintson ide a kezdő**. A szimulátor is generál egy példány JSON-t közvetlenül is használhatja.
1. Lépjen vissza az eseményközpontba. Az új események a hub által fogadott kell megjelennie:

   ![telemetria][7]

<a id="json"></a>

## <a name="supported-json-shapes"></a>Támogatott JSON-alakzatok

### <a name="sample-1"></a>1. példa

#### <a name="input"></a>Input (Bemenet)

Egyszerű JSON-objektum.

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```

#### <a name="output---one-event"></a>Kimenet – egy esemény

|id|időbélyeg|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>2. példa

#### <a name="input"></a>Input (Bemenet)

JSON-tömb két JSON-objektummal. Minden JSON-objektum eseménnyé lesz átalakítva.
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

#### <a name="output---two-events"></a>Kimenet – két esemény

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

#### <a name="output---two-events"></a>Kimenet – két esemény

Figyelje meg, hogy a "location" tulajdonság mindegyik eseménybe át van másolva.

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>4. példa

#### <a name="input"></a>Input (Bemenet)

Két JSON-objektumot tartalmazó beágyazott JSON-tömbbel rendelkező JSON-objektum. Ez a bemenet azt szemlélteti, hogy a komplex JSON-objektumban a globális tulajdonságok is szerepelhetnek.

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

#### <a name="output---two-events"></a>Kimenet – két esemény

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tekintse meg a környezetet a Time Series Insights Explorerben](https://insights.timeseries.azure.com).

<!-- Images -->
[1]: media/send-events/updated.png
[2]: media/send-events/consumer-group.png
[3]: media/send-events/shared-access-policy.png
[4]: media/send-events/shared-access-policy-2.png
[5]: media/send-events/sample-code-connection-string.png
[6]: media/send-events/updated_two.png
[7]: media/send-events/telemetry.png
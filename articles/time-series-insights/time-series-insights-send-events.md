---
title: "Események küldése Azure Time Series Insights-környezetbe | Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan küldhet eseményeket a Time Series Insights-környezetbe"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: venkatja
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 92e3e64f235e165a6a1772b6e1724789f3ec3049
ms.lasthandoff: 04/25/2017

---
# <a name="send-events-to-a-time-series-insights-environment-via-event-hub"></a>Események küldése Time Series Insights-környezetbe eseményközponton keresztül

Az oktatóanyag elmagyarázza, hogyan hozhat létre és konfigurálhat egy eseményközpontot, és hogyan futtathat egy mintaalkalmazást események leküldéséhez. Ha rendelkezik egy meglévő eseményközponttal, amelyben JSON formátumú események találhatóak, átugorhatja ezt az oktatóanyagot, és megtekintheti a környezetet a [Time Series Explorerben](https://insights.timeseries.azure.com).

## <a name="configure-an-event-hub"></a>Eseményközpont konfigurálása
1. Eseményközpont létrehozásához kövesse az Event Hubs [dokumentációjában](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) foglalt utasításokat.

2. Olyan fogyasztói csoportot hozzon létre, amelyet csak a Time Series Insights-eseményforrás használ.

  > [!IMPORTANT]
  > Ügyeljen arra, hogy ezt a fogyasztói csoportot ne használja másik szolgáltatás (például Stream Analytics-feladat vagy másik Time Series Insights-környezet). Ha a fogyasztói csoportot más szolgáltatások is használják, az zavarhatja az olvasási műveleteket ebben a környezetben és a többi szolgáltatásban is. Ha a „$Default” elemet használja a fogyasztói csoportként, előfordulhat, hogy más olvasók újra fel fogják használni a csoportot.

  ![Az eseményközpont fogyasztói csoportjának kiválasztása](media/send-events/consumer-group.png)

3. Az eseményközpontban hozza létre a „MySendPolicy” elnevezésű házirendet, amelyet az alábbi mintában az események küldésére használunk majd.

  ![A Megosztott elérési házirendek kiválasztása, majd kattintás a Hozzáadás gombra](media/send-events/shared-access-policy.png)  

  ![Új megosztott elérési házirend hozzáadása](media/send-events/shared-access-policy-2.png)  

## <a name="create-time-series-insights-event-source"></a>Time Series Insights-eseményforrás létrehozása
1. Ha még nem hozott létre eseményforrást, tegye ezt meg az [itt](time-series-insights-add-event-source.md) található utasításokat követve.

2. Adja meg a „deviceTimestamp” értéket az időbélyegző-tulajdonság neveként – ezt a tulajdonságot használja a rendszer a tényleges időbélyegzőként az alábbi mintában. Az időbélyegző-tulajdonság neve megkülönbözteti a kis- és nagybetűket, és az értékeknek __éééé-HH-nnTÓÓ:pp:mm.FFFFFFFK__ formátumban kell lenniük, ha JSON formátumban lesznek elküldve az eseményközpontba. Ha a tulajdonság nem létezik az eseményben, akkor a rendszer azt az időpontot használja, amikor az eseményt sorba helyezték az eseményközpontban.

  ![Eseményforrás létrehozása](media/send-events/event-source-1.png)

## <a name="run-sample-code-to-push-events"></a>Mintakód futtatása események leküldéséhez
1. Lépjen a „MySendPolicy” eseményközpont-házirendhez, és másolja a házirendkulccsal rendelkező kapcsolati karakterláncot.

  ![A MySendPolicy kapcsolati karakterlánc másolása](media/send-events/sample-code-connection-string.png)

2. Futtassa a következő kódot, amely 600 eseményt küld mindhárom eszközre. Frissítse az `eventHubConnectionString` elemet a kapcsolati karakterlánccal.

```csharp
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.Rdx.DataGenerator
{
    internal class Program
    {
        private static void Main(string[] args)
        {
            var from = new DateTime(2017, 4, 20, 15, 0, 0, DateTimeKind.Utc);
            Random r = new Random();
            const int numberOfEvents = 600;

            var deviceIds = new[] { "device1", "device2", "device3" };

            var events = new List<string>();
            for (int i = 0; i < numberOfEvents; ++i)
            {
                for (int device = 0; device < deviceIds.Length; ++device)
                {
                    // Generate event and serialize as JSON object:
                    // { "deviceTimestamp": "utc timestamp", "deviceId": "guid", "value": 123.456 }
                    events.Add(
                        String.Format(
                            CultureInfo.InvariantCulture,
                            @"{{ ""deviceTimestamp"": ""{0}"", ""deviceId"": ""{1}"", ""value"": {2} }}",
                            (from + TimeSpan.FromSeconds(i * 30)).ToString("o"),
                            deviceIds[device],
                            r.NextDouble()));
                }
            }

            // Create event hub connection.
            var eventHubConnectionString = @"Endpoint=sb://...";
            var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubConnectionString);

            using (var ms = new MemoryStream())
            using (var sw = new StreamWriter(ms))
            {
                // Wrap events into JSON array:
                sw.Write("[");
                for (int i = 0; i < events.Count; ++i)
                {
                    if (i > 0)
                    {
                        sw.Write(',');
                    }
                    sw.Write(events[i]);
                }
                sw.Write("]");

                sw.Flush();
                ms.Position = 0;

                // Send JSON to event hub.
                EventData eventData = new EventData(ms);
                eventHubClient.Send(eventData);
            }
        }
    }
}

```
## <a name="supported-json-shapes"></a>Támogatott JSON-alakzatok
### <a name="sample-1"></a>1. példa

#### <a name="input"></a>Input (Bemenet)

Egyszerű JSON-objektum.

```json
{
    "deviceId":"device1",
    "deviceTimestamp":"2016-01-08T01:08:00Z"
}
```
#### <a name="output---1-event"></a>Kimenet – 1 esemény

|deviceId|deviceTimestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>2. példa

#### <a name="input"></a>Input (Bemenet)
JSON-tömb két JSON-objektummal. Minden JSON-objektum eseménnyé lesz átalakítva.
```json
[
    {
        "deviceId":"device1",
        "deviceTimestamp":"2016-01-08T01:08:00Z"
    },
    {
        "deviceId":"device2",
        "deviceTimestamp":"2016-01-17T01:17:00Z"
    }
]
```
#### <a name="output---2-events"></a>Kimenet – 2 esemény

|deviceId|deviceTimestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>3. példa

#### <a name="input"></a>Input (Bemenet)

Két JSON-objektumot tartalmazó beágyazott JSON-tömbbel rendelkező JSON-objektum.
```json
{
    "location":"WestUs",
    "events":[
        {
            "deviceId":"device1",
            "deviceTimestamp":"2016-01-08T01:08:00Z"
        },
        {
            "deviceId":"device2",
            "deviceTimestamp":"2016-01-17T01:17:00Z"
        }
    ]
}

```
#### <a name="output---2-events"></a>Kimenet – 2 esemény
A „location” tulajdonság mindegyik eseménybe át van másolva.

|location|events.deviceId|events.deviceTimestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>4. példa

#### <a name="input"></a>Input (Bemenet)

```json
{
    "location":"WestUs",
    "manufacturerInfo":{
        "name":"manufacturer1",
        "location":"EastUs"
    },
    "events":[
        {
            "deviceId":"device1",
            "deviceTimestamp":"2016-01-08T01:08:00Z",
            "deviceData":{
                "type":"pressure",
                "units":"psi",
                "value":108.09
            }
        },
        {
            "deviceId":"device2",
            "deviceTimestamp":"2016-01-17T01:17:00Z",
            "deviceData":{
                "type":"vibration",
                "units":"abs G",
                "value":217.09
            }
        }
    ]
}
```
#### <a name="output---2-events"></a>Kimenet – 2 esemény

|location|manufacturerInfo.name|manufacturerInfo.location|events.deviceId|events.deviceTimestamp|events.deviceData.type|events.deviceData.units|events.deviceData.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Következő lépések

* A környezet megtekintése a [Time Series Insights portálon](https://insights.timeseries.azure.com)


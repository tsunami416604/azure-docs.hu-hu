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
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 9f2d3b57a42efb7b04566278d3267b3cdbed713a
ms.contentlocale: hu-hu
ms.lasthandoff: 07/01/2017

---
<a id="send-events-to-a-time-series-insights-environment-via-event-hub" class="xliff"></a>

# Események küldése Time Series Insights-környezetbe eseményközponton keresztül

Az oktatóanyag elmagyarázza, hogyan hozhat létre és konfigurálhat egy eseményközpontot, és hogyan futtathat egy mintaalkalmazást események leküldéséhez. Ha már van JSON formátumú eseményeket tartalmazó eseményközpontja, ugorja át ezt az oktatóanyagot, és tekintse meg a környezetet a [Time Series Insightsban](https://insights.timeseries.azure.com).

<a id="configure-an-event-hub" class="xliff"></a>

## Eseményközpont konfigurálása
1. Eseményközpont létrehozásához kövesse az Event Hubs [dokumentációjában](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) foglalt utasításokat.

2. Olyan fogyasztói csoportot hozzon létre, amelyet csak a Time Series Insights-eseményforrás használ.

  > [!IMPORTANT]
  > Ügyeljen arra, hogy ezt a fogyasztói csoportot ne használja másik szolgáltatás (például Stream Analytics-feladat vagy másik Time Series Insights-környezet). Ha a fogyasztói csoportot más szolgáltatások is használják, az zavarhatja az olvasási műveleteket ebben a környezetben és a többi szolgáltatásban is. Ha a „$Default” elemet használja a fogyasztói csoportként, előfordulhat, hogy más olvasók újra fel fogják használni a csoportot.

  ![Az eseményközpont fogyasztói csoportjának kiválasztása](media/send-events/consumer-group.png)

3. Az eseményközpontban hozza létre a „MySendPolicy” elnevezésű szabályzatot, amelyet az alábbi C#-példában az események küldésére használunk majd.

  ![A Megosztott elérési házirendek kiválasztása, majd kattintás a Hozzáadás gombra](media/send-events/shared-access-policy.png)  

  ![Új megosztott elérési házirend hozzáadása](media/send-events/shared-access-policy-2.png)  

<a id="create-time-series-insights-event-source" class="xliff"></a>

## Time Series Insights-eseményforrás létrehozása
1. Ha még nem hozott létre eseményforrást, tegye ezt meg az [itt](time-series-insights-add-event-source.md) található utasításokat követve.

2. Adja meg a „deviceTimestamp” értéket az időbélyegző-tulajdonság neveként – ezt a tulajdonságot használja a rendszer a tényleges időbélyegzőként a C#-példában. Az időbélyegző-tulajdonság neve megkülönbözteti a kis- és nagybetűket, és az értékeknek __éééé-HH-nnTÓÓ:pp:mm.FFFFFFFK__ formátumban kell lenniük, ha JSON formátumban lesznek elküldve az eseményközpontba. Ha a tulajdonság nem létezik az eseményben, akkor a rendszer azt az időpontot használja, amikor az eseményt sorba helyezték az eseményközpontban.

  ![Eseményforrás létrehozása](media/send-events/event-source-1.png)

<a id="sample-code-to-push-events" class="xliff"></a>

## Mintakód események leküldéséhez
1. Lépjen a „MySendPolicy” eseményközpont-házirendhez, és másolja a házirendkulccsal rendelkező kapcsolati karakterláncot.

  ![A MySendPolicy kapcsolati karakterlánc másolása](media/send-events/sample-code-connection-string.png)

2. A következő kód futtatásával 600 eseményt küld mindhárom eszközre. Frissítse az `eventHubConnectionString` elemet a kapcsolati karakterlánccal.

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
<a id="supported-json-shapes" class="xliff"></a>

## Támogatott JSON-alakzatok
<a id="sample-1" class="xliff"></a>

### 1. példa

<a id="input" class="xliff"></a>

#### Input (Bemenet)

Egyszerű JSON-objektum.

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```
<a id="output---1-event" class="xliff"></a>

#### Kimenet – 1 esemény

|id|időbélyeg|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

<a id="sample-2" class="xliff"></a>

### 2. példa

<a id="input" class="xliff"></a>

#### Input (Bemenet)
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
<a id="output---2-events" class="xliff"></a>

#### Kimenet – 2 esemény

|id|időbélyeg|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|
<a id="sample-3" class="xliff"></a>

### 3. példa

<a id="input" class="xliff"></a>

#### Input (Bemenet)

Két JSON-objektumot tartalmazó beágyazott JSON-tömbbel rendelkező JSON-objektum.
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
<a id="output---2-events" class="xliff"></a>

#### Kimenet – 2 esemény
A „location” tulajdonság mindegyik eseménybe át van másolva.

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

<a id="sample-4" class="xliff"></a>

### 4. példa

<a id="input" class="xliff"></a>

#### Input (Bemenet)

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
<a id="output---2-events" class="xliff"></a>

#### Kimenet – 2 esemény

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

<a id="next-steps" class="xliff"></a>

## Következő lépések

* A környezet megtekintése a [Time Series Insights portálon](https://insights.timeseries.azure.com)


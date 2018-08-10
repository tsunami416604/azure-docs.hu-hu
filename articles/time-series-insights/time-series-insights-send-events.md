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
ms.date: 04/09/2018
ms.openlocfilehash: 30b83c54d314934f1de170955eec22e7b2a264b8
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629752"
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Események küldése Time Series Insights-környezetbe eseményközponton keresztül
Ez a cikk azt ismerteti, hogyan hozhat létre és konfigurálhatja az eseményközpont, és futtathat egy mintaalkalmazást események leküldéséhez. Ha egy meglévő eseményközponton eseményekkel rendelkező JSON-formátumban, átugorhatja ezt az oktatóanyagot, és megtekintheti a környezetet a [Time Series Insights](https://insights.timeseries.azure.com).

## <a name="configure-an-event-hub"></a>Eseményközpont konfigurálása
1. Eseményközpont létrehozásához kövesse az Event Hubs [dokumentációjában](../event-hubs/event-hubs-create.md) foglalt utasításokat.

2. Keresse meg **eseményközpont** a keresősávba. Kattintson a **az Event Hubs** a visszaadott listában.

3. Az eseményközpont nevére kattintva kiválaszthat.

4. A **entitások** középső konfigurációs ablakában kattintson **az Event Hubs** újra.

5. Válassza ki az event hubs konfigurálásához nevét.

  ![Az eseményközpont fogyasztói csoportjának kiválasztása](media/send-events/consumer-group.png)

6. A **entitások**válassza **fogyasztói csoportok**.
 
7. Olyan fogyasztói csoportot hozzon létre, amelyet csak a Time Series Insights-eseményforrás használ.

   > [!IMPORTANT]
   > Ügyeljen arra, hogy ezt a fogyasztói csoportot ne használja másik szolgáltatás (például Stream Analytics-feladat vagy másik Time Series Insights-környezet). Ha a fogyasztói csoportot más által használt szolgáltatások, olvassa el a műveletet, az zavarhatja az ebben a környezetben és az egyéb szolgáltatások. Ha a „$Default” elemet használja a fogyasztói csoportként, előfordulhat, hogy más olvasók újra fel fogják használni a csoportot.

8. Alatt a **beállítások** szakaszban kattintson **megosztás hozzáférési szabályzatok**.

9. Az eseményközpontok felé, hozzon létre **MySendPolicy** események küldésére #-példában használt.

  ![A Megosztott elérési házirendek kiválasztása, majd kattintás a Hozzáadás gombra](media/send-events/shared-access-policy.png)  

  ![Új megosztott elérési házirend hozzáadása](media/send-events/shared-access-policy-2.png)  

## <a name="add-time-series-insights-reference-data-set"></a>A Time Series Insights referencia-adatkészlet hozzáadása 
Referenciaadatok használata a TSI-ben nyújtott a telemetriai adatokat.  Ebben a kontextusban jelentése ad hozzá az adatokat, és könnyebbé szűrő és összesítés.  A TSI illesztések referenciaadatok bejövő időpontban, és visszamenőlegesen nem tud csatlakozni az adatok.  Ezért fontos a referenciaadatok adatokkal eseményforrás hozzáadása előtt adja hozzá.  Adatok helye vagy az érzékelőadatok írja be például, amelyeket érdemes eszköz/címke vagy adatbiztonságra csatlakoztatása hasznos dimenziók könnyebb szelet és szűrő azonosítója.  

> [!IMPORTANT]
> Konfigurált egy referencia-adatkészlet kellene fontos előzményadatok feltöltésekor.

Gondoskodjon arról, hogy a referenciaadatok helyen, tömeges TSI korábbi adatok feltöltése során.  Ne feledje, a TSI azonnal megkezdi olvasási illesztett esemény forrásból származó adatokat a forrás-e.  Hasznos csatlakoztatása eseményforrás TSI mindaddig, amíg a referenciaadatok működik a különösen akkor, ha a forrás adatokat tartalmaz, várjon. Azt is megteheti akkor megvárhatja az adatok leküldéséhez az esemény-adatforráshoz, amíg a referencia-adatkészlet van beállítva.

Referenciaadatok kezelése, szerepel a webes felhasználói felület a TSI Explorerben engedélyezett, és egy programozható C# API-t. A TSI Explorer egy vizuális felhasználói felülettel fájlok feltöltése vagy illessze be a meglévő hivatkozási adatkészletek JSON vagy CSV formátumban van. Az API-val létrehozhat egy egyéni alkalmazást, amikor szükséges.

A Time Series Insights referencia-adatok kezeléséről további információkért lásd: a [adatok referenciacikk](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

## <a name="create-time-series-insights-event-source"></a>Time Series Insights-eseményforrás létrehozása
1. Ha még nem hozott létre eseményforrást, tegye ezt meg [ezeket az utasításokat](time-series-insights-how-to-add-an-event-source-eventhub.md) követve.

2. Adja meg **deviceTimestamp** az időbélyegző-tulajdonság neveként – ezt a tulajdonságot használja, a tényleges időbélyegzőként a C#-minta. Az időbélyegző-tulajdonság neve megkülönbözteti a kis- és nagybetűket, és az értékeknek __éééé-HH-nnTÓÓ:pp:mm.FFFFFFFK__ formátumban kell lenniük, ha JSON formátumban lesznek elküldve az eseményközpontba. Ha a tulajdonság nem létezik az eseményben, akkor a rendszer azt az időpontot használja, amikor az eseményt sorba helyezték az eseményközpontban.

  ![Eseményforrás létrehozása](media/send-events/event-source-1.png)

## <a name="sample-code-to-push-events"></a>Mintakód események leküldéséhez
1. Nyissa meg az eseményközpont házirendjének nevű **MySendPolicy**. Másolás a **kapcsolati karakterlánc** házirendkulccsal rendelkező.

  ![A MySendPolicy kapcsolati sztring másolása](media/send-events/sample-code-connection-string.png)

2. A következő kód futtatásával 600 eseményt küld mindhárom eszközre. Frissítse az `eventHubConnectionString` elemet a kapcsolati sztringgel.

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
> [Tekintse meg a környezetet a Time Series Insights explorer](https://insights.timeseries.azure.com).

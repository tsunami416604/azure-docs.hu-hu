---
title: Események küldése az Azure idő adatsorozat Insights környezetre |} Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogyan létrehozása és konfigurálása az event hubs, és futtassa a mintaalkalmazást, leküldéses események jelennek meg Azure idő adatsorozat Insights.
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MarkMcGeeAtAquent
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 04/09/2018
ms.openlocfilehash: b418d1114cf6b906dcdee46bbf7e094cbc4a0521
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Események küldése Time Series Insights-környezetbe eseményközponton keresztül
Ez a cikk ismerteti, hogyan hozza létre és konfigurálja az eseményközpont, és futtassa a mintaalkalmazást leküldéses eseményekre. Ha egy meglévő event hubs eseményközpontot, az események JSON formátumban, ez az oktatóanyag kihagyhatja, és megtekintheti a környezet [idő adatsorozat Insights](https://insights.timeseries.azure.com).

## <a name="configure-an-event-hub"></a>Eseményközpont konfigurálása
1. Eseményközpont létrehozásához kövesse az Event Hubs [dokumentációjában](../event-hubs/event-hubs-create.md) foglalt utasításokat.

2. Keresse meg **eseményközpont** a keresési sávon. Kattintson a **Event Hubs** a visszaadott lista.

3. Az eseményközpont kijelöléséhez kattintson a nevére.

4. A **entitások** középső konfigurációs ablakában kattintson **Event Hubs** újra.

5. Válassza ki az event hubs konfigurálásának nevét.

  ![Az eseményközpont fogyasztói csoportjának kiválasztása](media/send-events/consumer-group.png)

6. A **entitások**, jelölje be **fogyasztói csoportok**.
 
7. Olyan fogyasztói csoportot hozzon létre, amelyet csak a Time Series Insights-eseményforrás használ.

   > [!IMPORTANT]
   > Ügyeljen arra, hogy ezt a fogyasztói csoportot ne használja másik szolgáltatás (például Stream Analytics-feladat vagy másik Time Series Insights-környezet). Ha használ egyéb a fogyasztói csoportot szolgáltatások olvasási művelete negatívan befolyásolja ebben a környezetben, és az egyéb szolgáltatások. Ha a „$Default” elemet használja a fogyasztói csoportként, előfordulhat, hogy más olvasók újra fel fogják használni a csoportot.

8. A a **beállítások** elemcsoportban válasszon **megosztás hozzáférési házirendek**.

9. Az eseményközpontok felé, hozzon létre **MySendPolicy** csharp minta események küldésére szolgál.

  ![A Megosztott elérési házirendek kiválasztása, majd kattintás a Hozzáadás gombra](media/send-events/shared-access-policy.png)  

  ![Új megosztott elérési házirend hozzáadása](media/send-events/shared-access-policy-2.png)  

## <a name="add-time-series-insights-reference-data-set"></a>Idő adatsorozat Insights referencia-adatkészlet hozzáadása 
A telemetriai adatok referenciaadatok használatát ÁME contextualizes.  Ebben a kontextusban jelentését felvétele az adatok, és megkönnyíti szűrő és összesítést.  ÁME illesztések referenciaadatok érkező időpontban, és ezek az adatok visszamenőleges nem tudja csatlakoztatni.  Ezért kiemelten fontos annak referenciaadatok egy eseményforrás adatokkal hozzáadása előtt adja hozzá.  Például a hely vagy érzékelő típus esetén hasznos, amelyeket érdemes eszköz/címke/érzékelő csatlakoztatása dimenziók könnyebb szelet és szűrő azonosítója.  

> [!IMPORTANT]
> Hogy konfigurált egy referencia adatkészlet fontos előzményadatokat feltöltésekor.

Győződjön meg arról, hogy referenciaadatok helyen amikor feltöltés korábbi adatok ÁME tömeges.  Ne feledje, ÁME azonnal megkezdi olvasási illesztett esemény forrásból származó adatokat, hogy a forrás-e.  Akkor célszerű csatlakoztatása egy eseményforrás ÁME mindaddig, amíg a referenciaadatok van érvényben, különösen akkor, ha a forrás adatokat tartalmaz, a várakozási idő után. Másik lehetőségként megvárhatja adatokat küldeni a forrás, amíg a referencia-adatkészlet rendelkezésre áll.

Hivatkozás adatok kezelésére, a webes felhasználói felület be van a ÁME Explorer, és van a programozott C# API. ÁME Explorer egy visual felhasználói felülettel fájlokat vagy illessze be a meglévő hivatkozási adatkészletek JSON- vagy CSV formátumban rendelkezik. Az API-val hozhat létre egy egyéni alkalmazást, amikor szükséges.

Referenciaadatok idő adatsorozat insightsban kezeléséről további információkért lásd: a [hivatkozás adatok cikk](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

## <a name="create-time-series-insights-event-source"></a>Time Series Insights-eseményforrás létrehozása
1. Ha még nem hozott létre eseményforrást, tegye ezt meg [ezeket az utasításokat](time-series-insights-how-to-add-an-event-source-eventhub.md) követve.

2. Adja meg **deviceTimestamp** a időbélyeg-tulajdonság neve – Ez a tulajdonság része lesz a C# mintában tényleges időbélyegző. Az időbélyegző-tulajdonság neve megkülönbözteti a kis- és nagybetűket, és az értékeknek __éééé-HH-nnTÓÓ:pp:mm.FFFFFFFK__ formátumban kell lenniük, ha JSON formátumban lesznek elküldve az eseményközpontba. Ha a tulajdonság nem létezik az eseményben, akkor a rendszer azt az időpontot használja, amikor az eseményt sorba helyezték az eseményközpontban.

  ![Eseményforrás létrehozása](media/send-events/event-source-1.png)

## <a name="sample-code-to-push-events"></a>Mintakód események leküldéséhez
1. Nyissa meg az event hub csoportházirend nevű **MySendPolicy**. Másolás a **kapcsolati karakterlánc** a házirend-kulccsal.

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
#### <a name="output---one-event"></a>Kimeneti - események

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
#### <a name="output---two-events"></a>Kimeneti - két esemény

|id|időbélyeg|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>3. példa

#### <a name="input"></a>Input (Bemenet)

Egy beágyazott JSON-tömb, amely két JSON-objektum tartalmazza a JSON-objektum:
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
#### <a name="output---two-events"></a>Kimeneti - két esemény
Figyelje meg, hogy a tulajdonság "hely" másolja a rendszer minden, az esemény.

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
#### <a name="output---two-events"></a>Kimeneti - két esemény

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

### <a name="json-shaping-strategies"></a>JSON formálására stratégiák
Most használja az alábbi példa egy esemény kezdési pont, és majd tárgyalja, és ezek a problémák kiküszöböléséhez stratégiák kapcsolatos problémákat.

#### <a name="payload-1"></a>1. tartalom:
```json
[{
            "messageId": "LINE_DATA",
            "deviceId": "FXXX",
            "timestamp": 1522355650620,
            "series": [{
                        "chId": 3,
                        "value": -3750.0
                  }, {
                        "chId": 13,
                        "value": 0.58015072345733643
                  }, {
                        "chId": 11,
                        "value": 800.0
                  }, {
                        "chId": 21,
                        "value": 0.0
                  }, {
                        "chId": 14,
                        "value": -999.0
                  }, {
                        "chId": 37,
                        "value": 2.445906400680542
                  }, {
                        "chId": 39,
                        "value": 0.0
                  }, {
                        "chId": 40,
                        "value": 1.0
                  }, {
                        "chId": 1,
                        "value": 1.0172575712203979
                  }
            ],
            "EventProcessedUtcTime": "2018-03-29T20:36:21.3245900Z",
            "PartitionId": 2,
            "EventEnqueuedUtcTime": "2018-03-29T20:34:11.0830000Z",
            "IoTHub": {
                  "MessageId": "<17xxx2xx-36x0-4875-9x1x-x428x41x1x68>",
                  "CorrelationId": "<x253x5xx-7xxx-4xx3-91x4-xxx3bx2xx0x3>",
                  "ConnectionDeviceId": "AAAA-ZZ-001",
                  "ConnectionDeviceGenerationId": "<123456789012345678>",
                  "EnqueuedTime": "2018-03-29T20:34:10.7990000Z",
                  "StreamId": null
            }
      }
]
 ```

Ha a tömb események, a hasznos adatok között ÁME leküldéses, minden egyes mérték értéke egy esemény kerül. Így az események, amelyek nem mindig ideális megoldás többlet hozhat létre. Figyelje meg, hogy segítségével a referenciaadatok ÁME tulajdonságként jelentéssel bíró neveket adhat hozzá.  Például létrehozhat referencia-adatkészlet kulcstulajdonság = chId:  

chId mérték egység 24 motor olaj nyomás PSI 25 Számológép szivattyú arány bbl/perc

Referenciaadatok idő adatsorozat insightsban kezeléséről további információkért lásd: a [hivatkozás adatok cikk](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

Az első forgalma egy másik probléma az időbélyeg ezredmásodpercben. ÁME csak ISO formátumú időbélyegeket fogad el. Egy megoldás az, hogy timestamp alapértelmezés ÁME, amely a várólistában levő időbélyeg használatára.

A fenti forgalma alternatívájaként vizsgáljuk meg egy másik példa.  

#### <a name="payload-2"></a>2. tartalom:
```json
{
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "STATE Engine State": 1,
      "unit": "NONE"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "MPC_AAAA-ZZ-001",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Well Head Px 1": -494162.8515625,
      "unit": "psi"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate": 0,
      "unit": "bbl/min"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Engine Fuel Pressure": 0,
      "unit": "psi"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Engine Oil Pressure": 0.58015072345733643,
      "unit": "psi"
}
```

Például a tartalom 1 ÁME fogja tárolni a minden egyes mért érték egyedi eseményként.  A figyelmet a jelentősebb különbség az, hogy ÁME olvassák a *időbélyeg* helyesen ide, mint a ISO.  

Ha a küldött események számát csökkenteni kell, majd volt a információt küld a következők.  

#### <a name="payload-3"></a>3. tartalom:
```json
{
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate": 0,
      "CALC Pump Rate.unit": "bbl/min"
      "Engine Oil Pressure": 0.58015072345733643,
      "Engine Oil Pressure.unit": "psi"
      "Engine Fuel Pressure": 0,
      "Engine Fuel Pressure.unit": "psi"
}
```
Egy végső javaslat nem éri el.

#### <a name="payload-4"></a>4. tartalom:
```json
{
              "line": "Line01",
              "station": "Station 11",
              "gatewayid": "AAAA-ZZ-001",
              "deviceid": "F12XX",
              "timestamp": "2018-03-29T20:34:15.0000000Z",
              "CALC Pump Rate": {
                           "value": 0,
                           "unit": "bbl/min"
              },
              "Engine Oil Pressure": {
                           "value": 0.58015072345733643,
                           "unit": "psi"
              },
              "Engine Fuel Pressure": {
                           "value": 0,
                           "unit": "psi"
              }
}
```

Ez a példa bemutatja a kimeneti JSON egybesimítását után:

```json
{
      "line": "Line01",
      "station": "Station 11",,
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate.value": 0,
      "CALC Pump Rate.unit": "bbl/min"
      "Engine Oil Pressure.value": 0.58015072345733643,
      "Engine Oil Pressure.unit": "psi"
      "Engine Fuel Pressure.value": 0,
      "Engine Fuel Pressure.unit": "psi"
}
```

Lehetősége van a szabadsága más tulajdonságokkal minden, a csatornák belül a saját json-objektumból, miközben továbbra is az események száma alacsony. Ez a megközelítés egybesimított foglalnak további területet, amely a fontos figyelembe venni. ÁME kapacitás események és a mérete alapján, amelyik előbb következik be.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Tekintse meg a környezetben idő adatsorozat Insights explorer](https://insights.timeseries.azure.com).

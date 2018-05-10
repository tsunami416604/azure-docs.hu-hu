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
ms.openlocfilehash: 2621b7fd7a72c4ac3c8cbe7b166a6504f316e3d5
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
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



## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Tekintse meg a környezetben idő adatsorozat Insights explorer](https://insights.timeseries.azure.com).

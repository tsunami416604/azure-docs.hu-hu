---
title: "Azure IoT Hub műveletek figyelése |} Microsoft Docs"
description: "Hogyan használható az Azure IoT Hub műveletek figyelési műveletek az IoT hub valós idejű állapotának figyelésére."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a299f3a5-b14d-4586-9c3b-44aea14ed013
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2017
ms.author: nberdy
ms.openlocfilehash: 94cbef9d01299547a48923876cf134d5f3dafa6b
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2018
---
# <a name="iot-hub-operations-monitoring"></a>Az IoT-központ műveletek figyelése

Figyelési IoT-központ műveletek lehetővé teszi az IoT hub valós idejű műveletek állapotának figyelése. Az IoT-központ között számos modulkategória közül műveletek nyomon követi az események. Dönthet úgy is, az események küldése az egy vagy több kategóriához végpont az IoT hub feldolgozás esetén. Az adatok a hibák figyelése, vagy adatokat minták alapján összetettebb feldolgozás beállítása.

>[!NOTE]
>Az IoT-központ műveletek figyelési elavult, és eltávolítja az IoT-központ 2018. október 10. a. Az operatív és az IoT-központ állapotának figyeléséhez, lásd: [Azure IoT Hub állapotának figyelésére, és a problémák diagnosztizálásához gyorsan][lnk-monitor]. Az elavultként ütemterv kapcsolatos további információkért lásd: [figyelése az Azure IoT-megoldások Azure monitorral és az Azure Resource Health][lnk-blog-announcement].

Az IoT-központ figyeli az események hat kategóriák:

* Eszköz identitása műveletek
* Telemetriát
* Felhő-eszközre küldött üzenetek
* Kapcsolatok
* Fájlfeltöltések
* Üzenetirányítás

> [!IMPORTANT]
> Az IoT-központ műveletek figyelése nem garantálja megbízható és rendezett események. Attól függően, hogy az IoT-központ alapul szolgáló infrastruktúra egyes események előfordulhat, hogy elvész, vagy nem megfelelő sorrendben kézbesíteni. Például sikertelen kapcsolódási kísérletek vagy nagyon gyakori kapcsolat megszakadása egyes eszközökre hiba jelek alapján riasztásokat figyelési műveletek használata. Az eszköz állapotát a konzisztens tároló létrehozásához események figyelése műveletek alapján nem igazolható, pl. követési áruházbeli csatlakoztatva, vagy egy eszköz kapcsolata megszakadt. 

## <a name="how-to-enable-operations-monitoring"></a>Figyelési műveletek engedélyezése

1. Létrehoz egy IoT-központot. Az IoT-központ a létrehozásával találhat útmutatót a [Ismerkedés] [ lnk-get-started] útmutató.

1. Az IoT hub panel megnyitásához. Itt kattintson **figyelési műveletek**.

    ![A portálon konfigurációs figyelési műveletek][1]

1. Válassza ki a figyelési kategóriákat szeretne figyelni, és kattintson a **mentése**. Az események állnak rendelkezésre a felsorolt Event Hub-kompatibilis végpont a olvasását **figyelési beállítások**. Az IoT-központ végpontjának nevezik `messages/operationsmonitoringevents`.

    ![Az IoT hub a figyelési műveletek konfigurálása][2]

> [!NOTE]
> Kiválasztása **részletes** figyelését a **kapcsolatok** kategória hatására az IoT-központ létrehozhat további diagnosztikai üzeneteket. Az összes egyéb kategóriába a **részletes** beállítása a módosításokat az IoT-központ adatok mennyiségére minden hibaüzenet tartalmazza.

## <a name="event-categories-and-how-to-use-them"></a>Esemény kategóriák és a használatukat

Minden egyes kategória nyomon követi a figyelési műveletek IoT-központot, és minden felügyeleti kategória interakcióba más típusú rendelkezik, amely meghatározza, milyen események kategória felépítése séma.

### <a name="device-identity-operations"></a>Eszköz identitása műveletek

Az identitás műveletek eszközkategória nyomon követi az létrehozása, frissíteni vagy törölni egy bejegyzést az IoT hub identitásjegyzékhez tett kísérlet során előforduló hibákat. Ebbe a kategóriába követési akkor hasznos, forgatókönyvek történő üzembe helyezéséhez.

```json
{
    "time": "UTC timestamp",
    "operationName": "create",
    "category": "DeviceIdentityOperations",
    "level": "Error",
    "statusCode": 4XX,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "durationMs": 1234,
    "userAgent": "userAgent",
    "sharedAccessPolicy": "accessPolicy"
}
```

### <a name="device-telemetry"></a>Telemetriát

Az eszközkategória telemetriai nyomon követi a telemetria-feldolgozási folyamat kapcsolódnak, és az IoT hub előforduló hibákról. Ebbe a kategóriába olyan hibák telemetriai események (például a szabályozás) küldésekor és fogadásakor telemetriai események (például a jogosulatlan olvasó). Ez a kategória nem dolgozza fel a futó maga az eszköz által okozott hibákat.

```json
{
    "messageSizeInBytes": 1234,
    "batching": 0,
    "protocol": "Amqp",
    "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="cloud-to-device-commands"></a>Felhő eszközparancsok

A felhő eszközparancsok kategória követi nyomon, hogy a felhő-eszközre küldött üzenetek feldolgozási folyamat kapcsolódnak, és az IoT hub előforduló hibákról. Ebbe a kategóriába tartoznak (például a jogosulatlan feladótól) felhő eszközre üzenetküldésre, (például kézbesítési száma túllépve) felhő eszközre üzenetek fogadására, és a felhő-eszközre küldött üzenetek visszajelzés fogadása (például a visszajelzés lejárt) előforduló hibákat. Ez a kategória nem dolgozza fel a hibák egy eszközről, amely nem megfelelően kezeli a felhő eszközre üzenetet, ha a felhő eszközre üzenet sikeresen lett kézbesítve.

```json
{
    "messageSizeInBytes": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "deliveryAcknowledgement": 0,
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "C2DCommands",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="connections"></a>Kapcsolatok

A kapcsolatok kategória nyomon követi az eszközök csatlakozni, vagy válassza le az IoT-központ előforduló hibákat. Ebbe a kategóriába követési akkor hasznos, jogosulatlan kapcsolódási kísérletek azonosítására és nyomon követése, ha a kapcsolat azért területein gyenge hálózati eszközökhöz.

```json
{
    "durationMs": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID"
}
```

### <a name="file-uploads"></a>Fájlfeltöltések

A fájl feltöltése kategória nyomon követi a fájl feltöltése funkció kapcsolódnak, és az IoT hub előforduló hibákról. Ez a kategória tartalmazza:

* A SAS URI-azonosítóhoz, például amikor egy eszköz értesíti a központ, a feltöltött előtt lejár előforduló hibákat.
* Nem sikerült az eszköz által jelentett feltöltések.
* Ha egy fájl nem található a tároló az IoT-központ értesítési üzenet létrehozása során előforduló hibákat.

Ez a kategória nem dolgozza fel a jelentkező hibák közvetlenül az eszközre van egy feltöltés tároló.

```json
{
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "HTTP",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "fileUpload",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "blobUri": "http//bloburi.com",
    "durationMs": 1234
}
```

### <a name="message-routing"></a>Üzenetirányítás

Az üzenet útválasztási kategória nyomon követi az üzenet útvonal értékelési és végpont-állapotot az IoT-központ által érzékelt során előforduló hibákat. Ebbe a kategóriába olyan események, például amikor egy szabály akkor ad vissza "nem definiált", ha IoT-központ állapotúként jelöli meg a végpont kézbesítetlen, és bármely más hibák végpont. Ebbe a kategóriába nem tartalmazza az üzenetek maguk (például a szabályozás hibák eszköz), a "telemetriát" kategóriához tartozó jelentett bizonyos hibákat.

```json
{
    "messageSizeInBytes": 1234,
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "routes",
    "level": "Error",
    "deviceId": "device-ID",
    "messageId": "ID of message",
    "routeName": "myroute",
    "endpointName": "myendpoint",
    "details": "ExternalEndpointDisabled"
}
```

## <a name="view-events"></a>Események megtekintése

Használhatja a *IOT hubbal-explorer* eszköz gyors teszteléséhez, hogy az IoT hub előállító figyelési esemény. Az eszköz telepítéséhez tekintse át a megjelenő utasításokat a [IOT hubbal-explorer] [ lnk-iothub-explorer] GitHub-tárházban.

1. Győződjön meg arról, hogy a **kapcsolatok** figyelő kategória értéke **részletes** a portálon.

1. Parancsot egy parancssorba a következő parancsot a figyelési végpont olvasásakor:

    ```
    iothub-explorer monitor-ops --login {your iothubowner connection string}
    ```

1. Egy másik-parancssorban futtassa a következő parancsot egy eszköz, eszköz-felhő üzenetküldésre szimulálása:

    ```
    iothub-explorer simulate-device {your device name} --send "My test message" --login {your iothubowner connection string}
    ```

1. A szimulált eszköz csatlakozik az IoT hub, az első parancssori látható a figyelési esemény.

## <a name="connect-to-the-monitoring-endpoint"></a>A figyelési végponthoz kapcsolódni

A figyelési az IoT hub-végpont Event Hub-kompatibilis végpont. Bármely mechanizmus, amely az Event Hubs figyelési üzenetek olvasásakor a végpont is használhatja. Az alábbi minta létrehoz egy alapszintű olvasót, amely nem alkalmas a magas teljesítmény központi telepítés. Az Event Hubs-üzenetek feldolgozásával kapcsolatos további információkért lásd [az Event Hubs használatának első lépéseit][lnk-eventhubs-tutorial] ismertető oktatóanyagot.

A figyelési végponthoz kapcsolódni kell egy kapcsolati karakterláncot és a végpont neve. A következő lépések bemutatják a szükséges értékek keresése a portál:

1. A portálon lépjen az IoT-központ erőforráspanelre.

1. Válasszon **figyelési műveletek**, és jegyezze fel a a **Event Hub-kompatibilis neve** és **Event Hub-kompatibilis végpont** értékeket:

    ![Event Hub-kompatibilis végpont értékek][img-endpoints]

1. Válasszon **megosztott elérési házirendek**, majd válassza a **szolgáltatás**. Jegyezze fel a **elsődleges kulcs** érték:

    ![Szolgáltatás megosztott elérési házirend elsődleges kulcs][img-service-key]

A következő C# kódminta származik a Visual Studio **klasszikus Windows asztal** C# konzolalkalmazást. A projekt már a **WindowsAzure.ServiceBus** NuGet-csomagja telepítve.

* Cserélje le a kapcsolati karakterlánc helyőrzőjét egy olyan kapcsolati karakterlánccal, amely használja a **Event Hub-kompatibilis végpont** és szolgáltatás **elsődleges kulcs** értékek, akkor azt már korábban említettük a következő példában látható módon:

    ```cs
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* A figyelési végpont nevét helyőrzőt cserélje le a **Event Hub-kompatibilis neve** korábban feljegyzett érték.

```cs
class Program
{
    static string connectionString = "{your monitoring endpoint connection string}";
    static string monitoringEndpointName = "{your monitoring endpoint name}";
    static EventHubClient eventHubClient;

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();

        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }

        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }

            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));

            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }
    }
}
```

## <a name="next-steps"></a>További lépések
Az IoT-központ képességeit további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató][lnk-devguide]
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png
[img-endpoints]: media/iot-hub-operations-monitoring/monitoring-endpoint.png
[img-service-key]: media/iot-hub-operations-monitoring/service-key.png

[lnk-blog-announcement]: https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health
[lnk-monitor]: iot-hub-monitor-resource-health.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md

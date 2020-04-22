---
title: Az Azure IoT Hub-műveletek figyelése (elavult) | Microsoft dokumentumok
description: Az Azure IoT Hub-műveletek figyelésének használata az IoT-központ műveleteinek állapotát valós időben figyelheti.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: nberdy
ms.custom: amqp
ms.openlocfilehash: edbc3431c860794c7cd1dd8e5011c0d7d11d692d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732236"
---
# <a name="iot-hub-operations-monitoring-deprecated"></a>IoT Hub-műveletek figyelése (elavult)

Az IoT Hub-műveletek figyelése lehetővé teszi, hogy valós időben figyelje az IoT hub on operations állapotát. Az IoT Hub a műveletek több kategóriája között követi nyomon az eseményeket. Választhatja az események küldését egy vagy több kategóriából az IoT hub feldolgozásra szolgáló végpontjára. Figyelheti az adatokat a hibákat, vagy összetettebb feldolgozást állíthat be az adatminták alapján.

>[!NOTE]
>**2019. március 10-én az IoT Hub-műveletek figyelése elavult, és az IoT Hubból eltávolították.** Az IoT Hub műveleteinek és állapotának figyelése: [Az Azure IoT Hub állapotának figyelése és a problémák gyors diagnosztizálása](iot-hub-monitor-resource-health.md)című témakörben található. Az eprecációs ütemtervről az [Azure IoT-megoldások figyelése az Azure Monitor és](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health)az Azure Resource Health segítségével című témakörben talál további információt.

Az IoT Hub hat eseménykategóriát figyel:

* Eszközidentitás-műveletek
* Eszköztelemetria
* Felhőből eszközre irányuló üzenetek
* Kapcsolatok
* Fájlfeltöltések
* Üzenetek útválasztása

> [!IMPORTANT]
> Az IoT Hub-műveletek figyelése nem garantálja az események megbízható vagy rendezett kézbesítését. Az IoT Hub alapjául szolgáló infrastruktúrától függően előfordulhat, hogy egyes események elvesznek vagy nem sorrendben kézbesítenek. A műveletek figyelésével hibajelzéseken, például sikertelen csatlakozási kísérleteken vagy bizonyos eszközök nagyfrekvenciás kapcsolatbontáson alapuló riasztásokat hozhat létre. Ne hagyatkozzon a műveletek figyelésére szolgáló eseményekre, hogy egységes tárolót hozzon létre az eszköz állapotához, például egy eszköz csatlakoztatott vagy leválasztott állapotát. 

## <a name="how-to-enable-operations-monitoring"></a>A műveletek figyelésének engedélyezése

1. Hozzon létre egy IoT hubot. Az IT-központ létrehozásáról az Első [lépések](quickstart-send-telemetry-dotnet.md) útmutatóban talál útmutatást.

2. Nyissa meg az IoT hub paneljét. Itt kattintson **a Műveletek figyelése gombra.**

    ![Hozzáférési műveletek figyelési konfigurációja a portálon](./media/iot-hub-operations-monitoring/enable-OM-1.png)

3. Jelölje ki a figyelni kívánt figyelési kategóriákat, majd kattintson a **Mentés gombra.** Az események a **Figyelési beállításokban**felsorolt Event Hub-kompatibilis végpontról érhetők el. Az IoT Hub végpont `messages/operationsmonitoringevents`neve.

    ![Műveletek figyelésének konfigurálása az IoT hubon](./media/iot-hub-operations-monitoring/enable-OM-2.png)

> [!NOTE]
> Ha **részletes figyelést** választ a **Kapcsolatok** kategóriában, az IoT Hub további diagnosztikai üzeneteket hoz létre. Az összes többi kategória esetében a részletes beállítás **módosítja** az IoT Hub által az egyes hibaüzenetekben szereplő információk mennyiségét.

## <a name="event-categories-and-how-to-use-them"></a>Eseménykategóriák és használatuk

Minden művelet figyelési kategória nyomon követi a különböző típusú interakció az IoT Hub, és minden figyelési kategória rendelkezik egy séma, amely meghatározza, hogyan események az adott kategóriában épülnek.

### <a name="device-identity-operations"></a>Eszközidentitás-műveletek

Az eszközidentitás-műveletek kategóriában nyomon követi azokat a hibákat, amelyek akkor fordulnak elő, amikor megpróbál létrehozni, frissíteni vagy törölni egy bejegyzést az IoT hub identitás-beállításjegyzékében. Ez a kategória nyomon követése hasznos a kiépítési forgatókönyvek.

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

### <a name="device-telemetry"></a>Eszköztelemetria

Az eszköz telemetriai kategóriában nyomon követi az IoT hubon előforduló és a telemetriai folyamathoz kapcsolódó hibákat. Ez a kategória tartalmazza a telemetriai események (például a szabályozás) és a telemetriai események (például a jogosulatlan olvasó) küldésekor előforduló hibákat. Ez a kategória nem tudja észlelni az eszközön futó kód által okozott hibákat.

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

### <a name="cloud-to-device-commands"></a>Felhőből az eszközre parancsok

A felhőből az eszközre irányuló parancsok kategória nyomon követi az IoT hubon előforduló és a felhőből az eszközre irányuló üzenetfolyamathoz kapcsolódó hibákat. Ebbe a kategóriába tartoznak a felhőből az eszközre irányuló üzenetek küldésekor (például a jogosulatlan feladó), a felhőből az eszközre irányuló üzenetek fogadása (például a kézbesítési szám túllépése), valamint a felhőből az eszközre irányuló üzenetek visszajelzésének (például a lejáró visszajelzések) fogadásakor előforduló hibák. Ez a kategória nem észleli a hibákat egy olyan eszközről, amely nem megfelelően kezeli a felhőből az eszközre irányuló üzenetet, ha a felhőből az eszközre irányuló üzenet sikeresen kézbesítve lett.

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

A kapcsolatok kategória nyomon követi azokat a hibákat, amelyek akkor fordulnak elő, amikor az eszközök csatlakoznak vagy leválasztják az IoT-elosztót. A kategória nyomon követése akkor hasznos, ha azonosítani tudja a jogosulatlan csatlakozási kísérleteket, és nyomon követheti, ha megszakad a kapcsolat a gyenge kapcsolati területeken lévő eszközök számára.

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

A fájlfeltöltési kategória nyomon követi az IoT hubon előforduló és a fájlfeltöltési funkcióval kapcsolatos hibákat. Ez a kategória a következőket tartalmazza:

* Hibák, amelyek a SAS URI-val, például amikor lejár, mielőtt egy eszköz értesíti a hub egy befejezett feltöltés.

* Az eszköz által jelentett sikertelen feltöltések.

* Hibák, amelyek akkor fordulnak elő, ha egy fájl nem található a tárolóban az IoT Hub értesítési üzenetek létrehozása során.

Ez a kategória nem tudja észlelni azolyan hibákat, amelyek közvetlenül akkor fordulnak elő, amikor az eszköz feltölt egy fájlt a tárolóba.

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

### <a name="message-routing"></a>Üzenetek útválasztása

Az üzenetút-küldési kategória nyomon követi az üzenetútvonal-kiértékelés és a végpont állapota során az IoT Hub által érzékelt hibákat. Ez a kategória olyan eseményeket tartalmaz, mint például amikor egy szabály kiértékeli a "nem definiált", amikor az IoT Hub egy végpontot halottként jelöl meg, és a végpontról kapott egyéb hibákat. Ez a kategória nem tartalmazza az üzenetekkel kapcsolatos konkrét hibákat (például az eszköz szabályozási hibáit), amelyek az "eszköz telemetriai adatok" kategóriában vannak jelentve.

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

## <a name="connect-to-the-monitoring-endpoint"></a>Csatlakozás a figyelési végponthoz

Az IoT hub figyelési végpontja egy Event Hub-kompatibilis végpont. Bármilyen mechanizmust, amely együttműködik az Event Hubs a figyelési üzenetek olvasása ebből a végpontból. A következő minta létrehoz egy alapszintű olvasót, amely nem alkalmas a nagy átviteli sebességű telepítéshez. Az Event Hubs-üzenetek feldolgozásával kapcsolatos további információkért lásd [az Event Hubs használatának első lépéseit](../event-hubs/event-hubs-csharp-ephcs-getstarted.md) ismertető oktatóanyagot.

A figyelési végponthoz való csatlakozáshoz kapcsolati karakterláncra és a végpont nevére van szükség. A következő lépések bemutatják, hogyan találhatja meg a szükséges értékeket a portálon:

1. A portálon keresse meg az IoT Hub erőforrás panel.

2. Válassza **az Operations monitoring**lehetőséget, és jegyezze fel az Event **Hub-kompatibilis nevet** és az Event **Hub-kompatibilis** végpontértékeket:

    ![Az Event Hub-kompatibilis végpontértékek](./media/iot-hub-operations-monitoring/monitoring-endpoint.png)

3. Válassza **a Megosztott hozzáférési házirendek**lehetőséget, majd a **szolgáltatás**lehetőséget. Jegyezze fel az **Elsődleges kulcs** értékét:

    ![Szolgáltatás megosztott hozzáférési házirend elsődleges kulcsa](./media/iot-hub-operations-monitoring/service-key.png)

A következő C# kódminta a Visual Studio **Windows Classic Desktop** C# konzolalkalmazásból származik. A projekten telepítve van a **WindowsAzure.ServiceBus** NuGet csomag.

* Cserélje le a kapcsolati karakterlánc helyőrzője egy kapcsolati karakterláncot, amely az **Event Hub-kompatibilis végpont** ot és a szolgáltatás **elsődleges kulcsértékeit** használja, amelyeket korábban a következő példában láthatómódon ismert:

    ```csharp
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Cserélje le a figyelési végpont névhelyőrzőt a korábban említett **Event Hub-kompatibilis** névértékre.

```csharp
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

Az IoT Hub képességeinek további megismeréséhez lásd:

* [Az IoT Hub fejlesztői útmutatója](iot-hub-devguide.md)

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)
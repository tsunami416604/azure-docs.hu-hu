---
title: Az Azure IoT Hub-műveletek (elavult) figyelése |} A Microsoft Docs
description: Hogyan használható az Azure IoT Hub-műveletek állapotát és működését az IoT hub valós idejű figyelésére.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: nberdy
ms.openlocfilehash: 84f28a1cb411e7df156fc08fa683efe7f83eda64
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59258113"
---
# <a name="iot-hub-operations-monitoring-deprecated"></a>IoT Hub-műveletek figyelése (elavult)

Az IoT Hub-műveletek figyelése lehetővé teszi az IoT hub valós idejű műveleti állapotának figyelése. Az IoT Hub események nyomon követi a műveletek számos kategóriájában. Mekkorák egy vagy több kategóriához eseményeket küldeni egy végpontot az IoT hub feldolgozás céljából. Adatok minták alapján összetettebb feldolgozási beállítása, illetve figyelheti az adatait a hibákat.

>[!NOTE]
>Az IoT Hub **műveletek figyelése elavult, és el lett távolítva a 2019. március 10 IoT-központból**. Az operatív és az IoT Hub állapotának figyelése, lásd: [Azure IoT Hub állapotának Monitorozásához és a problémák gyorsan diagnosztizálása](iot-hub-monitor-resource-health.md). Az elavulással kapcsolatos ütemterv kapcsolatos további információkért lásd: [monitorozása az Azure IoT-megoldások az Azure monitorral és az Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health).

Az IoT Hub hat azokat a eseményeket figyeli:

* Eszközművelet identitás
* Eszköztelemetria
* Felhőből az eszközre irányuló üzenetek
* Kapcsolatok
* Fájlfeltöltések
* Üzenetirányítás

> [!IMPORTANT]
> IoT Hub-műveletek monitorozása nem garantálja a megbízható és rendezett kézbesíti az eseményeket. Az IoT Hub alapul szolgáló infrastruktúrát, attól függően néhány esemény előfordulhat, hogy elvész, vagy -i sorrendben. Például a sikertelen csatlakozási kísérletek, illetve az egyes eszközöktől nagy gyakoriságú szétkapcsolások hiba jelek alapján riasztásokat generálni figyelési műveletek használata. Műveletek figyelése az eseményeket az eszköz állapotát a konzisztens tároló létrehozása nem támaszkodhat, például nyomon követése áruházbeli csatlakoztatott vagy le van választva egy eszköz állapotát. 

## <a name="how-to-enable-operations-monitoring"></a>Műveletek figyelése engedélyezése

1. Hozzon létre egy IoT hubot. Az IoT hub létrehozása a talál útmutatást a [Ismerkedés](quickstart-send-telemetry-dotnet.md) útmutató.

2. Az IoT hub panel megnyitásához. Itt kattintson **műveletek figyelése**.

    ![Hozzáférési műveletek konfigurációját a portálon figyelése](./media/iot-hub-operations-monitoring/enable-OM-1.png)

3. Válassza ki a figyelése, és kattintson a kívánt figyelési kategóriák **mentése**. Az események állnak rendelkezésre a felsorolt Event Hub-kompatibilis végpontról való olvasáshoz **figyelési beállítások**. Az IoT Hub-végponton nevezzük `messages/operationsmonitoringevents`.

    ![Műveletek figyelése az IoT hub konfigurálása](./media/iot-hub-operations-monitoring/enable-OM-2.png)

> [!NOTE]
> Kiválasztásával **részletes** figyelését a **kapcsolatok** kategória hatására az IoT Hub létrehozásához további diagnosztikai üzeneteket. Minden más kategóriák a **részletes** beállítása a módosításokat az IoT Hub információ mennyisége minden hibaüzenet tartalmazza.

## <a name="event-categories-and-how-to-use-them"></a>Eseménykategóriák és azok használatát

Egyes műveletek figyelése kategória nyomon követi az IoT Hub és az egyes figyelési kategóriák való interakció más típusú rendelkezik egy sémát, amely meghatározza, milyen események, amelynek felépítése.

### <a name="device-identity-operations"></a>Eszközművelet identitás

Eszközkategória identitás műveletek nyomon követi, létrehozásához, frissítéséhez vagy törléséhez egy bejegyzés az IoT hub eszközidentitás-jegyzékben lévő megkísérlésekor előforduló hibákat. Ebbe a kategóriába követési hasznos forgatókönyvek kiépítéshez.

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

Az eszközkategória telemetriai előforduló hibák az IoT hubra kapcsolódnak, a telemetriai adatok folyamatot követi nyomon. Ez a kategória tartalmazza (például a szabályozás) telemetriai események küldése során előforduló hibákat, valamint telemetrikus eseményeket (például illetéktelen olvasó) fogadását. Ez a kategória nem tényleges magán az eszközön futó által okozott hibákat.

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

### <a name="cloud-to-device-commands"></a>Felhő–eszköz irányú parancsok

A felhőből az eszközre irányuló parancsok kategória előforduló hibák az IoT hubra a felhőből az eszközre irányuló üzenetek folyamat kapcsolódó követi nyomon. Ez a kategória tartalmazza a hibák fordulhatnak elő, amikor a felhőből az eszközre irányuló üzenetküldés (például illetéktelen küldő), (például a szállítási darabszám túllépve) a felhőből az eszközre irányuló üzenetek fogadása és visszajelzés a felhőből az eszközre irányuló üzenetek fogadása (mint például a visszajelzések lejárt). Ez a kategória nem dolgozza hibák az eszközről, amely nem megfelelően kezeli a felhőből az eszközre irányuló üzenet, ha a felhőből az eszközre irányuló üzenet sikeresen kézbesítve.

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

A kapcsolatok kategória hibák fordulhatnak elő, amikor az eszközök csatlakoztatása, vagy válassza le az IoT hub követi nyomon. Ebbe a kategóriába követési akkor hasznos, jogosulatlan kapcsolódási kísérletek azonosításához és nyomon követésére, ha a kapcsolat azért területein gyenge hálózati eszközökhöz.

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

A fájl feltöltése kategória nyomon követi az IoT hubra és a fájlfeltöltési funkciókhoz kapcsolódó hibák. Ez a kategória tartalmazza:

* Az SAS URI-t, például amikor egy eszköz értesíti a hub egy befejezett feltöltésről, mielőtt lejár az előforduló hibákat.

* Nem sikerült az eszköz által jelentett feltöltések.

* Ha egy fájl nem található a tároló az IoT Hub értesítési üzenet létrehozása során előforduló hibákat.

Ez a kategória nem tényleges jelentkező hibák közvetlenül az eszköz egy fájlt tölt Storage.

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

Az üzenet-útválasztási kategória üzenet útvonal értékelése és a végpont-állapotot az IoT Hub által érzékelt során felmerülő hibák nyomon követi. Ez a kategória tartalmazza az eseményeket, mint például amikor egy szabály eredménye "nem definiált", amikor az IoT Hub jelöli meg a végpont kézbesítetlen, valamint a végpont kapott más hibák. Ebbe a kategóriába nem tartalmazza a konkrét hibákat maguk az üzenetek (például a szabályozási hibák eszköz), amely az "eszköz telemetriai" kategóriában szerepelnek.

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

## <a name="connect-to-the-monitoring-endpoint"></a>A felügyeleti végponthoz való csatlakozás

A figyelési végponthoz az IoT hub Event Hub-kompatibilis végpont. Minden olyan mechanizmus, amely együttműködik a erről a végpontról figyelési üzenetek olvasásához az Event Hubs is használhatja. Az alábbi minta létrehoz egy alapszintű olvasót, amely nem alkalmas a nagy átviteli sebességű üzemelő. Event hubs szolgáltatástól érkező üzenetek feldolgozásával kapcsolatos további információkért lásd: a [Event Hubs használatának első lépései](../event-hubs/event-hubs-csharp-ephcs-getstarted.md) oktatóanyag.

A felügyeleti végponthoz csatlakozik, szüksége van egy kapcsolati karakterláncot, és a végpont neve. A következő lépések bemutatják, hogyan találhatja meg a szükséges értékek a portálon:

1. A portálon lépjen az IoT Hub-erőforrás paneljének.

2. Válassza ki **műveletek figyelése**, és jegyezze fel a **Event Hub-kompatibilis nevet** és **Event Hub-kompatibilis végponthoz** értékek:

    ![Event Hub-compatible endpoint values](./media/iot-hub-operations-monitoring/monitoring-endpoint.png)

3. Válasszon **megosztott elérési házirendek**, majd válassza a **szolgáltatás**. Jegyezze fel a **elsődleges kulcs** érték:

    ![Megosztott hozzáférési szabályzat elsődleges kulcsot](./media/iot-hub-operations-monitoring/service-key.png)

Az alábbi C# kódminta egy Visual Studio nézetéből **Windows klasszikus Asztalialkalmazás** C# konzolalkalmazást. A projekt már a **WindowsAzure.ServiceBus** NuGet-csomag telepítve van.

* A kapcsolati karakterlánc helyőrzőjét cserélje le a kapcsolati karakterlánccal, amely a **Event Hub-kompatibilis végpont** és a szolgáltatás **elsődleges kulcs** az alábbi példában látható módon korábban feljegyzett értékekre:

    ```csharp
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Cserélje le a felügyeleti végpont neve helyőrzőjét az **Event Hub-kompatibilis nevet** korábban feljegyzett értéket.

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

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Az IoT Hub fejlesztői útmutató](iot-hub-devguide.md)

* [Edge-eszközök mesterséges Intelligencia telepítése az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)
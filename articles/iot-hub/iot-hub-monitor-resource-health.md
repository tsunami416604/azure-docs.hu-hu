---
title: Az Azure IoT Hub állapotának figyelésére |} Microsoft Docs
description: Azure figyelése és az Azure Resource Health használatával figyelheti az IoT Hub és a problémák diagnosztizálásához gyorsan
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/09/2017
ms.author: kgremban
ms.openlocfilehash: 39171f7d7a7b27ec54f67b592e184e90134a1a52
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850389"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Azure IoT Hub állapotának figyelésére, és a problémák diagnosztizálásához gyorsan

Vállalatok számára, amelyek megvalósítják az Azure IoT Hub erőforrásaik a megbízható teljesítménycsökkenés várható. Segíti a Bezárás tekintse meg a műveletek, az IoT-központ teljesen integrálva van a [Azure figyelő] [ lnk-AM] és [Azure Resource Health] [ lnk-ARH]. A két szolgáltatás használatával látják el az adatokat az IoT-megoldások fel, és megfelelő állapotban fut kell párhuzamosan működik. 

Az Azure figyelőjének értéke a figyelés és naplózás minden Azure-szolgáltatások a egyetlen forrását. A diagnosztikai naplók, amely Azure figyelő hoz létre egyéni feldolgozásra küldhet Naplóelemzési, az Event Hubs vagy az Azure Storage. Azure metrikák és diagnosztikai beállításai biztosítják az erőforrások teljesítményét láthatósága. Ismerje meg, hogy a cikk elolvasása folytatni hogyan [használata Azure figyelő](#use-azure-monitor) az IoT hubbal. 

> [!IMPORTANT]
> Az eseményeket az Azure-figyelő diagnosztikai naplók segítségével az IoT-központ szolgáltatás által kibocsátott megbízható vagy rendezett nem garantált. Egyes események előfordulhat, hogy elvész, vagy nem megfelelő sorrendben kézbesíteni. Diagnosztikai naplók is nem célja, hogy a valós idejű, és a választott cél kerülő események több percig is eltarthat.

Az Azure Resource Health segít diagnosztizálni és segítségre van szüksége, amikor egy Azure problémák hatással van az erőforrások. A személyre szabott irányítópultok biztosít az IoT-központok aktuális és korábbi állapotát. Ismerje meg, hogy a cikk elolvasása folytatni hogyan [használata Azure Resource Health](#use-azure-resource-health) az IoT hubbal. 

Mellett a két szolgáltatás integrálása, IoT-központ is biztosít a saját metrikákat, amelyek segítségével az IoT-erőforrások állapotának ismertetése. További tudnivalókért lásd: [megérteni az IoT-központ metrikák][lnk-metrics].

## <a name="use-azure-monitor"></a>Az Azure Monitor használata

Az Azure erőforrás-szintű diagnosztikai információkat, ami azt jelenti, hogy történik az IoT hub-műveletek figyelheti biztosít. 

Az Azure figyelő diagnosztikai beállítások cserél az IoT-központ műveletek figyelése Ha figyelési műveletek jelenleg használ, át kell telepítenie a munkafolyamatokat. További információkért lásd: [származó Diagnostics figyelési beállítások áttelepítése][lnk-migrate].

Az adott mérőszámok és eseményeket, amelyek Azure figyelő figyeli kapcsolatos további információkért lásd: [támogatott Azure-figyelő metrikák] [ lnk-AM-metrics] és [az Azure-szolgáltatások, a sémák és a kategóriák támogatott Diagnosztikai naplók][lnk-AM-schemas].

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>A naplók megértése

Az Azure figyelő azt követi nyomon az IoT-központ előforduló különféle műveletekkel. Minden kategória egy séma, amely meghatározza, milyen események kategória jelenti. 

#### <a name="connections"></a>Kapcsolatok

A kapcsolatok kategória nyomon követi eszköz csatlakozni, és egy IoT-központot, valamint a hibák kapcsolatbontási eseményt. Ebbe a kategóriába követési akkor hasznos, jogosulatlan kapcsolódási kísérletek azonosítására és nyomon követése, ha a kapcsolat azért területein gyenge hálózati eszközökhöz.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Information",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}", 
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-commands"></a>Felhő eszközparancsok

A felhő eszközparancsok kategória követi nyomon, hogy a felhő-eszközre küldött üzenetek feldolgozási folyamat kapcsolódnak, és az IoT hub előforduló hibákról. Ebbe a kategóriába tartoznak (például a jogosulatlan feladótól) felhő eszközre üzenetküldésre, (például kézbesítési száma túllépve) felhő eszközre üzenetek fogadására, és a felhő-eszközre küldött üzenetek visszajelzés fogadása (például a visszajelzés lejárt) előforduló hibákat. Ez a kategória nem dolgozza fel a hibák egy eszközről, amely nem megfelelően kezeli a felhő eszközre üzenetet, ha a felhő eszközre üzenet sikeresen lett kézbesítve.

```json
{
    "time": " UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "messageExpired",
    "category": "C2DCommands",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddresss\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
    "location": "Resource location"
}
```

#### <a name="device-identity-operations"></a>Eszköz identitása műveletek

Az identitás műveletek eszközkategória nyomon követi az létrehozása, frissíteni vagy törölni egy bejegyzést az IoT hub identitásjegyzékhez tett kísérlet során előforduló hibákat. Ebbe a kategóriába követési akkor hasznos, forgatókönyvek történő üzembe helyezéséhez.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "get",
    "category": "DeviceIdentityOperations",
    "level": "Error",    
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
    "location": "Resource location"
}
```

#### <a name="routes"></a>Útvonalak

Az üzenet útválasztási kategória nyomon követi az üzenet útvonal értékelési és végpont-állapotot az IoT-központ által érzékelt során előforduló hibákat. Ebbe a kategóriába olyan események, például amikor egy szabály akkor ad vissza "nem definiált", ha IoT-központ állapotúként jelöli meg a végpont kézbesítetlen, és bármely más hibák végpont. Ebbe a kategóriába nem tartalmazza az üzenetek maguk (például a szabályozás hibák eszköz), a "telemetriát" kategóriához tartozó jelentett bizonyos hibákat.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "endpointUnhealthy",
    "category": "Routes",
    "level": "Error",
    "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
    "location": "Resource location"
}
```

#### <a name="device-telemetry"></a>Telemetriát

Az eszközkategória telemetriai nyomon követi a telemetria-feldolgozási folyamat kapcsolódnak, és az IoT hub előforduló hibákról. Ebbe a kategóriába olyan hibák telemetriai események (például a szabályozás) küldésekor és fogadásakor telemetriai események (például a jogosulatlan olvasó). Ez a kategória nem dolgozza fel a futó maga az eszköz által okozott hibákat.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
    "location": "Resource location"
}
```

#### <a name="file-upload-operations"></a>Fájlfeltöltési műveletek

A fájl feltöltése kategória nyomon követi a fájl feltöltése funkció kapcsolódnak, és az IoT hub előforduló hibákról. Ez a kategória tartalmazza:

* A SAS URI-azonosítóhoz, például amikor egy eszköz értesíti a központ, a feltöltött előtt lejár előforduló hibákat.
* Nem sikerült az eszköz által jelentett feltöltések.
* Ha egy fájl nem található a tároló az IoT-központ értesítési üzenet létrehozása során előforduló hibákat.

Ez a kategória nem dolgozza fel a jelentkező hibák közvetlenül az eszközre van egy feltöltés tároló.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "ingress",
    "category": "FileUploadOperations",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-twin-operations"></a>Felhő eszközre iker műveletek

A felhő eszközre iker műveletek kategória az eszköz twins nyomon követi az szolgáltatás által kezdeményezett események. Ezeket a műveleteket is get iker tartalmazza, jelentett tulajdonságainak frissítése, és feliratkozhat a kívánt tulajdonságai

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "read",
    "category": "C2DTwinOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}", 
    "location": "Resource location"
}
```

#### <a name="device-to-cloud-twin-operations"></a>Eszköz-felhő iker műveletek

Az eszközről a felhőbe a két műveletek kategória az eszköz twins nyomon követi az eszköz által kezdeményezett események. Ezeket a műveleteket is get iker tartalmazza, frissítése vagy cserélje le a címkék, és frissíteni vagy lecserélni kívánt tulajdonságokkal. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "update",
    "category": "D2CTwinOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}", 
    "location": "Resource location"
}
```

#### <a name="twin-queries"></a>A két lekérdezések

A kettős lekérdezések kategória jelentések lekérdezési kérelmek az eszköz twins kezdeményezett a felhőben. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "query",
    "category": "TwinQueries",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}", 
    "location": "Resource location"
}
```

#### <a name="jobs-operations"></a>Feladatműveletek

A feladatok műveletek kategória jelentések feladatkéréseket eszköz twins frissítése vagy közvetlen metódusok több eszközön. Ezek a kérelmek kezdeményezett a felhőben. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "jobCompleted",
    "category": "JobsOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}", 
    "location": "Resource location"
}
```

#### <a name="direct-methods"></a>Közvetlen módszer

A közvetlen módszerek-kategória nyomon követi az egyes eszközökről küldött kérelem-válasz interakciókat. Ezek a kérelmek kezdeményezett a felhőben. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "send",
    "category": "DirectMethods",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\", \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}", 
    "location": "Resource location"
}
```

### <a name="read-logs-from-azure-event-hubs"></a>Olvasási naplók az Azure Event Hubs

Miután beállította a diagnosztikai beállítások keresztül az eseménynaplózás, alkalmazásokat, amelyek olvasható ki a naplókat, hogy elvégezhető művelet a bennük foglalt információk alapján is létrehozhat. A mintakód naplók kikeresi az eseményközpontok:

```csharp
class Program 
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}"; 
    static string monitoringEndpointName = "{your AMS event hub endpoint name}"; 
    static EventHubClient eventHubClient; 
//This is the Diagnostic Settings schema 
    class AzureMonitorDiagnosticLog 
    { 
        string time { get; set; } 
        string resourceId { get; set; } 
        string operationName { get; set; } 
        string category { get; set; } 
        string level { get; set; } 
        string resultType { get; set; } 
        string resultDescription { get; set; } 
        string durationMs { get; set; } 
        string callerIpAddress { get; set; } 
        string correlationId { get; set; } 
        string identity { get; set; } 
        string location { get; set; } 
        Dictionary<string, string> properties { get; set; } 
    }; 
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
                var deserializer = new JavaScriptSerializer(); 
                //deserialize json data to azure monitor object 
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result); 
 
            } 
        } 
    } 
} 
```

## <a name="use-azure-resource-health"></a>Használja az Azure-erőforrás állapota

Az Azure Resource Health segítségével figyelheti, hogy az IoT hub működik és elérhető-e. Itt olvashat e regionális kimaradás érintő-e az IoT hub állapotát. Szeretné megtudni, az Azure IoT Hub állapotának részletes adatait, akkor javasoljuk, hogy Ön [használata Azure figyelő](#use-azure-monitor). 

Azure IoT Hub regionális szintű állapotát jelzi. Ha nincs hatással az IoT hub regionális kimaradás, állapotát jeleníti meg **ismeretlen**. A megadott állapot-ellenőrzési eredményeire Azure Resource Health végző kapcsolatos további információkért lásd: [erőforrástípusainak és állapotát ellenőrzi az Azure-erőforrás állapotának][lnk-ARH-checks].

Az IoT-központok állapotának ellenőrzéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Navigáljon a **szolgáltatásának állapota** > **erőforrás állapota**.
1. A legördülő mezőben válassza ki az előfizetés és **IoT-központ**.

Adatok értelmezése kapcsolatos további információkért lásd: [Azure-erőforrás állapotának áttekintése][lnk-ARH]

## <a name="next-steps"></a>További lépések

- [Az IoT-központ metrikák ismertetése][lnk-metrics]
- [Az IoT távoli figyelés és az értesítések az Azure Logic Apps csatlakoztatása az IoT-központ és postaláda][lnk-monitoring-notifications]


[lnk-AM]: ../monitoring-and-diagnostics/index.yml
[lnk-ARH]: ../service-health/resource-health-overview.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-migrate]: iot-hub-migrate-to-diagnostics-settings.md
[lnk-AM-metrics]: ../monitoring-and-diagnostics/monitoring-supported-metrics.md
[lnk-AM-schemas]: ../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md
[lnk-ARH-checks]: ../service-health/resource-health-checks-resource-types.md
[lnk-monitoring-notifications]: iot-hub-monitoring-notifications-with-azure-logic-apps.md

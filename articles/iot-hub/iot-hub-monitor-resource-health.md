---
title: Az Azure IoT Hub állapotának figyelése |} A Microsoft Docs
description: Az Azure Monitor és Azure Resource Health segítségével figyelheti az IoT Hub és a problémák gyorsan diagnosztizálása
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/09/2018
ms.author: kgremban
ms.openlocfilehash: d3c32c2258f7542a02549fbc531aa9e8293d0235
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996298"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Azure IoT Hub állapotának monitorozásához és a problémák gyorsan diagnosztizálása

Azure IoT Hub alkalmazó vállalatok erőforrásaikat a megbízható teljesítmény várható. Segítséget egy közeli nézze meg a műveletek a kezelése, az IoT Hub teljesen integrálva van a [Azure Monitor] [ lnk-AM] és [az Azure Resource Health] [ lnk-ARH]. A két szolgáltatás biztosítja az adatok megőrzése be az IoT-megoldások és kifogástalan állapotban fut párhuzamosan működik. 

Az Azure Monitor egyetlen adatforrás a figyelés és naplózás az Azure-szolgáltatásokhoz. Elküldheti a diagnosztikai naplók, amely az Azure Monitor hoz létre a Log Analytics, az Event Hubs vagy Azure Storage egyéni feldolgozáshoz. Az Azure Monitor-metrikák és diagnosztikai beállításait az erőforrások betekintést nyújtanak. Olvassa ebből a cikkből megtudhatja, hogyan [használata az Azure Monitor](#use-azure-monitor) az IoT hubbal. 

> [!IMPORTANT]
> A diagnosztikai naplók az Azure Monitor használatával az IoT Hub-szolgáltatás által kibocsátott események a rendszer nem garantált, hogy a megbízható és rendezett. Néhány esemény esetleg elvész, vagy -i üzemen kívüli. Diagnosztikai naplók is nincsenek szinkronban kell lennie a valós idejű, és jelentkezzen be a kívánt rendeltetési események több percig is eltarthat.

Az Azure Resource Health segítségével diagnosztizálhatja és a támogatás igénylésében, ha az Azure problémái kihat az erőforrásaira. A személyre szabott irányítópultok az IoT-központok biztosít a jelenlegi és korábbi állapotát. Olvassa ebből a cikkből megtudhatja, hogyan [használata az Azure Resource Health](#use-azure-resource-health) az IoT hubbal. 

A két szolgáltatás integrálása, az IoT Hub is nyújt a saját mérőszámok, amelyek segítségével az IoT-erőforrások állapotának ismertetése. További tudnivalókért lásd: [megismerheti az IoT Hub-metrikák][lnk-metrics].

## <a name="use-azure-monitor"></a>Az Azure Monitor használata

Az Azure Monitor erőforrás-szintű diagnosztikai adatokat, ami azt jelenti, hogy figyelemmel kísérheti a történik az IoT hub-műveletek biztosít. 

Az Azure Monitor diagnosztikai beállításait lecseréli az IoT Hub-műveletek monitorozása. Ha jelenleg használja a műveletek figyelése, át kell telepítenie a munkafolyamatokat. További információkért lásd: [a műveletek figyelése a diagnosztikai beállítások][lnk-migrate].

Az adott mérőszámok és eseményeket figyeli az Azure Monitor kapcsolatos további információkért lásd: [az Azure monitorban támogatott mérőszámok] [ lnk-AM-metrics] és [támogatott szolgáltatások, sémákat és kategóriák az Azure-hoz Diagnosztikai naplók][lnk-AM-schemas].

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>A naplók értelmezése

Az Azure Monitor nyomon követi az IoT Hub előforduló különféle műveletek. Az egyes kategóriákhoz hogyan készül jelentés adott kategóriába tartozó eseményeket definiáló séma. 

#### <a name="connections"></a>Kapcsolatok

A kapcsolatok kategóriában nyomon követi eszköz csatlakoztatása, és események leválasztása az IoT hubra, valamint a hibák. Ebbe a kategóriába követési akkor hasznos, jogosulatlan kapcsolódási kísérletek azonosításához és nyomon követésére, ha a kapcsolat azért területein gyenge hálózati eszközökhöz.

> [!NOTE]
> Az eszközök megbízható kapcsolat állapotának ellenőrzése [eszköz szívverés][lnk-devguide-heartbeat].

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

#### <a name="cloud-to-device-commands"></a>Felhőből az eszközre irányuló parancsok

A felhőből az eszközre irányuló parancsok kategória előforduló hibák az IoT hubra a felhőből az eszközre irányuló üzenetek folyamat kapcsolódó követi nyomon. Ez a kategória tartalmazza a hibák fordulhatnak elő, amikor a felhőből az eszközre irányuló üzenetküldés (például illetéktelen küldő), (például a szállítási darabszám túllépve) a felhőből az eszközre irányuló üzenetek fogadása és visszajelzés a felhőből az eszközre irányuló üzenetek fogadása (mint például a visszajelzések lejárt). Ez a kategória nem dolgozza hibák az eszközről, amely nem megfelelően kezeli a felhőből az eszközre irányuló üzenet, ha a felhőből az eszközre irányuló üzenet sikeresen kézbesítve.

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

#### <a name="device-identity-operations"></a>Eszközművelet identitás

Eszközkategória identitás műveletek nyomon követi, létrehozásához, frissítéséhez vagy törléséhez egy bejegyzés az IoT hub eszközidentitás-jegyzékben lévő megkísérlésekor előforduló hibákat. Ebbe a kategóriába követési hasznos forgatókönyvek kiépítéshez.

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

Az üzenet-útválasztási kategória üzenet útvonal értékelése és a végpont-állapotot az IoT Hub által érzékelt során felmerülő hibák nyomon követi. Ez a kategória tartalmazza az eseményeket, mint például amikor egy szabály eredménye "nem definiált", amikor az IoT Hub jelöli meg a végpont kézbesítetlen, valamint a végpont kapott más hibák. Ebbe a kategóriába nem tartalmazza a konkrét hibákat maguk az üzenetek (például a szabályozási hibák eszköz), amely az "eszköz telemetriai" kategóriában szerepelnek.

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

#### <a name="device-telemetry"></a>Eszköztelemetria

Az eszközkategória telemetriai előforduló hibák az IoT hubra kapcsolódnak, a telemetriai adatok folyamatot követi nyomon. Ez a kategória tartalmazza (például a szabályozás) telemetriai események küldése során előforduló hibákat, valamint telemetrikus eseményeket (például illetéktelen olvasó) fogadását. Ez a kategória nem tényleges magán az eszközön futó által okozott hibákat.

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

A fájl feltöltése kategória nyomon követi az IoT hubra és a fájlfeltöltési funkciókhoz kapcsolódó hibák. Ez a kategória tartalmazza:

* Az SAS URI-t, például amikor egy eszköz értesíti a hub egy befejezett feltöltésről, mielőtt lejár az előforduló hibákat.
* Nem sikerült az eszköz által jelentett feltöltések.
* Ha egy fájl nem található a tároló az IoT Hub értesítési üzenet létrehozása során előforduló hibákat.

Ez a kategória nem tényleges jelentkező hibák közvetlenül az eszköz egy fájlt tölt Storage.

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

#### <a name="cloud-to-device-twin-operations"></a>Felhőalapú ikereszköz műveletek

A felhő ikereszköz műveletkategória események szolgáltatás által az ikereszközök követi nyomon. Ezek a műveletek belefoglalhat get ikereszköz, frissítése vagy cserélje le a címkéket, és a frissítése vagy cserélje le a kívánt tulajdonságok. 

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

#### <a name="device-to-cloud-twin-operations"></a>Eszközről a felhőbe – az ikereszköz-műveletek

Az eszközről a felhőbe – az ikereszköz műveletkategória eszköz által kezdeményezett események az ikereszközök követi nyomon. Ezek a műveletek közé tartozik a get-twin, jelentett tulajdonságok frissítésére, és feliratkozhat a kívánt tulajdonságok.

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

#### <a name="twin-queries"></a>Ikereszköz-lekérdezések

Az ikereszköz-lekérdezéseket kategória, amely a felhőben kezdeményezett ikereszközök lekérdezésekre vonatkozó kérelmek kapcsolatos jelentések. 

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

A feladatok műveletkategória feladatkérések ikereszközök frissítéséhez, vagy több eszközön közvetlen metódusok meghívása kapcsolatos jelentések. Ezek a kérelmek kezdeményezett a felhőben. 

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

#### <a name="direct-methods"></a>Közvetlen metódusok

A közvetlen metódusok kategória nyomon követi a kérés-válasz interakciók egyes eszközöknek. Ezek a kérelmek kezdeményezett a felhőben. 

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

### <a name="read-logs-from-azure-event-hubs"></a>Az Azure Event Hubs naplóinak olvasása

Miután beállította a diagnosztikai beállításokon keresztül eseménynaplózás, olvassa el a naplókat, hogy azok az információk alapján műveleteket végezheti el alkalmazásokat hozhat létre. A mintakód beolvassa a naplók egy adott eseményközpontból:

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

## <a name="use-azure-resource-health"></a>Az Azure Resource Health eszközt használni

Az Azure Resource Health segítségével figyelheti az IoT hub működik-e. Emellett megismerjük, akár regionális kimaradás az IoT hub állapotának negatív hatással van. Szeretné megtudni, részletes adatait az Azure IoT Hub állapotát, akkor javasoljuk, hogy Ön [használata az Azure Monitor](#use-azure-monitor). 

Az Azure IoT Hub egy regionális szinten állapotát jelzi. Ha van egy regionális kimaradás, mely negatív hatással az IoT hub, az egészségügyi állapota **ismeretlen**. Az Azure Resource Health végző adott állapot-ellenőrzések kapcsolatos további információkért lásd: [erőforrástípusok és állapot-ellenőrzések a az Azure resource health segítségével elérhető][lnk-ARH-checks].

Az IoT hub állapotának ellenőrzéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Navigáljon a **Service Health** > **a Resource health**.
1. A legördülő mezőben válassza ki az előfizetését és **az IoT Hub**.

Egészségügyi adatok értelmezése kapcsolatos további információkért lásd: [az Azure resource health áttekintése][lnk-ARH]

## <a name="next-steps"></a>További lépések

- [Megismerheti az IoT Hub-metrikák][lnk-metrics]
- [IoT távoli figyelés és értesítések az Azure Logic Apps csatlakoztatása az IoT hub és a postaláda][lnk-monitoring-notifications]


[lnk-AM]: ../azure-monitor/index.yml
[lnk-ARH]: ../service-health/resource-health-overview.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-migrate]: iot-hub-migrate-to-diagnostics-settings.md
[lnk-AM-metrics]: ../monitoring-and-diagnostics/monitoring-supported-metrics.md
[lnk-AM-schemas]: ../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md
[lnk-ARH-checks]: ../service-health/resource-health-checks-resource-types.md
[lnk-monitoring-notifications]: iot-hub-monitoring-notifications-with-azure-logic-apps.md
[lnk-devguide-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat

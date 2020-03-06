---
title: Az Azure-IoT Hub állapotának monitorozása | Microsoft Docs
description: A Azure Monitor és a Azure Resource Health segítségével figyelheti a IoT Hubeket, és gyorsan diagnosztizálhatja a problémákat
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: kgremban
ms.openlocfilehash: f801abc40caf273c28a0c01dedf9735f5198c2af
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359558"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Az Azure-IoT Hub állapotának monitorozása és a problémák gyors diagnosztizálása

Az Azure IoT Hubt megvalósító vállalkozások megbízható teljesítményt várnak erőforrásaik számára. A IoT Hub teljes mértékben integrálva van a [Azure monitor](../azure-monitor/index.yml) és a [Azure Resource Health](../service-health/resource-health-overview.md)használatával, hogy segítsen a szoros figyelésben. Ez a két szolgáltatás a IoT-megoldások kifogástalan állapotú fenntartásához szükséges adatmennyiség biztosítására szolgál.

Azure Monitor az összes Azure-szolgáltatás figyelésének és naplózásának egyetlen forrása. Elküldheti azokat a diagnosztikai naplókat, amelyeket Azure Monitor hoz létre az egyéni feldolgozáshoz Azure Monitor naplók, Event Hubs vagy Azure Storage számára. Azure Monitor metrikái és diagnosztikai beállításai megtekinthetik az erőforrások teljesítményét. Folytassa a cikk olvasásával, hogy megtudja, hogyan [használhatja a Azure monitort](#use-azure-monitor) az IoT hub használatával. 

> [!IMPORTANT]
> A IoT Hub szolgáltatás által a Azure Monitor diagnosztikai naplók használatával kibocsátott események nem garantáltak megbízhatónak vagy rendezettnek. Előfordulhat, hogy egyes események elvesznek, vagy kézbesítésük megtörtént. A diagnosztikai naplók nem valódi időpontot jelentenek, és több percet is igénybe vehet, hogy az események a választott célhelyre legyenek naplózva.

Azure Resource Health segítséget nyújt a diagnosztizálásban és a támogatásban, ha egy Azure-probléma hatással van az erőforrásaira. Az irányítópultok az egyes IoT-hubok aktuális és múltbeli állapotát biztosítják. Folytassa a cikk alján található szakasztal, hogy megtudja, hogyan [használhatja a Azure Resource Healtht](#use-azure-resource-health) az IoT hub használatával. 

A IoT Hub saját mérőszámokat is biztosít, amelyek segítségével megismerheti a IoT-erőforrások állapotát. További információ: [IoT hub mérőszámok ismertetése](iot-hub-metrics.md).

## <a name="use-azure-monitor"></a>Az Azure Monitor használata

A Azure Monitor diagnosztikai adatokat biztosít az Azure-erőforrásokhoz, ami azt jelenti, hogy nyomon követheti az IoT hub-ban elvégezhető műveleteket.

Azure Monitor diagnosztikai beállításai felváltják a IoT Hub Operations monitort. Ha jelenleg a műveletek figyelését használja, akkor át kell telepítenie a munkafolyamatokat. További információ: [áttelepítés az Operations monitoring és a diagnosztikai beállítások között](iot-hub-migrate-to-diagnostics-settings.md).

Ha többet szeretne megtudni az órákat Azure Monitor kapcsolódó mérőszámokról és eseményekről, tekintse meg a [támogatott mérőszámokat Azure monitor](../azure-monitor/platform/metrics-supported.md) és [támogatott szolgáltatásokkal, sémákkal és kategóriákkal az Azure diagnosztikai naplókhoz](../azure-monitor/platform/diagnostic-logs-schema.md).

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>A naplók ismertetése

A Azure Monitor a IoT Hubban előforduló különböző műveleteket követi nyomon. Minden kategória tartalmaz egy sémát, amely meghatározza, hogy az adott kategóriában milyen eseményeket kell jelenteni.

#### <a name="connections"></a>Kapcsolatok

A kapcsolatok kategória nyomon követi az eszköz csatlakoztatását, és leválasztja az eseményeket egy IoT-hubhoz, valamint a hibákat. Ez a kategória hasznos lehet a jogosulatlan kapcsolódási kísérletek azonosításához, illetve az eszközökhöz való csatlakozás elvesztésével kapcsolatos riasztásokhoz.

> [!NOTE]
> Az eszközök megbízható kapcsolatok állapotának ellenõrzéséhez az [eszköz szívverését](iot-hub-devguide-identity-registry.md#device-heartbeat)kell megadnia.

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-commands"></a>A felhőből az eszközre irányuló parancsok

A felhőből az eszközre irányuló parancsok kategóriája nyomon követi az IoT-központban előforduló hibákat, és a felhőből az eszközre irányuló üzenet folyamatához kapcsolódik. Ez a kategória olyan hibákat tartalmaz, amelyek a következő esetekben jelentkeznek:

* A felhőből az eszközre irányuló üzenetek küldése (például jogosulatlan feladói hibák),
* A felhőből az eszközre irányuló üzenetek fogadása (például a kézbesítések száma túllépte a hibákat) és
* A felhőből az eszközre irányuló üzenetek visszajelzésének fogadása (például a visszajelzések lejárt hibái).

Ez a kategória nem fog hibákat észlelni, ha a felhőből az eszközre irányuló üzenet sikeresen kézbesítésre kerül, de az eszköz nem megfelelően kezeli őket.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-identity-operations"></a>Eszköz-identitási műveletek

Az Eszközállapot-üzemeltetési kategória azokat a hibákat követi, amelyek akkor jelentkeznek, amikor megpróbál létrehozni, frissíteni vagy törölni egy bejegyzést az IoT hub Identity registryben. A kategória követése hasznos lehet a kiépítési forgatókönyvek esetében.

```json
{
    "records":
    [
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
    ]
}
```

#### <a name="routes"></a>Útvonalak

Az üzenet-útválasztási kategória nyomon követi az üzenetek útvonalának kiértékelése és a végpont állapota során az IoT Hub által észlelt hibákat. Ez a kategória olyan eseményeket tartalmaz, mint például:

* Egy szabály "nem definiált" értékre értékeli ki a következőt:
* IoT Hub a végpontot Holtként jelöli meg, vagy
* A végponttól érkezett hibák. 

Ez a kategória nem tartalmaz konkrét hibákat az üzenetekről (például az eszköz-szabályozási hibákról), amelyek az "eszköz telemetria" kategóriában vannak bejelentve.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "endpointUnhealthy",
            "category": "Routes",
            "level": "Error",
            "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-telemetry"></a>Eszköz telemetria

Az eszköz telemetria kategóriája nyomon követi az IoT hub-ban előforduló hibákat, és a telemetria-folyamathoz kapcsolódik. Ez a kategória olyan hibákat tartalmaz, amelyek a telemetria-események (például a szabályozás) és a telemetria-események (például a jogosulatlan olvasók) küldésekor fordulnak elő. Ez a kategória nem képes az eszközön futó kód által okozott hibák megfogására.

```json
{
    "records":
    [
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
    ]
}
```

#### <a name="file-upload-operations"></a>Fájlfeltöltési műveletek

A fájlfeltöltés kategóriája nyomon követi az IoT-központban előforduló hibákat, és a fájlfeltöltés funkcióhoz kapcsolódik. Ez a kategória a következőket tartalmazza:

* A SAS URI-val kapcsolatos hibák, például amikor lejárnak, mielőtt egy eszköz értesíti a befejezett feltöltési központ központi telepítéséről.

* Az eszköz által jelentett feltöltések sikertelenek.

* Hibák, amelyek akkor jelentkeznek, ha egy fájl nem található a tárolóban IoT Hub értesítési üzenet létrehozásakor.

Ez a kategória nem tudja felfogni azokat a hibákat, amelyek közvetlenül bekövetkeznek, miközben az eszköz feltölt egy fájlt a tárolóba.

```json
{
    "records":
    [
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
    ]
}
```

#### <a name="cloud-to-device-twin-operations"></a>A felhőből az eszközre irányuló kettős műveletek

A felhő – eszköz kettős műveletek kategóriája nyomon követi a szolgáltatás által kezdeményezett eseményeket az eszköz Twins-on. Ezek a műveletek magukban foglalhatják a Get Twin, a Update vagy a Replace címkéket, valamint a kívánt tulajdonságok frissítését vagy cseréjét.

```json
{
    "records":
    [
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
    ]
}
```

#### <a name="device-to-cloud-twin-operations"></a>Az eszközről a felhőbe irányuló kettős művelet

Az eszközről a felhőbe irányuló kettős műveletek kategóriája az eszköz által kezdeményezett eseményeket követi nyomon az eszközökön. Ezek a műveletek magukban foglalhatják a Get Twin, a frissített jelentett tulajdonságokat, és előfizethetnek a kívánt tulajdonságokra.

```json
{
    "records":
    [
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
    ]
}
```

#### <a name="twin-queries"></a>Dupla lekérdezés

A Twin querys kategória jelentést készít a felhőben kezdeményezett Device Twins-lekérdezési kérelmekről.

```json
{
    "records":
    [
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
    ]
}
```

#### <a name="jobs-operations"></a>Feladatműveletek

A feladatok műveleti kategóriánként jelentéseket készítenek az eszköz-ikrek frissítéséhez vagy közvetlen metódusok meghívásához több eszközön. Ezeket a kérelmeket a felhőben kezdeményezték.

```json
{
    "records":
    [
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
    ]
}
```

#### <a name="direct-methods"></a>Közvetlen metódusok

A közvetlen metódusok kategória az egyes eszközökre küldött kérelem-válasz interakciókat követi nyomon. Ezeket a kérelmeket a felhőben kezdeményezték.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="distributed-tracing-preview"></a>Elosztott nyomkövetés (előzetes verzió)

Az elosztott nyomkövetési kategória a nyomkövetési környezet fejlécét tartalmazó üzenetek korrelációs azonosítóit követi nyomon. A naplók teljes körű engedélyezéséhez az ügyféloldali kódot a következő elemzéssel kell frissíteni, [és diagnosztizálni kell a IoT-alkalmazásokat végpontok közötti IoT hub elosztott nyomkövetéssel (előzetes verzió)](iot-hub-distributed-tracing.md).

Vegye figyelembe, hogy `correlationId` megfelel a [W3C-nyomkövetési kontextusra](https://github.com/w3c/trace-context) vonatkozó javaslatnak, ahol `trace-id` és `span-id`tartalmaz.

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT Hub D2C (eszközről a felhőbe irányuló) naplók

IoT Hub rögzíti ezt a naplót, ha egy érvényes nyomkövetési tulajdonságokat tartalmazó üzenet érkezik a IoT Hub.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

A `durationMs` nem számítja ki, mert a IoT Hub órája nem szinkronizálható az eszköz órájával, így az időtartam kiszámítása félrevezető lehet. Javasoljuk, hogy a logikát a `properties` szakaszban található időbélyegek használatával rögzítse az eszközről a felhőbe irányuló késésben lévő tüskéket.

| Tulajdonság | Típus | Leírás |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Egész szám | Az eszközről a felhőbe irányuló üzenet mérete bájtban |
| **deviceId** | ASCII 7 bites alfanumerikus karakterek karakterlánca | Az eszköz identitása |
| **callerLocalTimeUtc** | UTC timestamp | Az üzenet létrehozásának ideje, amelyet az eszköz helyi órája jelentett. |
| **calleeLocalTimeUtc** | UTC timestamp | A IoT Hub átjárójának a IoT Hub szolgáltatás által jelzett óra által jelentett időpontja |

##### <a name="iot-hub-ingress-logs"></a>Bejövő IoT Hubi naplók

IoT Hub rögzíti ezt a naplót, ha az érvényes nyomkövetési tulajdonságokat tartalmazó üzenet belső vagy beépített Event hub-ba ír.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

A `properties` szakaszban ez a napló további információkat tartalmaz az üzenetek beérkezéséről.

| Tulajdonság | Típus | Leírás |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | Sztring | Igaz vagy hamis érték esetén azt jelzi, hogy engedélyezve van-e az üzenet-útválasztás a IoT Hub |
| **parentSpanId** | Sztring | A fölérendelt üzenet [span-azonosítója](https://w3c.github.io/trace-context/#parent-id) , amely ebben az esetben a D2C-üzenet nyomkövetése lenne |

##### <a name="iot-hub-egress-logs"></a>IoT Hub kimenő naplók

IoT Hub rögzíti ezt a naplót, ha az [Útválasztás](iot-hub-devguide-messages-d2c.md) engedélyezve van, és az üzenet egy [végpontba](iot-hub-devguide-endpoints.md)íródik. Ha az Útválasztás nincs engedélyezve, IoT Hub nem rögzíti ezt a naplót.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

A `properties` szakaszban ez a napló további információkat tartalmaz az üzenetek beérkezéséről.

| Tulajdonság | Típus | Leírás |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **Végpontneve** | Sztring | Az útválasztási végpont neve |
| **endpointType** | Sztring | Az útválasztási végpont típusa |
| **parentSpanId** | Sztring | A fölérendelt üzenet [span-azonosítója](https://w3c.github.io/trace-context/#parent-id) , amely a IoT hub bejövő üzenet nyomkövetése lenne ebben az esetben |

#### <a name="configurations"></a>Konfigurációk

IoT Hub konfigurációs naplók nyomon követi az eseményeket és a hibát az automatikus Eszközkezelő szolgáltatás számára.

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>Eszköz streamek (előzetes verzió)

Az eszköz Streams kategória nyomon követi az egyes eszközökre küldött kérelem-válasz interakciókat.

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

### <a name="read-logs-from-azure-event-hubs"></a>Naplók beolvasása az Azure Event Hubs

Miután beállította az eseménynaplózást a diagnosztikai beállításokon, létrehozhat olyan alkalmazásokat, amelyek beolvasják a naplókat, így a bennük található információk alapján műveleteket hajthat végre. Ez a mintakód egy Event hub naplóit kérdezi le:

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
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
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

## <a name="use-azure-resource-health"></a>Azure Resource Health használata

Azure Resource Health segítségével figyelje meg, hogy az IoT hub működik-e. Azt is megtudhatja, hogy a regionális leállás hatással van-e az IoT hub állapotára. Ha meg szeretné ismerni az Azure-IoT Hub állapotával kapcsolatos konkrét adatokat, javasoljuk, hogy [használja a Azure monitor](#use-azure-monitor).

Az Azure IoT Hub regionális szinten jelzi az állapotot. Ha a regionális leállás hatással van az IoT hub-ra, az állapot **ismeretlenként**jelenik meg. További információ: [erőforrástípusok és állapot-ellenőrzések az Azure Resource Health-ben](../service-health/resource-health-checks-resource-types.md).

Az IoT-hubok állapotának vizsgálatához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).

2. Navigáljon **Service Health** > **Erőforrás-állapot**elemre.

3. A legördülő listából válassza ki az előfizetést, majd válassza az **IoT hub** lehetőséget az erőforrástípus mezőben.

Az állapotadatok értelmezésével kapcsolatos további tudnivalókért tekintse meg az [Azure Resource Health áttekintése](../service-health/resource-health-overview.md)című témakört.

## <a name="next-steps"></a>Következő lépések

* [IoT Hub mérőszámok ismertetése](iot-hub-metrics.md)
* [IoT távoli figyelés és értesítések Azure Logic Apps az IoT hub és a postaláda csatlakoztatásával](iot-hub-monitoring-notifications-with-azure-logic-apps.md)

---
title: Az Azure IoT Hub állapotának figyelése | Microsoft dokumentumok
description: Az Azure Monitor és az Azure Resource Health segítségével figyelheti az IoT Hubot, és gyorsan diagnosztizálhatja a problémákat
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: kgremban
ms.openlocfilehash: f801abc40caf273c28a0c01dedf9735f5198c2af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271082"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Az Azure IoT Hub állapotának monitorozása és a problémák gyorsan diagnosztizálása

Az Azure IoT Hubot megvalósító vállalkozások megbízható teljesítményt várnak az erőforrásaiktól. A műveletek szoros figyelemmel kísérése érdekében az IoT Hub teljes mértékben integrálva van az [Azure Monitor](../azure-monitor/index.yml) és az [Azure Resource Health szolgáltatással.](../service-health/resource-health-overview.md) Ez a két szolgáltatás az IoT-megoldások kifogástalan állapotban tartásához szükséges adatokkal szolgál.

Az Azure Monitor egyetlen forrása a figyelés és a naplózás az összes Azure-szolgáltatások. Az Azure Monitor által létrehozott diagnosztikai naplókat elküldheti az Azure Monitor-naplókba, az Event Hubs-ba vagy az Azure Storage-ba egyéni feldolgozásra. Az Azure Monitor metrikák és diagnosztikai beállítások segítségével betekintést nyerhet az erőforrások teljesítményébe. Olvassa el ezt a cikket, és ismerje meg, hogyan használhatja az [Azure Monitort](#use-azure-monitor) az IoT-központtal. 

> [!IMPORTANT]
> Az IoT Hub szolgáltatás által az Azure Monitor diagnosztikai naplók használatával kibocsátott események nem garantáltan megbízhatóak vagy rendezettek. Előfordulhat, hogy egyes események elvesznek vagy nem sorrendben kézbesítik őket. Diagnosztikai naplók is nem azt jelentette, hogy valós idejű, és több percig is eltarthat, amíg az események et naplózza a választott cél.

Az Azure Resource Health segítségével diagnosztizálhatja és kaphat támogatást, ha egy Azure-probléma hatással van az erőforrásokra. Az irányítópult az egyes IoT-központok aktuális és múltbeli állapotát biztosítja. Folytassa a cikk alján található szakaszban, hogy megtudja, hogyan [használhatja az Azure Resource Health](#use-azure-resource-health) az IoT-központ használatával. 

Az IoT Hub saját metrikákat is biztosít, amelyek segítségével megismerheti az IoT-erőforrások állapotát. További információ: [Az IoT Hub-metrikák megismerése.](iot-hub-metrics.md)

## <a name="use-azure-monitor"></a>Az Azure Monitor használata

Az Azure Monitor diagnosztikai információkat biztosít az Azure-erőforrásokhoz, ami azt jelenti, hogy figyelheti az IoT-központon belül végzett műveleteket.

Az Azure Monitor diagnosztikai beállításai felülírják az IoT Hub műveleti figyelője. Ha jelenleg a műveletek figyelését használja, át kell telepítenie a munkafolyamatokat. További információ: [Áttelepítés a műveletek figyeléséről diagnosztikai beállításokra.](iot-hub-migrate-to-diagnostics-settings.md)

Ha többet szeretne megtudni az Azure Monitor által figyelt konkrét metrikákról és eseményekről, olvassa [el a Támogatott metrikák az Azure Monitor](../azure-monitor/platform/metrics-supported.md) és a Támogatott [szolgáltatások, sémák és az Azure diagnosztikai naplók kategóriái című témakört.](../azure-monitor/platform/diagnostic-logs-schema.md)

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>A naplók értelmezése

Az Azure Monitor az IoT Hubban előforduló különböző műveleteket követi nyomon. Minden kategóriában van egy séma, amely meghatározza, hogyan események ebben a kategóriában kell jelenteni.

#### <a name="connections"></a>Kapcsolatok

A kapcsolatok kategória nyomon követi az eszköz csatlakoztatása és leválasztása események egy IoT hub, valamint a hibák. Ez a kategória akkor hasznos, ha azonosítani tudja a jogosulatlan csatlakozási kísérleteket, és riasztást ad, ha megszakad az eszközkapcsolat.

> [!NOTE]
> Az eszközök megbízható kapcsolati állapotához ellenőrizze [az eszköz szívverését.](iot-hub-devguide-identity-registry.md#device-heartbeat)

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

#### <a name="cloud-to-device-commands"></a>Felhőből az eszközre parancsok

A felhőből az eszközre irányuló parancsok kategória nyomon követi az IoT hubon előforduló és a felhőből az eszközre irányuló üzenetfolyamathoz kapcsolódó hibákat. Ez a kategória a következő hibákat tartalmazza:

* Felhőből az eszközre irányuló üzenetek küldése (például illetéktelen feladói hibák),
* Felhőből az eszközre irányuló üzenetek fogadása (például a kézbesítési szám túllépte a hibákat), és
* Felhőből az eszközre irányuló üzenetekvisszajelzésfogadása (például lejárt visszajelzési hibák).

Ez a kategória nem észleli a hibákat, ha a felhőből az eszközre küldött üzenet sikeresen kézbesítése, de majd az eszköz nem megfelelően kezeli.

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

#### <a name="device-identity-operations"></a>Eszközidentitás-műveletek

Az eszközidentitás-műveletek kategóriában nyomon követi azokat a hibákat, amelyek akkor fordulnak elő, amikor megpróbál létrehozni, frissíteni vagy törölni egy bejegyzést az IoT hub identitás-beállításjegyzékében. Ez a kategória nyomon követése hasznos a kiépítési forgatókönyvek.

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

Az üzenetút-küldési kategória nyomon követi az üzenetútvonal-kiértékelés és a végpont állapota során az IoT Hub által érzékelt hibákat. Ebbe a kategóriába tartoznak az olyan események, mint például:

* A szabály "meghatározatlan" értéket ad ki,
* Az IoT Hub egy végpontot halottként jelöl meg, vagy
* A végpontról kapott hibák. 

Ez a kategória nem tartalmazza az üzenetekkel kapcsolatos konkrét hibákat (például az eszköz szabályozási hibáit), amelyek az "eszköz telemetriai adatok" kategóriában vannak jelentve.

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

#### <a name="device-telemetry"></a>Eszköztelemetria

Az eszköz telemetriai kategóriában nyomon követi az IoT hubon előforduló és a telemetriai folyamathoz kapcsolódó hibákat. Ez a kategória tartalmazza a telemetriai események (például a szabályozás) és a telemetriai események (például a jogosulatlan olvasó) küldésekor előforduló hibákat. Ez a kategória nem tudja észlelni az eszközön futó kód által okozott hibákat.

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

A fájlfeltöltési kategória nyomon követi az IoT hubon előforduló és a fájlfeltöltési funkcióval kapcsolatos hibákat. Ez a kategória a következőket tartalmazza:

* Hibák, amelyek a SAS URI-val, például amikor lejár, mielőtt egy eszköz értesíti a hub egy befejezett feltöltés.

* Az eszköz által jelentett sikertelen feltöltések.

* Hibák, amelyek akkor fordulnak elő, ha egy fájl nem található a tárolóban az IoT Hub értesítési üzenetek létrehozása során.

Ez a kategória nem tudja észlelni azolyan hibákat, amelyek közvetlenül akkor fordulnak elő, amikor az eszköz feltölt egy fájlt a tárolóba.

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

#### <a name="cloud-to-device-twin-operations"></a>Felhőből az eszközre irányuló ikerműveletek

A felhőből az eszközre ikerműveletek kategória nyomon követi a szolgáltatás által kezdeményezett események et az eszköz twins.The cloud-to-device twin operations category tracks service-initiated events on device twins. Ezek a műveletek magukban foglalhatják a két személyes rendszer bekéselése, a címkék frissítése vagy cseréje, valamint a kívánt tulajdonságok frissítése vagy cseréje.

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

#### <a name="device-to-cloud-twin-operations"></a>Az eszközök közötti ikerműveletek

Az eszközről a felhőbe ikerműveletek kategória nyomon követi az eszköz által kezdeményezett események eszköz twins. Ezek a műveletek lehetnek ikerbek, frissítse a jelentett tulajdonságokat, és iratkozzon fel a kívánt tulajdonságokra.

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

#### <a name="twin-queries"></a>Ikerlekérdezések

A kettős lekérdezések kategória jelentések a felhőben kezdeményezett ikereszközök lekérdezési kérelmek lekérdezési kérelmekről.

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

A feladatok műveletek kategória jelentések feladatkérelmek eszköz twins frissítésére vagy közvetlen metódusok meghívására több eszközön. Ezek a kérelmek a felhőben kezdeményezett.

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

#### <a name="direct-methods"></a>Közvetlen módszerek

A közvetlen módszerek kategória nyomon követi az egyes eszközökre küldött kérés-válasz interakciókat. Ezek a kérelmek a felhőben kezdeményezett.

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

Az elosztott nyomkövetési kategória a nyomkövetési környezet fejlécét tartalmazó üzenetek korrelációs azonosítóit követi nyomon. A naplók teljes engedélyezéséhez az ügyféloldali kódot frissíteni kell az [IoT-alkalmazások elemzésével és diagnosztizálásával, az IoT Hub elosztott nyomkövetésével (előzetes verzió)](iot-hub-distributed-tracing.md)követve.

Vegye `correlationId` figyelembe, hogy megfelel a [W3C Trace](https://github.com/w3c/trace-context) `trace-id` Context javaslatnak, ahol egy és egy `span-id`.

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT Hub D2C (eszközről felhőbe) naplók

Az IoT Hub rögzíti ezt a naplót, ha érvényes nyomkövetési tulajdonságokat tartalmazó üzenet érkezik az IoT Hubhoz.

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

Itt `durationMs` nem számítjuk ki, mivel az IoT Hub órája nem lehet szinkronban az eszköz órájával, így az időtartam számítása félrevezető lehet. Azt javasoljuk, hogy a szakasz `properties` időbélyegei használatával írja a logikát az eszközről a felhőbe irányuló késés csúcsai rögzítéséhez.

| Tulajdonság | Típus | Leírás |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize (üzenetmérete)** | Egész szám | Az eszközről a felhőbe irányuló üzenet mérete bájtban |
| **deviceId** | ASCII 7 bites alfanumerikus karakterek karakterlánca | A készülék azonossága |
| **callerLocalTimeUtc** | UTC időbélyeg | Az üzenet létrehozási ideje az eszköz helyi órája szerint |
| **calleeLocalTimeUtc** | UTC időbélyeg | Az Üzenetek érkezésének időpontja az IoT Hub átjárójára az IoT Hub szolgáltatás oldali órája szerint |

##### <a name="iot-hub-ingress-logs"></a>Az IoT Hub be- és visszafektusai

Az IoT Hub rögzíti ezt a naplót, ha az érvényes nyomkövetési tulajdonságokat tartalmazó üzenet a belső vagy a beépített Event Hubba ír.

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

A `properties` szakaszban ez a napló további információkat tartalmaz az üzenetek be- és beviteléről.

| Tulajdonság | Típus | Leírás |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | Sztring | Igaz vagy hamis, azt jelzi, hogy az üzenetek útválasztása engedélyezve van-e az IoT Hubban |
| **parentSpanId** | Sztring | A [szülőüzenet span-id-je,](https://w3c.github.io/trace-context/#parent-id) amely ebben az esetben a D2C üzenet nyomkövetése lenne |

##### <a name="iot-hub-egress-logs"></a>IoT-központ kimenő naplók

Az IoT Hub akkor rögzíti ezt a naplót, ha az [útválasztás](iot-hub-devguide-messages-d2c.md) engedélyezve van, és az üzenet [egy végpontra van](iot-hub-devguide-endpoints.md)írva. Ha az útválasztás nincs engedélyezve, az IoT Hub nem rögzíti ezt a naplót.

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

A `properties` szakaszban ez a napló további információkat tartalmaz az üzenetek be- és beviteléről.

| Tulajdonság | Típus | Leírás |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **végpontneve** | Sztring | Az útválasztási végpont neve |
| **végponttípusa** | Sztring | Az útválasztási végpont típusa |
| **parentSpanId** | Sztring | A [szülőüzenet span-idazonosítója,](https://w3c.github.io/trace-context/#parent-id) amely ebben az esetben az IoT Hub bejövő üzenetek nyomon követését jelenti |

#### <a name="configurations"></a>Konfigurációk

Az IoT Hub konfigurációs naplói nyomon követik az automatikus eszközkezelés szolgáltatáskészlet eseményeit és hibáit.

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

### <a name="device-streams-preview"></a>Eszközadatfolyamok (előzetes verzió)

Az eszközadatfolyamok kategória nyomon követi az egyes eszközökre küldött kérés-válasz interakciókat.

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

### <a name="read-logs-from-azure-event-hubs"></a>Naplók olvasása az Azure Event Hubs-ból

Miután beállította az eseménynaplózást a diagnosztikai beállításokon keresztül, létrehozhat olyan alkalmazásokat, amelyek felolvassák a naplókat, így a bennük lévő információk alapján műveleteket hajthat végre. Ez a mintakód egy eseményközpontból olvassa be a naplókat:

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

## <a name="use-azure-resource-health"></a>Az Azure-erőforrások állapotának használata

Az Azure Resource Health használatával figyelheti, hogy az IoT hub működik-e és működik-e. Azt is megtudhatja, hogy egy regionális kimaradás hatással van-e az IoT hub állapotára. Az Azure IoT Hub állapotának konkrét részleteinek megértéséhez azt javasoljuk, hogy [használja az Azure Monitort.](#use-azure-monitor)

Az Azure IoT Hub regionális szintű állapotot jelez. Ha egy regionális kimaradás hatással van az IoT hub, az állapot ismeretlen jelenik **meg.** További információ: [Erőforrástípusok és állapot-ellenőrzések az Azure-erőforrások állapotában.](../service-health/resource-health-checks-resource-types.md)

Az IoT-központok állapotának ellenőrzéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

2. Nyissa meg a **Szolgáltatás-egészségügyi** > **erőforrás állapotát.**

3. A legördülő mezőkből válassza ki az előfizetést, majd válassza az **IoT Hub** erőforrás-típusként.

Az állapotadatok értelmezéséről az [Azure-erőforrások állapotának áttekintése című témakörben olvashat bővebben.](../service-health/resource-health-overview.md)

## <a name="next-steps"></a>További lépések

* [Az IoT Hub-metrikák ismertetése](iot-hub-metrics.md)
* [IoT-távfigyelés és értesítések az IoT-központés postaláda összekötő Azure Logic Apps alkalmazásokkal](iot-hub-monitoring-notifications-with-azure-logic-apps.md)

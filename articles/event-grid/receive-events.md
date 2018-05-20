---
title: Események fogadása Azure esemény rács egy HTTP-végpont
description: Ismerteti, hogyan lehet HTTP-végponttal, akkor kap, és ellenőrizheti deszerializálni Azure esemény rácsban események
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: babanisa
ms.openlocfilehash: 89d0f11ccfb9a359ca3e43bc1a370e0fb7514574
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="receive-events-to-an-http-endpoint"></a>Események fogadása HTTP-végponton

Ez a cikk ismerteti, hogyan [ellenőrzése HTTP-végponttal](security-authentication.md#webhook-event-delivery) események fogadásához az esemény-előfizetésből fogadni és események deszerializálni. Ebben a cikkben az Azure-függvény bemutatási célokra, azonban koncepciók vonatkoznak, függetlenül attól, ahol az alkalmazás üzemel.

> [!NOTE]
> Az **erősen** ajánlott, hogy egy [rács eseményindító](../azure-functions/functions-bindings-event-grid.md) amikor kiváltó esemény rácshoz egy Azure-függvényt. Egy általános WebHook eseményindító használata demonstrative.

## <a name="prerequisites"></a>Előfeltételek

* Egy függvény alkalmazás szüksége lesz egy [HTTP indított függvény](../azure-functions/functions-create-generic-webhook-triggered-function.md)

## <a name="add-dependencies"></a>Adja hozzá a függőségek

A .NET, fejlesztői [hozzáadjon egy függőséget](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) a függvénynek a a `Microsoft.Azure.EventGrid` [Nuget-csomag](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). SDK-k más nyelveken érhetők el a [közzététele SDK-k](./sdk-overview.md#data-plane-sdks) hivatkozás. Ezek a csomagok tartalmaznak, mint a modelleket a natív eseménytípusok `EventGridEvent`, `StorageBlobCreatedEventData`, és `EventHubCaptureFileCreatedEventData`.

Az Azure-függvény (jobb oldali legtöbb ablaktáblán az az Azure functions portálon) a "Nézet fájlok" hivatkozásra, és hozzon létre egy project.json nevű fájlt. Adja hozzá a következő tartalmat a `project.json` fájlt, és mentse azt:

 ```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.Azure.EventGrid": "1.1.0-preview"
      }
    }
   }
}
```

![A hozzáadott NuGet-csomag](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Végpont érvényesítése

Először is meg szeretné akkor kezeli `Microsoft.EventGrid.SubscriptionValidationEvent` események. Minden alkalommal, amikor valaki előfizet egy eseményt, esemény rács küld egy érvényesítési esemény a végpont egy `validationCode` az adatokat tartalmaz. A végpont nem szükséges ebben a az adott válasz törzsének az echo [igazolnia a végpont nem érvényes, és a tulajdonosuk](security-authentication.md#webhook-event-delivery). Ha használ egy [rács eseményindító](../azure-functions/functions-bindings-event-grid.md) helyett egy WebHook működésbe. a függvény végpont érvényesítésről meg. Ha egy külső API szolgáltatását használja (például [Zapier](https://zapier.com) vagy [IFTTT](https://ifttt.com/)), előfordulhat, hogy nem fogja tudni a Ellenőrzőkód programozottan echo. Ezek a szolgáltatások esetén manuálisan ellenőrizheti az előfizetés az előfizetés érvényesítése esemény küldött érvényesítési URL-cím. Másolja az URL-címet a `validationUrl` tulajdonság, majd küldje el egy GET kérelem a többi ügyfél, illetve a webböngésző keresztül.

Manuális érvényesítés jelenleg előzetes verzióban érhető. A használatához telepítenie kell a [esemény rács bővítmény](/cli/azure/azure-cli-extensions-list) a [AZ CLI 2.0](/cli/azure/install-azure-cli). A későbbiekben telepítheti az `az extension add --name eventgrid`. Ha a REST API-t használ, győződjön meg arról, használ `api-version=2018-05-01-preview`.

A Ellenőrzőkód programozottan echo, használja a következő kódot:

```csharp
using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json.Serialization;
using Microsoft.Azure.EventGrid.Models;

class SubscriptionValidationEventData
{
    public string ValidationCode { get; set; }
}

class SubscriptionValidationResponseData
{
    public string ValidationResponse { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{

    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";

    string requestContent = await req.Content.ReadAsStringAsync();
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        JObject dataObject = eventGridEvent.Data as JObject;

        // Deserialize the event data into the appropriate type based on event type
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>();
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response
            var responseData = new SubscriptionValidationResponseData();
            responseData.ValidationResponse = eventData.ValidationCode;
            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}

```

```javascript
var http = require('http');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }
    }
    context.done();
};
```

### <a name="test-validation-response"></a>Érvényesítési válasz tesztelése

A érvényesítési válasz függvény tesztelése a teszt mezőbe, a függvény a mintaesemény beillesztésével:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Ha a Futtatás gombra kattint, a kimeneti le kell 200 OK és `{"ValidationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` törzsében:

![a válasz érvényesítése](./media/receive-events/validation-response.png)

## <a name="handle-blob-storage-events"></a>A Blob storage-események kezelésére

Most tegyük kiterjesztése kezelésére függvény `Microsoft.Storage.BlobCreated`:

```cs
using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json.Serialization;
using Microsoft.Azure.EventGrid.Models;

class SubscriptionValidationEventData
{
    public string ValidationCode { get; set; }
}

class SubscriptionValidationResponseData
{
    public string ValidationResponse { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";
    const string StorageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    string requestContent = await req.Content.ReadAsStringAsync();
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        JObject dataObject = eventGridEvent.Data as JObject;

        // Deserialize the event data into the appropriate type based on event type 
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>();
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");

            // Do any additional validation (as required) and then return back the below response
            var responseData = new SubscriptionValidationResponseData();
            responseData.ValidationResponse = eventData.ValidationCode;
            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }

        else if (string.Equals(eventGridEvent.EventType, StorageBlobCreatedEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<StorageBlobCreatedEventData>();
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}

```

```javascript
var http = require('http');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }
    }
    context.done();
};

```

### <a name="test-blob-created-event-handling"></a>Teszt eseménykezelésnek Blob létrehozása

Az új funkciókat, a függvény tesztelése tegyen egy [Blob storage esemény](./event-schema-blob-storage.md#example-event) a teszt pedig fut:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Az függvényt tartalmaz a blob URL-cím kimenetnek kell megjelennie:

![Kimeneti naplót](./media/receive-events/blob-event-response.png)

Azt is tesztelni, hozzon létre egy Blob storage-fiók vagy általános célú V2 (GPv2) Storage-fiók esetén [hozzáadásával és az esemény előfizetés](../storage/blobs/storage-blob-event-quickstart.md), és a végpont beállítása a függvény URL-cím:

![Függvény URL-címe](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Egyéni események kezelésére

Végezetül lehetővé teszi, hogy a funkció még egyszer kiterjesztése az, hogy az egyéni események is kezelni tud. Egy esemény-ellenőrzéssel kell kiegészíteni `Contoso.Items.ItemReceived`. A végső kódot hasonlóan kell kinéznie:

```cs
using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json.Serialization;
using Microsoft.Azure.EventGrid.Models;

class SubscriptionValidationEventData
{
    public string ValidationCode { get; set; }
}

class SubscriptionValidationResponseData
{
    public string ValidationResponse { get; set; }
}

class ContosoItemReceivedEventData
{
    public string id { get; set; }
    public string message { get; set; }
    public string time { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";
    const string StorageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    const string CustomTopicEvent = "Contoso.Items.ItemReceived";

    string requestContent = await req.Content.ReadAsStringAsync();
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        JObject dataObject = eventGridEvent.Data as JObject;

        // Deserialize the event data into the appropriate type based on event type
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>();
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response
            var responseData = new SubscriptionValidationResponseData();
            responseData.ValidationResponse = eventData.ValidationCode;
            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }

        else if (string.Equals(eventGridEvent.EventType, StorageBlobCreatedEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<StorageBlobCreatedEventData>();
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }

        else if (string.Equals(eventGridEvent.EventType, CustomTopicEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<ContosoItemReceivedEventData>();
            log.Info($"Got ContosoItemReceived event data, item URI {eventData.id}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}

```

```javascript
var http = require('http');
var t = require('tcomb');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";
    var ContosoItemReceivedEventData = t.struct({
        id: t.Str,
        message: t.Str,
        time: t.Str,
    })

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }

        else if (body.data && body.eventType == customEventType) {
            var payload = new ContosoItemReceivedEventData(body.data);
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
            context.log(payload instanceof ContosoItemReceivedEventData);
        }
    }
    context.done();
};

```

### <a name="test-custom-event-handling"></a>Teszt egyéni esemény kezelése

Végül tesztelheti, hogy a bővített függvény már tudja kezelni az egyéni esemény típusa:

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Microsoft.EventGrid.CustomEventType",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "id": "831e1650-001e-001b-66ab-eeb76e069631",
            "message": "Contoso item Foo",
            "time": "2017-06-26T18:41:00.9584103Z"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

Is tesztelheti ezt a funkciót élő által [CURL az egyéni esemény küldése a portálról](./custom-event-quickstart-portal.md) vagy [egy egyéni témához könyvelési](./post-to-custom-topic.md) használatával olyan szolgáltatást vagy alkalmazást, amely egy olyan végpont például KÜLDHET[Postman](https://www.getpostman.com/). Hozzon létre egy egyéni témakör és egy esemény-előfizetést állítja be a függvény URL-címet a végpont.

## <a name="next-steps"></a>További lépések

* Megismerkedhet a [Azure esemény rács felügyeleti és SDK-k közzététele](./sdk-overview.md)
* Megtudhatja, hogyan [küldje el a egyéni témakör](./post-to-custom-topic.md)
* Tekintse meg a részletes esemény rács és funkciók bemutatók például [blobtárolóba feltöltött képek átméretezése](resize-images-on-storage-blob-upload-event.md)

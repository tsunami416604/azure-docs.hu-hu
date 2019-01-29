---
title: Események fogadása az Azure Event Grid egy HTTP-végpontot
description: Ismerteti, hogyan lehet ellenőrizni egy HTTP-végpontot, majd fogadása és deszerializálni az Azure Event Grid-Eseményfolyam megtekintéséhez
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: babanisa
ms.openlocfilehash: bb22a2545466c72f7dac68f80668b8b530832c21
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094718"
---
# <a name="receive-events-to-an-http-endpoint"></a>Események fogadása HTTP-végponton

Ez a cikk azt ismerteti, hogyan [érvényesíteni egy HTTP-végpontot](security-authentication.md#webhook-event-delivery) események fogadása az esemény-előfizetés és fogadásához és deszerializálni az eseményeket. Ez a cikk egy Azure-függvényt használ a bemutatási célokra, azonban ugyanezek a fogalmak alkalmazhatók, függetlenül attól, ahol az alkalmazás üzemel.

> [!NOTE]
> Ez **erősen** ajánlott használni egy [Event Grid-Trigger](../azure-functions/functions-bindings-event-grid.md) , ha a riasztást kiváltó Azure-függvény, az Event GRID használatával. Itt általános WebHook eseményindítóként használata demonstrative.

## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz egy függvényalkalmazást egy HTTP által aktivált függvényt.

## <a name="add-dependencies"></a>Függőségek hozzáadása

Ha fejleszt, a .NET-ben, [hozzáadjon egy függőséget](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) a függvényhez tartozó a `Microsoft.Azure.EventGrid` [Nuget-csomag](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). Ebben a cikkben szereplő példák előírt 1.4.0-s vagy újabb.

SDK-k más nyelven keresztül érhető el a [SDK-k közzététele](./sdk-overview.md#data-plane-sdks) hivatkozást. Ezek a csomagok rendelkezik a modelleket a natív eseménytípusok például `EventGridEvent`, `StorageBlobCreatedEventData`, és `EventHubCaptureFileCreatedEventData`.

Az Azure-függvény (jobb oldali legtöbb ablaktáblán az Azure functions portálon) a "Fájlok megtekintése" hivatkozásra, és hozzon létre egy project.json nevű fájlt. Adja hozzá a következő tartalmát a `project.json` fájlt, és mentse azt:

 ```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.Azure.EventGrid": "2.0.0"
      }
    }
   }
}
```

![A hozzáadott NuGet-csomag](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Végpont ellenőrzése

Először is szeretné akkor kezeli `Microsoft.EventGrid.SubscriptionValidationEvent` eseményeket. Minden alkalommal, amikor valaki feliratkozik egy eseményt, Event Grid egy érvényesítési eseményt küld a végpontot egy `validationCode` a hasznos adatforgalmat. A végpont megadása kötelező, a válasz törzse a vissza ebben az echo [igazolja, hogy a végpont nem érvényes, és Ön a tulajdonosuk](security-authentication.md#webhook-event-delivery). Ha használ egy [Event Grid-Trigger](../azure-functions/functions-bindings-event-grid.md) helyett egy WebHook által aktivált függvény, a végpont ellenőrzése történik-e az Ön számára. Ha egy külső API-szolgáltatás használ (például [Zapier](https://zapier.com) vagy [IFTTT](https://ifttt.com/)), nem fogja tudni programozott módon echo az érvényesítési kódot. Ezeket a szolgáltatásokat a egy érvényesítési URL-címet, amely az előfizetés érvényesítése esemény küldése használatával manuálisan ellenőrizheti az előfizetés. Másolja az URL-CÍMRE a `validationUrl` tulajdonság és a egy GET küldési kérelmek REST-ügyféllel vagy a böngésző felületén keresztül.

Előzetes verzió van manuális érvényesítésre. A használatához telepítenie kell az [Event Grid-bővítményt](/cli/azure/azure-cli-extensions-list) az [Azure CLI](/cli/azure/install-azure-cli)-hez. A telepítést az `az extension add --name eventgrid` paranccsal tudja végrehajtani. Ha a REST API-t használ, ellenőrizze, hogy használ `api-version=2018-05-01-preview`.

A C# a `DeserializeEventGridEvents()` függvény deserializes az Event Grid-események. Az eseményadatok be a megfelelő típus, például StorageBlobCreatedEventData deserializes azt. Használja a `Microsoft.Azure.EventGrid.EventTypes` osztály támogatott eseménytípusok és neveket beolvasásához.

Programozott módon echo az érvényesítési kódot, használja az alábbi kódot. Kapcsolódó minták annak [Event Grid fogyasztói példához](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridConsumer).

```csharp
using System.Net;
using Newtonsoft.Json;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;

    string requestContent = await req.Content.ReadAsStringAsync();
    log.Info($"Received events: {requestContent}");

    EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

    EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        if (eventGridEvent.Data is SubscriptionValidationEventData)
        {
            var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response

            var responseData = new SubscriptionValidationResponse()
            {
                ValidationResponse = eventData.ValidationCode
            };

            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}
```

```javascript
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

### <a name="test-validation-response"></a>Tesztelje az érvényesítési válaszhoz

Az érvényesítési választ függvény tesztelése a mintaesemény illessze be a teszt mező, a függvény:

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

Amikor a Futtatás gombra kattint, a kimenet lesz 200-as rendben és `{"ValidationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` törzsében:

![érvényesítési válaszhoz](./media/receive-events/validation-response.png)

## <a name="handle-blob-storage-events"></a>Blob storage-események kezeléséhez

Most tekintsük kiterjesztése a függvény, amely kezelni `Microsoft.Storage.BlobCreated`:

```cs
using System.Net;
using Newtonsoft.Json;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;

    string requestContent = await req.Content.ReadAsStringAsync();
    log.Info($"Received events: {requestContent}");

    EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

    EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        if (eventGridEvent.Data is SubscriptionValidationEventData)
        {
            var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response

            var responseData = new SubscriptionValidationResponse()
            {
                ValidationResponse = eventData.ValidationCode
            };

            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
        else if (eventGridEvent.Data is StorageBlobCreatedEventData)
        {
            var eventData = (StorageBlobCreatedEventData)eventGridEvent.Data;
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}
```

```javascript
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

Tesztelje az új funkciót, a függvény által üzembe egy [Blob storage esemény](./event-schema-blob-storage.md#example-event) a vizsgálati mezőre, és fut:

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

A függvény naplójában a blob URL-cím kimenetnek kell megjelennie:

![Kimeneti naplót](./media/receive-events/blob-event-response.png)

Is letesztelheti létrehozása a Blob storage-fiók vagy általános célú V2 (GPv2) Storage-fiókot, [hozzáadása és az esemény előfizetésének](../storage/blobs/storage-blob-event-quickstart.md), és a végpont beállítása a függvény URL-cím:

![Függvény URL-címe](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Egyéni események kezeléséhez

Végül lehetővé teszi a funkció még egyszer kiterjesztése az, hogy az egyéni eseményeket is tud kezelni. 

A C# az SDK támogatja az eseménytípus nevét leképezése az esemény adattípus. Használja a `AddOrUpdateCustomEventMapping()` képezze le az egyéniesemény függvényt.

Adja hozzá az esemény ellenőrzi a `Contoso.Items.ItemReceived`. A végső kód hasonlóan kell kinéznie:

```cs
using System.Net;
using Newtonsoft.Json;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;

class ContosoItemReceivedEventData
{
    [JsonProperty(PropertyName = "itemSku")]
    public string ItemSku { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;

    string requestContent = await req.Content.ReadAsStringAsync();
    log.Info($"Received events: {requestContent}");

    EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();
    eventGridSubscriber.AddOrUpdateCustomEventMapping("Contoso.Items.ItemReceived", typeof(ContosoItemReceivedEventData));
    EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        if (eventGridEvent.Data is SubscriptionValidationEventData)
        {
            var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response

            var responseData = new SubscriptionValidationResponse()
            {
                ValidationResponse = eventData.ValidationCode
            };

            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
        else if (eventGridEvent.Data is StorageBlobCreatedEventData)
        {
            var eventData = (StorageBlobCreatedEventData)eventGridEvent.Data;
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }
        else if (eventGridEvent.Data is ContosoItemReceivedEventData)
        {
            var eventData = (ContosoItemReceivedEventData)eventGridEvent.Data;
            log.Info($"Got ContosoItemReceived event data, item SKU {eventData.ItemSku}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";

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
            var payload = body.data;
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
        }
    }
    context.done();
};
```

### <a name="test-custom-event-handling"></a>Teszt egyéni esemény kezelése

Végül ellenőrizze, hogy a függvény mostantól képes kezelni az egyéni esemény típusa:

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Contoso.Items.ItemReceived",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "itemSku": "Standard"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

Ez a funkció által az élő is tesztelheti [a curl használatával egyéni esemény küldése a portálról](./custom-event-quickstart-portal.md) vagy [közzététele egy egyéni témakör](./post-to-custom-topic.md) használatával bármilyen szolgáltatást vagy alkalmazást, amely egy végpontnak, például a FELTEHETI[Postman](https://www.getpostman.com/). Hozzon létre egy egyéni témakör és a egy esemény-előfizetést a végpontot, a függvény URL-Címének beállítása.

## <a name="next-steps"></a>További lépések

* Fedezze fel a [az Azure Event Grid felügyeleti és SDK-k közzététele](./sdk-overview.md)
* Ismerje meg, hogyan [közzététel egyéni témakörben](./post-to-custom-topic.md)
* Próbálja ki az Event Grid és a funkciók részletes oktatóanyagok egyikét például [a blobtárolóba feltöltött képek átméretezésének](resize-images-on-storage-blob-upload-event.md)

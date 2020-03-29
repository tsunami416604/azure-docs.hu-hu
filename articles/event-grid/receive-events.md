---
title: Események fogadása az Azure Event Gridből HTTP-végpontra
description: Http-végpont érvényesítésének, majd események fogadásának és deszerializálásának ismertetése az Azure Event Gridből
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: babanisa
ms.openlocfilehash: cb38fd17c0c1bfbe3e5957d8f432f0a43b285c93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60803806"
---
# <a name="receive-events-to-an-http-endpoint"></a>Események fogadása HTTP-végponton

Ez a cikk azt ismerteti, hogyan [érvényesítheti a HTTP-végpontesemények](security-authentication.md#webhook-event-delivery) et egy esemény-előfizetésből, majd fogadhat eseményeket, majd deszerializálhatja az eseményeket. Ez a cikk egy Azure-függvényt használ demonstrációs célokra, azonban ugyanazok a fogalmak vonatkoznak, függetlenül attól, hogy az alkalmazás hová van tárolva.

> [!NOTE]
> **Erősen** ajánlott, hogy egy [Eseményrács-eseményindító,amikor](../azure-functions/functions-bindings-event-grid.md) egy Azure-függvény eseményrács váltja ki. Az általános WebHook-eseményindító használata itt demonstráló.

## <a name="prerequisites"></a>Előfeltételek

Szüksége van egy függvényalkalmazásra EGY HTTP-aktivált funkcióval.

## <a name="add-dependencies"></a>Függőségek hozzáadása

Ha a .NET-ben fejleszt, [adjon hozzá függőséget](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) a `Microsoft.Azure.EventGrid` [Nuget csomag](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)funkciójához. A cikkben szereplő példák hoz 1.4.0-s vagy újabb verziót igényelnek.

Más nyelveks sdk-k érhetők el a [Közzététel SDK-k](./sdk-overview.md#data-plane-sdks) referencia. Ezek a csomagok a natív eseménytípusok modelljeivel rendelkeznek, például `EventGridEvent`a , `StorageBlobCreatedEventData`és `EventHubCaptureFileCreatedEventData`a.

Kattintson a "Fájlok megtekintése" hivatkozásra az Azure-függvényben (az Azure-függvények portálának jobb oldali ablaktáblájában), és hozzon létre egy project.json nevű fájlt. Adja hozzá a `project.json` következő tartalmat a fájlhoz, és mentse azt:

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

![Hozzáadott NuGet csomag](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Végpont érvényesítése

Az első dolog, amit `Microsoft.EventGrid.SubscriptionValidationEvent` meg akarsz tenni, hogy kezeld az eseményeket. Minden alkalommal, amikor valaki előfizet egy eseményre, az Event `validationCode` Grid egy érvényesítési eseményt küld a végpontnak egy adathasznos adattal. A végpontnak ezt a választörzsben kell [visszhangoznia annak bizonyításához, hogy a végpont érvényes és az Ön tulajdonában van.](security-authentication.md#webhook-event-delivery) Ha egy [Eseményrács-eseményindítót](../azure-functions/functions-bindings-event-grid.md) használ a WebHook aktivált függvény helyett, a végpontérvényesítés tetszése az Ön feladata. Ha külső API-szolgáltatást (például [Zapier](https://zapier.com) vagy [IFTTT)](https://ifttt.com/)használ, előfordulhat, hogy nem tudja programozott módon visszhangozni az érvényesítési kódot. Ezeka szolgáltatások esetében manuálisan érvényesítheti az előfizetést az előfizetés-érvényesítési eseményben küldött érvényesítési URL-cím használatával. Másolja az URL-címet a `validationUrl` tulajdonságba, és küldje el a GET-kérelmet egy REST-ügyfélen vagy a webböngészőn keresztül.

A C#-ban a `DeserializeEventGridEvents()` függvény deszerializálja az Event Grid eseményeket. Deszerializálja az eseményadatokat a megfelelő típusba, például StorageBlobCreatedEventData. Az `Microsoft.Azure.EventGrid.EventTypes` osztály segítségével támogatott eseménytípusokat és neveket kaphat.

Az érvényesítési kód programozott visszhangjának használatához használja a következő kódot. A kapcsolódó mintákat az [Event Grid Consumer example](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridConsumer)című helyen találja.

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

### <a name="test-validation-response"></a>Tesztérvényesítési válasz

Tesztelje a validációs válasz funkciót úgy, hogy a mintaeseményt beilleszti a függvény tesztmezőjébe:

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

Ha a Futtatás gombra kattint, a `{"ValidationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` kimenetnek 200 OK-nak kell lennie, és a törzsben:

![érvényesítési válasz](./media/receive-events/validation-response.png)

## <a name="handle-blob-storage-events"></a>Blob-tárolási események kezelése

Most, nézzük ki terjeszteni `Microsoft.Storage.BlobCreated`a funkciót kezelni:

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

### <a name="test-blob-created-event-handling"></a>Tesztblob létrehozva eseménykezelés

Tesztelje a funkció új funkcióit egy [Blob-tárolási esemény](./event-schema-blob-storage.md#example-event) tesztmezőbe helyezésével és futtatásával:

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

A blob URL-kimenetének a függvénynaplóban kell lennie:

![Kimeneti napló](./media/receive-events/blob-event-response.png)

Tesztelheti a Blob storage-fiók vagy az Általános célú V2 (GPv2) tárfiók létrehozásával, [az esemény-előfizetés hozzáadásával és az esemény-előfizetéssel,](../storage/blobs/storage-blob-event-quickstart.md)valamint a végpont nak a függvény URL-címére állításával:

![Függvény URL-címe](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Egyéni események kezelése

Végül lehetővé teszi, hogy még egyszer kiterjesszük a függvényt, hogy egyéni eseményeket is kezelhessen. 

A C#-ban az SDK támogatja az eseménytípus nevének hozzárendelését az esemény adattípusához. A `AddOrUpdateCustomEventMapping()` függvény segítségével képezze le az egyéni eseményt.

Adj hozzá egy `Contoso.Items.ItemReceived`csekket az eseményedhez. A végső kódnak így kell kinéznie:

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

### <a name="test-custom-event-handling"></a>Egyéni eseménykezelés tesztelése

Végül ellenőrizze, hogy a függvény képes-e kezelni az egyéni eseménytípust:

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

Ezt a funkciót élőben is tesztelheti, ha [egyéni eseményt küld a CURL-nel a portálról,](./custom-event-quickstart-portal.md) vagy egy egyéni [témakörbe küld egy olyan](./post-to-custom-topic.md) szolgáltatást vagy alkalmazást, amely postázhat egy végpontra, például [postásra.](https://www.getpostman.com/) Hozzon létre egy egyéni témakört és egy esemény-előfizetést a végpont beállítása a függvény URL-címeként.

## <a name="next-steps"></a>További lépések

* Az [Azure Event Grid-kezelés és az SDK-k közzététele](./sdk-overview.md)
* Tudnivalók egyéni [témakörök közzétételéről](./post-to-custom-topic.md)
* Próbálja ki az eseményrács és a funkciók egyik oktatóanyagát, például a [Blob storage-ba feltöltött képek átméretezését](resize-images-on-storage-blob-upload-event.md)

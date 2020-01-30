---
title: Azure Event Grid használata a CloudEvents-sémában lévő eseményekkel
description: Ismerteti, hogyan használható a CloudEvents séma a Azure Event Grid eseményeihez. A szolgáltatás támogatja a felhőalapú események JSON-implementációjában lévő eseményeket.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 25a24c5bb44c77038a508e4c2f4e099132101f6a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844734"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>CloudEvents v 1.0 séma használata Event Grid

Az [alapértelmezett esemény sémáján](event-schema.md)kívül Azure Event Grid natív módon támogatja a [CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) és a [http protokoll kötésének](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md)JSON-implementációjában lévő eseményeket. A [CloudEvents](https://cloudevents.io/) egy [nyílt specifikáció](https://github.com/cloudevents/spec/blob/v1.0/spec.md) az események leírásához.

A CloudEvents a felhőalapú események közzétételére és felhasználására szolgáló közös esemény sémájának használatával egyszerűsíti az együttműködési képességet. Ez a séma lehetővé teszi, hogy egységes eszközöket biztosítson, és szabványos módon irányítsa át & az események kezelését, valamint a külső esemény sémájának deszerializálásának univerzális módszereit. Közös sémával könnyebben integrálhatja a munkafolyamatokat a különböző platformokon.

A CloudEvents számos [közreműködő](https://github.com/cloudevents/spec/blob/master/community/contributors.md), például a Microsoft, a [Felhőbeli natív számítástechnikai alaprendszer](https://www.cncf.io/)segítségével építhető ki. Jelenleg 1,0-as verzióként érhető el.

Ez a cikk azt ismerteti, hogyan használható a CloudEvents séma a Event Grid használatával.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Az előzetes verzió funkciójának telepítése

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>CloudEvent séma

Íme egy példa egy Azure Blob Storage eseményre CloudEvents formátumban:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

[Itt](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)találja a CloudEvents 1.0-s verziójában elérhető mezők, típusok és definíciók részletes leírását.

A CloudEvents-sémában és a Event Grid sémában szállított események fejlécének értékei megegyeznek a `content-type`kivételével. A CloudEvents séma esetében a fejléc értéke `"content-type":"application/cloudevents+json; charset=utf-8"`. Event Grid séma esetében a fejléc értéke `"content-type":"application/json; charset=utf-8"`.

## <a name="configure-event-grid-for-cloudevents"></a>A CloudEvents Event Grid konfigurálása

A CloudEvents-sémában lévő események bemenetéhez és kimenetéhez Event Grid is használhatja. A CloudEvents-t használhatja rendszereseményekhez, például Blob Storage eseményekhez és IoT Hub eseményekhez és egyéni eseményekhez. Emellett átalakíthatja ezeket az eseményeket a huzalon oda-vissza.


| Bemeneti séma       | Kimeneti séma
|--------------------|---------------------
| CloudEvents formátuma | CloudEvents formátuma
| Event Grid formátum  | CloudEvents formátuma
| Event Grid formátum  | Event Grid formátum

Az összes esemény sémája esetében a Event Grid érvényesítést igényel az Event Grid-témakörre való közzétételkor és az esemény-előfizetés létrehozásakor. További információ: [Event Grid biztonság és hitelesítés](security-authentication.md).

### <a name="input-schema"></a>Bemeneti séma

Egyéni témakörhöz tartozó bemeneti sémát az egyéni témakör létrehozásakor állíthatja be.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

### <a name="output-schema"></a>Kimeneti séma

A kimeneti séma az esemény-előfizetés létrehozásakor állítható be.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

PowerShell esetén használja az alábbi parancsot:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 Jelenleg nem használhat Event Grid eseményindítót egy Azure Functions alkalmazáshoz, ha az esemény a CloudEvents-sémában kerül kézbesítésre. HTTP-trigger használata. A CloudEvents sémában eseményeket fogadó HTTP-triggerek megvalósítására példákat a [CloudEvents használata a Azure functions használatával](#azure-functions)című témakörben talál.

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>Végpont ellenőrzése a CloudEvents 1.0-s verziójában

Ha már ismeri a Event Gridt, érdemes lehet Event Grid végpont-ellenőrzési kézfogását a visszaélések megelőzésére. A CloudEvents 1.0-s verziójában a HTTP-beállítások módszer használatával valósítja meg a saját [visszaélés elleni védelem szemantikai](security-authentication.md#webhook-event-delivery) beállításait. További információk [itt](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Ha a kimenethez a CloudEvents sémát használja, a Event Grid a Event Grid érvényesítési esemény mechanizmusa helyett az CloudEvents 1.0-s verziójának védelme szolgáltatást használja.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Használat Azure Functions

A [Azure Functions Event Grid kötés](../azure-functions/functions-bindings-event-grid.md) nem támogatja natív módon a CloudEvents, ezért a http által aktivált függvények a CloudEvents üzenetek olvasására használhatók. Ha HTTP-triggert használ a CloudEvents olvasására, a Event Grid-trigger automatikus működéséhez kódot kell írnia:

* Érvényesítési választ küld egy [előfizetés-ellenőrzési kérelemre](../event-grid/security-authentication.md#webhook-event-delivery).
* Egyszer meghívja a függvényt a kérelem törzsében található Event tömbben.

További információ a függvény helyi meghívásához vagy az Azure-ban való futtatásához használt URL-címről: [http-trigger kötési útmutatója – dokumentáció](../azure-functions/functions-bindings-http-webhook.md)

A következő mintakód C# egy http-triggerhez szimulálja Event Grid trigger viselkedését.  Ezt a példát a CloudEvents sémában elküldött eseményekhez használhatja.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    if (message.Type == JTokenType.Array)
    {
        // If the request is for subscription validation, send back the validation code.
        if (string.Equals((string)message[0]["eventType"],
        "Microsoft.EventGrid.SubscriptionValidationEvent",
        System.StringComparison.OrdinalIgnoreCase))
        {
            log.LogInformation("Validate request received");
            return req.CreateResponse<object>(new
            {
                validationResponse = message[0]["data"]["validationCode"]
            });
        }
    }
    else
    {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        log.LogInformation($"Source: {message["source"]}");
        log.LogInformation($"Time: {message["eventTime"]}");
        log.LogInformation($"Event data: {message["data"].ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

A következő minta JavaScript-kód a HTTP-triggerekhez szimulálja Event Grid trigger viselkedését. Ezt a példát a CloudEvents sémában elküldött eseményekhez használhatja.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var message = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (message.length > 0 && message[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        var code = message[0].data.validationCode;
        context.res = { status: 200, body: { "ValidationResponse": code } };
    }
    else {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
    context.done();
};
```

## <a name="next-steps"></a>Következő lépések

* További információ az események kézbesítésének figyeléséről: [Event Grid üzenet kézbesítésének figyelése](monitor-event-delivery.md).
* Javasoljuk, hogy tesztelje, véleményezze és [járuljon hozzá](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) a CloudEvents.
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).

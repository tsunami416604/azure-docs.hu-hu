---
title: Az Azure Event Grid használata eseményekkel a CloudEvents sémában
description: Bemutatja, hogyan használhatja a CloudEvents sémát események az Azure Event Gridben. A szolgáltatás támogatja a Felhőesemények JSON-implementációjában lévő eseményeket.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 404052984cb99e37f7404a47f3ac374088d32d6c
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393472"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>CloudEvents 1.0-s verzióját használja az Eseményrácsgal
Az alapértelmezett [eseményséma](event-schema.md)mellett az Azure Event Grid natív módon támogatja a [CloudEvents 1.0-s és HTTP protokollkötési JSON-implementációjának](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) [eseményeit.](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md) [A CloudEvents](https://cloudevents.io/) egy [nyílt specifikáció](https://github.com/cloudevents/spec/blob/v1.0/spec.md) az eseményadatok leírására.

A CloudEvents leegyszerűsíti az interoperabilitást azáltal, hogy közös eseménysémát biztosít a közzétételhez és a felhőalapú események fogyasztásához. Ez a séma lehetővé teszi az egységes szerszámozást, az események & kezelés szabványos módjait, valamint a külső eseményséma deszerializálásának univerzális módjait. A közös sémával könnyebben integrálhatja a munkát a platformok között.

A CloudEvents szolgáltatást több közreműködő – köztük a Microsoft is – a [Cloud Native Computing Foundation (Felhőalapú számítástechnikai alapítvány)](https://www.cncf.io/) [építi.](https://github.com/cloudevents/spec/blob/master/community/contributors.md) Jelenleg 1.0-s verzióként érhető el.

Ez a cikk a CloudEvents séma event griddel való használatát ismerteti.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Az előzetes verzió telepítése szolgáltatás

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>CloudEvent séma

Íme egy példa egy CloudEvents formátumú Azure Blob Storage-eseményre:

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

A rendelkezésre álló mezők, azok típusai és definícióinak részletes leírása a CloudEvents 1.0-s verzióban [itt érhető el.](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)

A CloudEvents sémában és az Event Grid sémában leszállított események `content-type`fejlécértékei megegyeznek a . A CloudEvents séma esetén `"content-type":"application/cloudevents+json; charset=utf-8"`ez a fejlécérték a . Az Event Grid sémája esetén `"content-type":"application/json; charset=utf-8"`ez a fejlécérték .

## <a name="configure-event-grid-for-cloudevents"></a>Eseményrács konfigurálása a CloudEvents szolgáltatáshoz

Az Event Grid a CloudEvents séma eseményeinek bemenetére és kimenetére is használható. A CloudEvents rendszereseményekhez, például a Blob Storage-eseményekhez és az IoT Hub-eseményekhez, valamint az egyéni eseményekhez is használható. Azt is át ezeket az eseményeket a vezetéket oda-vissza.


| Bemeneti séma       | Kimeneti séma
|--------------------|---------------------
| CloudEvents formátum | CloudEvents formátum
| Eseményrács formátuma  | CloudEvents formátum
| Eseményrács formátuma  | Eseményrács formátuma

Az event grid minden eseménysémában érvényesítést igényel, amikor egy eseményrács-témakörben tesz közzé, illetve esemény-előfizetéslétrehozásakor. További információt az [Event Grid biztonsága és hitelesítése](security-authentication.md)című témakörben talál.

### <a name="input-schema"></a>Bemeneti séma

Az egyéni témakör létrehozásakor egyéni témakör bemeneti sémáját állíthatja be.

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

A kimeneti sémát az esemény-előfizetés létrehozásakor állíthatja be.

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

 Jelenleg nem használhatja az Event Grid eseményindító egy Azure Functions-alkalmazás, ha az esemény a CloudEvents sémában kézbesítve. HTTP-eseményindító használata. Példák egy HTTP-eseményindító megvalósítására, amely eseményeket fogad a CloudEvents sémában, [lásd: A CloudEvents használata](#azure-functions)az Azure Functions funkcióval című témakörben.

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>Végpont-érvényesítés a CloudEvents 1.0-s verzióját alkalmazva

Ha már ismeri az Event Gridet, előfordulhat, hogy ismeri az Event Grid végpontérvényesítési kézfogását a visszaélések megelőzése érdekében. A CloudEvents 1.0-s verziójásaját [visszaélésvédelmi szemantikát](security-authentication.md#webhook-event-delivery) valósít meg a HTTP OPTIONS metódus használatával. További információk [itt](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). A CloudEvents séma kimeneti használatakor az Event Grid a CloudEvents 1.0-s verzióját használja az Event Grid érvényesítési eseménymechanizmusa helyett.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Használat az Azure-függvényekkel

Az [Azure Functions Event Grid kötés](../azure-functions/functions-bindings-event-grid.md) nem támogatja a CloudEvents natív módon, így a HTTP-aktivált függvények a CloudEvents üzenetek olvasására szolgálnak. Ha http-eseményindítót használ a CloudEvents olvasásához, kódot kell írnia ahhoz, amit az Event Grid eseményindító automatikusan csinál:

* Érvényesítési választ küld egy [előfizetés-érvényesítési kérelemnek.](../event-grid/security-authentication.md#webhook-event-delivery)
* A függvényt a kérelemtörzsben található eseménytömb elemenként egyszer hívja meg.

A függvény helyi meghívásához vagy az Azure-ban való futtatáshoz használandó URL-címről a [HTTP-eseményindító-kötési hivatkozási dokumentációban](../azure-functions/functions-bindings-http-webhook.md) olvashat.

A HTTP-eseményindító c# kódja az eseményrács eseményindítójának működését szimulálja.  Ebben a példában a CloudEvents sémában szállított eseményekhez használható.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", "options", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    if (req.Method == "OPTIONS")
    {
        // If the request is for subscription validation, send back the validation code
        
        var response = req.CreateResponse(HttpStatusCode.OK);
        response.Add("Webhook-Allowed-Origin", "eventgrid.azure.net");

        return response;
    }

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    // The request is not for subscription validation, so it's for an event.
    // CloudEvents schema delivers one event at a time.
    log.LogInformation($"Source: {message["source"]}");
    log.LogInformation($"Time: {message["eventTime"]}");
    log.LogInformation($"Event data: {message["data"].ToString()}");

    return req.CreateResponse(HttpStatusCode.OK);
}
```

A HTTP-eseményindítóhoz a következő Minta JavaScript-kód szimulálja az Eseményrács eseményindítójának viselkedését. Ebben a példában a CloudEvents sémában szállított eseményekhez használható.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS) {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = { status: 200, body: { "ValidationResponse": code } };
        context.res.headers.append('Webhook-Allowed-Origin', 'eventgrid.azure.net');
    }
    else
    {
        var message = req.body;
        
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

## <a name="next-steps"></a>További lépések

* Az eseménykézbesítésfigyelésről az [Eseményrács üzenetkézbesítésének figyelése](monitor-event-delivery.md)című témakörben talál további információt.
* Javasoljuk, hogy tesztelje, véleményezze és [járuljon hozzá](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) a CloudEvents-hez.
* Az Azure Event Grid-előfizetés ek létrehozásáról az [Event Grid-előfizetésséma](subscription-creation-schema.md)című témakörben talál további információt.

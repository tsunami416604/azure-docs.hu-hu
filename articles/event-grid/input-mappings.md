---
title: Egyéni mező leképezése Azure Event Grid sémára
description: Ez a cikk azt ismerteti, hogyan alakíthatja át az egyéni sémát a Azure Event Grid sémára, ha az esemény adatai nem egyeznek Event Grid sémával.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: e8077068a265d659cf6009eb7762188637c373d6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721659"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Egyéni mezők leképezése Event Grid-sémára

Ha az esemény adatai nem egyeznek a várt [Event Grid sémával](event-schema.md), továbbra is használhatja a Event Gridt az események előfizetőknek való irányításához. Ez a cikk azt ismerteti, hogyan képezhető le a séma a Event Grid sémára.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Az előzetes verzió funkciójának telepítése

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Eredeti esemény sémája

Tegyük fel, hogy van egy olyan alkalmazás, amely a következő formátumban küld eseményeket:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Bár ez a formátum nem egyezik meg a szükséges sémával, Event Grid lehetővé teszi, hogy a mezőket a sémához rendelje. Vagy megadhatja az eredeti sémában található értékeket.

## <a name="create-custom-topic-with-mapped-fields"></a>Egyéni témakör létrehozása megfeleltetett mezőkkel

Egyéni témakör létrehozásakor adja meg, hogyan kívánja leképezni a mezőket az eredeti eseményről az Event Grid sémába. A leképezés testreszabásához három érték használható:

* A **bemeneti séma** értéke határozza meg a séma típusát. Az elérhető lehetőségek a CloudEvents séma, az egyéni esemény sémája vagy a Event Grid séma. Az alapértelmezett érték Event Grid séma. Ha egyéni leképezést hoz létre a séma és az Event Grid-séma között, használja az egyéni esemény sémáját. Ha az események a CloudEvents sémában vannak, használja a Cloudevents sémát.

* Az **alapértelmezett értékek leképezése** tulajdonság a Event Grid séma mezőinek alapértelmezett értékeit adja meg. Megadhatja `subject`, `eventtype`és `dataversion`alapértelmezett értékeit. Ezt a paramétert általában akkor használja, ha az egyéni séma nem tartalmaz olyan mezőt, amely megfelel az adott három mező egyikének. Megadhatja például, hogy az adatverzió mindig **1,0**-re legyen állítva.

* A **leképezési mezők** érték leképezi a sémában található mezőket az Event Grid sémába. Az értékeket szóközzel tagolt kulcs/érték párokban kell megadni. A kulcsnév esetében használja az Event Grid mező nevét. Az értéknél használja a mező nevét. `id`, `topic`, `eventtime`, `subject`, `eventtype`és `dataversion`nyilvánoskulcs-neveket is használhat.

Egyéni témakör az Azure CLI-vel való létrehozásához használja a következőt:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema \
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName myResourceGroup `
  -Name demotopic `
  -Location eastus2 `
  -InputSchema CustomEventSchema `
  -InputMappingField @{eventType="myEventTypeField"} `
  -InputMappingDefaultValue @{subject="DefaultSubject"; dataVersion="1.0" }
```

## <a name="subscribe-to-event-grid-topic"></a>Előfizetés az Event Grid-témakörre

Az egyéni témakörre való feliratkozáskor meg kell adnia az események fogadásához használni kívánt sémát. Meg kell adnia a CloudEvents sémáját, az egyéni esemény sémáját vagy a Event Grid sémát. Az alapértelmezett érték Event Grid séma.

Az alábbi példa egy Event Grid-témakörre utal, és a Event Grid sémát használja. Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

A következő példa az esemény bemeneti sémáját használja:

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

Az alábbi példa egy Event Grid-témakörre utal, és a Event Grid sémát használja. PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

A következő példa az esemény bemeneti sémáját használja:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>Esemény közzététele a témakörben

Most már készen áll egy esemény küldésére az egyéni témakörbe, és megtekintheti a leképezés eredményét. Az alábbi parancsfájl egy esemény a [példában szereplő sémában](#original-event-schema)való közzétételéhez:

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

event='[ { "myEventTypeField":"Created", "resource":"Users/example/Messages/1000", "resourceData":{"someDataField1":"SomeDataFieldValue"} } ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demotopic).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName myResourceGroup -Name demotopic

$htbody = @{
    myEventTypeField="Created"
    resource="Users/example/Messages/1000"
    resourceData= @{
        someDataField1="SomeDataFieldValue"
    }
}

$body = "["+(ConvertTo-Json $htbody)+"]"
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Most tekintse meg a webhook-végpontot. A két előfizetés különböző sémákban küldött eseményeket.

Az első előfizetés az Event Grid sémát használta. A kézbesítési esemény formátuma a következő:

```json
{
  "id": "aa5b8e2a-1235-4032-be8f-5223395b9eae",
  "eventTime": "2018-11-07T23:59:14.7997564Z",
  "eventType": "Created",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "subject": "DefaultSubject",
  "data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": {
      "someDataField1": "SomeDataFieldValue"
    }
  }
}
```

Ezek a mezők az egyéni témakörből származó leképezéseket tartalmazzák. a **myEventTypeField** a **EventType**van leképezve. A rendszer a **DataVersion** és a **Tárgy** alapértelmezett értékeit használja. Az **adatobjektum tartalmazza az eredeti** Event Schema mezőket.

A második előfizetés a bemeneti esemény sémáját használta. A kézbesítési esemény formátuma a következő:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": {
    "someDataField1": "SomeDataFieldValue"
  }
}
```

Figyelje meg, hogy az eredeti mezők kézbesítése megtörtént.

## <a name="next-steps"></a>Következő lépések

* További információ az események kézbesítéséről és újrapróbálkozásáról, [Event Grid az üzenetek kézbesítéséről, és próbálkozzon újra](delivery-and-retry.md).
* Az Event Grid ismertetése: [Az Event Grid bemutatása](overview.md).
* Az Event Grid használatának gyors megkezdéséhez tekintse meg [az egyéni események létrehozása és irányítása Azure Event Grid](custom-event-quickstart.md)használatával című témakört.

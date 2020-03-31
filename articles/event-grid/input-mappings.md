---
title: Egyéni mező hozzárendelése az Azure Event Grid sémájához
description: Ez a cikk ismerteti, hogyan konvertálhatja az egyéni sémát az Azure Event Grid séma, ha az esemény adatok nem egyeznek Event Grid séma.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: e8077068a265d659cf6009eb7762188637c373d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721659"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Egyéni mezők leképezése Event Grid-sémára

Ha az eseményadatok nem egyeznek meg a várt [Eseményrács-sémával,](event-schema.md)továbbra is használhatja az Event Grid et az esemény előfizetőkhöz való irányításához. Ez a cikk azt ismerteti, hogyan képezheti le a sémát az Event Grid sémára.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Az előzetes verzió telepítése szolgáltatás

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Eredeti eseményséma

Tegyük fel, hogy van egy olyan alkalmazása, amely az eseményeket a következő formátumban küldi:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Bár ez a formátum nem felel meg a szükséges sémának, az Event Grid lehetővé teszi a mezők sémához való hozzárendelését. Vagy fogadhatja az eredeti séma értékeit.

## <a name="create-custom-topic-with-mapped-fields"></a>Egyéni témakör létrehozása leképezett mezőkkel

Egyéni témakör létrehozásakor adja meg, hogyan képezheti le a mezőket az eredeti eseményről az eseményrácssémához. A leképezés testreszabásához három érték használható:

* A **bemeneti séma** értéke a séma típusát adja meg. A rendelkezésre álló lehetőségek a CloudEvents séma, az egyéni eseményséma vagy az Event Grid-séma. Az alapértelmezett érték az Eseményrács sémája. Amikor egyéni leképezést hoz létre a séma és az eseményrács sémája között, egyéni eseménysémát használjon. Ha az események a CloudEvents sémában vannak, használja a Cloudevents sémát.

* A **leképezési alapértelmezett értékek** tulajdonság az Eseményrács séma mezőinek alapértelmezett értékeit adja meg. A programhoz alapértelmezett `subject` `eventtype`értékeket `dataversion`állíthat be a és a hoz. Ezt a paramétert általában akkor használja, ha az egyéni séma nem tartalmaz olyan mezőt, amely a három mező egyikének felel meg. Megadhatja például, hogy az adatverzió mindig **1.0-ra**van állítva.

* A **leképezési mezők** értéke leképezi a mezőket a sémából az eseményrács sémájához. Az értékeket szóközre bontott kulcs-érték párokban adhatja meg. A kulcs nevéhez használja az eseményrács mező nevét. Az értékhez használja a mező nevét. Kulcsneveket `id`használhatunk `topic`a `eventtime` `subject`, `eventtype`, `dataversion`, , , , és .

Egyéni témakör létrehozásához az Azure CLI használatával használja:

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

## <a name="subscribe-to-event-grid-topic"></a>Feliratkozás az eseményrács témakörére

Az egyéni témakörre való feliratkozáskor megadhatja az események fogadásához használni kívánt sémát. Megadhatja a CloudEvents sémát, az egyéni eseménysémát vagy az Event Grid sémát. Az alapértelmezett érték az Eseményrács sémája.

A következő példa feliratkozik egy eseményrács-témakörre, és az Event Grid sémát használja. Azure CLI esetén használja az alábbi parancsot:

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

A következő példa feliratkozik egy eseményrács-témakörre, és az Event Grid sémát használja. PowerShell esetén használja az alábbi parancsot:

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

## <a name="publish-event-to-topic"></a>Esemény közzététele a témában

Most már készen áll arra, hogy egy eseményt küldjön az egyéni témakörnek, és tekintse meg a leképezés eredményét. A következő parancsfájl, amely egy eseményt tesz közzé a [példasémában:](#original-event-schema)

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

Most nézze meg a WebHook-végpontot. A két előfizetés különböző sémákban szállított eseményeket.

Az első előfizetés eseményrács-sémát használt. A leszállított esemény formátuma:

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

Ezek a mezők az egyéni témakör leképezéseit tartalmazzák. **A myEventTypeField** az **EventType típusra**van leképezve. A **DataVersion** és a **Tárgy** alapértelmezett értékeit használja a rendszer. Az **Adat** objektum az eredeti eseménysémamezőket tartalmazza.

A második előfizetés a bemeneti eseménysémát használta. A leszállított esemény formátuma:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": {
    "someDataField1": "SomeDataFieldValue"
  }
}
```

Figyelje meg, hogy az eredeti mezők kézbesítve voltak.

## <a name="next-steps"></a>További lépések

* Az eseménykézbesítésről és az újrapróbálkozásokról az [Event Grid üzenetkézbesítésével és újrapróbálkozásával](delivery-and-retry.md)kapcsolatos információkért kattintson.
* Az Event Grid ismertetése: [Az Event Grid bemutatása](overview.md).
* Az Event Grid használatának gyors megkezdéséhez olvassa el az [Egyéni események létrehozása és irányítása az Azure Event Griddel című témakört.](custom-event-quickstart.md)

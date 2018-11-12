---
title: Egyéni mező leképezése az Azure Event Grid-séma
description: Az egyéni séma átalakítása az Azure Event Grid-séma ismerteti.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: tomfitz
ms.openlocfilehash: ce9df1d45de82c759883dc90d50c28551bf62cdf
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51287304"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Egyéni mezők leképezése sémára Event Grid

Ha az eseményadatok nem egyezik a várt [Event Grid séma](event-schema.md), Event Grid útvonal esemény-előfizetők továbbra is használhatja. Ez a cikk ismerteti, hogyan képezhet le a séma az Event Grid-sémát.

## <a name="install-preview-feature"></a>Előzetes verziójú funkció telepítése

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Eredeti eseménysémája

Tegyük fel, hogy egy alkalmazást, amely elküldi az eseményeket a következő formátumban:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Bár a formátuma nem felel meg a szükséges séma, Event Grid lehetővé teszi, hogy a mezők leképezése a sémát. Vagy az eredeti sémában is kap, az értékeket.

## <a name="create-custom-topic-with-mapped-fields"></a>Leképezett mezők az egyéni témakör létrehozása

Egy egyéni témakör létrehozásakor adja meg, hogyan képezhet le a mezők az eredeti esemény az event grid-séma. Három értékek testre szabhatja a leképezés használatával:

* A **bemeneti séma** érték határozza meg a séma. Az elérhető lehetőségek a következők: CloudEvents-séma, egyéni eseményt séma vagy Event Grid-sémát. Az alapértelmezett értéke az Event Grid-sémát. Egyéni leképezés a séma- és az event grid-séma közötti létrehozásakor használja az egyéni esemény sémája. Amikor események válnak a CloudEvents-séma, Cloudevents-séma használja.

* A **alapértelmezett értékek leképezése** tulajdonság határozza meg a mezők alapértelmezett értékeit, az Event Grid-sémában. Az alapértelmezett értékeket megadhatja a `subject`, `eventtype`, és `dataversion`. Jellemzően ezt a paramétert használni, amikor az egyéni séma nem tartalmaz egy olyan mező, ezek három mezőt valamelyikének felel meg. Például megadhatja a verzió értéke mindig **1.0**.

* A **mezők leképezése** értéket rendeli az event grid-séma a sémát a mezők. Szóközzel elválasztott kulcs/érték párok értékeket határozhat meg. A kulcs nevét az event grid mező nevét használja. Az érték használja a mező nevét. Használhatja a kulcsnevek `id`, `topic`, `eventtime`, `subject`, `eventtype`, és `dataversion`.

Az Azure CLI-vel egy egyéni témakör létrehozásához használja:

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

## <a name="subscribe-to-event-grid-topic"></a>Fizessen elő az event grid-témakör

Amikor feliratkozik az egyéni témakörre, adja meg a séma, az események fogadására használni szeretné. Megadhatja a CloudEvents-séma használata, egyéni eseményt séma vagy Event Grid-sémát. Az alapértelmezett értéke az Event Grid-sémát.

Az alábbi példa feliratkozik egy event grid-témakört, és az Event Grid-sémát használja. Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

A következő példában a bemeneti sémát az esemény:

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema inputeventschema \
  --endpoint <endpoint_URL>
```

Az alábbi példa feliratkozik egy event grid-témakört, és az Event Grid-sémát használja. PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

A következő példában a bemeneti sémát az esemény:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>A témakör esemény közzététele

Most már készen áll, küldünk egy eseményt az egyéni témakör és az eredmény a leképezés megjelenítéséhez. A következő szkriptet az esemény közzététele a [példa séma](#original-event-schema):

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

Most tekintse meg a WebHook-végpontot. A két előfizetés különböző sémákkal leszállítani eseményeket.

Az első előfizetés event grid séma használja. A szállított események formátuma:

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

Ezek a mezők a leképezéseket, az egyéni témakör tartalmaz. **myEventTypeField** le van képezve **EventType**. Az alapértelmezett értékei **DataVersion** és **tulajdonos** szolgálnak. A **adatok** objektum az eredeti esemény séma mezőket tartalmazza.

A második előfizetés használni a bemeneti eseményt séma. A szállított események formátuma:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": {
    "someDataField1": "SomeDataFieldValue"
  }
}
```

Figyelje meg, hogy az eredeti mezők kézbesítése.

## <a name="next-steps"></a>További lépések

* Eseménykézbesítés és újrapróbálkozás [Event Grid az üzenetek kézbesítését, és ismételje meg](delivery-and-retry.md).
* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Tekintse meg a gyors kezdéshez Event Grid használatával [az Azure Event Griddel egyéni események létrehozása és útvonal](custom-event-quickstart.md).

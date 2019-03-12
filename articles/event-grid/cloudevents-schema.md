---
title: CloudEvents-séma események Azure Event Grid használata
description: Ismerteti, hogyan lehet az Azure Event Grid-események CloudEvents-séma beállítása.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: babanisa
ms.openlocfilehash: c9c0fd8bf7dc893ce07a6e3ce9cb392fc652a560
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57535852"
---
# <a name="use-cloudevents-schema-with-event-grid"></a>CloudEvents-séma használata az Event GRID használatával

Mellett a [alapértelmezett eseménysémája](event-schema.md), az Azure Event Grid natív módon támogatja az események a [CloudEvents JSON-sémájában](https://github.com/cloudevents/spec/blob/master/json-format.md). [CloudEvents](https://cloudevents.io/) van egy [nyissa meg a specifikációnak](https://github.com/cloudevents/spec/blob/master/spec.md) leíró események adatait.

CloudEvents együttműködési leegyszerűsíti egy közös eseménysémája azáltal, hogy a közzétételre, és a felhőt események alapján. Ez a séma lehetővé teszi, hogy egységes azokat az eszközöket, normál módon az Útválasztás és események kezelése és a külső eseménysémája deszerializálása univerzális módjait. Egy közös sémával rendelkező további egyszerűen integrálható a munkahelyi különböző platformokon.

CloudEvents létrehozása folyamatban van a több [közreműködők](https://github.com/cloudevents/spec/blob/master/community/contributors.md), Microsoft, beleértve a [Felhőbeli natív számítási Foundation](https://www.cncf.io/). Jelenleg a 0,1 verzióként érhető el.

Ez a cikk ismerteti a CloudEvents-séma használata az Event GRID használatával.

## <a name="install-preview-feature"></a>Előzetes verziójú funkció telepítése

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>CloudEvent séma

Íme egy példa egy Azure Blob Storage esemény CloudEvents formátumban:

``` JSON
{
    "cloudEventsVersion" : "0.1",
    "eventType" : "Microsoft.Storage.BlobCreated",
    "eventTypeVersion" : "",
    "source" : "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}#blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "eventID" : "173d9985-401e-0075-2497-de268c06ff25",
    "eventTime" : "2018-04-28T02:18:47.1281675Z",
    "data" : {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
}
```

CloudEvents v0.1 érhető el a következő tulajdonságokkal rendelkezik:

| CloudEvents        | Typo     | Példa JSON-értéket             | Leírás                                                        | Event Grid-leképezés
|--------------------|----------|--------------------------------|--------------------------------------------------------------------|-------------------------
| eventType          | String   | "com.example.someevent"          | Előfordulás történt típusa                                   | eventType
| eventTypeVersion   | String   | "1.0"                            | Az esemény típusa (nem kötelező) verziója                            | dataVersion
| cloudEventsVersion | String   | "0.1"                            | Az esemény használ CloudEvents-specifikáció verziója        | *átadni*
| source             | URI      | "/ mycontext"                     | Az esemény-előállítót ismerteti                                       | topic#subject
| eventID            | String   | "1234-1234-1234"                 | Esemény azonosítója                                                    | id
| eventTime          | Időbélyeg| "2018-04-05T17:31:00Z"           | Időbélyeg, ha az esemény történt-e (nem kötelező)                    | eventTime
| schemaURL          | URI      | „https://myschema.com”           | A séma, az adatok attribútum betartó (nem kötelező) mutató hivatkozás | *nincs használatban*
| contentType        | String   | "application/json"               | Ismerteti az adatok kódolási formátum (nem kötelező)                       | *nincs használatban*
| Bővítmények         | Térkép      | { "extA": "vA", "extB", "vB" }  | Minden további metaadatok (nem kötelező)                                 | *nincs használatban*
| adat               | Objektum   | { "objA": "vA", "objB", "vB" }  | Az eseménytartalom (nem kötelező)                                       | adat

További információkért lásd: a [CloudEvents specifikációja](https://github.com/cloudevents/spec/blob/master/spec.md#context-attributes).

A fejlécek CloudEvents-séma és az Event Grid-séma szállított események értékei azonos kivételével, `content-type`. CloudEvents-séma, hogy a fejléc értékét kötelező `"content-type":"application/cloudevents+json; charset=utf-8"`. Event Grid-séma, hogy a fejléc értékét kötelező `"content-type":"application/json; charset=utf-8"`.

## <a name="configure-event-grid-for-cloudevents"></a>Event Grid CloudEvents konfigurálása

Event Grid bemeneti és kimeneti események CloudEvents-séma is használható. CloudEvents rendszer események, például a Blob Storage-események és az IoT Hub-események és az egyéni eseményeket is használhat. Ez a hálózati oda-vissza események át is alakíthatja.


| A bemeneti séma       | Kimeneti sémája
|--------------------|---------------------
| CloudEvents-formátum | CloudEvents-formátum
| Event Grid-formátum  | CloudEvents-formátum
| CloudEvents-formátum | Event Grid-formátum
| Event Grid-formátum  | Event Grid-formátum

Az összes Eseménysémák Event Grid érvényesítést egy event grid-témakör való közzétételkor és a egy esemény-előfizetés létrehozásakor. További információkért lásd: [Event Grid biztonsági és hitelesítési](security-authentication.md).

### <a name="input-schema"></a>A bemeneti séma

Az egyéni témakör létrehozásakor beállíthatja egy egyéni témakör a bemeneti sémát.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventv01schema
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
  -InputSchema CloudEventV01Schema
```

CloudEvents jelenlegi verziója nem támogatja az események kötegelés. CloudEvent sémával események közzétételére egy témakört, egyenként tegye közzé minden esemény.

### <a name="output-schema"></a>Kimeneti sémája

Az esemény-előfizetés létrehozásakor megadhatja a kimeneti sémával.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventv01schema
```

PowerShell esetén használja az alábbi parancsot:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventV01Schema
```

A CloudEvents jelenlegi verziója nem támogatja az események kötegelés. Egy esemény-előfizetést, amely konfigurálva van a CloudEvent séma külön-külön mindegyik eseményt kap. Jelenleg nem használható egy Event Grid-trigger egy Azure Functions-alkalmazás, amikor az esemény továbbítsa a CloudEvents-séma. HTTP-trigger használja. HTTP-trigger, amely fogadja az eseményeket a CloudEvents-séma végrehajtási példákért lásd [HTTP-trigger használja, mint az Event Grid-trigger](../azure-functions/functions-bindings-event-grid.md#use-an-http-trigger-as-an-event-grid-trigger).

## <a name="next-steps"></a>További lépések

* Esemény kézbesítések figyelésével kapcsolatos további információkért lásd: [figyelő Event Grid üzenetkézbesítése](monitor-event-delivery.md).
* Javasoljuk, hogy tesztelje, Megjegyzés, és [közreműködés](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) CloudEvents való.
* Az Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid-előfizetés séma](subscription-creation-schema.md).

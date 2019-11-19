---
title: Azure Event Grid használata a CloudEvents-sémában lévő eseményekkel
description: Leírja, hogyan kell beállítani a CloudEvents sémát a Azure Event Grid eseményeihez.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: babanisa
ms.openlocfilehash: 8925110511f6c63a42dd9b121429ac7264cd4aa4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170246"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>CloudEvents v 1.0 séma használata Event Grid

Az [alapértelmezett esemény sémáján](event-schema.md)kívül Azure Event Grid natív módon támogatja a [CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) és a [http protokoll kötésének](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md)JSON-implementációjában lévő eseményeket. A [CloudEvents](https://cloudevents.io/) egy [nyílt specifikáció](https://github.com/cloudevents/spec/blob/v1.0/spec.md) az események leírásához.

A CloudEvents a felhőalapú események közzétételére és felhasználására szolgáló közös esemény sémájának használatával egyszerűsíti az együttműködési képességet. Ez a séma lehetővé teszi, hogy egységes eszközöket biztosítson, és szabványos módon irányítsa át & az események kezelését, valamint a külső esemény sémájának deszerializálásának univerzális módszereit. Közös sémával könnyebben integrálhatja a munkafolyamatokat a különböző platformokon.

A CloudEvents számos [közreműködő](https://github.com/cloudevents/spec/blob/master/community/contributors.md), például a Microsoft, a [Felhőbeli natív számítástechnikai alaprendszer](https://www.cncf.io/)segítségével építhető ki. Jelenleg 1,0-as verzióként érhető el.

Ez a cikk azt ismerteti, hogyan használható a CloudEvents séma a Event Grid használatával.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Előzetes verziójú funkció telepítése

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

[Itt](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)található az elérhető mezők, azok típusai és definíciói részletes leírása a CloudEvents v 0.1-ben.

A CloudEvents-sémában és a Event Grid sémában szállított események fejlécének értékei megegyeznek a `content-type`kivételével. A CloudEvents séma esetében a fejléc értéke `"content-type":"application/cloudevents+json; charset=utf-8"`. Event Grid séma esetében a fejléc értéke `"content-type":"application/json; charset=utf-8"`.

## <a name="configure-event-grid-for-cloudevents"></a>A CloudEvents Event Grid konfigurálása

A CloudEvents-sémában lévő események bemenetéhez és kimenetéhez Event Grid is használhatja. A CloudEvents-t használhatja rendszereseményekhez, például Blob Storage eseményekhez és IoT Hub eseményekhez és egyéni eseményekhez. Emellett átalakíthatja ezeket az eseményeket a huzalon oda-vissza.


| Bemeneti séma       | Kimeneti séma
|--------------------|---------------------
| CloudEvents formátuma | CloudEvents formátuma
| Event Grid formátum  | CloudEvents formátuma
| CloudEvents formátuma | Event Grid formátum
| Event Grid formátum  | Event Grid formátum

Az összes esemény sémája esetében a Event Grid érvényesítést igényel az Event Grid-témakörre való közzétételkor és az esemény-előfizetés létrehozásakor. További információkért lásd: [Event Grid biztonsági és hitelesítési](security-authentication.md).

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

A CloudEvents jelenlegi verziója nem támogatja az események kötegelt feldolgozását. Ha CloudEvent-sémával rendelkező eseményeket szeretne közzétenni egy témakörben, tegye közzé az egyes eseményeket egyenként.

### <a name="output-schema"></a>Kimeneti séma

A kimeneti séma az esemény-előfizetés létrehozásakor állítható be.

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

 Jelenleg nem használhat Event Grid eseményindítót egy Azure Functions alkalmazáshoz, ha az esemény a CloudEvents-sémában kerül kézbesítésre. HTTP-trigger használata. A CloudEvents sémában eseményeket fogadó HTTP-triggerek megvalósítására példákat a [http-trigger használata Event Grid triggerként](../azure-functions/functions-bindings-event-grid.md#use-an-http-trigger-as-an-event-grid-trigger)című témakörben talál.

## <a name="next-steps"></a>Következő lépések

* További információ az események kézbesítésének figyeléséről: [Event Grid üzenet kézbesítésének figyelése](monitor-event-delivery.md).
* Javasoljuk, hogy tesztelje, véleményezze és [járuljon hozzá](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) a CloudEvents.
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).

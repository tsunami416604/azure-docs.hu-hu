---
title: Az Azure Event Grid előfizetési séma
description: Ez a cikk a Azure Event Gridval való eseményre való feliratkozás tulajdonságait ismerteti. Event Grid előfizetési séma.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: reference
ms.date: 01/23/2020
ms.author: babanisa
ms.openlocfilehash: 4bb04d22b762f31a02515549b698030a5267e4cd
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720758"
---
# <a name="event-grid-subscription-schema"></a>Event Grid előfizetési séma

Event Grid-előfizetés létrehozásához, egy kérelmet küld az esemény létrehozása az előfizetési műveletre. Használja a következő formátumot:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Ha például egy `examplegroup`nevű erőforráscsoporthoz `examplestorage` nevű Storage-fiókhoz szeretne esemény-előfizetést létrehozni, használja a következő formátumot:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Az Eseményelőfizetés neve 3 – 64 karakter hosszúságúnak kell lennie, és tartalmazhat a – z, A-Z, 0 – 9 és "-". A cikk ismerteti a tulajdonságok és a kérelem törzsében sémáját.
 
## <a name="event-subscription-properties"></a>Esemény-előfizetés tulajdonságai

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| cél | objektum | Az objektum, amely meghatározza a végpontot. |
| szűrő | objektum | Eseménytípusok szűrése a választható mező. |

### <a name="destination-object"></a>Célobjektum

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| endpointType | sztring | Az előfizetés (a HTTP/webhook, Event Hub vagy üzenetsor) végpont típusa. | 
| endpointUrl | sztring | Az Eseményelőfizetés események cél URL-CÍMÉT. | 

### <a name="filter-object"></a>szűrő objektum

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| includedEventTypes | tömb | Egyezés, ha az esemény típusa az eseményüzenetben pontos egyezés az egyik ezek az események típus neve. Hibát jelez, amikor az esemény neve nem egyezik meg a regisztrált esemény típusa az eseményforrás nevét. Alapértelmezett illeszkedik az összes eseménytípust. |
| subjectBeginsWith | sztring | Előtag-egyezés szűrheti a tulajdonos mezőben az üzenetet. Az alapértelmezett vagy üres karakterlánc megfelel. | 
| subjectEndsWith | sztring | Utótag-egyezés szűrheti a tulajdonos mezőben az üzenetet. Az alapértelmezett vagy üres karakterlánc megfelel. |
| isSubjectCaseSensitive | sztring | Kis-és nagybetűket szűrőknek megfelelő szabályozza. |


## <a name="example-subscription-schema"></a>Példa előfizetési séma

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Következő lépések

* A Event Grid bemutatása: [Mi az Event Grid?](overview.md)

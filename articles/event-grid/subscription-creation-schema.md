---
title: Azure Event Grid-előfizetésséma
description: Ez a cikk ismerteti a tulajdonságok egy eseményre az Azure Event Grid használatával. Event Grid-előfizetésséma.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: reference
ms.date: 01/23/2020
ms.author: babanisa
ms.openlocfilehash: 4bb04d22b762f31a02515549b698030a5267e4cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720758"
---
# <a name="event-grid-subscription-schema"></a>Event Grid-előfizetésséma

Event Grid-előfizetés létrehozásához küld egy kérelmet az Esemény-előfizetés létrehozása műveletnek. Használja az alábbi formátumot:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Ha például egy nevű erőforráscsoportban `examplestorage` nevű tárfiókhoz `examplegroup`szeretne esemény-előfizetést létrehozni, használja a következő formátumot:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Az esemény-előfizetés nevének 3-64 karakter hosszúságúnak kell lennie, és csak a-z, A-Z, 0-9 és "-" karaktereket tartalmazhat. A cikk ismerteti a tulajdonságok és a séma a törzs a kérelem.
 
## <a name="event-subscription-properties"></a>Esemény-előfizetés tulajdonságai

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| destination | objektum | A végpontot meghatározó objektum. |
| filter | objektum | Választható mező az eseménytípusok szűréséhez. |

### <a name="destination-object"></a>célobjektum

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| végponttípusa | sztring | Az előfizetés végpontjának típusa (webhook/HTTP, Event Hub vagy várólista). | 
| endpointUrl | sztring | Az esemény-előfizetés eseményeinek cél URL-címe. | 

### <a name="filter-object"></a>szűrőobjektum

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| includedEventTypes | tömb | Egyezzen meg, ha az eseményüzenetben szereplő eseménytípus pontosan megegyezik az eseménytípus nevek egyikével. Hibát jelez, ha az esemény neve nem egyezik meg az eseményforrás regisztrált eseménytípusnevével. Az alapértelmezett érték minden eseménytípusnak megfelel. |
| subjectBeginsWith | sztring | Előtag-egyezésszűrő az eseményüzenet tárgymezőjéhez. Az alapértelmezett vagy üres karakterlánc mindegyiknek megfelel. | 
| subjectEndsWith | sztring | Utótag-egyezésszűrő az eseményüzenet tárgymezőjéhez. Az alapértelmezett vagy üres karakterlánc mindegyiknek megfelel. |
| isSubjectCaseSensitive | sztring | Szabályozza a kis- és nagybetűk megkülönböztetését a szűrők esetében. |


## <a name="example-subscription-schema"></a>Példa előfizetési sémára

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

## <a name="next-steps"></a>További lépések

* Az Event Grid bemutatása a [Mi az eseményrács?](overview.md)

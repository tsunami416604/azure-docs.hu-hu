---
title: Az Azure Event rács előfizetés séma
description: Az előfizetés a Azure esemény rács esemény tulajdonságait ismerteti.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 05/02/2018
ms.author: babanisa
ms.openlocfilehash: 406eb2c1974958eef5e83915e6b21e385cf7d2c7
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="event-grid-subscription-schema"></a>Esemény rács előfizetés séma

Esemény rács előfizetéssel, hozzon létre, az esemény létrehozása előfizetés működéséhez kérést kell küldenie. Használja a következő formátumot:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Például egy esemény-előfizetést a tárfiók létrehozásához nevű `examplestorage` erőforráscsoportban nevű `examplegroup`, használja a következő formátumot:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

A cikk ismerteti a tulajdonságok és a kérelem törzse sémáját.
 
## <a name="event-subscription-properties"></a>Az esemény-előfizetés tulajdonságai

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| Cél | objektum | Az objektum, amely meghatározza a végpont. |
| szűrő | objektum | Milyen típusú eseményeket szűréshez választható mező. |

### <a name="destination-object"></a>Célobjektum

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| endpointType | karakterlánc | Az előfizetés (webhook vagy HTTP, az Event Hubs vagy várólista) végpont típusa. | 
| endpointUrl | karakterlánc | Ez az esemény előfizetés események cél URL-CÍMÉT. | 

### <a name="filter-object"></a>szűrő objektum

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| includedEventTypes | tömb | Egyezés, ha az esemény típusát az eseményüzenet pontos egyezést e esemény típusnevek egyikére. Riasztást a hiba, ha az esemény neve nem egyezik meg a következő eseményforrás regisztrált esemény típusneve. Alapértelmezett megfelel az összes eseménytípust. |
| subjectBeginsWith | karakterlánc | Előtag-egyezés a mezőben az üzenet szűrésére. Az alapértelmezett vagy üres karakterlánc megfelel. | 
| subjectEndsWith | karakterlánc | Utótag-egyezés a mezőben az üzenet szűrésére. Az alapértelmezett vagy üres karakterlánc megfelel. |
| isSubjectCaseSensitive | karakterlánc | Kis-és nagybetűket szűrőknek megfelelő vezérlőket. |


## <a name="example-subscription-schema"></a>Példa előfizetés séma

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
      "subjectBeginsWith": "blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>További lépések

* Esemény rácshoz ismertetőért lásd: [Mi az az esemény rács?](overview.md)
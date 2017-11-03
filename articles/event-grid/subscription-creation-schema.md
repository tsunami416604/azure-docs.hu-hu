---
title: "Az Azure Event rács előfizetés séma"
description: "Az előfizetés a Azure esemény rács esemény tulajdonságait ismerteti."
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 08/17/2017
ms.author: babanisa
ms.openlocfilehash: eff2352066a76010d6d882a7b7e1961870cd2d46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="event-grid-subscription-schema"></a>Esemény rács előfizetés séma

Esemény rács előfizetéssel, hozzon létre, az esemény létrehozása előfizetés működéséhez kérést kell küldenie. Használja a következő formátumot:

```
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2017-06-15-preview
``` 

Például egy esemény-előfizetést a tárfiók létrehozásához nevű `examplestorage` erőforráscsoportban nevű `examplegroup`, használja a következő formátumot:

```
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2017-06-15-preview
``` 

A cikk ismerteti a tulajdonságok és a kérelem törzse sémáját.
 
## <a name="event-subscription-properties"></a>Az esemény-előfizetés tulajdonságai

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| Cél | Objektum | Az objektum, amely meghatározza a végpont. |
| Szűrő | Objektum | Milyen típusú eseményeket szűréshez választható mező. |

### <a name="destination-object"></a>Célobjektum

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| endpointType | Karakterlánc | Az előfizetés (webhook vagy HTTP, az Event Hubs vagy várólista) végpont típusa. | 
| végponti URL-cím | Karakterlánc |  | 

### <a name="filter-object"></a>szűrő objektum

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| includedEventTypes | A tömb | Egyezés, ha az esemény típusát az eseményüzenet pontos egyezést e esemény típusnevek egyikére. Riasztást a hiba, ha az esemény neve nem egyezik meg a következő eseményforrás regisztrált esemény típusneve. Alapértelmezett megfelel az összes eseménytípust. |
| subjectBeginsWith | Karakterlánc | Előtag-egyezés a mezőben az üzenet szűrésére. Az alapértelmezett vagy üres karakterlánc megfelel. | 
| subjectEndsWith | Karakterlánc | Utótag-egyezés a mezőben az üzenet szűrésére. Az alapértelmezett vagy üres karakterlánc megfelel. |
| subjectIsCaseSensitive | Karakterlánc | Kis-és nagybetűket szűrőknek megfelelő vezérlőket. |


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
      "includedEventTypes": [ "blobCreated", "blobDeleted" ],
      "subjectBeginsWith": "blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "subjectIsCaseSensitive": "true"
    }
  }
}
```

## <a name="next-steps"></a>Következő lépések

* Esemény rácshoz ismertetőért lásd: [Mi az az esemény rács?](overview.md)
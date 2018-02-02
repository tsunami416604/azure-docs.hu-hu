---
title: "Az Azure Event rács előfizetés séma"
description: "Az előfizetés a Azure esemény rács esemény tulajdonságait ismerteti."
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: babanisa
ms.openlocfilehash: a915473c67a7577582837b56d1a9ccec4d21c461
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="event-grid-subscription-schema"></a>Esemény rács előfizetés séma

Esemény rács előfizetéssel, hozzon létre, az esemény létrehozása előfizetés működéséhez kérést kell küldenie. Használja a következő formátumot:

```
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Például egy esemény-előfizetést a tárfiók létrehozásához nevű `examplestorage` erőforráscsoportban nevű `examplegroup`, használja a következő formátumot:

```
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
| subjectIsCaseSensitive | karakterlánc | Kis-és nagybetűket szűrőknek megfelelő vezérlőket. |


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

## <a name="next-steps"></a>További lépések

* Esemény rácshoz ismertetőért lásd: [Mi az az esemény rács?](overview.md)
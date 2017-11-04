---
title: "Az Azure Event rács esemény séma"
description: "Azure Event rácshoz események tartozó tulajdonságait ismerteti"
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 10/20/2017
ms.author: babanisa
ms.openlocfilehash: e251cbfe7c4d8dfbd492817a8fa7af48e6b379df
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2017
---
# <a name="azure-event-grid-event-schema"></a>Az Azure Event rács esemény séma

A cikkben a tulajdonságok és a séma események. Események állnak olyan öt szükséges kapcsolatikarakterlánc-tulajdonságokat, és egy szükséges objektumot. A Tulajdonságok megegyeznek az összes esemény bármely közzétevőtől. Az adatobjektum mindegyik közzétevő jellemző tulajdonságok tartalmazza. A rendszer témaköröket ezeket a tulajdonságokat vonatkoznak, például az Azure Storage vagy az Azure Event Hubs az erőforrás-szolgáltató.

Az események küldhetők Azure esemény rács tömbben, amely több esemény-objektumot is tartalmazhat. Ha csak egyetlen olyan esemény, a tömb hossza 1. A tömb egy teljes mérete legfeljebb 1 MB lehet. A tömb minden esemény értéke legfeljebb 64 KB.
 
## <a name="event-properties"></a>Esemény tulajdonságai

Összes esemény azonos következő legfelső szintű adatokat tartalmazzák:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| A témakör | Karakterlánc | A forrás teljes erőforrás elérési útja. Ez a mező nincs írható. |
| Tulajdonos | Karakterlánc | Az esemény tulajdonos közzétevő által megadott elérési útja. |
| Esemény típusa | Karakterlánc | Az esemény adatforrás regisztrált esemény típusok egyike. |
| eventTime | Karakterlánc | Az esemény jön létre az idő alapján a szolgáltató UTC idő szerint. |
| id | Karakterlánc | Az esemény egyedi azonosítója. |
| Adatok | Objektum | Eseményadatok jellemző az erőforrás-szolgáltató. |

## <a name="available-event-sources"></a>Rendelkezésre álló Eseményforrások

A következő eseményforrások közzé az eseményeket a felhasználásához esemény rács keresztül:

* Erőforráscsoportok (műveletek)
* Azure-előfizetések (műveletek)
* Az Event hubs
* Egyéni kapcsolatos témakörök

## <a name="azure-subscriptions"></a>Azure-előfizetések

Azure-előfizetések is most hozható létre az Azure erőforrás-kezelő felügyeleti események például a virtuális gép létrehozásakor, vagy a tárfiók törlődik.

### <a name="available-event-types"></a>Rendelkezésre álló eseményoszlopok típusok

- **Microsoft.Resources.ResourceWriteSuccess**: jelenik meg, ha egy erőforrás létrehozása vagy frissítése a művelet sikeres lesz.  
- **Microsoft.Resources.ResourceWriteFailure**: következik be, amikor az erőforrás létrehozása vagy frissítési művelet sikertelen lesz.  
- **Microsoft.Resources.ResourceWriteCancel**: jelenik meg, ha egy erőforrás létrehozása vagy frissítése a művelet megszakadt.  
- **Microsoft.Resources.ResourceDeleteSuccess**: jelenik meg, ha egy erőforrás-törlési művelet sikeres lesz.  
- **Microsoft.Resources.ResourceDeleteFailure**: jelenik meg, ha egy erőforrás-törlési művelet sikertelen lesz.  
- **Microsoft.Resources.ResourceDeleteCancel**: jelenik meg, ha egy erőforrás-törlési művelet megszakadt. Ez akkor fordul elő, amikor megszűnik, egy sablon-üzembehelyezés.

### <a name="example-event-schema"></a>Példa esemény séma

```json
[
    {
    "topic":"/subscriptions/{subscription-id}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```



## <a name="resource-groups"></a>Erőforráscsoportok

Erőforráscsoportok is most hozható létre az Azure erőforrás-kezelő felügyeleti események például a virtuális gép létrehozásakor, vagy a tárfiók törlődik.

### <a name="available-event-types"></a>Rendelkezésre álló eseményoszlopok típusok

- **Microsoft.Resources.ResourceWriteSuccess**: jelenik meg, ha egy erőforrás létrehozása vagy frissítése a művelet sikeres lesz.  
- **Microsoft.Resources.ResourceWriteFailure**: következik be, amikor az erőforrás létrehozása vagy frissítési művelet sikertelen lesz.  
- **Microsoft.Resources.ResourceWriteCancel**: jelenik meg, ha egy erőforrás létrehozása vagy frissítése a művelet megszakadt.  
- **Microsoft.Resources.ResourceDeleteSuccess**: jelenik meg, ha egy erőforrás-törlési művelet sikeres lesz.  
- **Microsoft.Resources.ResourceDeleteFailure**: jelenik meg, ha egy erőforrás-törlési művelet sikertelen lesz.  
- **Microsoft.Resources.ResourceDeleteCancel**: jelenik meg, ha egy erőforrás-törlési művelet megszakadt. Ez akkor fordul elő, amikor megszűnik, egy sablon-üzembehelyezés.

### <a name="example-event"></a>Példa esemény

```json
[
    {
    "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```



## <a name="event-hubs"></a>Event Hubs

Csak akkor, ha a fájl automatikusan elküld a rögzítése funkció keresztül tárolási jelenleg kibocsátott Hubs eseményeinek.

### <a name="available-event-types"></a>Rendelkezésre álló eseményoszlopok típusok

- **Microsoft.EventHub.CaptureFileCreated**: következik be, amikor egy rögzítési fájl jön létre.

### <a name="example-event"></a>Példa esemény

Ez a minta az esemény jelenik meg, ha a rögzítési szolgáltatás tárolja a fájlt az Event Hubs esemény sémája jeleníti meg: 

```json
[
    {
        "topic": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{event-hubs-ns}",
        "subject": "eventhubs/eh1",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-07-11T00:55:55.0120485Z",
        "id": "bd440490-a65e-4c97-8298-ef1eb325673c",
        "data": {
            "fileUrl": "https://gridtest1.blob.core.windows.net/acontainer/eventgridtest1/eh1/1/2017/07/11/00/54/54.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 0,
            "eventCount": 0,
            "firstSequenceNumber": -1,
            "lastSequenceNumber": -1,
            "firstEnqueueTime": "0001-01-01T00:00:00",
            "lastEnqueueTime": "0001-01-01T00:00:00"
        },
    }
]

```


## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="available-event-types"></a>Rendelkezésre álló eseményoszlopok típusok

- **Microsoft.Storage.BlobCreated**: blob létrehozásakor következik be.
- **Microsoft.Storage.BlobDeleted**: blob törlésekor következik be.

### <a name="example-event"></a>Példa esemény

Ez a minta az esemény következik be, amikor létrejön egy blob storage esemény séma jeleníti meg: 

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
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
]
```




## <a name="custom-topics"></a>Egyéni kapcsolatos témakörök

Az egyéni események adatainak hasznos az Ön által megadott, és lehet bármely jól formázott JSON-objektum. A legfelső szintű adatok szabványos erőforrás által definiált eseményként is ugyanazokat a mezőket kell tartalmaznia. Események közzététele egyéni témakörökre mutatnak, gondolja át az események tárgya útválasztási és szűrés modellezési.

### <a name="example-event"></a>Példa esemény

A következő példa bemutatja egy egyéni témakör esemény:
````json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.EventGrid/topics/myeventgridtopic",
    "subject": "/myapp/vehicles/motorcycles",    
    "id": "b68529f3-68cd-4744-baa4-3c0498ec19e2",
    "eventType": "recordInserted",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "data":{
      "make": "Ducati",
      "model": "Monster"
    }
  }
]

````

## <a name="next-steps"></a>Következő lépések

* Megismerkedhet az Azure Event rács, lásd: [Mi az az esemény rács?](overview.md).
* Egy esemény rács Azure-előfizetés létrehozásával kapcsolatos további információkért lásd: [esemény rács előfizetés séma](subscription-creation-schema.md).

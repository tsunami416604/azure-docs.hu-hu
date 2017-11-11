---
title: "Az Azure Event rács esemény séma"
description: "Azure Event rácshoz események tartozó tulajdonságait ismerteti"
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/07/2017
ms.author: babanisa
ms.openlocfilehash: caa709fdc2a59472ee812bde91f7300396aa5755
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema"></a>Az Azure Event rács esemény séma

Ez a cikk ismerteti a tulajdonságok és a sémában, amely az összes esemény találhatók. Események állnak olyan öt szükséges kapcsolatikarakterlánc-tulajdonságokat, és egy szükséges objektumot. A Tulajdonságok megegyeznek az összes esemény bármely közzétevőtől. Az adatobjektum mindegyik közzétevő jellemző tulajdonságok tartalmazza. A rendszer témaköröket ezeket a tulajdonságokat vonatkoznak, például az Azure Storage vagy az Azure Event Hubs az erőforrás-szolgáltató.

Az események küldhetők Azure esemény rács tömbben, amely több esemény-objektumot is tartalmazhat. Ha csak egyetlen olyan esemény, a tömb hossza 1. A tömb egy teljes mérete legfeljebb 1 MB lehet. A tömb minden esemény értéke legfeljebb 64 KB.

## <a name="event-schema"></a>Eseményséma

A következő példa bemutatja az összes esemény-közzétevők által használt tulajdonságokat:

```json
[
  {
    "topic": string,
    "subject": string,    
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    }
  }
]
```

Például az Azure Blob storage esemény közzé séma van:

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
 
## <a name="event-properties"></a>Esemény tulajdonságai

Összes esemény a következő azonos legfelső szintű adatokat tartalmazzák:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| A témakör | Karakterlánc | A forrás teljes erőforrás elérési útja. Ez a mező nincs írható. |
| Tulajdonos | Karakterlánc | Az esemény tulajdonos közzétevő által megadott elérési útja. |
| Esemény típusa | Karakterlánc | Az esemény adatforrás regisztrált esemény típusok egyike. |
| eventTime | Karakterlánc | Az esemény jön létre az idő alapján a szolgáltató UTC idő szerint. |
| id | Karakterlánc | Az esemény egyedi azonosítója. |
| Adatok | Objektum | Eseményadatok jellemző az erőforrás-szolgáltató. |

Az objektum tulajdonságait, lásd: a következő Eseményforrás:

* [Azure-előfizetések (műveletek)](event-schema-subscriptions.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Az Event hubs](event-schema-event-hubs.md)
* [Erőforráscsoportok (műveletek)](event-schema-resource-groups.md)

Az esemény-közzétevő egyéni témakörök határozza meg, az objektum. A legfelső szintű adatok szabványos erőforrás által definiált eseményként is ugyanazokat a mezőket kell tartalmaznia. Események közzététele egyéni témakörökre mutatnak, gondolja át az események tárgya útválasztási és szűrés modellezési.

## <a name="next-steps"></a>Következő lépések

* Megismerkedhet az Azure Event rács, lásd: [Mi az az esemény rács?](overview.md)
* Egy esemény rács Azure-előfizetés létrehozásával kapcsolatos további információkért lásd: [esemény rács előfizetés séma](subscription-creation-schema.md).

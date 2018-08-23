---
title: Az Azure Event Grid event hubs eseménysémája
description: Az event hubs-események az Azure Event Griddel biztosított tulajdonságait ismerteti
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: reference
ms.date: 08/17/2018
ms.author: tomfitz
ms.openlocfilehash: e301f3895126ed52b8d4c1f046f69dfcedb3563c
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42060167"
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Az event hubs az Azure Event Grid eseménysémája

Ez a cikk a tulajdonságok és a séma event hubs-események. Eseménysémák szeretné megismerni, lásd: [Azure Event Grid-esemény séma](event-schema.md).

Mintaszkriptek és oktatóanyagok listáját lásd: [az Event Hubs eseményforrás](event-sources.md#event-hubs).

### <a name="available-event-types"></a>Rendelkezésre álló események típusai

Az Event Hubs bocsát ki a **Microsoft.EventHub.CaptureFileCreated** eseménytípus rögzítési fájl létrehozásakor.

## <a name="example-event"></a>Példa esemény

A minta esemény sémáját, az event hubs esemény következik be, amikor a capture funkciójának tárolja egy fájlt mutatja be: 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        },
        "dataVersion": "",
        "metadataVersion": "1"
    }
]
```

## <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény a következő legfelső szintű adatokat tartalmaz:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | A forrás teljes erőforrás elérési útja. Ez a mező nem írható. Event Grid biztosítja ezt az értéket. |
| Tulajdonos | sztring | Az esemény tárgya közzétevő által megadott elérési útja. |
| eventType | sztring | Ehhez eseményre adatforráshoz regisztrált esemény típusok egyikét. |
| eventTime | sztring | Az esemény akkor jön létre az idő alapján a szolgáltató UTC idő. |
| id | sztring | Az esemény egyedi azonosítója. |
| adatok | objektum | Event hub eseményadatokat. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | sztring | Az esemény-metaadatok sémaverziója. Event Grid sémáját, a legfelső szintű tulajdonságait határozza meg. Event Grid biztosítja ezt az értéket. |

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| fileUrl | sztring | A capture-fájl elérési útja. |
| Fájltípus | sztring | A rögzítési fájl fájl típusa. |
| partitionId | sztring | A szegmens azonosítóját. |
| sizeInBytes | egész szám | A fájl mérete. |
| eventCount | egész szám | A fájl az események száma. |
| firstSequenceNumber | egész szám | A legkisebb sorszáma az üzenetsorból. |
| lastSequenceNumber | egész szám | Az utolsó számát az üzenetsorból. |
| firstEnqueueTime | sztring | Az első alkalommal az üzenetsorból. |
| lastEnqueueTime | sztring | A legutóbbi alkalommal az üzenetsorból. |

## <a name="next-steps"></a>További lépések

* Azure Event Grid bemutatása, lásd: [Mi az Event Grid?](overview.md)
* Az Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid-előfizetés séma](subscription-creation-schema.md).
* További információ az event hubs események kezelése: [Stream big Data típusú adatok egy data warehouse-bA](event-grid-event-hubs-integration.md).
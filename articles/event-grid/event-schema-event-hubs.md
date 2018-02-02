---
title: "Az Azure esemény rács event hubs esemény séma"
description: "Az event hubs eseményeit Azure esemény rács biztosított tulajdonságait ismerteti"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 9fdc8816d8db88d4f1fd7b6ce722b7d2763eeaeb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Az event hubs Azure esemény rács esemény séma

A cikkben a tulajdonságok és a séma event hubs események. Egy esemény sémák bemutatása, lásd: [Azure esemény rács esemény séma](event-schema.md).

### <a name="available-event-types"></a>Rendelkezésre álló eseményoszlopok típusok

Az Event Hubs bocsát ki a **Microsoft.EventHub.CaptureFileCreated** eseménytípus rögzítési fájl létrehozásakor.

## <a name="example-event"></a>Példa esemény

Ez a minta az esemény jelenik meg, ha a rögzítési szolgáltatás tárolja a fájlt event hubs esemény sémája jeleníti meg: 

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

Az esemény a következő legfelső szintű adatokat tartalmaz:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| A témakör | karakterlánc | A forrás teljes erőforrás elérési útja. Ez a mező nincs írható. Esemény rács biztosítja ezt az értéket. |
| Tulajdonos | karakterlánc | Az esemény tulajdonos közzétevő által megadott elérési útja. |
| eventType | karakterlánc | Az esemény adatforrás regisztrált esemény típusok egyike. |
| eventTime | karakterlánc | Az esemény jön létre az idő alapján a szolgáltató UTC idő szerint. |
| id | karakterlánc | Az esemény egyedi azonosítója. |
| adat | objektum | Event hub eseményadatok. |
| dataVersion | karakterlánc | Az objektum séma verziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | karakterlánc | Az esemény-metaadatok séma verziója. Esemény rács a séma legfelső szintű tulajdonság határozza meg. Esemény rács biztosítja ezt az értéket. |

Az objektum tulajdonságai a következők:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| fileUrl | karakterlánc | A rögzítési fájl elérési útja. |
| fileType | karakterlánc | A rögzítési fájl típusú fájl. |
| partitionId | karakterlánc | A szilánkok azonosítóját. |
| sizeInBytes | integer | A fájl mérete. |
| eventCount | integer | A fájl események száma. |
| firstSequenceNumber | integer | A legkisebb sorszám az üzenetsorból. |
| lastSequenceNumber | integer | A várólista utolsó számát. |
| firstEnqueueTime | karakterlánc | Először az üzenetsorból. |
| lastEnqueueTime | karakterlánc | A várólista utolsó időpontját. |

## <a name="next-steps"></a>További lépések

* Megismerkedhet az Azure Event rács, lásd: [Mi az az esemény rács?](overview.md)
* Egy esemény rács Azure-előfizetés létrehozásával kapcsolatos további információkért lásd: [esemény rács előfizetés séma](subscription-creation-schema.md).
* További információ az event hubs események kezelésére: [big Data típusú adatok adatfolyamként történő adatraktár](event-grid-event-hubs-integration.md).
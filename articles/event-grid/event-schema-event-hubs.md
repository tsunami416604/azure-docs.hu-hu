---
title: Azure-Event Hubs Event Grid forrásként
description: Az Event hubok eseményeihez megadott tulajdonságokat ismerteti Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: fd65c20f07a091fa1fc8a6cbf003986e1096ebe3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81393341"
---
# <a name="azure-event-hubs-as-an-event-grid-source"></a>Azure-Event Hubs Event Grid forrásként

Ez a cikk az Event hub eseményeinek tulajdonságait és sémáját ismerteti.Az események sémáinak bemutatása: [Azure Event Grid Event Schema](event-schema.md).

## <a name="event-grid-event-schema"></a>Event Grid-eseményséma

### <a name="available-event-types"></a>Elérhető események típusai

A Event Hubs rögzíti a **Microsoft. EventHub. CaptureFileCreated** esemény típusát a rögzítési fájl létrehozásakor.

### <a name="example-event"></a>Példa eseményre

Ez a példa egy Event hub-esemény sémáját jeleníti meg, amikor a rögzítési funkció egy fájlt tárol: 

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

### <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| témakör | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| tulajdonos | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| eventType | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| eventTime | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| id | sztring | Az esemény egyedi azonosítója. |
| adatok | objektum | Event hub-események adatvédelme. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| fileUrl | sztring | A rögzítési fájl elérési útja. |
| fileType | sztring | A rögzítési fájl fájltípusa. |
| partitionId | sztring | A szegmens azonosítója. |
| sizeInBytes | egész szám | A fájl mérete. |
| eventCount | egész szám | A fájlban lévő események száma. |
| firstSequenceNumber | egész szám | A várólista legkisebb sorszáma. |
| lastSequenceNumber | egész szám | A várólista utolsó sorszáma. |
| firstEnqueueTime | sztring | A várólista első időpontja. |
| lastEnqueueTime | sztring | A várólista utolsó időpontja. |

## <a name="tutorials-and-how-tos"></a>Oktatóanyagok és útmutatók

|Cím  |Leírás  |
|---------|---------|
| [Oktatóanyag: stream big data adattárházba](event-grid-event-hubs-integration.md) | Amikor Event Hubs létrehoz egy rögzítési fájlt, Event Grid küld egy eseményt egy Function alkalmazásnak. Az alkalmazás lekéri a rögzítési fájlt, és áttelepíti az adattárházba. |

## <a name="next-steps"></a>További lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
* Az Event hub-események kezelésével kapcsolatos információkért lásd: [Stream Big Data egy adattárházba](event-grid-event-hubs-integration.md).
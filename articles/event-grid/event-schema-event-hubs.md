---
title: Az Azure Event Grid eseményközpontok eseménysémája
description: Az Eseményközpontok eseményeihez az Azure Event Grid del biztosított tulajdonságok ismertetése
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 9c0113687d27bf43375f298057129a5594ec0a06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561828"
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Azure Event Grid eseménysémája eseményközpontokhoz

Ez a cikk az eseményközpontok eseményeinek tulajdonságait és sémáját tartalmazza.Az eseménysémák bemutatása az [Azure Event Grid eseménysémájában.](event-schema.md)

A mintaparancsfájlok és oktatóanyagok listáját az [Event Hubs eseményforrásában](event-sources.md#event-hubs)található.

### <a name="available-event-types"></a>Elérhető eseménytípusok

Az Event Hubs a **Microsoft.EventHub.CaptureFileCreated** eseménytípust bocsátki rögzítési fájl létrehozásakor.

## <a name="example-event"></a>Példa esemény

Ez a mintaesemény egy eseményközpontok eseményének sémáját jeleníti meg, amely akkor jelenik meg, amikor a rögzítési szolgáltatás egy fájlt tárol: 

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

Egy esemény legfelső szintű adatokat rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | Az eseményforrás teljes erőforráselérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| Tárgy | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| eventType | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| eventTime | sztring | Az esemény létrehozásának időpontja a szolgáltató UTC-ideje alapján. |
| id | sztring | Az esemény egyedi azonosítója |
| data | objektum | Eseményközpont eseményadatai. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| fileUrl | sztring | A rögzítési fájl elérési útja. |
| Filé | sztring | A rögzítési fájl fájltípusa. |
| partitionId | sztring | A szilánk azonosítója. |
| sizeInBájt | egész szám | A fájl mérete. |
| eventCount (eseményszáma) | egész szám | A fájlban lévő események száma. |
| firstSequenceNumber | egész szám | A várólista legkisebb sorozatszáma. |
| lastSequenceNumber | egész szám | A várólista utolsó sorszáma. |
| firstEnqueueTime | sztring | Az első alkalom a várólistából. |
| lastEnqueueTime | sztring | Utoljára a várólistából. |

## <a name="next-steps"></a>További lépések

* Az Azure Event Grid bemutatása a [Mi az eseményrács?](overview.md)
* Az Azure Event Grid-előfizetés ek létrehozásáról az [Event Grid-előfizetésséma](subscription-creation-schema.md)című témakörben talál további információt.
* Az eseményközpontok eseményeinek kezeléséről a [Big Data adattárlásadat-raktárba című témakörben](event-grid-event-hubs-integration.md)talál további információt.
---
title: Azure-jelző Event Grid forrásaként
description: Az Azure Signaler eseményeihez megadott tulajdonságok ismertetése Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 2ac391f366c4b9a82741a1b6b3135f5d7b5fe331
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86106651"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>A Signaler szolgáltatáshoz tartozó Azure Event Grid esemény sémája

Ez a cikk a Signaler szolgáltatás eseményeinek tulajdonságait és sémáját ismerteti.Az események sémáinak bemutatása: [Azure Event Grid Event Schema](event-schema.md). Emellett a gyors indulások és oktatóanyagok listáját is tartalmazza, amelyekkel az Azure-jeleket használhatja az esemény forrásaként.

## <a name="event-grid-event-schema"></a>Event Grid-eseményséma

### <a name="available-event-types"></a>Elérhető események típusai

A signaler szolgáltatás a következő típusú eseményeket bocsátja ki:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft. SignalRService. ClientConnectionConnected | Az Ügyfélkapcsolat kapcsolódásakor következik be. |
| Microsoft. SignalRService. ClientConnectionDisconnected | Az Ügyfélkapcsolat leválasztásakor következik be. |

### <a name="example-event"></a>Példa eseményre

Az alábbi példa egy ügyfélkapcsolathoz kapcsolódó esemény sémáját mutatja be: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Az ügyfélkapcsolati kapcsolat megszakadt eseményének sémája hasonló: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
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
| adatok | objektum | A jelző szolgáltatás eseményeinek adattípusa. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| időbélyeg | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| hubName | sztring | Az az elosztó, amelyhez az Ügyfélkapcsolat tartozik. |
| connectionId | sztring | Az Ügyfélkapcsolat egyedi azonosítója. |
| userId | sztring | A jogcímben definiált felhasználói azonosító. |
| errorMessage | sztring | A kapcsolat leválasztását okozó hiba. |

## <a name="tutorials-and-how-tos"></a>Oktatóanyagok és útmutatók
|Cím | Leírás |
|---------|---------|
| [Az Azure Signaler szolgáltatás eseményeire való reagálás Event Grid használatával](../azure-signalr/signalr-concept-event-grid-integration.md) | Az Azure Signaler szolgáltatás és a Event Grid integrálásának áttekintése. |
| [Az Azure szignáló szolgáltatás eseményeinek küldése Event Grid](../azure-signalr/signalr-howto-event-grid-integration.md) | Bemutatja, hogyan küldhetők az Azure Signaler szolgáltatás eseményei egy alkalmazásnak Event Gridon keresztül. |

## <a name="next-steps"></a>Következő lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).

---
title: Az Azure SingnalR eseményrács forrásaként
description: Az Azure Event Grid del az Azure SignalR-eseményekhez biztosított tulajdonságok ismertetése
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: 730d1a7a053ab636c45313dd0c35a537434eb782
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393397"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Azure Event Grid eseménysémája a SignalR szolgáltatáshoz

Ez a cikk a SignalR Service-események tulajdonságait és sémáját tartalmazza.Az eseménysémák bemutatása az [Azure Event Grid eseménysémájában.](event-schema.md) Emellett az Azure SignalR eseményforrásként való használatához az Azure SignalR használatával használható gyorsindítások és oktatóanyagok listáját is tartalmazza.

## <a name="event-grid-event-schema"></a>Eseményrács eseménysémája

### <a name="available-event-types"></a>Elérhető eseménytípusok

A SignalR szolgáltatás a következő eseménytípusokat bocsátja ki:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft.SignalRService.ClientConnected kapcsolat | Ügyfélkapcsolat csatlakoztatásakor. |
| Microsoft.SignalRService.ClientKapcsolatleválasztva | Ügyfélkapcsolat leválasztásakor. |

### <a name="example-event"></a>Példa esemény

A következő példa egy ügyfélkapcsolathoz csatlakoztatott esemény sémáját mutatja be: 

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

Az ügyfélkapcsolat leválasztott eseményének sémája hasonló: 

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

Egy esemény legfelső szintű adatokat rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | Az eseményforrás teljes erőforráselérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| Tárgy | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| eventType | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| eventTime | sztring | Az esemény létrehozásának időpontja a szolgáltató UTC-ideje alapján. |
| id | sztring | Az esemény egyedi azonosítója |
| data | objektum | SignalR Service eseményadatai. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| időbélyeg | sztring | Az esemény létrehozásának időpontja a szolgáltató UTC-ideje alapján. |
| hubName | sztring | Az a hub, amelyhez az ügyfélkapcsolat tartozik. |
| connectionId | sztring | Az ügyfélkapcsolat egyedi azonosítója. |
| userId | sztring | A jogcímcímben definiált felhasználói azonosító. |
| errorMessage | sztring | A kapcsolat megszakadását okozó hiba. |

## <a name="tutorials-and-how-tos"></a>Oktatóanyagok és útmutatók
|Cím | Leírás |
|---------|---------|
| [Reagálás az Azure SignalR-szolgáltatás eseményeire az Event Grid használatával](../azure-signalr/signalr-concept-event-grid-integration.md) | Az Azure SignalR-szolgáltatás és az Event Grid integrálásának áttekintése. |
| [Az Azure SignalR-szolgáltatás eseményeinek küldése az Event Gridbe](../azure-signalr/signalr-howto-event-grid-integration.md) | Bemutatja, hogyan küldhet Azure SignalR Service-eseményeket egy alkalmazásnak az Event Griden keresztül. |

## <a name="next-steps"></a>További lépések

* Az Azure Event Grid bemutatása a [Mi az eseményrács?](overview.md)
* Az Azure Event Grid-előfizetés ek létrehozásáról az [Event Grid-előfizetésséma](subscription-creation-schema.md)című témakörben talál további információt.

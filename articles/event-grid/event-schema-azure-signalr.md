---
title: Az Azure Event Grid Azure SignalR eseménysémája
description: Az Azure SignalR-események az Azure Event GRID használatával biztosított tulajdonságait ismerteti
services: event-grid
author: chenyl
ms.service: event-grid
ms.topic: reference
ms.date: 06/11/2019
ms.author: chenyl
ms.openlocfilehash: 3b072ff2b680ad6d144c7441190ab2df9870f5d0
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789071"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>A SignalR Service az Azure Event Grid eseménysémája

Ez a cikk a séma és a SignalR Service eseményeket. Eseménysémák szeretné megismerni, lásd: [Azure Event Grid-esemény séma](event-schema.md).


## <a name="available-event-types"></a>Rendelkezésre álló események típusai

SignalR Service a következő esemény típusú bocsát ki:

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.SignalRService.ClientConnectionConnected | Következik be, amikor egy ügyfél kapcsolat. |
| Microsoft.SignalRService.ClientConnectionDisconnected | Következik be, amikor egy ügyfél-kapcsolat megszakadt. |

## <a name="example-event"></a>Példa esemény

Az alábbi példa bemutatja az ügyfél-séma kapcsolat csatlakoztatott esemény: 

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

Egy ügyfél-csatlakozási leválasztott esemény sémája hasonlít: 

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

## <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény a következő legfelső szintű adatokat tartalmaz:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| topic | sztring | A forrás teljes erőforrás elérési útja. Ez a mező nem írható. Event Grid biztosítja ezt az értéket. |
| subject | Karakterlánc | Az esemény tárgya közzétevő által megadott elérési útja. |
| eventType | sztring | Ehhez eseményre adatforráshoz regisztrált esemény típusok egyikét. |
| eventTime | sztring | Az esemény akkor jön létre az idő alapján a szolgáltató UTC idő. |
| id | Karakterlánc | Az esemény egyedi azonosítója. |
| data | object | SignalR Service eseményadatokat. |
| dataVersion | Karakterlánc | Az adatobjektum sémaverziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | sztring | Az esemény-metaadatok sémaverziója. Event Grid sémáját, a legfelső szintű tulajdonságait határozza meg. Event Grid biztosítja ezt az értéket. |

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| timestamp | Karakterlánc | Az esemény akkor jön létre az idő alapján a szolgáltató UTC idő. |
| hubName | Karakterlánc | A központ, amely az ügyfél kapcsolata. |
| connectionId | Karakterlánc | Az ügyfél kapcsolata egyedi azonosítója. |
| userId | Karakterlánc | A felhasználói azonosító jogcím meghatározott. |
| Hibaüzenet | Karakterlánc | A hiba, amelynek hatására a kapcsolat megszakadt. |

## <a name="next-steps"></a>További lépések

* Azure Event Grid bemutatása, lásd: [Mi az Event Grid?](overview.md)
* Az Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid-előfizetés séma](subscription-creation-schema.md).

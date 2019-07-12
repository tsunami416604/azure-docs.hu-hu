---
title: Reagálás eseményekre az Azure SignalR Service
description: Azure Event Grid használatával fizessen elő az Azure SignalR Service eseményeket.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 06/12/2019
ms.topic: conceptual
ms.service: azure-signalr
ms.openlocfilehash: 02f88c5953d499b30f2ea3408318f70a72f42b0f
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789188"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reagálás eseményekre az Azure SignalR Service

Az Azure SignalR Service események alkalmazások reagálni azokra, csatlakoztatott vagy leválasztott modern, kiszolgáló nélküli architektúra használatával ügyfélkapcsolatok engedélyezése. Így összetettebb kódja vagy költséges és hatékony lekérdezési szolgáltatások nélkül hajtja végre.  Ehelyett eseményt leküld [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) például előfizetők [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), vagy akár a saját egyéni http-figyelőt, és csak akkor Mit kell fizetni.

Az Event Grid szolgáltatást biztosít a megbízható kézbesítést szolgáltatásokat az alkalmazások sokoldalú újrapróbálkozási szabályzatok és a kézbesíthetetlen levelek kézbesítési megbízhatóan kapnak az Azure SignalR Service eseményeket. További tudnivalókért lásd: [Event Grid az üzenetek kézbesítését, és ismételje meg](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

![Event Grid-modell](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Kiszolgáló nélküli állapota
Az Azure SignalR Service események csak akkor áll aktív, ha az ügyfélkapcsolatokat, kiszolgáló nélküli állapotban van. Általánosan fogalmazva Ha egy ügyfél nem átirányítása egy központi kiszolgálóval, kerül a kiszolgáló nélküli állapotot. Klasszikus üzemmódban a munkahelyi csak akkor, ha a hub, az ügyfélkapcsolatok csatlakozni, nem rendelkezik egy központi kiszolgálóval. Kiszolgáló nélküli módban ajánlott azonban néhány probléma elkerülése érdekében. Szolgáltatás módjával kapcsolatos további részletekért, lásd: [szolgáltatás mód kiválasztása](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Elérhető az Azure SignalR Service események
Event grid használ [esemény-előfizetések](../event-grid/concepts.md#event-subscriptions) eseményt üzenetek továbbítását-előfizetők számára. Az Azure SignalR Service eseményelőfizetések események két típusát támogatja:  

|Esemény neve|Leírás|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Következik be, amikor egy ügyfél kapcsolat van csatlakoztatva.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Következik be, amikor egy ügyfél-kapcsolat le van választva.|

## <a name="event-schema"></a>Eseményséma
Az Azure SignalR Service események az adatok változásait válaszolni kell minden információt tartalmaznak. Az Azure SignalR Service eventType tulajdonság esemény kezdődik a "Microsoft.SignalRService" azonosíthatja. További információ a használati az Event Grid-esemény tulajdonságai dokumentált [Event Grid-esemény séma](../event-grid/event-schema.md).  

Íme egy példa egy ügyfél kapcsolat csatlakoztatott esemény:
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

További információkért lásd: [SignalR Service események sémája](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>További lépések

További információ az Event Grid, és próbálja az Azure SignalR Service események:

> [!div class="nextstepaction"]
> [Próbálja ki az Azure SignalR Service egy minta Event Grid-integrációt](./signalr-howto-event-grid-integration.md)
> [Event Grid](../event-grid/overview.md)

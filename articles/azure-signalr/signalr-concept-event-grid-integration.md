---
title: Reagálás az Azure SignalR Szolgáltatás eseményeire
description: Az Azure Event Grid használatával előfizethet az Azure SignalR Service eseményeire. Ezek az események más downstream szolgáltatásokat is aktiválhatnak.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: a8e25907b40b910f2b91884d355b6ac85eeaa250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158187"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reagálás az Azure SignalR Service eseményeire

Az Azure SignalR Service-események lehetővé teszik az alkalmazások számára, hogy a modern kiszolgáló nélküli architektúrák használatával csatlakoztatott vagy leválasztott ügyfélkapcsolatokra reagáljanak. Ezt anélkül teszi, hogy bonyolult kódra vagy drága és nem hatékony közvélemény-kutatásra lenne szükség.  Ehelyett az események et az [Azure Event Grid-ön](https://azure.microsoft.com/services/event-grid/) keresztül az előfizetők, például [az Azure Functions,](https://azure.microsoft.com/services/functions/) [az Azure Logic Apps,](https://azure.microsoft.com/services/logic-apps/)vagy akár a saját egyéni http-figyelő, és csak akkor kell fizetnie, amit használ.

Az Azure SignalR Service-események megbízhatóan elküldve az Event Grid szolgáltatásnak, amely megbízható kézbesítési szolgáltatásokat nyújt az alkalmazásokszámára a gazdag újrapróbálkozási szabályzatok és a kézbesítési kézbesítés révén. További információ: [Event Grid üzenet kézbesítése és újra próbálkozás](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

![Eseményrács-modell](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Kiszolgáló nélküli állapot
Az Azure SignalR Service-események csak akkor aktívak, ha az ügyfélkapcsolatok kiszolgáló nélküli állapotban vannak. Általánosságban elmondható, hogy ha egy ügyfél nem irányítja a központi kiszolgáló, akkor megy a kiszolgáló nélküli állapotba. A klasszikus mód csak akkor működik, ha a hub, amelyhez az ügyfélkapcsolatok csatlakoznak, nem rendelkezik központi kiszolgálóval. A probléma elkerülése érdekében azonban ajánlott a kiszolgáló nélküli mód. A szolgáltatási móddal kapcsolatos további tudnivalóka A [Szolgáltatás mód kiválasztása](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Elérhető Azure SignalR-szolgáltatás események
Az eseményrács [esemény-előfizetéseket](../event-grid/concepts.md#event-subscriptions) használ az eseményüzenetek előfizetőkhöz való irányításához. Az Azure SignalR Service esemény-előfizetések kétféle eseményt támogatnak:  

|Esemény neve|Leírás|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Ügyfélkapcsolat csatlakoztatásakor előáll.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Az ügyfélkapcsolat leválasztásakor az előleg.|

## <a name="event-schema"></a>Eseményséma
Az Azure SignalR Service-események tartalmazzák az összes szükséges információt, hogy válaszoljon az adatok változásaira. Az Azure SignalR Service eseményazonosító azonosítója az eventType tulajdonsággal kezdődik a "Microsoft.SignalRService". Az Event Grid eseménytulajdonságainak használatáról további információt az [Event Grid eseménysémája](../event-grid/event-schema.md)dokumentál.  

Íme egy példa egy ügyfélkapcsolathoz csatlakoztatott eseményre:
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

További információ: [SignalR Service events séma](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>További lépések

Tudjon meg többet az Event Gridről, és próbálja ki az Azure SignalR Service eseményeit:

> [!div class="nextstepaction"]
> [Próbálja ki a minta eseményrács-integrációt az Azure SignalR szolgáltatással](./signalr-howto-event-grid-integration.md)
> [az Eseményrácsról](../event-grid/overview.md)

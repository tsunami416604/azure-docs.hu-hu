---
title: Azure Service Bus Event Grid forrásként
description: A Service Bus eseményekhez megadott tulajdonságokat ismerteti Azure Event Grid
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: 141a0e96071014dc3705d30f72b1a9257737298a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393245"
---
# <a name="azure-service-bus-as-an-event-grid-source"></a>Azure Service Bus Event Grid forrásként

Ez a cikk a Service Bus eseményeinek tulajdonságait és sémáját ismerteti.Az események sémáinak bemutatása: [Azure Event Grid Event Schema](event-schema.md).

## <a name="event-grid-event-schema"></a>Event Grid-eseményséma

### <a name="available-event-types"></a>Elérhető események típusai

Service Bus a következő típusú eseményeket bocsátja ki:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft. ServiceBus. ActiveMessagesAvailableWithNoListeners | Akkor következik be, amikor aktív üzenetek vannak egy várólistában vagy előfizetésben, és nincsenek figyelő fogadók. |
| Microsoft. ServiceBus. DeadletterMessagesAvailableWithNoListener | Akkor következik be, amikor aktív üzenetek vannak egy kézbesítetlen levelek várólistájában, és nincsenek aktív figyelők. |

### <a name="example-event"></a>Példa eseményre

Az alábbi példa a figyelőkkel nem rendelkező aktív üzenetek sémáját mutatja be:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

A kézbesítetlen levelek várólistája esemény sémája hasonló:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| tulajdonos | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| eventType | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| eventTime | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| id | sztring | Az esemény egyedi azonosítója. |
| data | objektum | BLOB Storage-események |
| dataVersion | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| Namespacename tulajdonság | sztring | A Service Bus névtér, amelyben az erőforrás található. |
| requestUri | sztring | Az eseményt kibocsátó adott üzenetsor vagy előfizetés URI azonosítója. |
| entityType | sztring | Az eseményeket kibocsátó Service Bus entitás típusa (Üzenetsor vagy előfizetés). |
| queueName | sztring | Az üzenetsor aktív üzenetekkel, ha egy várólistára van előfizetni. Az érték null, ha témaköröket vagy előfizetéseket használ. |
| topicName | sztring | A témakör az aktív üzenetekkel rendelkező Service Bus-előfizetéshez tartozik. Az érték null, ha üzenetsor használata. |
| subscriptionName | sztring | A Service Bus-előfizetés aktív üzenetekkel. Az érték null, ha üzenetsor használata. |

## <a name="tutorials-and-how-tos"></a>Oktatóanyagok és útmutatók
|Cím  |Leírás  |
|---------|---------|
| [Oktatóanyag: Azure Service Bus Azure Event Grid integrációs példák](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid üzeneteket küld Service Bus témakörből az alkalmazás és a logikai alkalmazás működéséhez. |
| [Azure Service Bus az integráció Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | A Service Bus és a Event Grid integrálásának áttekintése. |

## <a name="next-steps"></a>További lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
* A Azure Event Grid és a Service Bus használatával kapcsolatos további részletekért tekintse meg az [integráció áttekintése című Service Bus Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Próbálja meg [Service Bus-események fogadását functions vagy Logic apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).

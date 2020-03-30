---
title: Az Azure Event Grid Service Bus eseménysémája
description: A Service Bus-események Azure Event Griddel való szolgáltatásához megadott tulajdonságok ismertetése
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: babanisa
ms.openlocfilehash: f44d2c1c5be6ac895b6f5ea9feca29c0f8ed09f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561761"
---
# <a name="azure-event-grid-event-schema-for-service-bus"></a>Azure Event Grid eseménysémája a Service Bus-hoz

Ez a cikk a Service Bus-események tulajdonságait és sémáját tartalmazza.Az eseménysémák bemutatása az [Azure Event Grid eseménysémájában.](event-schema.md)

A mintaparancsfájlok és oktatóanyagok listáját a [Service Bus eseményforrásában téssze](event-sources.md#service-bus)el.

## <a name="available-event-types"></a>Elérhető eseménytípusok

A Service Bus a következő eseménytípusokat bocsátja ki:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | Akkor merül fel, ha aktív üzenetek vannak egy várólistában vagy előfizetésben, és nem figyelnek a fogadók. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Ha aktív üzenetek vannak a Kézbesítetlen levelek várólistájában, és nincsenek aktív figyelők. |

## <a name="example-event"></a>Példa esemény

A következő példa a figyelők esemény nélküli aktív üzenetek sémáját mutatja be:

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

A kézbesítetlen levelek várólistájának eseményének sémája hasonló:

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

## <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény legfelső szintű adatokat rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | Az eseményforrás teljes erőforráselérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| Tárgy | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| eventType | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| eventTime | sztring | Az esemény létrehozásának időpontja a szolgáltató UTC-ideje alapján. |
| id | sztring | Az esemény egyedi azonosítója |
| data | objektum | Blob tárolási esemény adatai. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| namespaceName | sztring | A Service Bus névtér, amelyben az erőforrás létezik. |
| requestUri | sztring | Az adott várólistára vagy az eseményt kibocsátó előfizetésre mutató URI.The URI to the specific queue or subscription ening the event. |
| entityType | sztring | Az eseményeket (várólistát vagy előfizetést) kibocsátó Service Bus entitás típusa. |
| queueName (sornév) | sztring | Az aktív üzeneteket tartalmazó várólista, ha várólistára iratkozik. Érték null, ha a témakörök / előfizetések. |
| topicName (témakör neve) | sztring | A témakör, amelyhez a Service Bus-előfizetés aktív üzeneteket tartalmaz. Null érték, ha várólistát használ. |
| subscriptionName | sztring | A Service Bus-előfizetés aktív üzenetekkel. Null érték, ha várólistát használ. |

## <a name="next-steps"></a>További lépések

* Az Azure Event Grid bemutatása a [Mi az eseményrács?](overview.md)
* Az Azure Event Grid-előfizetés ek létrehozásáról az [Event Grid-előfizetésséma](subscription-creation-schema.md)című témakörben talál további információt.
* Az Azure Event Grid service bus használatával kapcsolatos részletekért tekintse meg a [Service Bus to Event Grid integrációs áttekintést.](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md)
* Próbálja meg [fogadni a Service Bus-eseményeket a Functions vagy a Logic Apps segítségével.](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json)

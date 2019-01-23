---
title: Az Azure Event Grid a Service Bus eseménysémája
description: Ismerteti a szolgáltatást a Service Bus-eseményekre az Azure Event Griddel tulajdonságait
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: babanisa
ms.openlocfilehash: f44d2c1c5be6ac895b6f5ea9feca29c0f8ed09f3
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467740"
---
# <a name="azure-event-grid-event-schema-for-service-bus"></a>Service Bus számára, az Azure Event Grid eseménysémája

Ez a cikk a séma és a Service Bus-események. Eseménysémák szeretné megismerni, lásd: [Azure Event Grid-esemény séma](event-schema.md).

Mintaszkriptek és oktatóanyagok listáját lásd: [a Service Bus eseményforrás](event-sources.md#service-bus).

## <a name="available-event-types"></a>Rendelkezésre álló események típusai

Service Bus a következő esemény típusú bocsát ki:

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | Jön létre, ha aktív üzenetek találhatók egy üzenetsorban vagy előfizetésben, és nincsenek figyelő fogadók. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Ha aktív üzenetek találhatók egy feldolgozatlan üzenetek sorhoz, és nincs aktív figyelők következik be. |

## <a name="example-event"></a>Példa esemény

Az alábbi példa bemutatja a séma nincs figyelői eseménnyel aktív üzenetek:

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

Egy üzenetsor feldolgozatlan esemény sémája hasonlít:

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

Egy esemény a következő legfelső szintű adatokat tartalmaz:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | A forrás teljes erőforrás elérési útja. Ez a mező nem írható. Event Grid biztosítja ezt az értéket. |
| tárgy | sztring | Az esemény tárgya közzétevő által megadott elérési útja. |
| eventType | sztring | Ehhez eseményre adatforráshoz regisztrált esemény típusok egyikét. |
| eventTime | sztring | Az esemény akkor jön létre az idő alapján a szolgáltató UTC idő. |
| id | sztring | Az esemény egyedi azonosítója. |
| adat | objektum | A BLOB storage-eseményadatok. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | sztring | Az esemény-metaadatok sémaverziója. Event Grid sémáját, a legfelső szintű tulajdonságait határozza meg. Event Grid biztosítja ezt az értéket. |

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| namespaceName | sztring | A Service Bus-névtér az erőforrás megtalálható. |
| requestUri | sztring | Az URI-t az adott üzenetsor vagy az esemény kibocsátó előfizetésnek. |
| entityType | sztring | Események (üzenetsorok vagy előfizetések) kibocsátó Service Bus-entitás típusa. |
| queueName | sztring | Az üzenetsor, az aktív üzenetek, ha feliratkozik egy üzenetsorba. Érték null, ha a témakörök / előfizetések. |
| topicName | sztring | A témakör az aktív üzenetek a Service Bus-előfizetéshez tartozik. Hodnota null értékű, ha egy üzenetsor használata. |
| subscriptionName | sztring | Az aktív üzenetek a Service Bus-előfizetés. Hodnota null értékű, ha egy üzenetsor használata. |

## <a name="next-steps"></a>További lépések

* Azure Event Grid bemutatása, lásd: [Mi az Event Grid?](overview.md)
* Az Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid-előfizetés séma](subscription-creation-schema.md).
* Azure Event Grid használatával a Service Bus használatának részletes ismertetéséért tekintse meg a [Service Bus – Event Grid integráció áttekintése](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Próbálja ki [fogadása a Service Bus-eseményekre az funkciók vagy a Logic Apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).

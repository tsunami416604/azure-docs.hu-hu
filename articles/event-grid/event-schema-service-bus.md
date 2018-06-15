---
title: Az Azure esemény rács Service Bus event séma
description: Ismerteti a Service Bus események Azure esemény rácshoz tartozó tulajdonságait
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: reference
ms.date: 02/21/2018
ms.author: babanisa
ms.openlocfilehash: 991679eeb0f7c98606133750b193a5895f39178f
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34303317"
---
# <a name="azure-event-grid-event-schema-for-service-bus"></a>A Service Bus Azure esemény rács esemény séma

A cikkben a séma és a Service Bus-eseményeket. Egy esemény sémák bemutatása, lásd: [Azure esemény rács esemény séma](event-schema.md).

## <a name="available-event-types"></a>Rendelkezésre álló eseményoszlopok típusok

A Service Bus bocsát ki a következő esemény típusa:

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | A várólista vagy az előfizetés és a nem figyel fogadók active üzenetek esetén következik be. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Ha nincsenek aktív üzenetek a kézbesítetlen levelek várólistájához és a nem aktív figyelői következik be. |

## <a name="example-event"></a>Példa esemény

A következő példa bemutatja egy aktív üzenetek nincsenek figyelők eseménnyel sémája:

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

A séma halottlevél-várólista esemény hasonlít:

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

Az esemény a következő legfelső szintű adatokat tartalmaz:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| A témakör | karakterlánc | A forrás teljes erőforrás elérési útja. Ez a mező nincs írható. Esemény rács biztosítja ezt az értéket. |
| Tulajdonos | karakterlánc | Az esemény tulajdonos közzétevő által megadott elérési útja. |
| eventType | karakterlánc | Az esemény adatforrás regisztrált esemény típusok egyike. |
| eventTime | karakterlánc | Az esemény jön létre az idő alapján a szolgáltató UTC idő szerint. |
| id | karakterlánc | Az esemény egyedi azonosítója. |
| adat | objektum | A BLOB storage eseményadatok. |
| dataVersion | karakterlánc | Az adatobjektum sémaverziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | karakterlánc | Az esemény-metaadatok sémaverziója. Esemény rács a séma legfelső szintű tulajdonság határozza meg. Esemény rács biztosítja ezt az értéket. |

Az objektum tulajdonságai a következők:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| nameSpaceName | karakterlánc | A Service Bus-névtér az erőforrás található. |
| requestUri | karakterlánc | A megadott várólista vagy az esemény kibocsátó előfizetésének URI. |
| EntityType | karakterlánc | Események (várólista vagy előfizetést) kibocsátó Szolgáltatásbusz-entitás típusa. |
| queueName | karakterlánc | A várólista aktív üzeneteket, ha annak a várólistára előfizetés. Null értéket, ha a témakörök / előfizetések. |
| topicName | karakterlánc | A témakör aktív üzeneteket a Service Bus előfizetéshez tartozik. Érték null, ha a várólista alapján. |
| SubscriptionName | karakterlánc | A Service Bus-előfizetés aktív üzeneteket. Érték null, ha a várólista alapján. |

## <a name="next-steps"></a>További lépések

* Megismerkedhet az Azure Event rács, lásd: [Mi az az esemény rács?](overview.md)
* Egy esemény rács Azure-előfizetés létrehozásával kapcsolatos további információkért lásd: [esemény rács előfizetés séma](subscription-creation-schema.md).
* Az Azure Event rács használatával a Service busszal, lásd: a [Service Bus számára, esemény rács integrációjának áttekintése](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Próbálja [fogadja a Service Bus eseményeket funkciók vagy a Logic Apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).

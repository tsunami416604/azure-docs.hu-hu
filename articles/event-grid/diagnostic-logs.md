---
title: Azure Event Grid – témakörökhöz vagy tartományokhoz tartozó diagnosztikai naplók
description: Ez a cikk az Azure Event Grid-témakör vagy egy tartomány diagnosztikai naplóival kapcsolatos elméleti információkat tartalmaz.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: spelluru
ms.openlocfilehash: 93e7e47cbcc1ab9542ba333b89f7dd655a412489
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82630251"
---
#  <a name="diagnostic-logs-for-azure-event-grid-topicsdomains"></a>Diagnosztikai naplók Azure Event Grid témakörökhöz/tartományokhoz
A diagnosztikai beállítások lehetővé teszik Event Grid felhasználók számára a **közzétételi és kézbesítési hibák** naplóinak rögzítését vagy megtekintését egy Storage-fiókban, egy Event hub-ban vagy egy log Analytics-munkaterületen. Ez a cikk a naplókhoz és egy példaként szolgáló naplóbejegyzés sémáját ismerteti.


## <a name="schema-for-publishdelivery-failure-logs"></a>Közzétételi/kézbesítési hibák naplóihoz tartozó séma

| Tulajdonság neve | Adattípus | Leírás |
| ------------- | --------- | ----------- | 
| Idő | DateTime | Az az idő, amikor a naplóbejegyzés létrejött <p>**Példa értéke:** 01-29-2020 09:52:02.700</p> |
| EventSubscriptionName | Sztring | Az esemény-előfizetés neve <p>**Példa értéke:** "EVENTSUB1"</p> <p>Ez a tulajdonság csak a kézbesítési hibák naplói esetében létezik.</p>  |
| Kategória | Sztring | A napló kategóriájának neve. <p>**Példa értékei:** "DeliveryFailures" vagy "PublishFailures" | 
| OperationName | Sztring | A hiba észlelésekor végrehajtott művelet neve.<p>**Példa értékei:** Kézbesítési hibák esetén "kézbesítés". |
| Üzenet | Sztring | A hiba okát és egyéb további részleteket ismertető üzenet a felhasználó számára. |
| ResourceId | Sztring | A témakör/tartományi erőforrás erőforrás-azonosítója<p>**Példa értékei:**`/SUBSCRIPTIONS/SAMPLE-SUBSCRIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/TOPIC1` |

## <a name="example"></a>Példa

```json
{
    "time": "2019-11-01T00:17:13.4389048Z",
    "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
    "eventSubscriptionName": "SAMPLEDESTINATION",
    "category": "DeliveryFailures",
    "operationName": "Deliver",
    "message": "Message:outcome=NotFound, latencyInMs=2635, systemId=17284f7c-0044-46fb-84b7-59fda5776017, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
}
```

## <a name="next-steps"></a>További lépések
Ha szeretné megtudni, hogyan engedélyezheti a diagnosztikai naplókat a témakörök vagy tartományok számára, olvassa el a [diagnosztikai naplók engedélyezése](enable-diagnostic-logs-topic.md)című témakört.

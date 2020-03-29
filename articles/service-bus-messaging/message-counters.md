---
title: Azure Service Bus – üzenetek száma
description: A várólistákban és előfizetésekben tartott üzenetek számának lekérése az Azure Resource Manager és az Azure Service Bus NamespaceManager API-k használatával.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 3a4fca0b3b60fcb76bcdc4f5f2d53df816c5053b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756376"
---
# <a name="message-counters"></a>Üzenetszámlálók

A várólistákban és előfizetésekben tárolt üzenetek számát az Azure Resource Manager és a Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API-k segítségével kérheti le a .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A PowerShell segítségével a számlálót a következőképpen szerezheti be:

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Üzenetek számának részletei

Az aktív üzenetek számának ismerete hasznos annak meghatározásához, hogy egy várólista olyan hátralékot hoz-e létre, amely több erőforrást igényel a jelenleg üzembe helyezettnél. A [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) osztályban a következő számlálórészletek érhetők el:

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): A várólistában vagy előfizetésben lévő, aktív állapotban lévő és kézbesítésre kész üzenetek.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): Üzenetek a kézbesítetlen levelek várólistájában.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): Üzenetek ütemezett állapotban.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): Azok az üzenetek, amelyek nem tudtak átkerülni egy másik várólistába vagy témakörbe, és átkerültek az átadási kézbesítetlen levelek várólistájába.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): Egy másik várólistába vagy témakörbe átvinni függőben lévő üzenetek.

Ha egy alkalmazás a várólista hossza alapján szeretné felmérni az erőforrásokat, azt mért ütemben kell megtennie. Az üzenetszámlálók beszerzése egy drága művelet az üzenetközvetítőn belül, és annak végrehajtása gyakran közvetlenül és hátrányosan befolyásolja az entitás teljesítményét.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Service Bus üzenetküldéséről, olvassa el az alábbi témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

---
title: Azure Service Bus üzenetek száma
description: A várólistákban és előfizetésekben tárolt üzenetek számának lekérése Azure Resource Manager és az Azure Service Bus NamespaceManager API-k használatával.
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
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76756376"
---
# <a name="message-counters"></a>Üzenetszámlálók

A várólistákban és előfizetésekben tárolt üzenetek számát az Azure Resource Manager és a .NET-keretrendszer SDK Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API-k használatával kérheti le.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A PowerShell-lel a következőképpen kérheti le a darabszámot:

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Üzenetek számának részletei

Az aktív üzenetek számának ismerete hasznos annak meghatározásához, hogy egy várólista felépít-e egy várakozó várólistát, amely több erőforrást igényel a jelenleg üzembe helyezett erőforrások feldolgozásához. A következő számláló részletei a [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) osztályban érhetők el:

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): az üzenetsor vagy az előfizetés, amely aktív állapotban van, és készen áll a kézbesítésre.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): üzenetek a kézbesítetlen levelek várólistáján.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): az ütemezett állapotú üzenetek.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): nem sikerült átvinni az üzeneteket egy másik várólistába vagy témakörbe, és áthelyezték azokat a kézbesítetlen levelek várólistába.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): egy másik várólistára vagy témakörbe való átvitelre váró üzenetek.

Ha egy alkalmazás a várólista hossza alapján szeretné méretezni az erőforrásokat, azt a mért tempóval kell végrehajtania. Az üzenetek számlálóinak beszerzése egy költséges művelet az üzenetkezelőben, és gyakran közvetlenül, illetve az entitás teljesítményének hátrányos kihatásával jár.

## <a name="next-steps"></a>Következő lépések

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

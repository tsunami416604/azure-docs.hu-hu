---
title: "Az Azure Service Bus-üzenetek száma |} Microsoft Docs"
description: "Azure Service Bus üzenetek számának beolvasása."
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: sethm
ms.openlocfilehash: f5d2aa551bbe77a66459907cf5cd1313bb907981
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="message-counters"></a>Üzenetszámlálók

A várólisták és előfizetések használata Azure Resource Manager és a Service Bus által tárolt üzenetek száma le [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) a .NET-keretrendszer SDK API-k.

A PowerShell használatával ezt úgy szerezheti be a számláló az alábbiak szerint:

```powershell
(Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Szám – részletek

Az aktív üzenetek száma tudatában akkor hasznos, annak meghatározásához, hogy a várólista elkészít egy várakozó fájlok számát, mint mi jelenleg telepített feldolgozni több erőforrást igényel. A következő teljesítményszámláló adatait érhetők el a [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) osztály:

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): a várólista vagy az előfizetés az aktív állapotának és kézbesítési készen áll a üzenetek.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): a kézbesítetlen levelek várólistában lévő üzenetek.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): az ütemezett állapotban üzeneteket.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): nem sikerült átviteli be egy másik üzenetsor vagy témakör és az átvitel kézbesítetlen levelek várólistájára helyezett üzenetek.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): átviteli be egy másik üzenetsor vagy témakör függőben levő üzenetek.

Ha egy alkalmazás kíván méretezni a várólista hossza erőforrásokhoz, akkor kell ehhez a nagyon mért nő. A üzenetszámlálók megszerzését belül az üzenet broker drága művelet, és futtatnia kell a gyakran közvetlenül és kedvezőtlen hatással van az entitás teljesítményét.

## <a name="next-steps"></a>Következő lépések

Tudhat meg többet a Service Bus üzenetkezelés, a következő témakörökben:

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
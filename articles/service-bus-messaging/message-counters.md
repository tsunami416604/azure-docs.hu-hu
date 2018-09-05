---
title: Az Azure Service Bus-üzenetek száma |} A Microsoft Docs
description: Azure Service Bus-üzenetek számának beolvasása.
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: spelluru
ms.openlocfilehash: f20893de235ac02fc5a94b54518af2405e4549ff
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696958"
---
# <a name="message-counters"></a>Üzenetszámlálók

Kérheti, hogy az üzenetsorok és -előfizetések az Azure Resource Manager és a Service Bus által tárolt üzenetek száma [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) a .NET-keretrendszer SDK API-k.

A PowerShell-lel szerezheti be a számláló a következő:

```powershell
(Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Üzenetek száma részleteit

Az aktív üzenetek száma ismerete hasznos meghatározásához, hogy egy üzenetsorba elkészít egy várakozó fájlok feldolgozására több erőforrásban mi jelenleg telepített igénylő. A következő teljesítményszámláló-adatok érhetők el a [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) osztály:

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): üzenetek az üzenetsorban vagy előfizetésben lévő az aktív állapotú, és szállításra kész.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): a kézbesítetlen üzenetek.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): üzenetek az ütemezett állapotban.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): nem sikerült az átviteli be egy másik üzenetsor vagy témakör és az átvitel kézbesítetlen levelek várólistájára helyezett üzenetek.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): egy másik üzenetsorra vagy témakörbe történő átvitel függőben lévő üzenetek.

Ha egy alkalmazás szeretné méretezni az erőforrásokat, az üzenetsor hossza alapján, azt kell ehhez a nagyon mért ütemben. A üzenetszámlálók megszerzését belül a közvetítő drága művelet, és futtassa a jelentést, gyakran közvetlenül és kedvezőtlen hatással van az a entitás teljesítményre.

## <a name="next-steps"></a>További lépések

További információ a Service Bus-üzenetkezelés, tekintse meg a következő témaköröket:

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
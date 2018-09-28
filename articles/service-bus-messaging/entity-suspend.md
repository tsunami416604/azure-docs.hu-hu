---
title: Az Azure Service Bus üzenetküldési entitások felfüggesztése |} A Microsoft Docs
description: Felfüggesztése és újraaktiválása Azure Service Bus-üzenet entitások.
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
ms.date: 09/26/2018
ms.author: spelluru
ms.openlocfilehash: bd75fd5c34a3518f6da0dbd56eca2ad152d60ba9
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405716"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Felfüggesztése és újraaktiválása üzenetküldési entitások (letiltva)

Üzenetsorok, témakörök és előfizetések átmenetileg felfüggeszti is. Felfüggesztés tárolási tartani minden üzenetet letiltott állapotba helyezi az entitás. Azonban üzeneteket nem lehet hozzáadott vagy eltávolított, és a megfelelő protokoll műveletek eddig is számtalan előnyét hibákat.

Egy entitás felfüggesztése sürgős felügyeleti okok miatt általában történik. A forgatókönyv egy kellene üzembe helyezett egy hibás fogadót, amely üzeneteket az üzenetsor ki nem sikerül a feldolgozást, és még nem megfelelően befejezi az üzenetek és eltávolítja azokat a. Ha ezt a viselkedést észlelnek, az üzenetsor letiltható az kap, amíg nem javított kód üzembe helyezése és további megelőzhető az adatvesztés a hibás kód okozza.

A felfüggesztés vagy Újraaktiválási is elvégezhető, a felhasználó által vagy a rendszer. A rendszer felfüggeszti az entitások, például az előfizetés a költségkeret lenyomásával súlyos felügyeleti okok csak. Rendszer – letiltva entitások nem lehet újraaktiválni a felhasználó által, de visszaállnak, ha a felfüggesztés okának elhárítása.

A portálon a **tulajdonságok** az adott entitás szakasz lehetővé teszi, hogy az állapotának módosítása; az alábbi képernyőfelvételen látható a váltógomb várólista:

![][1]

A portál csak lehetővé teszi a teljes körűen le az üzenetsorok. Is tiltsa le a küldési és fogadási műveletek külön-külön használatával a Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API-k, a .NET-keretrendszer SDK vagy az Azure Resource Manager-sablon Azure CLI-vel vagy az Azure Powershellen keresztül.

## <a name="suspension-states"></a>Felfüggesztés állapotok

A várólista beállítható állapotok a következők:

-   **Aktív**: A várólista aktív.
-   **Letiltott**: A várólista fel van függesztve.
-   **SendDisabled**: A várólista részben fel függesztve, a receive megengedett.
-   **ReceiveDisabled**: A várólista részlegesen felfüggesztve, küldjön megengedett.

Az előfizetésekre és -témaköröket, csak **aktív** és **letiltott** akkor állítható be.

A [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) enumerálás is határozza meg, amely csak állítható be a rendszer átmeneti állapotok. Tiltsa le egy várólistába a PowerShell-parancsot az alábbi példában látható. A Újraaktiválási parancs a következő egyenértékű beállítás `Status` való **aktív**.

```powershell
$q = Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>További lépések

További információ a Service Bus-üzenetkezelés, tekintse meg a következő témaköröket:

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png


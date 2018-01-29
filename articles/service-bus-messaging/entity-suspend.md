---
title: "Az Azure Service Bus üzenetküldési entitások felfüggesztése |} Microsoft Docs"
description: "Felfüggesztheti, és aktiválja újra a Azure Service Bus message entitásokat."
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
ms.date: 01/26/2018
ms.author: sethm
ms.openlocfilehash: 1984b113f695107f8d4d80e5bbf25c7dc39d13f6
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Felfüggesztése és újraaktiválása üzenetküldési entitások (letiltva)

Üzenetsorok, témakörök és előfizetések ideiglenesen is felfüggesztve. Felfüggesztés az entitás, amelyben az összes üzenet tároló karbantartása letiltott állapotba helyezi. Azonban üzeneteket nem lehet hozzáadott vagy eltávolított, és a megfelelő protokollt műveletek yield hibák.

Egy entitás felfüggesztése sürgős felügyeleti okok miatt általában történik. A forgatókönyv egy kellene telepíteni a hibás fogadó, amely a várólista ki üzenetek feldolgozási, sikertelen, és még helytelenül befejezi az üzenetek és eltávolítja azokat. Ha ezt a viselkedést észlelnek, a várólista letiltható az amíg javított kód van telepítve, és a hibás kódot okozta adatvesztés megakadályozása további kap.

A felfüggesztés vagy újraaktiválása végezheti el a felhasználó vagy a rendszer. A rendszer csak felfüggeszti entitások súlyos felügyeleti lehetnek például elérte a költségkeret előfizetés-e. Rendszer-letiltva entitások nem lehet újraaktiválni a felhasználó által, de a felfüggesztés okát javított visszaállítását végzi.

A portálon a **tulajdonságok** a megfelelő entitás szakasz lehetővé teszi, hogy az állapotváltás; az alábbi képernyőfelvételen látható a váltógomb várólista:

![][1]

A portál csak lehetővé teszi a várólisták teljesen letiltja. Is letilthatja a küldési és fogadási műveletek külön használatával a Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API-k, a .NET-keretrendszer SDK, vagy egy Azure Resource Manager-sablon Azure CLI vagy az Azure PowerShell használatával.

## <a name="suspension-states"></a>Felfüggesztés állapotok

A várólista beállítható állapot a következő:

-   **Aktív**: A várólista nincs aktív.
-   **Letiltott**: A várólista fel van függesztve.
-   **SendDisabled**: A várólista részben felfüggesztett rendelkező receive megengedett.
-   **ReceiveDisabled**: A várólista részben felfüggesztve, a küldés alatt álló engedélyezett.

Az előfizetések és a témaköröket, csak **aktív** és **letiltott** állítható be.

A [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) számbavételi is meghatároz egy átmeneti állapotokat, amelyeket a rendszer csak állítani. A várólista letiltása a PowerShell-parancsot az alábbi példában látható. Az újbóli aktiválást parancs egyenértékű, az beállítása `Status` való **aktív**.

```powershell
$q = Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>További lépések

Tudhat meg többet a Service Bus üzenetkezelés, a következő témakörökben:

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png


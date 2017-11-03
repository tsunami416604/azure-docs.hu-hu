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
ms.date: 10/03/2017
ms.author: sethm
ms.openlocfilehash: 09aee1ba9ec3ce72732cb1f60c9a840ffc4beb2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Felfüggesztése és újraaktiválása üzenetküldési entitások (letiltva)

Üzenetsorok, témakörök és előfizetések ideiglenesen is felfüggesztve. Felfüggesztés letiltott állapotba, tároló karbantartása az összes üzenet helyezi az entitás. Azonban üzeneteket nem lehet hozzáadott vagy eltávolított, és a megfelelő protokollt műveletek yield hibák.

Egy entitás felfüggesztése sürgős felügyeleti okok miatt általában történik. A forgatókönyv egy kellene telepíteni a hibás fogadó, amely a várólista ki üzenetek feldolgozási, sikertelen, és még helytelenül befejezi az üzenetek és eltávolítja azokat. Ha ezt a viselkedést észlelnek, a várólista letiltható az amíg javított kód van telepítve, és a hibás kódot okozta adatvesztés megakadályozása további kap.

A felfüggesztés vagy újraaktiválása végezheti el a felhasználó vagy a rendszer. A rendszer csak felfüggeszti entitások súlyos felügyeleti lehetnek például elérte a költségkeret előfizetés-e. Rendszer-letiltva entitások nem lehet újraaktiválni a felhasználó által, de a felfüggesztés okát javított visszaállítását végzi.

A portálon a **tulajdonságok** a megfelelő entitás szakasz lehetővé teszi, hogy az állapotváltás; az alábbi képernyőfelvételen látható a váltógomb várólista:

![][1]

A portál csak lehetővé teszi a várólisták teljesen letiltja. Is letilthatja a küldési és fogadási műveletek külön használatával a Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API a .NET-keretrendszer SDK, vagy egy Azure Resource Manager-sablon Azure CLI vagy az Azure PowerShell használatával.

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

## <a name="next-steps"></a>Következő lépések

Tudhat meg többet a Service Bus üzenetkezelés, a következő témakörökben:

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png


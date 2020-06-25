---
title: Azure Service Bus – üzenetkezelési entitások felfüggesztése
description: Ez a cikk azt ismerteti, hogyan lehet ideiglenesen felfüggeszteni és újraaktiválni Azure Service Bus üzenet entitásait (várólisták, témakörök és előfizetések).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 4c4e554ada8c66b56d5d466c7becfc813701402a
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85339999"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Üzenetkezelési entitások felfüggesztése és újraaktiválása (Letiltás)

A várólisták, témakörök és előfizetések ideiglenesen fel lesznek függesztve. A felfüggesztés letiltott állapotba helyezi az entitást, amelyben az összes üzenet a tárolóban marad. Az üzenetek azonban nem távolíthatók el és nem vehetők fel, és a vonatkozó protokollok műveleteinek hibái.

Az entitások felfüggesztése általában sürgős adminisztratív okokból történik. Az egyik forgatókönyv egy olyan hibás fogadó üzembe helyezése, amely üzeneteket helyez el a várólistából, a feldolgozás sikertelen lesz, és még nem megfelelően végzi el az üzenetek telepítését és eltávolítását. Ha ezt a viselkedést diagnosztizálják, a várólistát le lehet tiltani a fogadáshoz, amíg a korrigált kód üzembe lett helyezve, és a hibás kód által okozott további adatvesztés megakadályozható.

A felfüggesztés vagy az újraaktiválás a felhasználó vagy a rendszer által végezhető el. A rendszerek csak olyan súlyos adminisztratív okok miatt felfüggesztik az entitásokat, mint például az előfizetés költségkeretének korlátozása. A felhasználó nem tudja újraaktiválni a rendszer által letiltott entitásokat, de a felfüggesztés oka miatt visszaállnak.

A portálon az adott entitás **Tulajdonságok** szakasza lehetővé teszi az állapot módosítását; az alábbi képernyőfelvételen egy üzenetsor váltógomb látható:

![][1]

A portál csak teljesen letiltja a várólistákat. A küldési és fogadási műveleteket külön is letilthatja a .NET-keretrendszer SDK-ban található Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API-kkal, vagy egy Azure Resource Manager sablonnal az Azure CLI vagy a Azure PowerShell használatával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="suspension-states"></a>Felfüggesztési állapotok

A várólistára beállítható állapotok a következők:

-   **Aktív**: a várólista aktív.
-   **Letiltva**: a várólista fel van függesztve.
-   **SendDisabled**: a várólista részlegesen fel van függesztve, és a fogadás engedélyezett.
-   **ReceiveDisabled**: a várólista részlegesen fel van függesztve, és a küldés engedélyezett.

Előfizetések és témakörök esetében csak az **aktív** és a **letiltott** lehetőség állítható be.

A [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) enumerálás olyan átmeneti állapotokat is meghatároz, amelyeket csak a rendszer adhat meg. A várólista letiltására szolgáló PowerShell-parancs az alábbi példában látható. Az újraaktiválási parancs egyenértékű, aktív értékre van állítva `Status` . **Active**

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>További lépések

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png


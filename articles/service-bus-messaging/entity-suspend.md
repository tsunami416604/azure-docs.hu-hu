---
title: Azure Service Bus – üzenetkezelő entitások felfüggesztése
description: Ez a cikk bemutatja, hogyan lehet ideiglenesen felfüggeszteni és újraaktiválni az Azure Service Bus üzenetentitásait (várólisták, témakörök és előfizetések).
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
ms.openlocfilehash: 7386932f19eee064926184eb17f5e92e30add98e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760385"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Üzenetkezelő entitások felfüggesztése és újraaktiválása (letiltás)

A várólisták, témakörök és előfizetések ideiglenesen felfüggeszthetők. A felfüggesztés letiltott állapotba helyezi az entitást, amelyben az összes üzenet a tárolóban marad. Az üzenetek azonban nem távolíthatók el és nem vehetők fel, és a megfelelő protokollműveletek hibákat eredményeznek.

Egy entitás felfüggesztése általában sürgős adminisztratív okokból történik. Az egyik forgatókönyv az, hogy üzembe helyezett egy hibás fogadó, amely leveszi az üzeneteket a várólistáról, nem működik feldolgozása, és mégis helytelenül befejezi az üzeneteket, és eltávolítja őket. Ha ezt a viselkedést diagnosztizálják, a várólista letiltható a fogadott, amíg a javított kód telepítve van, és a hibás kód által okozott további adatvesztés megelőzhető.

A felfüggesztést vagy az újraaktiválást a felhasználó vagy a rendszer is elvégezheti. A rendszer csak súlyos felügyeleti okok miatt függeszti fel az entitásokat, például az előfizetés költési korlátjának való leütésmiatt. A rendszer letiltott entitásokat a felhasználó nem tudja újraaktiválni, de a felfüggesztés okának kezelése esetén visszaáll.

A portálon a **tulajdonságok** szakasz az adott entitás lehetővé teszi az állapot módosítását; a következő képernyőképen egy várólista váltógombja látható:

![][1]

A portál csak a várólisták teljes letiltását engedélyezi. A küldési és fogadási műveleteket külön is letilthatja a Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API-k használatával a .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="suspension-states"></a>Felfüggesztési állapotok

A várólistához beállítható állapotok a következők:

-   **Aktív**: A várólista aktív.
-   **Letiltva**: A várólista fel van függesztve.
-   **SendDisabled**: A várólista részlegesen fel van függesztve, és a fogadás engedélyezett.
-   **ReceiveDisabled**: A várólista részlegesen fel van függesztve, és a küldés engedélyezett.

Előfizetések és témakörök esetén csak **az Aktív** és **a Letiltva** beállítás állítható be.

Az [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) enumerálás olyan átmeneti állapotokat is meghatároz, amelyeket csak a rendszer állíthat be. A következő példában megjelenik a PowerShell-parancs egy várólista letiltásához. Az újraaktiválási parancs `Status` egyenértékű az **Aktív beállítással.**

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Service Bus üzenetküldéséről, olvassa el az alábbi témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png


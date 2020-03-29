---
title: AMQP-hibák elhárítása az Azure Service Busban | Microsoft dokumentumok
description: Az Azure Service Bus használata során esetlegesen előforduló AMQP-hibák listáját és a hibák okát tartalmazza.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2019
ms.author: aschhab
ms.openlocfilehash: 85d24a9e7c753ec5dba80c4f259dd3fb51d9c14b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60402785"
---
# <a name="amqp-errors-in-azure-service-bus"></a>AMQP-hibák az Azure Service Busban
Ez a cikk az AMQP Azure Service Bus használatával kapcsolatos hibákat ismerteti. Ezek mind a szolgáltatás szokásos viselkedései. Elkerülheti őket, ha küldési/fogadási hívásokat kezdeményez a kapcsolaton/kapcsolaton, amely automatikusan újralétrehozza a kapcsolatot/kapcsolatot.

## <a name="link-is-closed"></a>A kapcsolat le van zárva 
A következő hibaüzenet jelenik meg, ha az AMQP-kapcsolat és -kapcsolat aktív, de a hivatkozást 10 percig nem történik hívás (például küldés vagy fogadás). Szóval, a kapcsolat zárva van. A kapcsolat még nyitva van.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>A kapcsolat levan zárva
A következő hibaüzenet jelenik meg az AMQP-kapcsolaton, ha a kapcsolat összes kapcsolata megszakadt, mert nem volt tevékenység (tétlen), és 5 percen belül nem jött létre új kapcsolat.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>A hivatkozás nem jön létre 
Ez a hibaüzenet akkor jelenik meg, ha új AMQP-kapcsolat jön létre, de az AMQP-kapcsolat létrehozását követő 1 percen belül nem jön létre hivatkozás.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az AMQP-ről és a Service Bus-ról, látogasson el az alábbi linkekre:

* [Service Bus AMQP – áttekintés]
* [AMQP 1.0 protokoll – útmutató]
* [AMQP a Windows Server service busszolgáltatásban]

[Service Bus AMQP – áttekintés]: service-bus-amqp-overview.md
[AMQP 1.0 protokoll – útmutató]: service-bus-amqp-protocol-guide.md
[AMQP a Windows Server service busszolgáltatásban]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)

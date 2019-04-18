---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 7b05f3d8bcca5f26161f4c362078fa134518cafd
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59689070"
---
## <a name="what-are-service-bus-topics-and-subscriptions"></a>Mik azok a Service Bus-üzenettémák és -előfizetések?
A Service Bus-üzenettémák és -előfizetések *közzétételi/előfizetési* modellt biztosítanak az üzenettovábbításhoz. Üzenettémák és előfizetések használata esetén az elosztott alkalmazások összetevői nem közvetlenül egymással kommunikálnak, hanem egy közvetítőként szolgáló üzenettémakörön keresztül.

![Az üzenettémakörök alapfogalmai](./media/howto-service-bus-topics/sb-topics-01.png)

Ellentétben a Service Bus-üzenetsorok, ahol minden üzenetet dolgoz fel egy-egy fogyasztó, üzenettémák és előfizetések közötti kommunikáció, a közzététel/előfizetés minta használatával "egy-a-többhöz" egyfajta biztosítanak. Egy üzenettémakörhöz több előfizetést is lehet regisztrálni. Ha egy üzenetet elküldenek egy témakörbe, az összes előfizetés számára elérhetővé válik, amelyek egymástól függetlenül kezelhetik és dolgozhatják fel az üzenetet.

Az egyes témakörökre való előfizetés egy virtuális üzenetsorra hasonlít, amely minden, a témakörnek elküldött üzenetről kap egy másolatot. Egy adott üzenettémához minden előfizetés-szűrési szabályokat lehet regisztrálni. Állapotszűrő szabályok lehetővé teszik annak szűrését vagy korlátozását, hogy melyik előfizetések által fogadott üzeneteket a témakörökbe melyik.

Service Bus-üzenettémák és előfizetések lehetővé teszik méretezhető és nagy mennyiségű üzenetet feldolgozni a számos felhasználótól és alkalmazások.

[Azure portal]: https://portal.azure.com
[create-namespace]: ./media/howto-service-bus-topics/create-namespace.png
[connection-info]: ./media/howto-service-bus-topics/connection-info.png
[connection-string]: ./media/howto-service-bus-topics/connection-string.png



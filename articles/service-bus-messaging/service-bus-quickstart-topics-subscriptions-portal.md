---
title: Rövid útmutató – az Azure portal segítségével hozza létre a Service Bus-üzenettémák és előfizetések |} A Microsoft Docs
description: Ez a rövid útmutatóban megismerheti, hogyan hozhat létre egy Service Bus-témakörbe, és az előfizetést az üzenettémára az Azure portal használatával.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: 2af346b2c9fa5c46593aa9421c3a762bda78dc2f
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610305"
---
# <a name="quickstart-use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Gyors útmutató: A témakör előfizetési és a egy Service Bus-témakör létrehozása az Azure portal használatával
Ebben a rövid útmutatóban használatával az Azure Portalon hozzon létre egy Service Bus-témakörbe, és hozzon létre előfizetéseket az adott üzenettémakörhöz. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Mik azok a Service Bus-üzenettémák és -előfizetések?
A Service Bus-üzenettémák és -előfizetések *közzétételi/előfizetési* modellt biztosítanak az üzenettovábbításhoz. Üzenettémák és előfizetések használata esetén az elosztott alkalmazások összetevői nem közvetlenül egymással kommunikálnak, hanem egy közvetítőként szolgáló üzenettémakörön keresztül.

![Az üzenettémakörök alapfogalmai](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Ellentétben a Service Bus-üzenetsorok, amelyben minden üzenetet dolgoz fel egy-egy fogyasztó, üzenettémák és előfizetések adjon meg egy-a-többhöz űrlap kommunikációs, a közzététel/előfizetés minta használatával. Egy üzenettémakörhöz több előfizetést is lehet regisztrálni. Ha egy üzenetet elküldenek egy témakörbe, az összes előfizetés számára elérhetővé válik, amelyek egymástól függetlenül kezelhetik és dolgozhatják fel az üzenetet. Az egyes témakörökre való előfizetés egy virtuális üzenetsorra hasonlít, amely minden, a témakörnek elküldött üzenetről kap egy másolatot. A témakör, amely lehetővé teszi annak szűrését vagy korlátozását, hogy melyik előfizetések által fogadott üzeneteket a témakörökbe melyik előfizetésenként alapon szűrési szabályokat lehet regisztrálni.

Service Bus-üzenettémák és előfizetések lehetővé teszi nagy mennyiségű üzenetet feldolgozni a felhasználók és alkalmazások nagy számú skálázhatja.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]



## <a name="next-steps"></a>További lépések
Megtudhatja, hogyan küldhet üzeneteket egy üzenettémát és egy reiceve keresztül egy előfizetés üzeneteket, tekintse meg a következő cikket: válassza ki a programozási nyelvet a tartalomjegyzékben. 

> [!div class="nextstepaction"]
> [Közzététel és előfizetés üzenetek](service-bus-dotnet-how-to-use-topics-subscriptions.md)

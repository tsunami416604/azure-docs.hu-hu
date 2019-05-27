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
ms.openlocfilehash: a392f8b11a7ab1ad72f4da289c54e34b022f1ea6
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990317"
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

> [!NOTE]
> A Service Bus-erőforrások is kezelhetők [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/). A Service Bus Explorer lehetővé teszi, hogy a felhasználók csatlakozni a Service Bus-névtér és üzenetküldési entitások felügyelete egyszerű módon. Az eszköz például importálás/exportálás funkció vagy tesztelhetik, témakör, üzenetsorok, előfizetések, relay-szolgáltatások, a notification hubs és események hubok speciális szolgáltatásokat biztosítja. 

## <a name="next-steps"></a>További lépések
Megtudhatja, hogyan üzenetek küldése egy üzenettémakörbe, és a egy előfizetés útján üzeneteket kapni, tekintse meg a következő cikket: válassza ki a programozási nyelvet a tartalomjegyzékben. 

> [!div class="nextstepaction"]
> [Közzététel és előfizetés üzenetek](service-bus-dotnet-how-to-use-topics-subscriptions.md)

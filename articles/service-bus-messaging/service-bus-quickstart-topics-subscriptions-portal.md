---
title: Az Azure Portal használata Service Bus-témakörök és -előfizetések létrehozásához
description: 'Rövid útmutató: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre egy Service Bus-témakört és előfizetéseket az Azure Portal használatával.'
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: quickstart
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: a1e330a62a58daaa3fb2a2e8758d14791a3208c1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76260820"
---
# <a name="quickstart-use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Rövid útmutató: Az Azure Portal használatával hozzon létre egy Service Bus-témakört és előfizetéseket a témakörhöz
Ebben a rövid útmutatóban az Azure Portal használatával hozzon létre egy Service Bus-témakört, majd hozzon létre előfizetéseket az adott témakörhöz. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Mik azok a Service Bus-üzenettémák és -előfizetések?
A Service Bus-üzenettémák és -előfizetések *közzétételi/előfizetési* modellt biztosítanak az üzenettovábbításhoz. Üzenettémák és előfizetések használata esetén az elosztott alkalmazások összetevői nem közvetlenül egymással kommunikálnak, hanem egy közvetítőként szolgáló üzenettémakörön keresztül.

![Az üzenettémakörök alapfogalmai](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Ellentétben a Service Bus-várólistákkal, amelyekben minden egyes üzenetet egyetlen fogyasztó dolgoz fel, a témakörök és az előfizetések egy-a-többhöz kommunikációs formát biztosítanak egy közzétételi/előfizetési minta használatával. Egy üzenettémakörhöz több előfizetést is lehet regisztrálni. Ha egy üzenetet elküldenek egy témakörbe, az összes előfizetés számára elérhetővé válik, amelyek egymástól függetlenül kezelhetik és dolgozhatják fel az üzenetet. Az egyes témakörökre való előfizetés egy virtuális üzenetsorra hasonlít, amely minden, a témakörnek elküldött üzenetről kap egy másolatot. Igény szerint előfizetésenként regisztrálhat szűrőszabályokat egy témakörhöz, amely lehetővé teszi, hogy szűrje vagy korlátozza, hogy mely témakör-előfizetések mely üzeneteket fogadják a témakörhöz.

A Service Bus-témakörök és -előfizetések lehetővé teszik, hogy nagyszámú üzenetet dolgozzon fel nagyszámú felhasználó és alkalmazás között.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> A Service Bus erőforrásait a [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)segítségével kezelheti. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy csatlakozzanak a Service Bus névtér és felügyeli az üzenetkezelési entitások egyszerű módon. Az eszköz speciális funkciókat biztosít, például importálási/exportálási funkciókat, vagy a témakör, a várólisták, az előfizetések, a továbbítási szolgáltatások, az értesítési központok és az eseményközpontok tesztelését. 

## <a name="next-steps"></a>További lépések
Ha meg szeretné tudni, hogyan küldhet üzeneteket egy témakörnek, és hogyan fogadhatja ezeket az üzeneteket előfizetéssel, olvassa el a következő cikket: válassza ki a programozási nyelvet a játékcsomagban. 

> [!div class="nextstepaction"]
> [Üzenetek közzététele és feliratkozás rájuk](service-bus-dotnet-how-to-use-topics-subscriptions.md)

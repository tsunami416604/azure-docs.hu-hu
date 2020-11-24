---
title: Service Bus üzenetsor létrehozásához használja a Azure Portal
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Service Bus névteret és egy várólistát a névtérben a Azure Portal használatával.
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 79dd751c43443790aafc494d89ad45e3b6705a64
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95799230"
---
# <a name="use-azure-portal-to-create-a-service-bus-namespace-and-a-queue"></a>Service Bus névtér és üzenetsor létrehozása Azure Portal használatával
Ez a rövid útmutató bemutatja, hogyan hozhat létre Service Bus névteret és üzenetsor-kezelést a [Azure Portal][Azure portal]használatával. Azt is bemutatjuk, hogyan kérheti le az olyan engedélyezési hitelesítő adatokat, amelyeket az ügyfélalkalmazás használhat a várólistára irányuló üzenetek küldéséhez/fogadásához. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha nem rendelkezik Azure-előfizetéssel, akkor a Kezdés előtt létrehozhat egy [ingyenes fiókot][] .

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="next-steps"></a>További lépések
Ebben a cikkben egy Service Bus névteret és egy várólistát hozott létre a névtérben. Ha meg szeretné tudni, hogyan küldhet/fogadhat üzeneteket a várólistára, tekintse meg az alábbi rövid útmutatók egyikét a **küldési és fogadási üzenetek** szakaszban. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[ingyenes fiók]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png

---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: ef6d5d22f70d5fff38f90b457a7c945ab59fc67c
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52331069"
---
## <a name="what-are-service-bus-topics-and-subscriptions"></a>Mik azok a Service Bus-üzenettémák és -előfizetések?
A Service Bus-üzenettémák és -előfizetések *közzétételi/előfizetési* modellt biztosítanak az üzenettovábbításhoz. Üzenettémák és előfizetések használata esetén az elosztott alkalmazások összetevői nem közvetlenül egymással kommunikálnak, hanem egy közvetítőként szolgáló üzenettémakörön keresztül.

![Az üzenettémakörök alapfogalmai](./media/howto-service-bus-topics/sb-topics-01.png)

Ellentétben a Service Bus-üzenetsorok, ahol minden üzenetet dolgoz fel egy-egy fogyasztó, üzenettémák és előfizetések közötti kommunikáció, a közzététel/előfizetés minta használatával "egy-a-többhöz" egyfajta biztosítanak. Egy üzenettémakörhöz több előfizetést is lehet regisztrálni. Ha egy üzenetet elküldenek egy témakörbe, az összes előfizetés számára elérhetővé válik, amelyek egymástól függetlenül kezelhetik és dolgozhatják fel az üzenetet.

Az egyes témakörökre való előfizetés egy virtuális üzenetsorra hasonlít, amely minden, a témakörnek elküldött üzenetről kap egy másolatot. Egy adott üzenettémához minden előfizetés-szűrési szabályokat lehet regisztrálni. Állapotszűrő szabályok lehetővé teszik annak szűrését vagy korlátozását, hogy melyik előfizetések által fogadott üzeneteket a témakörökbe melyik.

Service Bus-üzenettémák és előfizetések lehetővé teszik méretezhető és nagy mennyiségű üzenetet feldolgozni a számos felhasználótól és alkalmazások.

## <a name="create-a-namespace"></a>Névtér létrehozása
A Service Bus-üzenettémák és -előfizetések Azure-ban való használatához először létre kell hoznia egy *szolgáltatásnévteret*. A névtér egy hatókörkezelési tárolót biztosít a Service Bus erőforrásainak címzéséhez az alkalmazáson belül.

Névtér létrehozása:

1. Jelentkezzen be az [Azure Portalra][Azure portal].
2. A portál bal oldali navigációs panelén kattintson **erőforrás létrehozása**, majd kattintson a **vállalati integráció**, és kattintson a **a Service Bus**.
3. A **Névtér létrehozása** párbeszédpanelen adja meg a névtér nevét. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.
4. Miután ellenőrizte, hogy a névtér neve elérhető-e, válassza ki a tarifacsomagot (Basic, Standard vagy Prémium).
5. Az **Előfizetés** mezőben válassza ki azt az Azure-előfizetést, amelyben a névteret létre kívánja hozni.
6. Az a **erőforráscsoport** mezőben válasszon ki egy meglévő erőforráscsoportot, ahol a névtérben található, vagy hozzon létre egy újat.      
7. A **Hely** mezőben válassza ki azt az országot vagy régiót, amelyben a névtér üzemeltetve lesz.
   
    ![Névtér létrehozása][create-namespace]
8. Kattintson a **Létrehozás** gombra. A rendszer ekkor létrehozza és engedélyezi a névteret. Előfordulhat, hogy néhány percet várnia kell, amíg a rendszer kiosztja az erőforrásokat a fiókja számára.

### <a name="obtain-the-credentials"></a>Hitelesítő adatok beszerzése
1. A névterek listájában kattintson az újonnan létrehozott névtér nevére.
2. Az a **Service Bus-névtér** ablaktáblán kattintson a **megosztott elérési házirendek**.
3. Az a **megosztott elérési házirendek** ablaktáblán kattintson a **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. Az a **házirend: RootManageSharedAccessKey** ablaktáblán kattintson a Másolás gombra a következő gombra a **kapcsolati karakterlánc – elsődleges kulcs**, hogy másolja a kapcsolati karakterláncot a vágólapra későbbi használatra.
   
    ![connection-string][connection-string]

[Azure portal]: https://portal.azure.com
[create-namespace]: ./media/howto-service-bus-topics/create-namespace.png
[connection-info]: ./media/howto-service-bus-topics/connection-info.png
[connection-string]: ./media/howto-service-bus-topics/connection-string.png



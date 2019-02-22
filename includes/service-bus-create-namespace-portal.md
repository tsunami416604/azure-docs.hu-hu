---
title: fájl belefoglalása
description: fájl belefoglalása
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d976a1c5e9366069b82cff718593ce72d7ad8a08
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588942"
---
## <a name="create-a-namespace-in-the-azure-portal"></a>Névtér létrehozása az Azure Portalon
A Service Bus-üzenetküldési entitások Azure-ban való használatának megkezdéséhez először létre kell hoznia egy, az Azure-ban egyedi névvel rendelkező névteret. A névtér egy hatókörkezelési tárolót biztosít a Service Bus erőforrásainak címzéséhez az alkalmazáson belül.

Névtér létrehozása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
2. A portál bal oldali navigációs panelén válassza **+ erőforrás létrehozása**válassza **integrációs**, majd válassza ki **a Service Bus**.

    ![Erőforrás létrehozása integráció -> a Service Bus ->](./media/service-bus-create-namespace-portal/create-resource-service-bus-menu.png)
3. Az a **névtér létrehozása** párbeszédpanelen tegye a következőket: 
    1. Adjon meg egy **a névtér neve**. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.
    2. Válassza ki a tarifacsomagot (Basic, Standard vagy prémium) a névtérhez. Ha a használni kívánt [üzenettémák és előfizetések](../articles/service-bus-messaging/service-bus-queues-topics-subscriptions.md#topics-and-subscriptions), válassza a Standard vagy Premium. A témakörök és az előfizetések nem támogatottak az alapszintű tarifacsomagban.
    3. Ha bejelölte a **prémium** tarifacsomagban van, kövesse az alábbi lépéseket: 
        1. Adja meg, hány **üzenetkezelési egységek**. A prémium szintű erőforrás-elkülönítést, a CPU és memória szintjén biztosít, így minden számítási feladata elkülönítve fut. Ez az erőforrás-tárolónak egy üzenetkezelési egység neve. Egy prémium szintű névtér legalább egy üzenetkezelési egység van. Kiválaszthatja, 1, 2 vagy 4 üzenetkezelési egység minden prémium szintű Service Bus-névtér esetében. További információkért lásd: [Service Bus prémium szintű üzenetkezelés](../articles/service-bus-messaging/service-bus-premium-messaging.md).
        2. Adja meg, hogy, hogy a névtér **zónaredundáns**. A zone redudancy fokozott rendelkezésre állást biztosítja a replikák terjesztésével további költségek nélkül egy régión belül a rendelkezésre állási zónák között. További információkért lásd: [az Azure-beli rendelkezésre állási zónák](../articles/availability-zones/az-overview.md).
    4. A **előfizetés**, válassza ki az Azure-előfizetés, amelyben a névteret létre kívánja hozni.
    5. A **erőforráscsoport**, válasszon ki egy meglévő erőforráscsoportot, amelyben a névtér lesz élő, vagy hozzon létre egy újat.      
    6. A **hely**, válassza ki a régiót, amelyben a névtér üzemeltetve lesz.
    7. Kattintson a **Létrehozás** gombra. A rendszer ekkor létrehozza és engedélyezi a névteret. Előfordulhat, hogy néhány percet várnia kell, amíg a rendszer kiosztja az erőforrásokat a fiókja számára.
   
        ![Névtér létrehozása](./media/service-bus-create-namespace-portal/create-namespace.png)
4. Győződjön meg arról, hogy a service bus-névtér sikeres üzembe helyezését. Az értesítések megtekintéséhez válassza ki a **harang ikonra (riasztások)** az eszköztáron. Válassza ki a **az erőforráscsoport neve** az értesítésben a képen látható módon. Az erőforráscsoport, amely tartalmazza a service bus-névtér megjelenik.

    ![Központi telepítési riasztás](./media/service-bus-create-namespace-portal/deployment-alert.png)
5. Az a **erőforráscsoport** oldalon az erőforráscsoport, jelölje be a **service bus-névtér**. 

    ![Erőforráscsoport oldala – válassza ki a service bus-névtér](./media/service-bus-create-namespace-portal/resource-group-select-service-bus.png)
6. A service bus-névtér tekintse meg a kezdőlapján. 

    ![A service bus-névtér kezdőlapja](./media/service-bus-create-namespace-portal/service-bus-namespace-home-page.png)

## <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése 
Egy új névtér létrehozásával automatikusan létrejön egy kezdeti közös hozzáférésű jogosultságkódra (SAS) vonatkozó szabály egy elsődleges és egy másodlagos kulcsból álló kulcspárral, amelyek mindegyike teljes hozzáférést biztosít a névtér minden területéhez. Lásd: [Service Bus-hitelesítés és engedélyezés](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) a további szabályok létrehozásával kapcsolatos információkat, korlátozottabb jogokat a normál küldők és fogadók. Másolja az elsődleges és másodlagos kulcsot a névtérhez, kövesse az alábbi lépéseket: 

1. Kattintson az **Összes erőforrás** elemre, majd az újonnan létrehozott névtér nevére.
2. A névtér ablakában kattintson a **Megosztott elérési házirendek** elemre.
3. A **Megosztott elérési házirendek** képernyőn kattintson a **RootManageSharedAccessKey** elemre.
   
    ![connection-info](./media/service-bus-create-namespace-portal/connection-info.png)
4. Az a **házirend: RootManageSharedAccessKey** ablakban, a Másolás gombra a következő gombra a **elsődleges kapcsolati karakterlánc**, hogy másolja a kapcsolati karakterláncot a vágólapra későbbi használatra. Illessze be ezt az értéket a Jegyzettömbbe vagy egy másik ideiglenes helyre.
   
    ![connection-string](./media/service-bus-create-namespace-portal/connection-string.png)
5. A későbbi használat érdekében ismételje meg az előző lépést, és másolja ki és illessze be az **Elsődleges kulcs** értékét egy ideiglenes helyre.

<!--Image references-->


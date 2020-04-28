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
ms.openlocfilehash: dc80141d796b66dd7e610342166f7b88df58f530
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75927750"
---
## <a name="create-a-namespace-in-the-azure-portal"></a>Névtér létrehozása az Azure Portalon
A Service Bus-üzenetküldési entitások Azure-ban való használatának megkezdéséhez először létre kell hoznia egy, az Azure-ban egyedi névvel rendelkező névteret. A névtér egy hatókörkezelési tárolót biztosít a Service Bus erőforrásainak címzéséhez az alkalmazáson belül.

Névtér létrehozása:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com)
2. A portál bal oldali navigációs paneljén válassza az **+ erőforrás létrehozása**lehetőséget, válassza az **integráció**lehetőséget, majd válassza a **Service Bus**lehetőséget.

    ![Hozzon létre egy erőforrás-> integráció-> Service Bus](./media/service-bus-create-namespace-portal/create-resource-service-bus-menu.png)
3. A **névtér létrehozása** párbeszédpanelen hajtsa végre a következő lépéseket: 
    1. Adja meg **a névtér nevét**. A rendszer azonnal ellenőrzi, hogy a név elérhető-e. A névterek elnevezésére vonatkozó szabályok listáját lásd: [névtér létrehozása REST API](/rest/api/servicebus/create-namespace).
    2. Válassza ki a névtérhez tartozó díjszabási szintet (alapszintű, standard vagy prémium). Ha [témaköröket és előfizetéseket](../articles/service-bus-messaging/service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)szeretne használni, válassza a standard vagy a prémium lehetőséget. A témakörök és az előfizetések nem támogatottak az alapszintű tarifacsomagban.
    3. Ha a **prémium** szintű díjszabást választotta, kövesse az alábbi lépéseket: 
        1. Az **üzenetkezelési egységek**számának meghatározása. A prémium szint erőforrás-elkülönítést biztosít a processzor és a memória szintjén, így az egyes számítási feladatok elkülönítésben futnak. Ennek az erőforrás-tárolónak a neve üzenetkezelési egység. A prémium szintű névterek legalább egy üzenetkezelő egységgel rendelkeznek. Az egyes Service Bus prémium szintű névterekhez 1, 2 vagy 4 üzenetkezelési egység is kiválasztható. További információ: [Service Bus Premium Messaging](../articles/service-bus-messaging/service-bus-premium-messaging.md).
        2. Itt adhatja meg, hogy redundánsan kívánja-e a névtér **zónáját**. A zóna-redundancia nagyobb rendelkezésre állást biztosít azáltal, hogy a replikákat az egyik régióban lévő rendelkezésre állási zónák között terjeszti fel, külön díj nélkül. További információ: [rendelkezésre állási zónák az Azure-ban](../articles/availability-zones/az-overview.md).
    4. Az **előfizetés**mezőben válassza ki azt az Azure-előfizetést, amelyben létre kívánja hozni a névteret.
    5. Az **erőforráscsoport**mezőben válasszon ki egy meglévő erőforráscsoportot, amelyben a névtér él, vagy hozzon létre egy újat.      
    6. A **hely**mezőben válassza ki azt a régiót, amelyben a névteret üzemeltetni szeretné.
    7. Kattintson a **Létrehozás** gombra. A rendszer ekkor létrehozza és engedélyezi a névteret. Előfordulhat, hogy néhány percet várnia kell, amíg a rendszer kiosztja az erőforrásokat a fiókja számára.
   
        ![Névtér létrehozása](./media/service-bus-create-namespace-portal/create-namespace.png)
4. Győződjön meg arról, hogy a Service Bus-névtér üzembe helyezése sikeresen megtörtént. Az értesítések megtekintéséhez válassza a **harang ikont (riasztások)** az eszköztáron. Válassza ki az **erőforráscsoport nevét** az értesítésben a képen látható módon. Megjelenik a Service Bus-névteret tartalmazó erőforráscsoport.

    ![Üzembe helyezési riasztás](./media/service-bus-create-namespace-portal/deployment-alert.png)
5. Az erőforráscsoport **erőforráscsoport** lapján válassza ki a **Service Bus-névteret**. 

    ![Erőforráscsoport lap – a Service Bus-névtér kiválasztása](./media/service-bus-create-namespace-portal/resource-group-select-service-bus.png)
6. Megjelenik a Service Bus-névtér kezdőlapja. 

    ![A Service Bus-névtér kezdőlapja](./media/service-bus-create-namespace-portal/service-bus-namespace-home-page.png)

## <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése 
Egy új névtér létrehozásával automatikusan létrejön egy kezdeti közös hozzáférésű jogosultságkódra (SAS) vonatkozó szabály egy elsődleges és egy másodlagos kulcsból álló kulcspárral, amelyek mindegyike teljes hozzáférést biztosít a névtér minden területéhez. Tekintse meg a [Service Bus hitelesítés és engedélyezés](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) című témakört, amely arról nyújt tájékoztatást, hogyan hozhatók létre szabályok több korlátozott jogokkal a normál küldők és a fogadók számára. A névtér elsődleges és másodlagos kulcsainak másolásához kövesse az alábbi lépéseket: 

1. Kattintson az **Összes erőforrás** elemre, majd az újonnan létrehozott névtér nevére.
2. A névtér ablakában kattintson a **Megosztott elérési házirendek** elemre.
3. A **Megosztott elérési házirendek** képernyőn kattintson a **RootManageSharedAccessKey** elemre.
   
    ![connection-info](./media/service-bus-create-namespace-portal/connection-info.png)
4. A **Szabályzat: RootManageSharedAccessKey** ablakban az **Elsődleges kapcsolati sztring** melletti Másolás gombra kattintva másolja a kapcsolati sztringet a vágólapra későbbi használatra. Illessze be ezt az értéket a Jegyzettömbbe vagy egy másik ideiglenes helyre.
   
    ![connection-string](./media/service-bus-create-namespace-portal/connection-string.png)
5. A későbbi használat érdekében ismételje meg az előző lépést, és másolja ki és illessze be az **Elsődleges kulcs** értékét egy ideiglenes helyre.

<!--Image references-->


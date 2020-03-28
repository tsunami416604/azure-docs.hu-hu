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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75927750"
---
## <a name="create-a-namespace-in-the-azure-portal"></a>Névtér létrehozása az Azure Portalon
A Service Bus-üzenetküldési entitások Azure-ban való használatának megkezdéséhez először létre kell hoznia egy, az Azure-ban egyedi névvel rendelkező névteret. A névtér egy hatókörkezelési tárolót biztosít a Service Bus erőforrásainak címzéséhez az alkalmazáson belül.

Névtér létrehozása:

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com)
2. A portál bal oldali navigációs ablakában válassza a **+ Erőforrás létrehozása**lehetőséget, válassza az **Integráció**lehetőséget, majd a **Service Bus**lehetőséget.

    ![Erőforrás létrehozása -> Integráció -> Szolgáltatásbusz](./media/service-bus-create-namespace-portal/create-resource-service-bus-menu.png)
3. A **Névtér létrehozása** párbeszédpanelen tegye a következő lépéseket: 
    1. Adja meg **a névtér nevét.** A rendszer azonnal ellenőrzi, hogy a név elérhető-e. A névterek elnevezésére vonatkozó szabályok listáját a [Névtér REST API létrehozása című témakörben tetszetős.](/rest/api/servicebus/create-namespace)
    2. Válassza ki a névtér tarifacsomag (alapszintű, standard vagy prémium). Ha [témaköröket és előfizetéseket](../articles/service-bus-messaging/service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)szeretne használni, válassza a Standard vagy a Premium lehetőséget. A témakörök és az előfizetések nem támogatottak az alapszintű tarifacsomagban.
    3. Ha a **prémium díjszabási** szintet választotta, kövesse az alábbi lépéseket: 
        1. Adja meg az **üzenetegységek**számát. A prémium szint biztosítja az erőforrások elkülönítését a processzor és a memória szintjén, így minden számítási feladatok elszigetelten futnak. Ennek az erőforrás-tárolónak a neve üzenetkezelési egység. A prémium szintű névtér rendelkezik legalább egy üzenetkezelő egység. Minden Service Bus Premium-névtérhez 1, 2 vagy 4 üzenetküldési egységet választhat. További információ: [Service Bus Premium Messaging](../articles/service-bus-messaging/service-bus-premium-messaging.md).
        2. Adja meg, hogy **redundánssá**kívánja-e tenni a névtérzónát. A zónaredundancia fokozott rendelkezésre állást biztosít azáltal, hogy a replikákat egy régión belül egy régión belül, további költségek nélkül szétosztja a rendelkezésre állási zónák között. További információt az [Azure rendelkezésre állási zónái című témakörben talál.](../articles/availability-zones/az-overview.md)
    4. **Előfizetés esetén**válasszon egy Azure-előfizetést, amelyben létre szeretné hozni a névteret.
    5. Az **Erőforráscsoport csoport**ban válasszon egy meglévő erőforráscsoportot, amelyben a névtér fog élni, vagy hozzon létre egy újat.      
    6. A **Hely területen**válassza ki azt a régiót, ahol a névteret üzemeltetni kell.
    7. Kattintson a **Létrehozás** gombra. A rendszer ekkor létrehozza és engedélyezi a névteret. Előfordulhat, hogy néhány percet várnia kell, amíg a rendszer kiosztja az erőforrásokat a fiókja számára.
   
        ![Névtér létrehozása](./media/service-bus-create-namespace-portal/create-namespace.png)
4. Ellenőrizze, hogy a szolgáltatásbusz névtere sikeresen telepítve van-e. Az értesítések megtekintéséhez jelölje ki a **csengő ikont (Riasztások)** az eszköztáron. Válassza ki **az erőforráscsoport nevét** az értesítésben a képen látható módon. Megjelenik a szolgáltatásbusz névtere tartalmazó erőforráscsoport.

    ![Telepítési riasztás](./media/service-bus-create-namespace-portal/deployment-alert.png)
5. Az erőforráscsoport **Erőforráscsoport** lapján válassza ki a **szolgáltatásbusz névterét.** 

    ![Erőforráscsoport lap – válassza ki a szolgáltatásbusz névterét](./media/service-bus-create-namespace-portal/resource-group-select-service-bus.png)
6. A szolgáltatásbusz névterének kezdőlapja látható. 

    ![A szolgáltatásbusz névteréhez való kezdőlap](./media/service-bus-create-namespace-portal/service-bus-namespace-home-page.png)

## <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése 
Egy új névtér létrehozásával automatikusan létrejön egy kezdeti közös hozzáférésű jogosultságkódra (SAS) vonatkozó szabály egy elsődleges és egy másodlagos kulcsból álló kulcspárral, amelyek mindegyike teljes hozzáférést biztosít a névtér minden területéhez. A [Service Bus hitelesítése és engedélyezése](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) című témakörben talál tájékoztatást arendszeres feladók és fogadók korlátozottabb jogosultságokkal rendelkező szabályailétrehozásáról. A névtér elsődleges és másodlagos kulcsainak másolásához hajtsa végre az alábbi lépéseket: 

1. Kattintson az **Összes erőforrás** elemre, majd az újonnan létrehozott névtér nevére.
2. A névtér ablakában kattintson a **Megosztott elérési házirendek** elemre.
3. A **Megosztott elérési házirendek** képernyőn kattintson a **RootManageSharedAccessKey** elemre.
   
    ![connection-info](./media/service-bus-create-namespace-portal/connection-info.png)
4. A **Szabályzat: RootManageSharedAccessKey** ablakban az **Elsődleges kapcsolati sztring** melletti Másolás gombra kattintva másolja a kapcsolati sztringet a vágólapra későbbi használatra. Illessze be ezt az értéket a Jegyzettömbbe vagy egy másik ideiglenes helyre.
   
    ![connection-string](./media/service-bus-create-namespace-portal/connection-string.png)
5. A későbbi használat érdekében ismételje meg az előző lépést, és másolja ki és illessze be az **Elsődleges kulcs** értékét egy ideiglenes helyre.

<!--Image references-->


---
title: Felhő beállítása az Azure IoT Hub Device Provisioning Service-hez a Portalon | Microsoft Docs
description: IoT Hub automatikus eszközkiépítés az Azure Portalon
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ccc699a500cbaf20c9b90d71e7c730e617bc572c
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145536"
---
# <a name="configure-cloud-resources-for-device-provisioning-with-the-iot-hub-device-provisioning-service"></a>Felhőalapú erőforrások konfigurálása az IoT Hub Device Provisioning Service használatával történő eszközkiépítéshez

Ez az oktatóanyag bemutatja, hogyan állíthatja be a felhőt az automatikus eszközkiépítéshez az IoT Hub Device Provisioning Service használatával. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * IoT Hub Device Provisioning Service létrehozása és az azonosító hatókörének lekérése az Azure Portallal
> * IoT Hub létrehozása
> * Az IoT Hub csatolása a Device Provisioning Service-hez
> * A Device Provisioning Service kiosztási szabályzatának beállítása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-device-provisioning-service-instance-and-get-the-id-scope"></a>Device Provisioning Service-példány létrehozása és az azonosító hatókörének lekérése

Kövesse az alábbi lépéseket egy új Device Provisioning Service-példány létrehozásához.

1. Kattintson az Azure Portal bal felső sarkában az **Erőforrás létrehozása** gombra.
2. A keresőmezőbe írja be a **device provisioning** kifejezést. 
3. Kattintson az **IoT Hub Device Provisioning Service** elemre.
4. Töltse ki az **IoT Hub Device Provisioning Service** űrlapját a következő információkkal:
    
   | Beállítás       | Ajánlott érték | Leírás | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Name (Név)** | Bármely egyedi név | -- | 
   | **Előfizetés** | Az Ön előfizetése  | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
   | **Erőforráscsoport** | myResourceGroup | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. |
   | **Hely** | Bármely érvényes hely | A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket. |   

   ![Írja be a DPS alapvető adatait a portálon](./media/tutorial-set-up-cloud/create-iot-dps-portal.png)

5. Kattintson a **Create** (Létrehozás) gombra. A rendszer néhány pillanaton belül létrehozza a Device Provisioning Service-példányt, majd megjeleníti az **Áttekintés** lapot.
6. Az új szolgáltatáspéldány **Áttekintés** lapján másolja az **Azonosító hatóköre** értéket későbbi felhasználás céljából. Az érték a regisztrációs azonosítók azonosítására szolgál, továbbá garantálja a regisztrációs azonosító egyediségét.
7. Jegyezze fel a **Szolgáltatásvégpont** értéket is későbbi felhasználás céljából. 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Létrehozta az IoT Hubot, és rendelkezik az oktatóanyag további részeinek teljesítéséhez szükséges állomásnévvel és IoT Hub kapcsolati sztringgel.

## <a name="link-the-device-provisioning-service-to-an-iot-hub"></a>A Device Provisioning Service csatolása az IoT Hubhoz

A következő lépés a Device Provisioning Service és az IoT-központ összekapcsolása, hogy az IoT Hub Device Provisioning Service regisztrálni tudja az eszközöket a központba. A szolgáltatás csak olyan IoT-központokba tud eszközöket kiépíteni, amelyek össze vannak kapcsolva a Device Provisioning Service-szel. Kövesse az alábbi lépéseket.

1. Az **Összes erőforrás** lapon kattintson a korábban létrehozott Device Provisioning Service-példányra.
2. A Device Provisioning Service lapján kattintson a **Csatolt IoT-központok** lehetőségre.
3. Kattintson a **Hozzáadás** parancsra.
4. A **Hivatkozás hozzáadása IoT hubhoz** lapon adja meg az alábbi adatokat, majd kattintson a **Mentés**gombra:

    * **Előfizetés:** Győződjön meg arról, hogy az IoT hubot tartalmazó szolgáltatás van kiválasztva. Másik előfizetésben található IoT hubot is csatlakoztathat.
    * **IoT hub:** Válassza ki a Device Provisioning Service-példányhoz csatlakoztatni kívánt IoT hub nevét.
    * **Hozzáférési szabályzat:** Válassza az **iothubowner** hitelesítő adatait az IoT Hubra mutató kapcsolat kiépítésének használatához.

   ![A központ nevének csatolása a DPS-hez a portálon](./media/tutorial-set-up-cloud/link-iot-hub-to-dps-portal.png)

## <a name="set-the-allocation-policy-on-the-device-provisioning-service"></a>A Device Provisioning Service kiosztási szabályzatának beállítása

A kiosztási szabályzat egy IoT Hub Device Provisioning Service-beállítás, amely meghatározza az eszközök IoT-központokhoz való hozzárendelésének módját. Három támogatott kiosztási szabályzat létezik: 

1. **Legkisebb mértékű késleltetés**: Az eszközök abban az IoT-központban lesznek kiépítve, amely a legkisebb mértékű késleltetéssel rendelkezik az adott eszköz irányába.
2. **Egyenletesen súlyozott elosztás** (alapértelmezett): Az eszközök egyforma valószínűséggel lesznek kiépítve a csatolt IoT-központokban. Ez az alapértelmezett beállítás. Akkor érdemes megtartani, ha csak egy IoT-központban épít ki eszközöket. 
3. **Statikus konfigurálás a regisztrációs listán keresztül**: A regisztrációs listában meghatározott IoT-központ elsőbbséget élvez a Device Provisioning Service-szintű kiosztási szabályzattal szemben.

A kiosztási szabályzat beállításához kattintson a **Kiosztási szabályzat kezelése** lehetőségre a Device Provisioning Service lapján. Győződjön meg arról, hogy a kiosztási házirend **Egyenletesen súlyozott elosztás** értékre van állítva (ez az alapértelmezett). A módosítások elvégzése után kattintson a **Mentés** gombra.

![Kiosztási szabályzat kezelése](./media/tutorial-set-up-cloud/iot-dps-manage-allocation.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyűjtemény részét képező többi oktatóanyag erre az oktatóanyagra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal vagy az oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben az oktatóanyagban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti az Azure Portalon az oktatóanyaghoz létrehozott összes erőforrást.

1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az IoT Hub Device Provisioning Service-példányt. Az **Összes erőforrás** lap tetején kattintson a **Törlés** elemre.  
2. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az IoT Hubot. Az **Összes erőforrás** lap tetején kattintson a **Törlés** elemre.
 
## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * IoT Hub Device Provisioning Service létrehozása és az azonosító hatókörének lekérése az Azure Portallal
> * IoT Hub létrehozása
> * Az IoT Hub csatolása a Device Provisioning Service-hez
> * A Device Provisioning Service kiosztási szabályzatának beállítása

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan állíthatja be az eszközt a kiépítéshez.

> [!div class="nextstepaction"]
> [Eszköz beállítása a kiépítéshez](tutorial-set-up-device.md)

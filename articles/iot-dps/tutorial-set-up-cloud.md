---
title: "Beállítása felhőalapú Azure IoT Hub eszköz kiépítése szolgáltatáshoz portálon |} Microsoft Docs"
description: "Az IoT-központ automatikus eszköz kiépítése az Azure portálon"
services: iot-dps
keywords: 
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 088d127521ce89d3a82e30ad8797fe5746ae7e03
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-cloud-resources-for-device-provisioning-with-the-iot-hub-device-provisioning-service"></a>Felhőalapú erőforrások eszköz üzembe helyezéséhez, a szolgáltatással az IoT Hub eszköz kiépítése konfigurálása

Ez az oktatóanyag bemutatja, hogyan állíthat be a felhőben az IoT Hub-kiépítés szolgáltatást használó telepítés automatikus eszközhöz. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Használja az Azure-portálon az IoT Hub eszköz kiépítése szolgáltatás létrehozása, és az azonosító hatókör
> * IoT Hub létrehozása
> * Az IoT hub hivatkozásra az kiépítése szolgáltatáshoz
> * A foglalási házirendjének beállítása az eszköz kiépítése szolgáltatáshoz

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-the-azure-portal"></a>Jelentkezzen be az Azure portálra.

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-a-device-provisioning-service-instance-and-get-the-id-scope"></a>Egy eszköz kiépítése szolgáltatáspéldány létrehozása és az azonosító hatókör beolvasása

Kövesse az alábbi lépéseket egy új eszköz kiépítése szolgáltatáspéldány létrehozása.

1. Az Azure portál bal felső sarkában kattintson **új**.
2. Írja be a keresőmezőbe, **eszköz kiépítése**. 
3. Kattintson a **IoT Hub eszköz-üzembehelyezési szolgáltatás**.
4. Töltse ki a **IoT Hub eszköz kiépítése szolgáltatáshoz** képernyőn a következő információkat:
    
   | Beállítás       | Ajánlott érték | Leírás | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Name (Név)** | Egy egyedi nevet | -- | 
   | **Előfizetés** | Az Ön előfizetése  | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
   | **Erőforráscsoport** | myResourceGroup | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. |
   | **Hely** | Bármely érvényes hely | A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket. |   

   ![Adja meg a terjesztési pontok alapvető adatait a portálon](./media/tutorial-set-up-cloud/create-iot-dps-portal.png)

5. Kattintson a **Create** (Létrehozás) gombra.
6. A *azonosító hatókör* regisztrációs azonosítók azonosítására szolgál, és biztosítja, hogy a regisztrációs Azonosítót is egyedi garanciát. Ez az érték letöltéséhez kattintson **áttekintése** megnyitásához a **Essentials** lapon az eszköz kiépítése szolgáltatáshoz. Másolás a **azonosító hatókör** érték egy ideiglenes helyre későbbi használatra.
7. Is jegyezze fel a **szolgáltatásvégpont** értékét, vagy másolja azt egy ideiglenes helyre későbbi használatra. 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Létrehozta a IoT Hubot, és rendelkezik az oktatóanyag további részeinek teljesítéséhez szükséges állomásnévvel és IoT Hub kapcsolati karakterlánccal.

## <a name="link-the-device-provisioning-service-to-an-iot-hub"></a>Az eszköz kiépítése szolgáltatáshoz kapcsolódik az IoT-központ

A következő lépés, hogy az eszköz kiépítése szolgáltatáshoz, és az IoT hub hivatkozásra, hogy az IoT Hub-kiépítés szolgáltatást regisztrálhatja az eszközöket, hogy hubhoz. A szolgáltatás csak építhető ki az IoT-központok az kiépítése szolgáltatáshoz kapcsolódó eszközök. Kövesse az alábbi lépéseket.

1. Az a **összes erőforrás** lapján kattintson a korábban létrehozott eszköz kiépítése Service-példány.
2. Az eszköz kiépítése szolgáltatáshoz oldalon kattintson **csatolt IoT-központok**.
3. Kattintson az **Add** (Hozzáadás) parancsra.
4. Az a **hivatkozás hozzáadása az IoT hubhoz** lapon, a megfelelő választógomb segítségével meghatározhatja, hogy a csatolt IoT-központ az aktuális előfizetésben, vagy egy másik előfizetésben található. Ezután válassza ki az IoT-központ nevét, a **IoT-központ** mezőbe.
5. Kattintson a **Save** (Mentés) gombra.

   ![Hivatkozás a központ nevét a terjesztési pontok, a portál összekapcsolása](./media/tutorial-set-up-cloud/link-iot-hub-to-dps-portal.png)

## <a name="set-the-allocation-policy-on-the-device-provisioning-service"></a>A foglalási házirendjének beállítása az eszköz kiépítése szolgáltatáshoz

Az-elosztási házirend egy IoT Hub eszköz kiépítése szolgáltatáshoz beállítás határozza meg, hogyan eszközök hozzá vannak-e rendelve az IoT-központ. Számos három támogatott elosztási házirendeket. 

1. **Legkisebb mértékű késleltetést**: eszközökhöz a hub alapján a legkisebb mértékű késleltetést, az eszköznek az IoT hubhoz törlődnek.
2. **Terjesztési egyenletesen súlyozott** (alapértelmezett): csatolt IoT-központok valószínűleg egyaránt számukra kiosztott eszközök. Ez az alapértelmezett beállítás. Ha csak egy IoT-központ eszközök kiépíteni, ezzel a beállítással tárolhatja. 
3. **A beléptetési listán keresztül statikus konfigurációs**: a regisztráció listán a kívánt IoT-központ megadását prioritást élvez az eszközök kiépítését szolgáltatásiszint-elosztási házirend.

A foglalási házirend beállítása a eszköz kiépítése szolgáltatáshoz lapon kattintson a **elosztási házirend kezelése**. Győződjön meg arról, hogy a Foglalás házirend beállítása **egyenletesen súlyozott terjesztési** (alapértelmezett). Ha bármilyen módosításhoz kattintson **mentése** Ha elkészült.

![Elosztási házirend kezelése](./media/tutorial-set-up-cloud/iot-dps-manage-allocation.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyűjteményben lévő többi oktatóanyag ebben az oktatóanyagban épül. Ha azt tervezi, a későbbi gyors üzembe helyezések vagy az oktatóanyagok munka folytatásához, üríti az oktatóanyagban szereplő létrejött erőforrásokat. Ha nem tervezi folytatja, ez az oktatóanyag az Azure-portálon létrehozott összes erőforrás törléséhez használja a következő lépéseket.

1. Az Azure-portálon a bal oldali menüben kattintson a **összes erőforrás** , és válassza a IoT Hub eszköz kiépítése szolgáltatáshoz példányát. Felső részén a **összes erőforrás** kattintson **törlése**.  
2. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az IoT Hubot. Felső részén a **összes erőforrás** kattintson **törlése**.
 
## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Használja az Azure-portálon az IoT Hub eszköz kiépítése szolgáltatás létrehozása, és az azonosító hatókör
> * IoT Hub létrehozása
> * Az IoT hub hivatkozásra az kiépítése szolgáltatáshoz
> * A foglalási házirendjének beállítása az eszköz kiépítése szolgáltatáshoz

Továbblépés a következő oktatóanyag áttekintésével megismerheti, hogyan állíthatja be az eszköz üzembe helyezéséhez.

> [!div class="nextstepaction"]
> [Kialakítási eszköz beállítása](tutorial-set-up-device.md)

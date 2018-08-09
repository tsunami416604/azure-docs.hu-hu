---
title: Eszközkiépítés beállítása az Azure Portalon | Microsoft Docs
description: Azure gyors üzembe helyezés – Azure IoT Hub eszközkiépítési szolgáltatás üzembe helyezése az Azure Portalon
author: wesmc7777
ms.author: wesmc
ms.date: 07/12/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: f4bae9ba41f61223a8e18e88e30b8fbf89eff1a9
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39527592"
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal

Ezek a lépések azt mutatják be, hogyan történik az Azure felhőbeli erőforrások üzembe helyezése a portálon az eszközkiépítéshez. A cikkben ismertetett lépésekkel a következők hajthatók végre: az IoT Hub és egy új IoT Hub Device Provisioning szolgáltatás létrehozása, valamint a két szolgáltatás összekapcsolása. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]


## <a name="create-a-new-instance-for-the-iot-hub-device-provisioning-service"></a>Új példány létrehozása az IoT Hub eszközkiépítési szolgáltatáshoz

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.

2. *Keresse meg a Marketplace piactéren* az **eszközkiépítési szolgáltatást**. Válassza az **IoT Hub Device Provisioning Service** elemet, és kattintson a **Létrehozás** gombra. 

3. Adja meg az alábbi információkat az új Device Provisioning Service-példányra vonatkozóan, és kattintson a **Létrehozás** gombra.

    * **Név:** Adjon egy egyedi nevet az új Device Provisioning Service-példánynak. Ha a megadott név elérhető, egy zöld pipa jelenik meg.
    * **Előfizetés:** Válassza ki a Device Provisioning Service-példány létrehozásához használni kívánt előfizetést.
    * **Erőforráscsoport:** A mezőben létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy meglévőt, amely az új példányt tartalmazza majd. Válassza ugyanazt az erőforráscsoportot, amely a fent létrehozott IoT Hubot tartalmazza, például: **TestResources**. Ha az összes kapcsolódó erőforrást egy csoportban helyezi el, akkor mindet együtt kezelheti. Az erőforráscsoport törlése például az adott csoportban található összes erőforrást is törli. További információkat az [Azure-erőforrások erőforráscsoportokkal való kezeléséről](../azure-resource-manager/resource-group-portal.md) szóló cikkben olvashat.
    * **Hely:** Válassza ki azt a helyet, amely az eszközökhöz legközelebb esik.
    * **Rögzítés az irányítópulton:** Jelölje be ezt a jelölőnégyzetet a példány az irányítópulton történő rögzítéséhez, hogy könnyebben megtalálhassa.

    ![Írja be a DPS példány alapvető adatait a portálpanelen](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. A szolgáltatás sikeres üzembe helyezése után automatikusan megnyílik annak összefoglalás panelje.


## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Az IoT Hub és az eszközkiépítési szolgáltatás csatolása

Ebben a szakaszban egy konfigurációt fog hozzáadni az irányítópulton a Device Provisioning Service-példányhoz. A konfiguráció állítja be azt az IoT Hubot, amelyen az eszközök létesítése megtörténik.

1. Kattintson az Azure Portal bal oldali menüjében lévő **Minden erőforrás** gombra. Válassza ki az eszközkiépítési szolgáltatás előző szakaszban létrehozott példányát.  

2. Az eszközkiépítési szolgáltatás összefoglalás panelén válassza a **Csatolt IoT Hubok** lehetőséget. Kattintson a felül látható **+ Hozzáadás** gombra. 

3. A **Hivatkozás hozzáadása IoT-központhoz** lapon adja meg az alábbi adatokat az új Device Provisioning Service-példány egy IoT Hubhoz történő hozzákapcsolásához. Ezután kattintson a **Mentés** gombra. 

    * **Előfizetés:** Válassza ki az új Device Provisioning Service-példányhoz kapcsolni kívánt IoT Hubot tartalmazó előfizetést.
    * **IoT Hub:** Válassza ki az új Device Provisioning Service-példányhoz kapcsolni kívánt IoT Hubot.
    * **Hozzáférési szabályzat:** Válassza az **iothubowner** hitelesítő adatait az IoT Hubra mutató kapcsolat kiépítéséhez.  

    ![A hub nevének csatolása a DPS példányhoz a portálpanelen](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Most látnia kell a kiválasztott hubot a **Csatolt IoT Hubok** panelen. Lehetséges, hogy a **Frissítés** gombra kell kattintania az **Összekapcsolt IoT-központok** megjelenítéséhez.



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal vagy az oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti az Azure Portalon a rövid útmutatóhoz létrehozott összes erőforrást.

1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az eszközkiépítési szolgáltatást. Az **Összes erőforrás** panel felső részén kattintson a **Törlés** elemre.  
2. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az IoT Hubot. Az **Összes erőforrás** panel felső részén kattintson a **Törlés** elemre.  

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban IoT Hubot és eszközkiépítési szolgáltatás példányt helyezett üzembe, és csatolta a két erőforrást. Ha szimulált eszköz kiépítéséhez szeretné használni ezt a beállítást, folytassa a szimulált eszköz létrehozásának rövid útmutatójával.

> [!div class="nextstepaction"]
> [Szimulált eszköz létrehozásának rövid útmutatója](./quick-create-simulated-device.md)

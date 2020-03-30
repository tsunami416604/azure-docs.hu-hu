---
title: IoT Hub-eszközkiépítési szolgáltatás beállítása az Azure Portalon
description: Rövid útmutató – Az Azure IoT Hub-eszközkiépítési szolgáltatás (DPS) beállítása az Azure Portalon
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 765b7e5bb7341974a6a16d3f1ed0bcabf415f9f3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76029176"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Rövid útmutató: Az IoT Hub-eszközkiépítési szolgáltatás beállítása az Azure Portalon

Az IoT Hub-eszközkiépítési szolgáltatás az IoT Hubsegítségével lehetővé teszi a nulla érintéses, just-in-time kiépítése a kívánt IoT hub emberi beavatkozás nélkül, amely lehetővé teszi az ügyfelek számára, hogy több millió IoT-eszközök biztonságos és méretezhető módon. Az Azure IoT Hub-eszközkiépítési szolgáltatás támogatja az IoT-eszközöket TPM, szimmetrikus kulcs és X.509 tanúsítvány-hitelesítésekkel. További információkért tekintse meg az [IoT Hub-eszközkiépítési szolgáltatás áttekintését.](./about-iot-dps.md)

Ebben a rövid útmutatóban megtudhatja, hogyan állíthatja be az IoT Hub-eszközkiépítési szolgáltatást az Azure Portalon az eszközök kiépítéséhez az alábbi lépésekkel:
> [!div class="checklist"]
> * IoT-központ létrehozása az Azure Portalon
> * IoT Hub Device Provisioning Service létrehozása és az azonosító hatókörének lekérése az Azure Portallal
> * Az IoT Hub csatolása a Device Provisioning Service-hez


Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.


## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="create-a-new-iot-hub-device-provisioning-service"></a>Új IoT Hub-eszközkiépítési szolgáltatás létrehozása

1. Ismét válassza a **+ Erőforrás létrehozása** gombot.

2. *Keressen a Piactéren* az **eszközkiépítési szolgáltatásért.** Válassza az **IoT Hub-eszközkiépítési szolgáltatás,** és nyomja meg a **Létrehozás** gombot. 

3. Adja meg a következő információkat az új eszközkiépítési szolgáltatás példányához, és nyomja meg a **Create (Létrehozás) lehetőséget.**

    * **Név:** Adjon meg egy egyedi nevet az új eszközkiépítési szolgáltatás példánya. Ha a megadott név elérhető, egy zöld pipa jelenik meg.
    * **Előfizetés:** Válassza ki az eszközkiépítési szolgáltatás példány létrehozásához használni kívánt előfizetést.
    * **Erőforráscsoport:** A mezőben létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy meglévőt, amely az új példányt tartalmazza majd. Válassza ugyanazt az erőforráscsoportot, amely a fent létrehozott IoT Hubot tartalmazza, például: **TestResources**. Ha az összes kapcsolódó erőforrást egy csoportban helyezi el, akkor mindet együtt kezelheti. Az erőforráscsoport törlése például az adott csoportban található összes erőforrást is törli. További információt az [Azure Resource Manager erőforráscsoportok kezelése](../azure-resource-manager/management/manage-resource-groups-portal.md)című témakörben talál.
    * **Helyszín:** Válassza ki az eszközökhöz legközelebb eső helyet.

      ![Adja meg az eszközkiépítési szolgáltatás példányával kapcsolatos alapvető információkat a portál panelen](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Válassza ki az értesítési gombot az erőforráspéldány létrehozásának figyeléséhez. A szolgáltatás sikeres üzembe helyezése után válassza a **Rögzítés az irányítópulton**lehetőséget, majd **az Ugrás az erőforrásra**lehetőséget.

    ![Az üzembehelyezési értesítés monitorozása](./media/quick-setup-auto-provision/pin-to-dashboard.png)

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Az IoT-központ és az eszközkiépítési szolgáltatás összekapcsolása

Ebben a szakaszban egy konfigurációt ad hozzá az eszközkiépítési szolgáltatás példányához. A konfiguráció állítja be azt az IoT Hubot, amelyen az eszközök létesítése megtörténik.

1. Válassza a **Minden erőforrás** gombot az Azure Portal bal oldali menüjében. Válassza ki az eszközkiépítési szolgáltatás előző szakaszban létrehozott példányát. 

    Ha a menü a portál beállításaiban a Dokkolt mód **helyett** a **Dokkolt** mód használatával van konfigurálva, a bal felső sarokban lévő 3 vonalra kell kattintania a portál menüjének megnyitásához a bal oldalon.  

2. Az Eszközkiépítési szolgáltatás menüjében válassza a **Csatolt IoT-központok lehetőséget.** Nyomja meg a **+ Add** gombot látható a tetején. 

3. Az **IoT hubhoz való hivatkozás hozzáadása** lapon adja meg a következő információkat az új eszközkiépítési szolgáltatás példányának egy IoT-központhoz való csatolásához. Ezután nyomja meg **a Mentés (Mentés) (Mentés) lenyom** 

    * **Előfizetés:** Válassza ki az új eszközkiépítési szolgáltatáspéldányhoz kapcsolni kívánt IoT-központot tartalmazó előfizetést.
    * **Sok elosztó:** Válassza ki az IoT hub ot az új eszközkiépítési szolgáltatáspéldányhoz való kapcsolódáshoz.
    * **Hozzáférési szabályzat:** Válassza az **iothubowner** hitelesítő adatait az IoT Hubra mutató kapcsolat kiépítéséhez.  

      ![A hub nevének csatolása az eszközkiépítési szolgáltatás példányához a portálpanelen](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Most látnia kell a kiválasztott hubot a **Csatolt IoT Hubok** panelen. Előfordulhat, hogy meg kell jelenjenek a **Frissítés gombra,** hogy megjelenjenek.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal vagy az oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti az Azure Portalon a rövid útmutatóhoz létrehozott összes erőforrást.

1. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** lehetőséget, majd válassza ki az eszközkiépítési szolgáltatást. Az eszköz részletes ablaktáblájának tetején válassza a **Törlés**lehetőséget.  
2. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** lehetőséget, majd válassza ki az IoT-központot. A hub részletes ablaktáblájának tetején válassza a **Törlés**lehetőséget.  

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy IoT-központot és egy eszközkiépítési szolgáltatáspéldányt, és összekapcsolta a két erőforrást. Ha meg szeretné tudni, hogyan használhatja ezt a beállítást egy szimulált eszköz kiépítési, folytassa a rövid útmutatót a szimulált eszköz létrehozásához.

> [!div class="nextstepaction"]
> [Gyorsútmutató szimulált eszköz létrehozásához](./quick-create-simulated-device-symm-key.md)

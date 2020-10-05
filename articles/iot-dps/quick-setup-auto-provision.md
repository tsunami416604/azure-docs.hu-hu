---
title: Gyors útmutató – IoT Hub Device Provisioning Service beállítása a Azure Portal
description: Rövid útmutató – az Azure IoT Hub Device Provisioning Service (DPS) beállítása a Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 644635d1ab7b0a43c8df3e10bbbd02d1ea67a94a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91297232"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Rövid útmutató: a IoT Hub Device Provisioning Service beállítása a Azure Portal

A IoT Hub Device Provisioning Service a IoT Hub használatával lehetővé teheti a nulla érintéses, igény szerinti üzembe helyezést a kívánt IoT hubhoz anélkül, hogy emberi beavatkozásra lenne szükség, ami lehetővé teszi, hogy az ügyfelek több millió IoT-eszköz biztonságos és skálázható módon legyenek kiépítve. Az Azure IoT Hub Device Provisioning Service a TPM, a szimmetrikus kulcs és az X. 509 tanúsítvány-hitelesítéssel rendelkező IoT-eszközöket támogatja. További információkért tekintse meg a [IoT hub Device Provisioning Service áttekintése](./about-iot-dps.md) című témakört.

Ebből a rövid útmutatóból megtudhatja, hogyan állíthatja be a IoT Hub Device Provisioning Service az Azure Portalon az eszközök kiépítés céljából az alábbi lépésekkel:

* IoT Hub létrehozásához használja a Azure Portal
* IoT Hub Device Provisioning Service létrehozása és az azonosító hatókörének lekérése az Azure Portallal
* Az IoT Hub csatolása a Device Provisioning Service-hez


Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.


## <a name="create-an-iot-hub"></a>IoT-központ létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="create-a-new-iot-hub-device-provisioning-service"></a>Új IoT Hub Device Provisioning Service létrehozása

1. Kattintson ismét az **+ erőforrás létrehozása** gombra.

2. *Keresse meg a piactéren* az **eszköz kiépítési szolgáltatását**. Válassza a **IoT hub Device Provisioning Service** lehetőséget, és nyomja le a **Létrehozás** gombot. 

3. Adja meg az alábbi adatokat az új eszköz kiépítési szolgáltatásának példányához, és kattintson a **create (létrehozás**).

    * **Név:** Adjon egyedi nevet az új eszköz-kiépítési szolgáltatás példányának. Ha a megadott név elérhető, egy zöld pipa jelenik meg.
    * **Előfizetés:** Válassza ki azt az előfizetést, amelyet az eszköz kiépítési szolgáltatási példányának létrehozásához használni kíván.
    * **Erőforráscsoport:** A mezőben létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy meglévőt, amely az új példányt tartalmazza majd. Válassza ugyanazt az erőforráscsoportot, amely a fent létrehozott IoT Hubot tartalmazza, például: **TestResources**. Ha az összes kapcsolódó erőforrást egy csoportban helyezi el, akkor mindet együtt kezelheti. Az erőforráscsoport törlése például az adott csoportban található összes erőforrást is törli. További információ: [Azure Resource Manager erőforráscsoportok kezelése](../azure-resource-manager/management/manage-resource-groups-portal.md).
    * **Hely:** Válassza ki az eszközök legközelebbi helyét.

      ![Adja meg az eszköz kiépítési szolgáltatási példányának alapvető adatait a portál paneljén.](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Válassza az értesítés gombot az erőforrás-példány létrehozásának figyeléséhez. A szolgáltatás sikeres üzembe helyezését követően válassza a **rögzítés az irányítópulton**lehetőséget, majd **nyissa meg az erőforrást**.

    ![Az üzembehelyezési értesítés monitorozása](./media/quick-setup-auto-provision/pin-to-dashboard.png)

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Az IoT hub és az eszköz kiépítési szolgáltatásának összekapcsolása

Ebben a szakaszban egy konfigurációt ad hozzá az eszköz kiépítési szolgáltatásának példányához. A konfiguráció állítja be azt az IoT Hubot, amelyen az eszközök létesítése megtörténik.

1. Válassza a **minden erőforrás** gombot a Azure Portal bal oldali menüjében. Válassza ki az eszközkiépítési szolgáltatás előző szakaszban létrehozott példányát. 

    Ha a menü úgy van beállítva, hogy a portál beállításai között a **dokkolt** **mód helyett a** nyílra van konfigurálva, akkor a bal oldalon található portál menü megnyitásához a bal felső sarokban található 3 sorra kell kattintania.  

2. Az eszköz kiépítési szolgáltatásának menüjében válassza a **csatolt IoT hubok**elemet. Nyomja meg a felül látható **+ Hozzáadás** gombot. 

3. Az **IoT hub hozzáadása** lapon adja meg a következő információkat az új eszköz kiépítési szolgáltatás példányának IoT hubhoz való összekapcsolásához. Ezután nyomja meg a **Mentés gombot**. 

    * **Előfizetés:** Válassza ki azt az előfizetést, amely a IoT hubot tartalmazza, amelyhez csatolni szeretné az új eszköz kiépítési szolgáltatásának példányát.
    * **IOT hub:** Válassza ki az IoT hubot az új eszköz kiépítési szolgáltatásának példánnyal való összekapcsoláshoz.
    * **Hozzáférési szabályzat:** Válassza az **iothubowner** hitelesítő adatait az IoT Hubra mutató kapcsolat kiépítéséhez.  

      ![A központ nevének összekapcsolása az eszköz kiépítési szolgáltatásának példányához a portál paneljén](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Most látnia kell a kiválasztott hubot a **Csatolt IoT Hubok** panelen. Előfordulhat, hogy a **frissítését** kell megtalálnia a megjelenítéshez.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal vagy az oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti az Azure Portalon a rövid útmutatóhoz létrehozott összes erőforrást.

1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az eszköz kiépítési szolgáltatását. Az eszköz részletes paneljének tetején válassza a **Törlés**lehetőséget.  
2. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az IoT hubot. A hub részletes paneljének tetején válassza a **Törlés**lehetőséget.  

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy IoT hubot és egy eszköz kiépítési szolgáltatási példányát, és összekapcsolta a két erőforrást. Ha szeretné megtudni, hogyan lehet szimulált eszközt kiépíteni a telepítővel, folytassa a szimulált eszköz létrehozására szolgáló rövid útmutatóval.

> [!div class="nextstepaction"]
> [Szimulált eszköz létrehozásának rövid útmutatója](./quick-create-simulated-device-symm-key.md)

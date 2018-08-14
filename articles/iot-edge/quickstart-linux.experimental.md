---
title: Az Azure IoT Edge szimulálása Linuxon | Microsoft Docs
description: Ebben a rövid útmutatóban megismerheti, hogyan helyezhet üzembe távolról előre összeállított kódokat egy IoT Edge-eszközön.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 07/02/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: dfbe931bbe5887e9c0545558c4d2b2565718dd0a
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578490"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>Rövid útmutató: Az első IoT Edge-modul üzembe helyezése x64-es Linux-eszközön

Az Azure IoT Edge segítségével elemzéseket és adatfeldolgozást végezhet el az eszközén anélkül, hogy az összes adatot a felhőbe kellene továbbítania. Az IoT Edge-oktatóanyagok ismertetik, hogyan helyezhetők üzembe a különböző típusú modulok, de először rendelkeznie kell egy eszközzel a teszteléshez. 

Ennek a rövid útmutatónak a segítségével megtanulhatja az alábbiakat:

1. IoT Hub létrehozása
2. IoT Edge-eszköz regisztrálása az IoT Hubon
3. Az IoT Edge-futtatókörnyezet elindítása.
4. Modul távoli üzembe helyezése IoT Edge-eszközön.

![Bemutató architektúra][2]

Ebben a rövid útmutatóban Linux rendszerű számítógépét vagy virtuális gépét IoT Edge-eszközzé alakíthatja. Ezután egy modult helyezhet üzembe az eszközén az Azure Portalról. A jelen rövid útmutatóban üzembe helyezett modul egy szimulált érzékelő, amely hőmérséklet-, páratartalom- és nyomásadatokat állít elő. A további Azure IoT Edge-oktatóanyagok az itt elvégzett munkára építkeznek olyan modulok üzembe helyezésével, amelyek a szimulált adatok elemzésével üzleti megállapításokat hoznak létre. 

Ha nem rendelkezik aktív Azure-előfizetéssel, kezdetnek hozzon létre egy [ingyenes fiókot][lnk-account].

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató egy linuxos gépet használ IoT Edge-eszközként. Ha nincs még Linux rendszerű virtuális gépe teszteléshez, kövesse a [Linux rendszerű virtuális gép az Azure Portalon történő létrehozását](../virtual-machines/linux/quick-create-portal.md) ismertető cikk utasításait. 
* Nem kell követnie a webkiszolgáló telepítését és futtatását bemutató lépéseket. Amint csatlakozott a virtuális gépéhez, nem kell követnie a további lépéseket.  
* Hozza létre a virtuális gépét egy új erőforráscsoportban, amelyet használhat a rövid útmutatóhoz létrehozott többi erőforrás létrehozásánál. Adjon neki egy könnyen beazonosítható segítő nevet (pl.: *IoTEdgeResources*). 
* Az IoT Edge teszteléséhez nincs szükség nagyon nagy virtuális gépre. A **B1ms** méret például elegendő. 

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

A rövid útmutató első lépéseként hozza létre az IoT Hubot az Azure Portalon.
![IoT Hub létrehozása][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>IoT Edge-eszköz regisztrálása

Regisztráljon egy IoT Edge-eszközt az újonnan létrehozott IoT Hubon.
![Eszköz regisztrálása][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]


## <a name="install-and-start-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet telepítése és elindítása

Telepítse és indítsa el eszközén az Azure IoT Edge-futtatókörnyezetet. 
![Eszköz regisztrálása][5]

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Három összetevőből áll. Az **IoT Edge biztonsági démon** az Edge-eszközök indulásakor lép működésbe, és az IoT Edge-ügynök elindításával elvégzi az eszköz rendszerindítását. Az **IoT Edge-ügynök** a modulok üzembe helyezését és monitorozását segíti az IoT Edge-eszközön, beleértve az IoT Edge-központot is. Az **IoT Edge-központ** az IoT Edge-eszközön lévő modulok, valamint az eszköz és az IoT Hub közötti kommunikációt kezeli. 

Hajtsa végre a következő lépéseket a rövid útmutatóhoz előkészített linuxos gépen vagy virtuális gépen. 

### <a name="register-your-device-to-use-the-software-repository"></a>Eszköz regisztrálása a szoftveradattár használatához

Az IoT Edge-futtatókörnyezet futtatásához szükséges csomagok kezelése egy szoftveradattárban történik. Az IoT Edge-eszközt az adattárhoz való hozzáférésre konfigurálhatja. 

A jelen szakasz lépései az **Ubuntu 16.04** rendszerű eszközökre vonatkoznak. Ha másik Linux-verzióról szeretné elérni a szoftveradattárat, tekintse meg az [Azure IoT Edge-futtatókörnyezet telepítése Linuxon (x64)](how-to-install-iot-edge-linux.md) vagy az [Azure IoT Edge-futtatókörnyezet telepítése Linuxon (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) című részt.

1. Telepítse az adattár konfigurációját az IoT Edge-eszközként használt számítógépen.

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

2. Telepítsen egy nyilvános kulcsot az adattárhoz való hozzáféréshez.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>Tároló-futtatókörnyezet telepítése

Az IoT Edge-futtatókörnyezet egy tárolókészlet, és az IoT Edge-eszközön üzembe helyezett logika tárolókként van csomagolva. Készítse elő eszközét ezekhez az összetevőkhöz egy tároló-futtatókörnyezet telepítésével.

Frissítse az **apt-get** parancsot.

   ```bash
   sudo apt-get update
   ```

Telepítse a **Moby** tároló-futtatókörnyezetet.

   ```bash
   sudo apt-get install moby-engine
   ```

Telepítse a Moby CLI-parancsait. 

   ```bash
   sudo apt-get install moby-cli
   ```

### <a name="install-and-configure-the-iot-edge-security-daemon"></a>Az IoT Edge biztonsági démon telepítése és konfigurálása

A biztonsági démon rendszerszolgáltatásként lesz telepítve, így az IoT Edge-futtatókörnyezet minden alkalommal elindul az eszköz indításakor. A telepítés egy **hsmlib** verziót is magában foglal, amely lehetővé teszi, hogy a biztonsági démon kommunikáljon az eszköz hardveres biztonsági rendszerével. 

1. Töltse le és telepítse az IoT Edge biztonsági démont. 

   ```bash
   sudo apt-get update
   sudo apt-get install iotedge
   ```

2. Nyissa meg az IoT Edge konfigurációs fájlját. Ez egy védett fájl, ezért lehet, hogy megemelt jogosultsági szintre van szükség az eléréséhez.
   
   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

3. Adja hozzá az IoT Edge-eszköz kapcsolati sztringjét. Keresse meg a **device_connection_string** értéket, és frissítse arra a sztringre, amelyet az eszköz regisztrálása után másolt ki.

4. Mentse és zárja be a fájlt. 

   `CTRL + X`, `Y`, `Enter`

4. Indítsa újra az IoT Edge biztonsági démont.

   ```bash
   sudo systemctl restart iotedge
   ```

5. Ellenőrizze, hogy az Edge biztonsági démon rendszerszolgáltatásként fut-e.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Az Edge démon rendszerszolgáltatásként való futásának megtekintése](./media/quickstart-linux/iotedged-running.png)

   Az Edge biztonsági démon naplóit is megtekintheti a következő parancs futtatásával:

   ```bash
   journalctl -u iotedge
   ```

6. Tekintse meg az eszközön futó modulokat. 

   >[!TIP]
   >Ha `iotedge`-parancsokat futtat, a *sudo* kifejezést kell eléjük írnia. Az engedélyek frissítéséhez jelentkezzen ki a gépről, majd jelentkezzen újra be. Ezután már futtathat emelt szintű jogosultságok nélkül is `iotedge`-parancsokat. 

   ```bash
   sudo iotedge list
   ```

   ![Egy modul megtekintése az eszközön](./media/quickstart-linux/iotedge-list-1.png)

## <a name="deploy-a-module"></a>Modul üzembe helyezése

Azure IoT Edge-eszközeit kezelheti a felhőből, és üzembe helyezhet egy olyan modult, amely telemetriaadatokat küld az IoT Hubra.
![Eszköz regisztrálása][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Ebben a rövid útmutatóban létrehozott egy új IoT Edge-eszközt, és telepítette rajta az IoT Edge-futtatókörnyezetet. Ezután az Azure Portal segítségével úgy futtatta az IoT Edge-modult az eszközön, hogy magát az eszközt nem kellett módosítania. Ebben az esetben az Ön által továbbított modul az oktatóanyagokhoz használható környezeti adatokat hoz létre. 

Nyissa meg újra a parancssort a szimulált eszközt futtató számítógépen. Győződjön meg arról, hogy a felhőből üzembe helyezett modul fut az IoT Edge-eszközön:

   ```bash
   sudo iotedge list
   ```

   ![Három modul megtekintése az eszközön](./media/quickstart-linux/iotedge-list-2.png)

Tekintse meg a tempSensor modul által küldött üzeneteket:

  ```bash
   sudo iotedge logs tempSensor -f 
   ```

Kijelentkezést és bejelentkezést követően a *sudo* nem szükséges a fenti parancshoz.

![A modulból származó adatok megtekintése](./media/quickstart-linux/iotedge-logs.png)

Előfordulhat, hogy a hőmérsékletérzékelő modul az Edge Hubhoz való csatlakozásra vár, ha a napló utolsó sora `Using transport Mqtt_Tcp_Only`. Próbálja meg leállítani a modult, és hagyja, hogy az Edge-ügynök újraindítsa. A modult a következő paranccsal állíthatja le: `sudo docker stop tempSensor`.

Az eszköz által küldött telemetriát is megtekintheti a [Visual Studio Code Azure IoT Toolkit bővítményével](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tovább szeretne dolgozni az IoT Edge-oktatóanyagokkal, használhatja az ebben a rövid útmutatóban regisztrált és létrehozott eszközt. Ha nem, törölheti a létrehozott Azure-erőforrásokat, és eltávolíthatja az IoT Edge-futtatókörnyezetet az eszközről. 

### <a name="delete-azure-resources"></a>Azure-erőforrások törlése

Ha a virtuális gépet és az IoT Hubot egy új erőforráscsoportban hozta létre, törölheti azt a csoportot és az összes társított erőforrást. Ha van valami abban az erőforráscsoportban, amit meg szeretne tartani, csak azokat a különálló erőforrásokat törölje, amelyektől meg szeretne szabadulni. 

Erőforráscsoport eltávolításához hajtsa végre az alábbi lépéseket: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.
2. A **Szűrés név alapján...** mezőbe írja be az IoT Hubot tartalmazó erőforráscsoport nevét. 
3. Az eredménylistában kattintson az erőforráscsoporttól jobbra lévő **…** ikonra, majd kattintson az **Erőforráscsoport törlése** elemre.
4. A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be újra az erőforráscsoport nevét, majd kattintson a **Törlés** elemre. A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

### <a name="remove-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet eltávolítása

Ha el szeretné távolítani a telepítéseket az eszközéről, azt a következő parancsokkal teheti meg.  

Távolítsa el az IoT Edge-futtatókörnyezetet.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Ha eltávolította az IoT Edge-futtatókörnyezetet, az általa létrehozott tárolók leállnak, de továbbra is ott lesznek az eszközön. Tekintse meg az összes tárolót.

   ```bash
   sudo docker ps -a
   ```

Törölje azokat a tárolókat, amelyeket az IoT Edge-futtatókörnyezet hozott létre az eszközén. Ha más nevet adott neki, módosítsa a tempSensor tároló nevét. 

   ```bash
   sudo docker rm -f tempSensor
   sudo docker rm -f edgeHub
   sudo docker rm -f edgeAgent
   ```

Távolítsa el a tároló-futtatókörnyezetet.

   ```bash
   sudo apt-get remove --purge moby
   ```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új IoT Edge-eszközt, és az Azure IoT Edge felhőalapú felülettel kódot telepített az eszközre. Most már van egy szimulált eszköze, amely nyers adatokat hoz létre a környezetéről. 

Ez a rövid útmutató minden IoT Edge-oktatóanyag előfeltétele. Továbbléphet bármelyik oktatóanyagra, és megtudhatja, hogyan alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Érzékelők adatainak szűrése Azure Functions-függvényekkel](tutorial-deploy-function.md)


<!-- Images -->
[1]: ./media/quickstart-linux/view-module.png
[2]: ./media/quickstart-linux/install-edge-full.png
[3]: ./media/quickstart-linux/create-iot-hub.png
[4]: ./media/quickstart-linux/register-device.png
[5]: ./media/quickstart-linux/start-runtime.png
[6]: ./media/quickstart-linux/deploy-module.png
[7]: ./media/quickstart-linux/iotedged-running.png
[8]: ./media/tutorial-simulate-device-linux/running-modules.png
[9]: ./media/tutorial-simulate-device-linux/sensor-data.png

<!-- Links -->
[lnk-account]: https://azure.microsoft.com/free
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 

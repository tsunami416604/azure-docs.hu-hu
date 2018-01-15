---
title: "Azure IoT Edge Linux szimulálása |} Microsoft Docs"
description: "Az Azure IoT peremhálózati runtime telepítését Linux a szimulált eszköz, és az első modul telepítése"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 55770c92f5d5959e83066b425bc6ccf2b9dcc62e
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux-or-macos---preview"></a>Azure IoT peremhálózati telepítheti Linux vagy MacOS szimulált eszköz – előzetes

Az Azure IoT peremhálózati lehetővé teszi a elemzés és az adatok feldolgozása az eszközök, nem muszáj küldje le az adatok a felhőben. Az IoT-Edge oktatóanyagok bemutatják, hogyan lehet különböző típusú modulokkal, az Azure-szolgáltatások vagy egyéni kód beépített központi telepítéséhez, de először meg kell tesztelni az eszköz. 

Ezen oktatóanyag segítségével megtanulhatja a következőket:

1. IoT Hub létrehozása
2. Az IoT-peremhálózati eszköz regisztrálása
3. Az IoT-Edge futásidejű indítása
4. A modulok telepítése

![Útmutató architektúrája][2]

A szimulált eszköz, amely ebben az oktatóanyagban létrehozhat egy figyelő, amely hőmérséklet, páratartalom és nyomás adatokat hoz létre. Az egyéb Azure IoT peremhálózati oktatóanyagok végzett munkát itt elemezhetők az adatok az üzleti elemzések modulok üzembe helyezésével épül. 

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag a számítógépén vagy virtuális gép például az eszközök internetes hálózatát eszközök használja. Kapcsolja be a számítógép az IoT-peremhálózati eszköz, a következő szolgáltatások szükségesek:

* Python pip, az IoT-Edge futásidejű telepítéséhez.
   * Linux: `sudo apt-get install python-pip`.
   * MacOS: `sudo easy_install pip`.
* Docker modulok az IoT él futtatásához.
   * [Telepítse a Docker Linux] [ lnk-docker-ubuntu] , és győződjön meg arról, hogy fut-e. 
   * [Telepítse a Docker Mac] [ lnk-docker-mac] , és győződjön meg arról, hogy fut-e. 

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Indítsa el az oktatóanyag az IoT Hub létrehozása.
![Az IoT Hub létrehozása][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Az IoT-peremhálózati eszköz regisztrálása

Az IoT-peremhálózati eszköz regisztrálása az újonnan létrehozott IoT Hub.
![Eszköz regisztrálása][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Telepítse és indítsa el az IoT-Edge futásidejű

Telepítse, és indítsa el az Azure IoT peremhálózati futásidejű az eszközön. 
![Eszköz regisztrálása][5]

Az IoT-Edge futásidejű minden IoT peremhálózati eszközön van telepítve. Ez magában foglalja a két modulok. A **IoT peremhálózati ügynök** elősegíti a központi telepítési és figyelési modulokat az IoT-peremhálózati eszközön. A **peremhálózati IoT hub** kezeli a kommunikációt az IoT-peremhálózati eszközön modulokat, valamint az eszköz és az IoT-központ között. A futtatókörnyezet új eszközén konfigurálásakor csak a IoT peremhálózati ügynök első időpontban fog elindulni. A peremhálózati IoT hub később származik, a modul telepítésekor. 

A számítógépen, ahol az IoT-peremhálózati eszköz fogja futtatni töltse le az IoT-Edge vezérlő parancsfájl:
```cmd
sudo pip install -U azure-iot-edge-runtime-ctl
```

A futtatókörnyezet származó az előző szakaszban az IoT peremhálózati eszköz kapcsolati karakterlánc konfigurálása:
```cmd
sudo iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

Indítsa el a futtatókörnyezet:
```cmd
sudo iotedgectl start
```

Ellenőrizze, hogy az IoT-Edge-ügynök fut-e modulként Docker:
```cmd
sudo docker ps
```

![A Docker edgeAgent lásd:](./media/tutorial-simulate-device-linux/docker-ps.png)

## <a name="deploy-a-module"></a>A modulok telepítése

Az Azure IoT peremhálózati eszköz felügyelete a felhőből, egy modult, amely telemetriai adatokat küld az IoT-központ telepítéséhez.
![Eszköz regisztrálása][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Adatok generált megtekintése

Ebben az oktatóanyagban létre egy új IoT peremhálózati eszköz, és telepítve van-e az IoT-Edge futásidejű. Az Azure-portálon, majd leküldéses egy IoT peremhálózati modul futtatható az eszközön anélkül, hogy a módosításokat az magához az eszközhöz használt. A modult, amely akkor leküldött ebben az esetben is használhatja az oktatóanyagok a környezeti adatokat hoz létre. 

Nyissa meg a parancssort a szimulált eszköz újra futtatni a számítógépen. Győződjön meg arról, hogy fut-e a modul telepítve a felhőben az IoT-peremhálózati eszközön:

```cmd
sudo docker ps
```

![Három modulok megtekintése az eszközön](./media/tutorial-simulate-device-linux/docker-ps2.png)

A tempSensor modulból a felhőbe küldött üzenetek megjelenítése:

```cmd
sudo docker logs -f tempSensor
```

![A modul az adatok megtekintése](./media/tutorial-simulate-device-linux/docker-logs.png)

A telemetriai adatokat küld az eszköz segítségével is megtekintheti a [IoT-központ explorer eszköz][lnk-iothub-explorer]. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létre egy új IoT peremhálózati eszköz, és az Azure IoT peremhálózati felhő illesztőfelületet kódot az eszköz telepítésére szolgál. Most hogy a szimulált eszköz nyers adatok a környezetre vonatkozó. 

Ez az oktatóanyag az előfeltételként szükséges összes az IoT-Edge oktatóanyagok. Megtudhatja, hogyan Azure IoT peremhálózati segítségével kapcsolja be az adatok az üzleti elemzések készítése a peremhálózaton egyéb oktatóprogramok valamelyikét be tovább.

> [!div class="nextstepaction"]
> [Fejlesztésekor és telepítésekor C#-kódban modulként](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/tutorial-install-iot-edge/view-module.png
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-docker-mac]: https://docs.docker.com/docker-for-mac/install/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer

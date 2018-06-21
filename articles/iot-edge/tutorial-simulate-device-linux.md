---
title: Az Azure IoT Edge szimulálása Linuxon | Microsoft Docs
description: Az Azure IoT Edge-futtatókörnyezet telepítése szimulált eszközre Linuxon, valamint az első modul telepítése
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 01/11/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 0b8b2658af9173cea6a7cdcb0147c7b0dc13a455
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630996"
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux-or-macos---preview"></a>Azure IoT Edge telepítése szimulált eszközre Linux vagy MacOS rendszerben - bemutató

Az Azure IoT Edge segítségével elemzéseket és adatfeldolgozást végezhet el az eszközén anélkül, hogy az összes adatot a felhőbe kellene továbbítania. Az IoT Edge-oktatóanyagok ismertetik, hogyan helyezhetők üzembe az Azure-szolgáltatásokból vagy egyedi kódból létrehozott különböző típusú modulok, de először rendelkeznie kell egy eszközzel a teszteléshez. 

Ezen oktatóanyag segítségével megtanulhatja a következőket:

1. IoT Hub létrehozása
2. IoT Edge-eszköz regisztrálása
3. Az IoT Edge-futtatókörnyezet elindítása
4. Modul üzembe helyezése

![Bemutató architektúra][2]

A jelen oktatóanyagban létrehozott szimulált eszköz egy figyelő, amely hőmérséklet-, páratartalom- és nyomásadatokat hoz létre. A további Azure IoT Edge-oktatóanyagok az itt elvégzett munkára építkeznek olyan modulok üzembe helyezésével, amelyek az adatok elemzésével üzleti megállapításokat hoznak létre. 

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag az eszközök internetes hálózatához csatlakozó eszközként használja a számítógépet vagy virtuális gépet. A gépének IoT Edge-eszközzé való átalakításához az alábbi szolgáltatásokra van szükség:

* Python pip az IoT Edge-futtatókörnyezet telepítéséhez.
   * Linux: `sudo apt-get install python-pip`.
     * _Vegye figyelembe, hogy bizonyos disztribúciók (pl.: Raspbian) esetén bizonyos pip-csomagok frissítésére és további függőségek telepítésére lehet szükség:_
     ```
     sudo pip install --upgrade setuptools pip
     
     sudo apt-get install python2.7-dev libffi-dev libssl-dev
     ```
   * MacOS: `sudo easy_install pip`.
* Docker az IoT Edge-modulok futtatásához
   * [Telepítse a Linuxhoz készült Dockert][lnk-docker-ubuntu] és ellenőrizze, hogy fut-e. 
   * [Telepítse a Machez készült Dockert][lnk-docker-mac] és ellenőrizze, hogy fut-e. 

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Az oktatóanyag első lépéseként hozza létre az IoT Hubot.
![IoT Hub létrehozása][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>IoT Edge-eszköz regisztrálása

Regisztráljon egy IoT Edge-eszközt az újonnan létrehozott IoT Hubon.
![Eszköz regisztrálása][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet telepítése és elindítása

Telepítse és indítsa el eszközén az Azure IoT Edge-futtatókörnyezetet. 
![Eszköz regisztrálása][5]

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Két modulból áll. Az **IoT Edge-ügynök** a modulok üzembe helyezését és monitorozását segíti az IoT Edge-eszközön. Az **IoT Edge-központ** az IoT Edge-eszközön lévő modulok, valamint az eszköz és az IoT Hub közötti kommunikációt kezeli. Amikor új eszközön konfigurálja a futtatókörnyezetet, először csak az IoT Edge-ügynök indul el. Az IoT Edge-központra a modulok üzembe helyezésekor lesz szükség. 

Töltse le az IoT Edge-vezérlőszkriptet az IoT Edge-eszközt futtató gépre:
```cmd
sudo pip install -U azure-iot-edge-runtime-ctl
```

Konfigurálja a futtatókörnyezetet IoT Edge-eszköz előző szakaszban megadott kapcsolati sztringjével:
```cmd
sudo iotedgectl setup --connection-string "{device connection string}" --nopass
```

Indítsa el a futtatókörnyezetet:
```cmd
sudo iotedgectl start
```

Ellenőrizze a Dockerben, hogy az IoT Edge-ügynök modulként fut-e:
```cmd
sudo docker ps
```

![Lásd a Docker edgeAgent paraméterét](./media/tutorial-simulate-device-linux/docker-ps.png)

## <a name="deploy-a-module"></a>Modul üzembe helyezése

A felhőből kezelheti Azure IoT Edge-eszközeit, és olyan modulokat helyezheti üzembe, amelyek telemetriaadatokat küldenek az IoT Hubnak.
![Eszköz regisztrálása][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Ebben az oktatóanyagban létrehozott egy új IoT Edge-eszközt. és telepítette rajta az IoT Edge-futtatókörnyezetet. Ezután az Azure Portal segítségével úgy futtatta az IoT Edge-modult az eszközön, hogy magát az eszközt nem kellett módosítania. Ebben az esetben az Ön által továbbított modul az oktatóanyagokhoz használható környezeti adatokat hoz létre. 

Nyissa meg újra a parancssort a szimulált eszközt futtató számítógépen. Győződjön meg arról, hogy a felhőből üzembe helyezett modul fut az IoT Edge-eszközön:

```cmd
sudo docker ps
```

![Három modul megtekintése az eszközön](./media/tutorial-simulate-device-linux/docker-ps2.png)

A tempSensor modul által a felhőbe küldött üzenetek megtekintése:

```cmd
sudo docker logs -f tempSensor
```

![A modulból származó adatok megtekintése](./media/tutorial-simulate-device-linux/docker-logs.png)

Az eszköz által küldött telemetriát is megtekintheti az [IoT Hub Explorer eszközzel][lnk-iothub-explorer]. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy új IoT Edge-eszközt, és az Azure IoT Edge felhőalapú felülettel kódot telepített az eszközre. Most már van egy szimulált eszköze, amely nyers adatokat hoz létre a környezetéről. 

Ez az oktatóanyag minden további IoT Edge-oktatóanyag előfeltétele. Továbbléphet bármely másik oktatóanyagra, és megtudhatja, hogyan alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [C#-kód modulként való fejlesztése és üzembe helyezése](tutorial-csharp-module.md)


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

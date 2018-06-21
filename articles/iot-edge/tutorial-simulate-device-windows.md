---
title: Az Azure IoT Edge szimulálása Windowson | Microsoft Docs
description: Az Azure IoT Edge-futtatókörnyezet telepítése szimulált eszközre Windowson, valamint az első modul telepítése
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/16/2017
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 7ad99a49a578de4997a2d76d48da33aba6847f3c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631190"
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>Azure IoT Edge telepítése szimulált eszközre Windows rendszerben – bemutató

Az Azure IoT Edge segítségével elemzéseket és adatfeldolgozást végezhet el az eszközén anélkül, hogy az összes adatot a felhőbe kellene továbbítania. Az IoT Edge-oktatóanyagok ismertetik, hogyan helyezhetők üzembe az Azure-szolgáltatásokból vagy egyedi kódból létrehozott különböző típusú modulok, de először rendelkeznie kell egy eszközzel a teszteléshez. 

Ezen oktatóanyag segítségével megtanulhatja a következőket:

1. IoT Hub létrehozása
2. IoT Edge-eszköz regisztrálása
3. Az IoT Edge-futtatókörnyezet elindítása
4. Modul üzembe helyezése

![Bemutató architektúra][2]

A jelen oktatóanyagban létrehozott szimulált eszköz egy szélturbina-figyelő, amely hőmérséklettel, páratartalommal és nyomással kapcsolatos adatokat állít elő. Ezek az adatok azért lehetnek érdekesek, mert a turbinák különböző környezeti feltételek mellett más-más hatékonysággal üzemelnek. A további Azure IoT Edge-oktatóanyagok az itt elvégzett munkára építkeznek olyan modulok üzembe helyezésével, amelyek az adatok elemzésével üzleti megállapításokat hoznak létre. 

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy Windows rendszerű számítógépet vagy virtuális gépet használ az eszközök internetes hálózatához csatlakozó eszköz szimulálására. 

>[!TIP]
>Ha a Windows virtuális gépen fut, engedélyezze a [beágyazott virtualizálást][lnk-nested], és foglaljon le legalább 2 GB memóriát. 

1. Győződjön meg arról, hogy a Windows támogatott verzióját használja:
   * Windows 10 
   * Windows Server
2. Telepítse a [Windowshoz készült Dockert][lnk-docker], és ellenőrizze, hogy fut-e.
3. Telepítse a [Pythont a Windowsra][lnk-python], és ellenőrizze, hogy tudja-e használni a pip parancsot. A oktatóanyag tesztelése a Python >=2.7.9-es és >=3.5.4-es verzióival történt.  
4. Az IoT Edge-vezérlőszkript letöltéséhez futtassa a következő parancsot.

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Az Azure IoT Edge Windows- és Linux-tárolókat is tud futtatni. Windows-tárolókat akkor használhat, ha a gépén az alábbi Windows-verziók valamelyike fut:
>    * Windows 10 Fall Creators Update
>    * Windows Server 1709 (16299-es build)
>    * Windows IoT Core (16299-es build) egy x64-alapú eszközön
>
> Windows IoT Core esetén kövesse az [IoT Edge-futtatókörnyezet Windows IoT Core-on történő telepítésével][lnk-install-iotcore] foglalkozó témakörben leírt utasításokat. Egyéb esetben egyszerűen [konfigurálja a Dockert Windows-tárolók használatára][lnk-docker-containers]. Ellenőrizze az előfeltételeket az alábbi paranccsal:
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```


## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Az oktatóanyag első lépéseként hozza létre az IoT Hubot.
![IoT Hub létrehozása][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>IoT Edge-eszköz regisztrálása

Regisztráljon egy IoT Edge-eszközt az újonnan létrehozott IoT Hubon.
![Eszköz regisztrálása][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet konfigurálása

Telepítse és indítsa el eszközén az Azure IoT Edge-futtatókörnyezetet. 
![Eszköz regisztrálása][5]

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Két modulból áll. Az **IoT Edge-ügynök** a modulok üzembe helyezését és monitorozását segíti az IoT Edge-eszközön. Az **IoT Edge-központ** az IoT Edge-eszközön lévő modulok, valamint az eszköz és az IoT Hub közötti kommunikációt kezeli. Amikor új eszközön konfigurálja a futtatókörnyezetet, először csak az IoT Edge-ügynök indul el. Az IoT Edge-központra a modulok üzembe helyezésekor lesz szükség. 


Konfigurálja a futtatókörnyezetet IoT Edge-eszköz előző szakaszban megadott kapcsolati sztringjével.

```cmd
iotedgectl setup --connection-string "{device connection string}" --nopass
```

Indítsa el a futtatókörnyezetet.

```cmd
iotedgectl start
```

Ellenőrizze a Dockerben, hogy az IoT Edge-ügynök modulként fut-e.

```cmd
docker ps
```

![Lásd a Docker edgeAgent paraméterét](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>Modul üzembe helyezése

A felhőből kezelheti Azure IoT Edge-eszközeit, és olyan modulokat helyezheti üzembe, amelyek telemetriaadatokat küldenek az IoT Hubnak.
![Eszköz regisztrálása][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Ebben az oktatóanyagban létrehozott egy új IoT Edge-eszközt. és telepítette rajta az IoT Edge-futtatókörnyezetet. Ezután az Azure Portal segítségével úgy futtatta az IoT Edge-modult az eszközön, hogy magát az eszközt nem kellett módosítania. Ebben az esetben az Ön által továbbított modul az oktatóanyagokhoz használható környezeti adatokat hoz létre. 

Nyissa meg újra a parancssort a szimulált eszközt futtató számítógépen. Győződjön meg arról, hogy a felhőből üzembe helyezett modul fut az IoT Edge-eszközön. 

```cmd
docker ps
```

![Három modul megtekintése az eszközön](./media/tutorial-simulate-device-windows/docker-ps2.png)

Tekintse meg a tempSensor modul által a felhőbe küldött üzeneteket. 

```cmd
docker logs -f tempSensor
```

![A modulból származó adatok megtekintése](./media/tutorial-simulate-device-windows/docker-logs.png)

Az eszköz által küldött telemetriát is megtekintheti az [IoT Hub Explorer eszközzel][lnk-iothub-explorer]. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy új IoT Edge-eszközt, és az Azure IoT Edge felhőalapú felülettel kódot telepített az eszközre. Most már van egy szimulált eszköze, amely nyers adatokat hoz létre a környezetéről. 

Ez az oktatóanyag minden további IoT Edge-oktatóanyag előfeltétele. Továbbléphet bármely másik oktatóanyagra, és megtudhatja, hogyan alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [C#-kód modulként való fejlesztése és üzembe helyezése](tutorial-csharp-module.md)

<!-- Images -->
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md
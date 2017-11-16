---
title: "Azure IoT Edge Linux szimulálása |} Microsoft Docs"
description: "Az Azure IoT peremhálózati runtime telepítését Linux a szimulált eszköz, és az első modul telepítése"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 041919fd729880d429e08d8942f8d1ee087ccf61
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux---preview"></a>Azure IoT peremhálózati telepítése Linux a szimulált eszköz – előzetes

Az Azure IoT peremhálózati lehetővé teszi a elemzés és az adatok feldolgozása az eszközök, nem muszáj küldje le az adatok a felhőben. Az IoT-Edge oktatóanyagok bemutatják, hogyan lehet különböző típusú modulokkal, az Azure-szolgáltatások vagy egyéni kód beépített központi telepítéséhez, de először meg kell tesztelni az eszköz. 

Ez az oktatóanyag bemutatja, hogyan szimulált IoT peremhálózati eszköz, majd telepítse a modul, amely az érzékelő adatokat állít elő létrehozásakor. Az alábbiak végrehajtásának módját ismerheti meg:

![Útmutató architektúrája][2]

A szimulált eszköz, amely ebben az oktatóanyagban létrehozhat egy figyelő, amely hőmérséklet, páratartalom és nyomás adatokat hoz létre. Az egyéb Azure IoT peremhálózati oktatóanyagok végzett munkát itt elemezhetők az adatok az üzleti elemzések modulok üzembe helyezésével épül. 

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy használ egy számítógépet vagy Linux rendszerű virtuális gép az eszközök internetes hálózatát eszközök szimulálására. A következő szolgáltatások sikeres telepítéséhez az IoT-peremhálózati eszköz van szükség:

- [Telepítse a Docker Linux] [ lnk-docker-ubuntu] , és győződjön meg arról, hogy fut-e. 
- A legtöbb Linux terjesztésekről, beleértve az Ubuntu, már telepített Python 2.7. A következő parancs használatával győződjön meg arról, hogy telepítve van-e a pip: `sudo apt-get install python-pip`.

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

Az IoT-Edge futásidejű minden IoT peremhálózati eszközön van telepítve. Ez magában foglalja a két modulok. Először az IoT-Edge ügynök elősegíti a központi telepítési és figyelési modulokat az IoT-peremhálózati eszközön. Második a peremhálózati IoT hub kezeli a kommunikációt az IoT-peremhálózati eszközön modulokat, valamint az eszköz és az IoT-központ között. 

Az alábbi lépések segítségével telepítheti, és indítsa el az IoT-Edge futásidejű:

1. A számítógépen, ahol az IoT-peremhálózati eszköz fogja futtatni az IoT-Edge vezérlő parancsprogram letöltése.

   ```
   sudo pip install -U azure-iot-edge-runtime-ctl
   ```

1. A futtatókörnyezet konfigurálása az IoT-peremhálózati eszköz kapcsolati karakterlánccal előző szakaszából.

   ```
   sudo iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
   ```

1. Indítsa el a futtatókörnyezetben.

   ```
   sudo iotedgectl start
   ```

1. Ellenőrizze, hogy az IoT-Edge-ügynök fut-e modulként Docker.

   ```
   sudo docker ps
   ```

## <a name="deploy-a-module"></a>A modulok telepítése

Az Azure IoT peremhálózati eszköz felügyelete a felhőből, egy modult, amely telemetriai adatokat küld az IoT-központ telepítéséhez.
![Eszköz regisztrálása][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Adatok generált megtekintése

A gyors üzembe helyezés létrehozott egy új IoT peremhálózati eszköz, és telepítve van-e az IoT-Edge futásidejű. Az Azure-portálon, majd leküldéses egy IoT peremhálózati modul futtatható az eszközön anélkül, hogy a módosításokat az magához az eszközhöz használt. A modult, amely akkor leküldött ebben az esetben is használhatja az oktatóanyagok a környezeti adatokat hoz létre. 

A tempSensor modulból küldött üzenetek megjelenítése:

```cmd/sh
docker logs -f tempSensor
```

A telemetriai adatokat küld az eszköz segítségével is megtekintheti a [IoT-központ explorer eszköz][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Következő lépések

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
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer

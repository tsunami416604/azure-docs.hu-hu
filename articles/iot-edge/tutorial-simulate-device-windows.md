---
title: "Azure IoT Edge Windows szimulálása |} Microsoft Docs"
description: "A szimulált eszköz a Windows Azure IoT peremhálózati futásidejű telepítse, és az első modul központi telepítése"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/16/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0207418cf71902ce9bc9d2911124d1d46889d893
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>A szimulált eszköz a Windows Azure IoT peremhálózati telepített – előzetes

Az Azure IoT peremhálózati lehetővé teszi a elemzés és az adatok feldolgozása az eszközök, nem muszáj küldje le az adatok a felhőben. Az IoT-Edge oktatóanyagok bemutatják, hogyan lehet különböző típusú modulokkal, az Azure-szolgáltatások vagy egyéni kód beépített központi telepítéséhez, de először meg kell tesztelni az eszköz. 

Ezen oktatóanyag segítségével megtanulhatja a következőket:

1. IoT Hub létrehozása
2. Az IoT-peremhálózati eszköz regisztrálása
3. Az IoT-Edge futásidejű indítása
4. A modulok telepítése

![Útmutató architektúrája][2]

A szimulált eszköz, amely ebben az oktatóanyagban létrehozhat egy figyelőt a hőmérséklet, páratartalom és nyomás adatokat generáló határi turbina. Továbbra is érdekli ezeket az adatokat, mert a turbinák hatékonyságát, attól függően, hogy a időjárási feltételek különböző szintjein végrehajtani. Az egyéb Azure IoT peremhálózati oktatóanyagok végzett munkát itt elemezhetők az adatok az üzleti elemzések modulok üzembe helyezésével épül. 

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy használ egy számítógépet vagy a Windows rendszerű virtuális gép az eszközök internetes hálózatát eszközök szimulálására. 

>[!TIP]
>Ha a Windows virtuális gépként fut, engedélyezni [beágyazott virtualizálási] [ lnk-nested] és legalább 2 GB memóriát lefoglalni. 

1. Ellenőrizze, hogy egy támogatott Windows-verzió használata:
   * Windows 10 
   * Windows Server
2. Telepítés [Docker for Windows] [ lnk-docker] , és győződjön meg arról, hogy fut-e.
3. Telepítés [Python 2.7-es Windows] [ lnk-python] , és győződjön meg arról, hogy a pip paranccsal.
4. A következő parancsot a IoT peremhálózati vezérlő parancsprogram letöltése.

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT peremhálózati futtathat Windows tárolók vagy a Linux-tárolók. Ha a következő Windows verziók egyikét futtatja, használhatja a Windows tárolók:
>    * Windows 10 alá esik Creators frissítése
>    * Windows Server 1709 (16299 összeállítása)
>    * Windows IoT mag (Build 16299) x64-alapú eszköz
>
> A Windows az IoT-Core, kövesse az utasításokat a [az IoT-Edge futásidejű telepíthető Windows IoT Core][lnk-install-iotcore]. Ellenkező esetben egyszerűen [konfigurálása a Windows-tárolók használatára Docker][lnk-docker-containers]. Az alábbi parancs segítségével az Előfeltételek ellenőrzése:
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```


## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Indítsa el az oktatóanyag az IoT Hub létrehozása.
![Az IoT Hub létrehozása][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Az IoT-peremhálózati eszköz regisztrálása

Az IoT-peremhálózati eszköz regisztrálása az újonnan létrehozott IoT Hub.
![Eszköz regisztrálása][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Az IoT-Edge futásidejű konfigurálása

Telepítse, és indítsa el az Azure IoT peremhálózati futásidejű az eszközön. 
![Eszköz regisztrálása][5]

Az IoT-Edge futásidejű minden IoT peremhálózati eszközön van telepítve. Ez magában foglalja a két modulok. A **IoT peremhálózati ügynök** elősegíti a központi telepítési és figyelési modulokat az IoT-peremhálózati eszközön. A **peremhálózati IoT hub** kezeli a kommunikációt az IoT-peremhálózati eszközön modulokat, valamint az eszköz és az IoT-központ között. A futtatókörnyezet új eszközén konfigurálásakor csak a IoT peremhálózati ügynök első időpontban fog elindulni. A peremhálózati IoT hub később származik, a modul telepítésekor. 


A futtatókörnyezet konfigurálása az IoT-peremhálózati eszköz kapcsolati karakterlánccal előző szakaszából.

```cmd
iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

Indítsa el a futtatókörnyezetben.

```cmd
iotedgectl start
```

Ellenőrizze, hogy az IoT-Edge-ügynök fut-e modulként Docker.

```cmd
docker ps
```

![A Docker edgeAgent lásd:](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>A modulok telepítése

Az Azure IoT peremhálózati eszköz felügyelete a felhőből, egy modult, amely telemetriai adatokat küld az IoT-központ telepítéséhez.
![Eszköz regisztrálása][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Adatok generált megtekintése

Ebben az oktatóanyagban létre egy új IoT peremhálózati eszköz, és telepítve van-e az IoT-Edge futásidejű. Az Azure-portálon, majd leküldéses egy IoT peremhálózati modul futtatható az eszközön anélkül, hogy a módosításokat az magához az eszközhöz használt. A modult, amely akkor leküldött ebben az esetben is használhatja az oktatóanyagok a környezeti adatokat hoz létre. 

Nyissa meg a parancssort a szimulált eszköz újra futtatni a számítógépen. Győződjön meg arról, hogy fut-e a modul telepítve a felhőben az IoT-peremhálózati eszközön. 

```cmd
docker ps
```

![Három modulok megtekintése az eszközön](./media/tutorial-simulate-device-windows/docker-ps2.png)

A tempSensor modulból a felhőbe küldött üzenetek megjelenítése. 

```cmd
docker logs -f tempSensor
```

![A modul az adatok megtekintése](./media/tutorial-simulate-device-windows/docker-logs.png)

A telemetriai adatokat küld az eszköz segítségével is megtekintheti a [IoT-központ explorer eszköz][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létre egy új IoT peremhálózati eszköz, és az Azure IoT peremhálózati felhő illesztőfelületet kódot az eszköz telepítésére szolgál. Most hogy a szimulált eszköz nyers adatok a környezetre vonatkozó. 

Ez az oktatóanyag az előfeltételként szükséges összes az IoT-Edge oktatóanyagok. Megtudhatja, hogyan Azure IoT peremhálózati segítségével kapcsolja be az adatok az üzleti elemzések készítése a peremhálózaton egyéb oktatóprogramok valamelyikét be tovább.

> [!div class="nextstepaction"]
> [Fejlesztésekor és telepítésekor C#-kódban modulként](tutorial-csharp-module.md)

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